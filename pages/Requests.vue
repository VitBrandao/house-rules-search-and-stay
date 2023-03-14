<template>
  <div>
    <Header />
    <h1>Requests</h1>
    <button @click="getData">GET</button>
    <button @click="create">CREATE</button>
    <button @click="getById">GET BY ID</button>
    <button @click="update">UPDATE</button>
    <button @click="deleteRule">DELETE</button>
    
    <h3>Please open your console to check requests results after clicking.</h3>
    <p> Just make sure you always CREATE a new one before clicking on DELETE. </p>
  </div>
</template>

<script>
import Header from "~/components/Header.vue";

const axios = require("axios");

export default {
  name: "Requests",
  components: {
    Header,
  },

  data() {
    return {
      token: "",
      rules: [],
      showResults: false,
      createdId: '',
    };
  },

  async asyncData({ $axios }) {
    const body = {
      login: {
        email: "task@searchandstay.com",
        password: "ph37i45K",
      },
    };

    const login = await $axios.post(
      "https://sys-dev.searchandstay.com/api/admin/login_json",
      body
    );

    const loginData = login.data;
    const receivedToken = loginData.data.result.access_token;

    const fetchData = await axios.get(
      "https://sys-dev.searchandstay.com/api/admin/house_rules",
      {
        headers: {
          Authorization: `Bearer ${receivedToken}`,
        },
      }
    );

    const rulesList = fetchData.data.data.entities;

    return {
      token: receivedToken,
      rules: rulesList,
    };
  },

  methods: {
    async getData() {
      this.showResults = false;
      const fetchData = await axios.get(
        "https://sys-dev.searchandstay.com/api/admin/house_rules",
        {
          headers: {
            Authorization: `Bearer ${this.token}`,
          },
        }
      );

      const rulesList = fetchData.data.data.entities;
      console.log(rulesList);

      return {
        rules: rulesList,
      };
    },

    async getById() {
      this.showResults = false;
      this.extraInput = true;
      this.rules = [];
      const fetchData = await axios.get(
        "https://sys-dev.searchandstay.com/api/admin/house_rules/118",
        {
          headers: {
            Authorization: `Bearer ${this.token}`,
          },
        }
      );

      const ruleId = fetchData.data.data;
      console.log(ruleId);

      return {
        rules: ruleId,
        showResults: true,
      };
    },

    async create() {
      this.showResults = false;
      const body = {
        house_rules: {
          name: "New House Rules",
          active: 1,
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
      this.createdId = created.data.id;
      console.log(created);
      this.showResults = true;
      return {
        rules: created,
      };
    },

    async update() {
      this.showResults = false;
      const body = {
        house_rules: {
          name: "Update holiday new",
          active: 0,
        },
      };

      const fetchData = await axios.put(
        "https://sys-dev.searchandstay.com/api/admin/house_rules/118",
        body,
        {
          headers: {
            Authorization: `Bearer ${this.token}`,
          },
        }
      );

      const updated = fetchData.data;
      console.log(updated);
      this.showResults = true;
      return {
        rules: updated,
      };
    },

    async deleteRule() {
      this.showResults = false;

      const fetchData = await axios.delete(
        `https://sys-dev.searchandstay.com/api/admin/house_rules/${this.createdId}`,
        {
          headers: {
            Authorization: `Bearer ${this.token}`,
          },
        }
      );

      const deleted = fetchData.data;
      console.log(deleted);
      this.showResults = true;

      return {
        rules: deleted,
      };
    },
  },
};
</script>

<style scoped>
    div {
        text-align: center;
    }

    button {
        width: 200px;
        height: 80px;
        font-size: 20px;
        font-family: 'Courier New';
    }

    h3, p, h1 {
        font-family: 'Helvetica';
    }
</style>