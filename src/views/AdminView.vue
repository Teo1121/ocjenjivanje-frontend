<template></template>

<script>
import axios from "axios";
import store from "@/store";

export default {
  name: "AdminView",
  data() {
    return {
      reviews: [],
      professor: "",
      score: "",
      store,
      error: "",
    };
  },
  mounted() {
    this.professor = this.$route.query.name;
    if (!this.professor) {
      this.$router.push({ name: "list" });
    }
    if (this.store.currentUser == null) {
      this.$router.push({ name: "home" });
    }
    if (!this.store.currentUser.roles.includes("ROLE_ADMIN")) {
      this.$router.push({ name: "home" });
    }

    axios
      .get("http://localhost:8080/api/professor/" + this.professor, {
        headers: {
          Authorization: "Bearer " + this.store.currentUser.accessToken,
        },
      })
      .then((response) => {
        this.reviews = response.data;
      })
      .catch((error) => {
        this.error = error.message;
      });
  },
  methods: {
    submit() {
      return;
    },
  },
};
</script>

<style lang="scss">
label {
  margin-right: 0.5rem;
  margin-left: 0.5rem;
}
.review {
  width: min-content;
  margin: 1rem;
  p.title {
    margin-bottom: 0;
    text-align: left;
    width: max-content;
  }
  p.comment {
    margin: 1rem;
  }
  div {
    border: solid;
    width: max-content;
  }
}
</style>
