<template>
  <p>{{ professor }}</p>
  <div class="review" v-for="rev in review" :key="rev">
    <p class="title">{{ rev.studentsName }} ocjena:{{ rev.score }}</p>
    <div>
      <p class="comment">{{ rev.comment }}</p>
    </div>
  </div>
  <form
    style="margin-top: 2rem"
    v-if="store.currentUser"
    @submit="submit()"
    action="#"
    onsubmit="return false"
  >
    <div>
      <label for="score"
        >Score: {{ score }}<br />
        <input
          type="range"
          class="form-range"
          min="1"
          max="5"
          id="score"
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
    <p style="color: red">
      {{ error }}
    </p>
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
      review: [],
      professor: "",
      score: "3",
      comment: "",
      anonymous: false,
      store,
      error: "",
    };
  },
  mounted() {
    this.professor = this.$route.query.name;
    if (this.store.currentUser == null) {
      return;
    }
    axios
      .get("http://localhost:8080/api/review/list/reviews/" + this.professor, {
        headers: {
          Authorization: "Bearer " + this.store.currentUser.accessToken,
        },
      })
      .then((response) => {
        this.review = response.data;
      })
      .catch((error) => {
        this.error = error.message;
      });
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
