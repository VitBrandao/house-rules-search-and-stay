<template>
  <div>
    <Header />
    <div>
      <h1>House Rules</h1>
      <p> You can edit and delete rules by clicking on them.</p>
      <div v-if="rulesNotEmpty">
        <div v-for="rule in rules" v-bind:key="rule.id">
          <button @click="redirectToRulePage(rule.id)">
            {{ rule.name }}
            <br/>
            {{ rule.active === 1 ? "ACTIVE" : "INACTIVE" }}
            <br />
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Header from "../components/Header.vue";

const axios = require("axios");

export default {
  name: "IndexPage",
  components: {
    Header,
  },

  data() {
    return {
      token: "",
      rules: [],
      ruleById: [],
      createdRule: [],
      updatedRule: [],
      deletedRule: [],
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
    // console.log(rulesList);

    return {
      token: receivedToken,
      rules: rulesList,
    };
  },

  methods: {
    rulesNotEmpty() {
      if (this.rules.length === 0) {
        return false;
      } else {
        return true;
      }
    },

    redirectToRulePage(id) {
      const page = `rule/${id}`;
      this.$router.push(page);
    },

    async getById() {
      const fetchData = await axios.get(
        "https://sys-dev.searchandstay.com/api/admin/house_rules/110",
        {
          headers: {
            Authorization: `Bearer ${this.token}`,
          },
        }
      );

      const rule = fetchData.data.data;
      console.log(rule);

      return {
        ruleById: rule,
      };
    },

    async create() {
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
      console.log(created);

      return {
        createdRule: created,
      };
    },

    async update(rule) {
      const body = {
        house_rules: {
          name: "Update holiday new",
          active: 0,
        },
      };

      const fetchData = await axios.put(
        `https://sys-dev.searchandstay.com/api/admin/house_rules/${rule.id}`,
        body,
        {
          headers: {
            Authorization: `Bearer ${this.token}`,
          },
        }
      );

      const updated = fetchData.data;
      console.log(updated);

      return {
        updatedRule: updated,
      };
    },

    async deleteRule() {
      const fetchData = await axios.delete(
        "https://sys-dev.searchandstay.com/api/admin/house_rules/110",
        {
          headers: {
            Authorization: `Bearer ${this.token}`,
          },
        }
      );

      const deleted = fetchData.data;
      console.log(deleted);

      return {
        deletedRule: deleted,
      };
    },
  },
};
</script>
