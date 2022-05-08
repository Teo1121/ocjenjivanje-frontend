<template>
  <nav>
    <router-link to="/">Home</router-link> |
    <router-link to="/about">About</router-link> |
    <router-link v-if="!isLoggedIn" to="/login">Login</router-link>
    <a href="#" v-if="isLoggedIn" @click="submit()">Logout</a>
  </nav>
  <router-view />
</template>

<script>
import store from "@/store";
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      store,
    };
  },
  computed: {
    isLoggedIn() {
      return store.currentUser != null;
    },
  },
  methods: {
    submit() {
      axios
        .post(
          "http://localhost:8080/api/auth/logout",
          {
            userId: this.store.currentUser.userId,
          },
          {
            withCredentials: true,
            headers: {
              Authorization: "Bearer " + this.store.currentUser.accessToken,
            },
          }
        )
        .then((response) => {
          this.store.currentUser = null;
        });
    },
  },
  mounted() {
    axios
      .get("http://localhost:8080/api/auth/refreshToken", {
        withCredentials: true,
      })
      .then((response) => {
        if (response.data.accessToken) {
          store.currentUser = response.data;
        }
      })
      .catch((error) => {
        console.log("refresh token exipered or non existant");
      });
  },
};
</script>
<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

nav {
  padding: 30px;

  a {
    font-weight: bold;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
</style>
