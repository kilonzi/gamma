<template>
  <div class="home">
    <main-navigation></main-navigation>
    <reading></reading>
  </div>
</template>

<script>
// @ is an alias to /src
import MainNavigation from "@/components/MainNavigation.vue";
import Reading from "@/components/Reading.vue";

export default {
  name: "Home",
  components: {
    MainNavigation,
    Reading
  }
};
</script>
