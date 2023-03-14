<template>
  <div>
    <Header />
    <h1>Create New Rule</h1>

    <div>
      <label for="ruleName">
        New Rule Name:
        <input type="text" name="ruleName" v-model="name" />
      </label>

      <label for="ruleName">
        Rule Status:
        <input
          type="text"
          name="ruleName"
          placeholder="0 for INACTIVE or 1 for ACTIVE"
          v-model="status"
        />
      </label>

      <button @click="createNewRule">Create</button>
    </div>

    <h3 v-if="showCreated"> Entity created successfully! </h3>
    <br />
    <br />
    <span v-if="showResponse"> 
        <h3>Response printed on console:</h3>
        {{  this.response }}
    </span>
  </div>
</template>

<script>
import Header from "~/components/Header.vue";
const axios = require("axios");
export default {
  name: "Create",

  data() {
    return {
      status: "",
      name: "",
      token: "",
      showCreated: false,
      response: [],
      showResponse: false,
    };
  },

  components: {
    Header,
  },

  async mounted() {
    const body = {
      login: {
        email: "task@searchandstay.com",
        password: "ph37i45K",
      },
    };

    const login = await axios.post(
      "https://sys-dev.searchandstay.com/api/admin/login_json",
      body
    );

    const loginData = login.data;
    const receivedToken = loginData.data.result.access_token;
    this.token = receivedToken;
  },

  methods: {
    async createNewRule() {
      const body = {
        house_rules: {
          name: this.name,
          active: parseInt(this.status),
        },
      };

      const fetchData = await axios.post(
        "https://sys-dev.searchandstay.com/api/admin/house_rules",
        body,
        {
          headers: {
            Authorization: `Bearer ${this.token}`,
          },
        }
      );

      const created = fetchData.data;
      console.log(created);

      this.name = '';
      this.status = '';
      this.showCreated = true;
      this.response = created;
      this.showResponse = true;
    },
  },
};
</script>