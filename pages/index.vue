<template lang="html">

  <main>
    <header class="main-header mb">
      <h1>Monzo API</h1>
    </header>
    <section class="flex-wrapper">
      <div class="flex-item pad">
        <label for="mClientId" class="c100 mt" >Monzo Client ID. <a href="https://developers.monzo.com/apps/home" class="tiny">Find it here</a></label>
        <input v-model="mClientId" id="mClientId" class="mt pad padfill" />
        <label for="mClientSecret" class="c100 mt" >Monzo Secret</label>
        <input v-model="mClientSecret" id="mClientSecret" class="mt pad padfill" />
        <button class="mt pad" @click="updateCredentials">Update</button>
      </div>
      <div class="flex-item">
        <div class="status-bar">
          <span v-if="!isAuthenticated">
            Not Logged in
            <a :href="`https://auth.monzo.com/?client_id=${mClientId}&redirect_uri=${redirectUri}&response_type=code&state=12345`">Click here to auth</a>
          </span>
          <span v-if="isAuthenticated">
            Authenticated. Access token: {{ mClientAccessToken }}
            <button @click="logout">Log out</button>
          </span>

        </div>
      </div>
    </section>
  </main>


</template>

<script>
export default {
  data(){
    return {
      isAuthenticated: false,
      mClientAccessToken: null,
      mClientId: null,
      mClientSecret: null,
      redirectUri: "http://localhost:3000"
    }
  },
  methods: {
    async handleAccessToken(){
      if (window.location.search.indexOf("?code") > -1 && !this.mClientAccessToken){
        console.log("handle token for me please...");
        let authCode = window.location.search.split("?code=")[1].split("&state")[0];
        let formData = new FormData();
        formData.append("grant_type", "authorization_code");
        formData.append("client_id", this.mClientId);
        formData.append("client_secret", this.mClientSecret);
        formData.append("redirect_uri", this.redirectUri);
        formData.append("code", authCode);
        let res = await this.$axios.post("https://api.monzo.com/oauth2/token", formData);
        if (res.data.access_token){
          this.isAuthenticated = true;
          this.mClientAccessToken = res.data.access_token;
          this.updateCredentials();
        }
      }
      else {
        this.isAuthenticated = true;
      }
    },
    async logout(){
      let formData = new FormData();
      formData.append("Authorization", `Bearer ${this.accessToken}`);
      let res = await this.$axios.post("https://api.monzo.com/oauth2/logout", formData);
      this.accessToken = null;
      this.isAuthenticated = false;
    },
    retreiveCredentials(){
      let credentials = localStorage.getItem("mMonzoCredentials");
      if (credentials.length > 0){
        let parsed = JSON.parse(credentials);
        this.mClientId = parsed.mClientId;
        this.mClientSecret = parsed.mClientSecret;
        this.mClientAccessToken = parsed.mClientAccessToken
      }
    },
    updateCredentials(){
      if (this.mClientId && this.mClientSecret){
        localStorage.setItem("mMonzoCredentials", JSON.stringify({
          mClientId: this.mClientId,
          mClientSecret: this.mClientSecret,
          mClientAccessToken: this.mClientAccessToken
        }));
      }
    }
  },
  async mounted(){
    await this.retreiveCredentials();
    this.handleAccessToken();
  }
}
</script>

<style lang="css" >

  h1, h2, h3, h4, h5, h6 {
    margin: 0;
    padding: 0;
  }

  body {
    margin: 0px;
    padding: 0px;
    font-family: monospace;
  }

  a {
    text-decoration: none;
    color: rgb(25, 160, 170);
  }

  .flex-wrapper {
    display: flex;
  }

  .flex-item {
    width: 50%;
  }

  .main-header {
    padding: 8px;
    background: rgba(0,0,0,0.1);
  }

  .mt {
    margin-top: 8px;
  }

  .mb {
    margin-bottom: 8px;
  }

  .pad {
    padding: 8px;
  }

  .c100 {
    width: 100%;
    display: inline-block;
  }

  input {
    width: calc(100% - 16px);
    outline: none;
    border: 1px solid rgb(25, 160, 170);
    font-family: monospace;
  }

   button {
    background: rgba(25, 160, 170, 0.17);
    border: 1px solid rgb(25, 160, 170);
    color:  rgb(11, 95, 101);
    font-family: monospace;
  }

  .tiny {
    font-size: 11px;
  }

</style>
