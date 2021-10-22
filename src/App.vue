<template>
<div id="app">
  <weather-widget />
</div>
</template>

<script>
import WeatherWidget from "./components/weather-widget.vue";
export default {
  name: "app",
  components: { WeatherWidget },

};
</script>


