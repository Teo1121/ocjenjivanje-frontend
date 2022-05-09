<template>
  <p>{{ professor }}</p>
  <form action="#">
    <div>
      <label for="score1"
        >1<br />
        <input type="radio" id="score1" name="score" />
      </label>
      <label for="score2"
        >2<br />
        <input type="radio" id="score2" name="score" />
      </label>

      <label for="score3"
        >3<br />
        <input type="radio" id="score3" name="score" />
      </label>

      <label for="score4"
        >4<br />
        <input type="radio" id="score4" name="score" />
      </label>
      <label for="score5"
        >5<br />
        <input type="radio" id="score5" name="score" />
      </label>
    </div>
    <label for="comment"
      >Comment:<br />
      <textarea type="text" id="comment" rows="4" cols="50" />
    </label>
  </form>
</template>

<script>
export default {
  name: "Review",
  data() {
    return {
      professor: "",
    };
  },
  mounted() {
    this.professor = this.$route.query.name;
  },
};
</script>

<style lang="scss">
label {
  margin-right: 0.5rem;
  margin-left: 0.5rem;
}
</style>
