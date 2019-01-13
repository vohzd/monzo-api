<template lang="html">
  <section>
    <h1>Monzo</h1>
    <p>{{ this.authed }}</p>
    <a href="https://auth.monzo.com/?client_id=oauth2client_00009elWsZBVOuml6LcLEf&redirect_uri=http://localhost:3000&response_type=code&state=12345">Click here to auth</a>
  </section>
</template>

<script>
export default {
  data(){
    return {
      authed: false
    }
  },
  async mounted(){
    if (window.location.search.indexOf("?code") > -1){
      let authCode = window.location.search.split("?code=")[1].split("&state")[0];

      let formData = new FormData();
      formData.append("grant_type", "authorization_code");
      formData.append("client_id", "oauth2client_00009elWsZBVOuml6LcLEf");
      formData.append("client_secret", "SECRET");
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

    }
  }
}
</script>

<style lang="css" scoped>
</style>
