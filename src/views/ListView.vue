<template>
  <h1>This the review page</h1>
  <div v-for="professor in professorList" v-bind:key="professor">
    <a href="#" @click="click(professor.name)">
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
</template>

<script>
import axios from "axios";
import store from "@/store";

export default {
  name: "ReviewView",
  data() {
    return {
      professorList: [],
      store,
      error: "",
      newProfessor: { name: "", details: "" },
    };
  },
  mounted() {
    if (localStorage.getItem("professorList") != null) {
      this.professorList = JSON.parse(localStorage.getItem("professorList"));
    } else {
      axios.get("http://localhost:8080/api/professor").then((response) => {
        this.professorList = response.data;
        localStorage.setItem("professorList", JSON.stringify(response.data));
      });
    }
  },
  methods: {
    click(name) {
      if (
        this.store.currentUser &&
        this.store.currentUser.roles.includes("ROLE_ADMIN")
      ) {
        this.$router.push({ name: "admin", query: { name } });
      } else {
        this.$router.push({ name: "review", query: { name } });
      }
    },
    submit(name) {
      axios
        .post("http://localhost:8080/api/professor", this.newProfessor, {
          headers: {
            Authorization: "Bearer " + this.store.currentUser.accessToken,
          },
        })
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

<style></style>

<style lang="scss">
label {
  margin-right: 0.5rem;
  margin-left: 0.5rem;
}
</style>
