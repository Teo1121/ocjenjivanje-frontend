<template>
  <h1>This the review page</h1>
  <div>
    <p
      @click="click(professor.name)"
      v-for="professor in professorList"
      v-bind:key="professor"
    >
      {{ professor.name + ": " + professor.details }}
    </p>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "ReviewView",
  data() {
    return {
      professorList: [],
    };
  },
  mounted() {
    if (localStorage.getItem("professorList") != null) {
      this.professorList = JSON.parse(localStorage.getItem("professorList"));
    } else {
      axios
        .get("http://localhost:8080/api/review/list/professors")
        .then((response) => {
          this.professorList = response.data;
          localStorage.setItem("professorList", JSON.stringify(response.data));
        });
    }
  },
  methods: {
    click(name) {
      this.$router.push({ name: "review", query: { name } });
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
