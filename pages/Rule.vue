<template>
  <div class="main_div">
    <Header />
    <h1>Rule Page nº{{ $route.params.id }}</h1>

    <div v-if="showRule">
      <div class="rule_div">
        <h3 class="rule_name">{{ rules.name }}</h3>
        <p class="rule_status">
          {{ rules.active === 1 ? "ACTIVE" : "INACTIVE" }}
        </p>
      </div>
      <br />
      <br />
      <button @click="updateRule()">UPDATE</button>

      <button @click="deleteRule(rules.id)">DELETE</button>

      <div v-if="updating">
        <input
          type="text"
          placeholder="Type new rule name"
          v-model="ruleName"
        />
        <input
          type="text"
          placeholder="Type 0 for INACTIVE or 1 for ACTIVE"
          v-model="ruleActive"
        />
        <button @click="updateDB(rules.id)">Save</button>
      </div>

      <div v-if="showReload">
        <p>Rule sucessfully updated!</p>
        <p>
          Please refresh the page or get back to House Rules page to see your
          updated data.
        </p>
      </div>

      <div v-if="showDelete">
        <p>Rule sucessfully deleted!</p>
        <p>
          Which means that this page is no longer availabe. We recommend you to
          get back to House Rules page.
        </p>
      </div>
    </div>

    <div v-else>
      <span> Loading... </span>
    </div>
  </div>
</template>

<script>
import Header from "~/components/Header.vue";
const axios = require("axios");
export default {
  name: "Rule",
  components: {
    Header,
  },

  data() {
    return {
      token: "",
      rules: [],
      showRule: false,
      showReload: false,
      showDelete: false,
      updating: false,
      ruleName: "",
      ruleActive: "",
    };
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
    const idParams = parseInt(this.$route.params.id);
    const fetchData = await axios.get(
      `https://sys-dev.searchandstay.com/api/admin/house_rules/${idParams}`,
      {
        headers: {
          Authorization: `Bearer ${receivedToken}`,
        },
      }
    );

    const rulesList = fetchData.data.data;
    this.token = receivedToken;
    this.rules = rulesList;
    this.showRule = true;
  },

  methods: {
    updateRule() {
      this.updating = true;
    },

    async updateDB(id) {
      const body = {
        house_rules: {
          name: this.ruleName,
          active: parseInt(this.ruleActive),
        },
      };

      const fetchData = await axios.put(
        `https://sys-dev.searchandstay.com/api/admin/house_rules/${id}`,
        body,
        {
          headers: {
            Authorization: `Bearer ${this.token}`,
          },
        }
      );

      const updated = fetchData.data;
      this.updating = false;
      this.showReload = true;
    },

    async deleteRule(id) {
      const fetchData = await axios.delete(
        `https://sys-dev.searchandstay.com/api/admin/house_rules/${id}`,
        {
          headers: {
            Authorization: `Bearer ${this.token}`,
          },
        }
      );

      const deleted = fetchData.data;
      console.log(deleted);
      this.showDelete = true;
    },
  },
};
</script>

<style scoped>
.main_div {
  text-align: center;
}

h1 {
  font-family: Helvetica;
}

button {
  padding: 5px;
  background-color: #222;
  color: white;
  font-weight: bold;
  border: 2px solid #222;
  font-size: 16px;
  cursor: pointer;
  transition: 0.5s;
  font-family: "Courier New", Courier, monospace;
}

button:hover {
  color: turquoise;
}

.rule_div {
  border: 2px gray ridge;
  font-size: 42px;
  display: inline-block;
  padding: 20px;
}

.rule_name {
  font-family: "Courier New";
  font-size: 30px;
}

.rule_status {
  font-size: 20px;
}
</style>