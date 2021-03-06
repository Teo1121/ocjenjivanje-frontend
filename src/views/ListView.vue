<template>
  <h1>List of professors</h1>
  <div v-for="professor in professorList" v-bind:key="professor">
    <a href="#" @click="click(professor)">
      {{ professor.name + ": " + professor.details }} </a
    ><br />
  </div>
  <form
    style="margin-top: 2rem"
    v-if="store.currentUser && store.currentUser.roles.includes('ROLE_ADMIN')"
    @submit="submit()"
    action="#"
    onsubmit="return false"
  >
    <p>New professor</p>
    <div>
      <label for="name"
        >Name {{ newProfessor.name }}<br />
        <input type="text" id="name" v-model="newProfessor.name" />
      </label>
    </div>
    <div>
      <label for="details"
        >Details:<br />
        <textarea
          type="text"
          id="details"
          rows="4"
          cols="50"
          v-model="newProfessor.details"
        />
      </label>
    </div>
    <p style="color: red">
      {{ error }}
    </p>
    <div>
      <button type="submit" class="btn btn-primary">Submit</button>
    </div>
  </form>
  <div
    style="display: flex; place-content: center; margin-top: 2rem"
    v-if="
      store.currentUser && store.currentUser.roles.includes('ROLE_MODERATOR')
    "
  >
    <label for="doughnut"
      >Number of reviews
      <DoughnutChart
        id="doughnut"
        style="margin-right: 2rem"
        :vueChartData="chartData"
    /></label>
    <label for="bar"
      >Average review scores
      <BarChart id="bar" style="margin-left: 2rem" :vueChartData="chartData"
    /></label>
  </div>
</template>

<script>
import axios from "axios";
import store from "@/store";

import DoughnutChart from "@/components/DoughnutChart.vue";
import BarChart from "@/components/BarChart.vue";

export default {
  name: "ReviewView",
  components: { DoughnutChart, BarChart },
  data() {
    return {
      professorList: [],
      store,
      error: "",
      newProfessor: { name: "", details: "" },
      chartData: [],
    };
  },
  mounted() {
    if (localStorage.getItem("professorList") != null) {
      this.professorList = JSON.parse(localStorage.getItem("professorList"));
    } else {
      axios
        .get("https://powerful-scrubland-44605.herokuapp.com/api/professor")
        .then((response) => {
          this.professorList = response.data;
          localStorage.setItem("professorList", JSON.stringify(response.data));
        });
    }
    setTimeout(() => {
      if (
        store.currentUser &&
        store.currentUser.roles.includes("ROLE_MODERATOR")
      ) {
        axios
          .get(
            "https://powerful-scrubland-44605.herokuapp.com/api/review/stats",
            {
              headers: {
                Authorization: "Bearer " + this.store.currentUser.accessToken,
              },
            }
          )
          .then((response) => {
            this.chartData = response.data;
          });
      }
    }, 200);
  },

  methods: {
    click(prof) {
      if (this.store.currentUser) {
        localStorage.setItem("selectedProf", JSON.stringify(prof));
        if (
          this.store.currentUser.roles.includes("ROLE_ADMIN") ||
          this.store.currentUser.roles.includes("ROLE_MODERATOR")
        ) {
          this.$router.push({ name: "admin" });
        } else if (this.store.currentUser.roles.includes("ROLE_USER")) {
          this.$router.push({ name: "review" });
        }
      }
    },
    submit(name) {
      axios
        .post(
          "https://powerful-scrubland-44605.herokuapp.com/api/professor",
          this.newProfessor,
          {
            headers: {
              Authorization: "Bearer " + this.store.currentUser.accessToken,
            },
          }
        )
        .then((response) => {
          localStorage.removeItem("professorList");
          this.$router.push({ name: "home" });
        })
        .catch((error) => {
          this.error = error.message;
        });
    },
  },
};
</script>

<style></style>

<style lang="scss">
label {
  margin-right: 0.5rem;
  margin-left: 0.5rem;
}
</style>
