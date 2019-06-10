<template>
<div>
  <h2>Enter your steam id</h2>
  <input v-model="account" id="steam_id" placeholder="Enter your account here">

  <div>
    <input type="submit" v-on:click="linkAccount(account)">
    <h2>session: {{ session_id }} </h2>
    <h2>timestamp: {{ timestamp }} </h2>
    <h2>account: {{ account }} </h2>
  </div>
</div>

</template>

<script>
import md5 from 'md5';
import Moment from 'moment';
import axios from 'axios';

export default {
  name: "Player",
  data: function () {
    return {
      devID: '',
      authKey: '',
      realm_url: 'http://api.realmroyale.com/realmapi.svc',
      session_id: null,
      timestamp: null,
      account: null
    }
  },
  // CreateSession[ResponseFormat]/{devID}/{signature}/{timestamp}
  mounted: function () {
    this.timestamp = this.getTimestamp()
    var sig = this.getSignature('createsession', this.timestamp);

    axios
      .get(`${this.realm_url}/CreateSessionJSON/${this.devID}/${sig}/${this.timestamp}`)
      .then(response => this.session_id = response.data.session_id)
  },
  methods: {
    getTimestamp: function ()  {
      return Moment.utc().format ('YYYYMMDDHHmmss');
    },
    getSignature: function (method, time) {
      return md5(`${this.devID}${method}${this.authKey}${time}`);
      },

    // GetPlayer/{devId}/{signature}/{session_id}/{timestamp}/{playerId}/{platform}
    // SearchPlayers/{devId}/{signature}/{session_id}/{timestamp}/{playerId}
    linkAccount: function (account) {
      var sig = this.getSignature('getplayer', this.timestamp);

      axios
        .get(`${this.realm_url}/GetPlayerJSON/${this.devID}/${sig}/${this.session_id}/${this.timestamp}/${account}/steam_id`)
        .then(response => this.account = response.data)
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
