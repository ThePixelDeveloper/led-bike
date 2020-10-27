<template>
  <div class="home">
    <h1>Bike LED Project</h1>
    <div>
      <button @click.prevent="compile">Compile</button>
    </div>
    <div>
      <textarea readonly v-model="code" rows="10" cols="50"></textarea>
    </div>
    <div>
      <wokwi-neopixel pin="6" pixel="0"></wokwi-neopixel>
      <wokwi-neopixel pin="6" pixel="1"></wokwi-neopixel>
      <wokwi-neopixel pin="6" pixel="2"></wokwi-neopixel>
      <wokwi-neopixel pin="6" pixel="3"></wokwi-neopixel>
      <wokwi-neopixel pin="6" pixel="4"></wokwi-neopixel>
      <wokwi-neopixel pin="6" pixel="5"></wokwi-neopixel>
      <wokwi-neopixel pin="6" pixel="6"></wokwi-neopixel>
      <wokwi-neopixel pin="6" pixel="7"></wokwi-neopixel>
      <wokwi-neopixel pin="6" pixel="8"></wokwi-neopixel>
      <wokwi-neopixel pin="6" pixel="9"></wokwi-neopixel>
      <wokwi-neopixel pin="6" pixel="10"></wokwi-neopixel>
    </div>
  </div>
</template>

<script>
import "@wokwi/elements";
import { buildHex } from "@/compiler/compiler";
import { WS2812Controller } from "@/ws2812";
import { AVRRunner } from "@/execute";

export default {
  name: "Home",
  data() {
    return {
      code: `
#include <Adafruit_NeoPixel.h>

// Which pin on the Arduino is connected to the LEDs?
#define PIN 6 

// How many LEDs are attached to the Arduino?
#define NUMPIXELS 116

// When setting up the NeoPixel library, we tell it how many pixels,
// and which pin to use to send signals. Note that for older NeoPixel
// strips you might need to change the third parameter -- see the
// strandtest example for more information on possible values.
Adafruit_NeoPixel pixels(NUMPIXELS, PIN, NEO_GRBW + NEO_KHZ800);

#define DELAYVAL 50 // Time (in milliseconds) to pause between pixels

// Individual bike components
const int seatstay_left[] = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22};
const int chainstay_left[] = {23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41};
const int downtube_left[] = {42, 43, 44, 45, 46, 47, 48, 49, 50, 51, 52, 53, 54, 55, 56, 57, 58, 59, 60, 61, 62, 63, 64, 65, 66, 67, 68, 69, 70, 71};

// Two diminsion array for easier transformation
const int* bike[] = {seatstay_left, chainstay_left, downtube_left};

void setup() {
  pixels.begin();
  pixels.setBrightness(20);
}

void loop() {
  
  pixels.setPixelColor(0, pixels.Color(0, 0, 150));
  pixels.setPixelColor(1, pixels.Color(0, 0, 150));
  pixels.setPixelColor(2, pixels.Color(0, 0, 150));
  pixels.setPixelColor(3, pixels.Color(0, 0, 150));
  pixels.show();
}
`.trim(),
      hex: ""
    };
  },
  components: {},
  methods: {
    async compile() {
      this.hex = (await buildHex(this.code)).hex;
      this.execute();
    },
    execute() {
      const runner = new AVRRunner(this.hex);
      const MHZ = 16000000;

      const cpuNanos = () => Math.round((runner.cpu.cycles / MHZ) * 1000000000);

      const matrixController = new WS2812Controller(116);

      runner.portD.addListener(() => {
        matrixController.feedValue(runner.portD.pinState(6), cpuNanos());
      });
      runner.execute(() => {
        const pixels = matrixController.update(cpuNanos());
        if (pixels) {
          debugger;
        }
      });
    }
  }
};
</script>
