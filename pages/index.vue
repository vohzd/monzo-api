<template lang="html">

  <main>
    <header class="main-header mb">
      <h1>Monzo API</h1>
    </header>
    <section class="flex-wrapper">
      <div class="flex-item pad">
        <label for="mClientId" class="c100 mt" >Monzo Client ID. <a href="https://developers.monzo.com/apps/home" class="tiny">Find it here</a></label>
        <input v-model="mClientId" id="mClientId" class="mt c100" />
        <label for="mClientSecret" class="c100 mt" >Monzo Secret</label>
        <input v-model="mClientSecret" id="mClientSecret" class="mt c100" />
      </div>
      <div class="flex-item">
        <div class="status-bar">
          {{ this.status }}
          <a :href="`https://auth.monzo.com/?client_id=${mClientId}&redirect_uri=http://localhost:3000&response_type=code&state=12345`">
            Click here to auth
          </a>
        </div>
      </div>
    </section>
  </main>


</template>

<script>
export default {
  data(){
    return {
      status: "Not Logged In",
      mClientId: null,
      mClientSecret: null,
      redirectUri: window.location
    }
  },
  methods: {
    handleAccessToken(){
      console.log("handle token for me please...");
      console.log(this.redirectUri);
      /*
      if (window.location.search.indexOf("?code") > -1){
        let authCode = window.location.search.split("?code=")[1].split("&state")[0];
        let formData = new FormData();
        formData.append("grant_type", "authorization_code");
        formData.append("client_id", this.mClientId);
        formData.append("client_secret", this.mClientSecret);
        formData.append("redirect_uri", "http://localhost:3000");
        formData.append("code", authCode);
            this.$axios({
                  method: 'post',
                  url: 'https://api.monzo.com/oauth2/token',
                  data: formData,
                })
                .then(function (response) {
                    //handle success
                    console.log(response);
                })
                .catch(function (response) {
                    //handle error
                    console.log(response);
                });
      }*/
    }
  },
  async mounted(){
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
    color: rgb(40, 37, 193);
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

  .tiny {
    font-size: 11px;
  }

</style>
