<template>
  <Exchange />
</template>

<script>
import Exchange from "./components/Exchange.vue";

export default {
  name: "App",
  components: {
    Exchange,
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: sans-serif;
}

#app {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  padding: 0 10px;
  background: #42b883;
}
</style>
