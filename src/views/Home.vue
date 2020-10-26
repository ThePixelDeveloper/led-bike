<template>
  <div class="home">
    <h1>Bike LED Project</h1>
    <wokwi-neopixel pin="6" pixel="0"></wokwi-neopixel>
    <wokwi-neopixel pin="6" pixel="1"></wokwi-neopixel>
    <wokwi-neopixel pin="6" pixel="2"></wokwi-neopixel>
    <wokwi-neopixel pin="6" pixel="3"></wokwi-neopixel>
    <wokwi-neopixel pin="6" pixel="4"></wokwi-neopixel>
    <wokwi-neopixel pin="6" pixel="5"></wokwi-neopixel>
    <wokwi-neopixel pin="6" pixel="6"></wokwi-neopixel>
    <wokwi-neopixel pin="6" pixel="7"></wokwi-neopixel>
  </div>
</template>

<script>
import "@wokwi/elements";

export default {
  name: "Home",
  components: {

  },
  methods: {
    compile() {
      return "something";
    }
  }
};
</script>
