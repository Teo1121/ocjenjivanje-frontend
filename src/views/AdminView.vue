<template>
  <p>{{ professor.name }}: {{ professor.details }}</p>
  <form
    style="margin-top: 2rem"
    v-if="store.currentUser && store.currentUser.roles.includes('ROLE_ADMIN')"
    @submit="submit()"
    action="#"
    onsubmit="return false"
  >
    <p>Edit professor</p>
    <div>
      <label for="name"
        >Name {{ newName }}<br />
        <input type="text" id="name" v-model="newName" />
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
          v-model="newDetails"
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
</template>

<script>
import axios from "axios";
import store from "@/store";

export default {
  name: "AdminView",
  data() {
    return {
      reviews: [],
      professor: { name: "", details: "" },
      newName: "",
      newDetails: "",
      store,
      error: "",
    };
  },
  mounted() {
    if (!this.$route.query.name) {
      this.$router.push({ name: "list" });
    }
    if (this.store.currentUser == null) {
      this.$router.push({ name: "login" });
    }
    if (!this.store.currentUser.roles.includes("ROLE_ADMIN")) {
      this.$router.push({ name: "home" });
    }

    axios
      .get("http://localhost:8080/api/professor/" + this.$route.query.name, {
        headers: {
          Authorization: "Bearer " + this.store.currentUser.accessToken,
        },
      })
      .then((response) => {
        this.professor = response.data;
      })
      .catch((error) => {
        localStorage.clear();
        this.$router.push({ name: "list" });
        this.error = error.message;
      });
  },
  methods: {
    submit() {
      let professorName = this.professor.name;
      let newProfessor = this.professor;
      if (this.newName) {
        newProfessor.name = this.newName;
      }
      if (this.newDetails) {
        newProfessor.details = this.newDetails;
      }
      axios
        .put(
          "http://localhost:8080/api/professor/" + professorName,
          newProfessor,
          {
            headers: {
              Authorization: "Bearer " + this.store.currentUser.accessToken,
            },
          }
        )
        .then((response) => {
          localStorage.clear();
          this.$router.push({ name: "home" });
        })
        .catch((error) => {
          this.error = error.message;
        });
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
