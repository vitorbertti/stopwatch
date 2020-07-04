<template>
   <div id="app">
      <Stopwatch msg="Stopwatch" />
   </div>
</template>

<script>
import Stopwatch from './components/Stopwatch.vue';

export default {
   name: 'App',
   components: {
      Stopwatch,
   },data: function () {
      return {
         key: 0,
      }
   }
};
</script>

<style>
#app {
   margin: 0;
   padding: 0;
   box-sizing: border-box;
}
*,
button,
input {
   border: 0;
   outline: 0;
   font-family: 'Roboto', sans-serif;
}
body {
   background: #f0f0f5;
}
</style>
