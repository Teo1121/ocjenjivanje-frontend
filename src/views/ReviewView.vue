<template>
  <p>{{ professor }}</p>
  <form @submit="submit()" action="#" onsubmit="return false">
    <div>
      <label for="score1"
        >1<br />
        <input
          type="radio"
          id="score1"
          name="score"
          value="1"
          v-model="score"
        />
      </label>
      <label for="score2"
        >2<br />
        <input
          type="radio"
          id="score2"
          name="score"
          value="2"
          v-model="score"
        />
      </label>

      <label for="score3"
        >3<br />
        <input
          type="radio"
          id="score3"
          name="score"
          value="3"
          v-model="score"
        />
      </label>

      <label for="score4"
        >4<br />
        <input
          type="radio"
          id="score4"
          name="score"
          value="4"
          v-model="score"
        />
      </label>
      <label for="score5"
        >5<br />
        <input
          type="radio"
          id="score5"
          name="score"
          value="5"
          v-model="score"
        />
      </label>
    </div>
    <div>
      <label for="comment"
        >Comment:<br />
        <textarea
          type="text"
          id="comment"
          rows="4"
          cols="50"
          v-model="comment"
        />
      </label>
    </div>
    <div>
      <label for="anon" style="margin-right: 2rem"
        >Anonymous:
        <input type="checkbox" id="anon" v-model="anonymous" />
      </label>
      <button type="submit" class="btn btn-primary">Submit</button>
    </div>
  </form>
</template>

<script>
import axios from "axios";
import store from "@/store";

export default {
  name: "Review",
  data() {
    return {
      professor: "",
      score: "0",
      comment: "",
      anonymous: false,
      store,
    };
  },
  mounted() {
    this.professor = this.$route.query.name;
  },
  methods: {
    submit() {
      this.error = "";
      axios
        .post(
          "http://localhost:8080/api/review/post",
          {
            professorsName: this.professor,
            studentsName: this.store.currentUser.username,
            score: parseInt(this.score),
            anonymous: this.anonymous,
            comment: this.comment,
          },
          {
            headers: {
              Authorization: "Bearer " + this.store.currentUser.accessToken,
            },
          }
        )
        .then((response) => {
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
</style>
