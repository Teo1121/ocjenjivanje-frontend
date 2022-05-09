<template>
  <h1>This the review page</h1>
  <div>
    <p
      @click="click(professor)"
      v-for="professor in professorList"
      v-bind:key="professor"
    >
      {{ professor }}
    </p>
  </div>
</template>

<script>
export default {
  name: "ReviewView",
  data() {
    return {
      professorList: ["Test Testovic: Nezna nista", "Ivan Horvat: Sposoban"],
    };
  },
  methods: {
    click(name) {
      name = name.split(":")[0];
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
