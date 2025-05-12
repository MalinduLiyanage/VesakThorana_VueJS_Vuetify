<template>
  <div v-if="visibility" class="circle-wrapper">
    <img
      :src="imgSrc"
      alt="Example"
      class="circle-image"
    />
    <div class="number-badge">{{badgeNumber}}</div>
    <div
      v-for="(light, index) in lights"
      :key="index"
      class="light"
      :class="patterns[currentPatternIndex]"
      :style="{
        left: `${light.x}px`,
        top: `${light.y}px`,
        backgroundColor: light.color,
        animationDelay: light.delay,
      }"
    ></div>
  </div>
  <div v-else class="circle-wrapper"></div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'CircleImage',
  props: {
    imgSrc: {
      type: String,
      required: true,
    },
    visibility: {
      type: Boolean,
      required: true,
    },
    badgeNumber: {
      type: [String, Number],
      required: false,
      default: null,
    },
  },
  data() {
    return {
      numLights: 25,
      radius: 115,
      center: 122,
      patterns: ['wave', 'twinkle', 'bounce', 'rotate', 'blink', 'pulse'],
      colorModes: ['random', 'Ared', 'green', 'blue', 'red-green', 'blue-yellow'],
      currentPatternIndex: 0,
      currentColorModeIndex: 0,
      lights: [] as { x: number; y: number; color: string; delay: string }[],
      intervalId: null as number | null,
    };
  },
  methods: {
    getColor(): string {
      const mode = this.colorModes[this.currentColorModeIndex];

      const colorPalettes: Record<string, string[]> = {
        random: ['#FF00FF', '#00FFFF', '#FFFF00', '#FF4500', '#00FF00', '#1E90FF', '#FF1493'],
        red: ['#FF0000'],
        green: ['#00FF00'],
        blue: ['#0000FF'],
        white: ['#FFFFFF'],
        yellow: ['#FFFF00'],
        pink: ['#FF69B4'],
        'red-green': ['#FF0000', '#00FF00'],
        'blue-yellow': ['#0000FF', '#FFFF00'],
        'green-blue': ['#00FF00', '#0000FF'],
        'purple-cyan': ['#800080', '#00FFFF'],
        sunset: ['#FF4500', '#FF6347', '#FFD700'],
        neon: ['#39FF14', '#FF073A', '#00FFFF', '#FF00FF']
      };

      const palette = colorPalettes[mode] || colorPalettes['random'];
      return palette[Math.floor(Math.random() * palette.length)];
    },
    createLights(): void {
      this.lights = [];
      for (let i = 0; i < this.numLights; i++) {
        const angle = (2 * Math.PI / this.numLights) * i;
        const x = this.center + this.radius * Math.cos(angle) - 8;
        const y = this.center + this.radius * Math.sin(angle) - 8;

        this.lights.push({
          x,
          y,
          color: this.getColor(),
          delay: `${i * 0.1}s`,
        });
      }
    },
    changePattern(): void {
      this.currentPatternIndex = (this.currentPatternIndex + 1) % this.patterns.length;
      this.currentColorModeIndex = (this.currentColorModeIndex + 1) % this.colorModes.length;
      this.lights = this.lights.map(light => ({
        ...light,
        color: this.getColor(),
      }));
    },
  },
  mounted() {
    this.createLights();
    this.intervalId = setInterval(this.changePattern, 10000);
  },
  beforeUnmount() {
    if (this.intervalId) {
      clearInterval(this.intervalId);
    }
  },
});
</script>

<style scoped>
.circle-wrapper {
  position: relative;
  width: 200px;
  height: 200px;
}

.circle-image {
  width: 200px;
  height: 200px;
  border-radius: 50%;
  object-fit: cover;
  border: 3px solid #444;
  position: absolute;
  top: 20px;
  left: 20px;
  z-index: 2;
}

.number-badge {
  position: absolute;
  top: 25px;
  left: 60%;
  transform: translateX(-50%);
  background-color: white;
  color: black;
  width: 24px;
  height: 24px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
  z-index: 3;
  box-shadow: 0 0 5px rgba(0,0,0,0.5);
  font-size: 14px;
}


.light {
  width: 16px;
  height: 16px;
  border-radius: 50%;
  position: absolute;
  box-shadow:
    0 0 20px rgba(255, 255, 255, 0.8),
    0 0 30px currentColor;
  transition:
    background-color 0.6s ease-in-out,
    transform 0.3s ease-in-out,
    opacity 0.3s ease-in-out;
}

@keyframes blink {
  0% { opacity: 1; }
  100% { opacity: 0.3; }
}

@keyframes pulse {
  0% { transform: scale(1); opacity: 1; }
  50% { transform: scale(1.3); opacity: 0.2; }
  100% { transform: scale(1); opacity: 1; }
}

@keyframes wave {
  0% { transform: scale(1); opacity: 1; }
  50% { transform: scale(1.5); opacity: 0.8; }
  100% { transform: scale(1); opacity: 0.5; }
}

@keyframes twinkle {
  0%, 100% { opacity: 0.2; transform: scale(0.9); }
  50% { opacity: 1; transform: scale(1.1); }
}

@keyframes bounce {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-5px); }
}

@keyframes rotate {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.blink {
  animation: blink 1s infinite alternate;
}

.pulse {
  animation: pulse 1.2s infinite;
}

.wave {
  animation: wave 0.8s infinite;
}

.twinkle {
  animation: twinkle 1.5s infinite;
}

.bounce {
  animation: bounce 1s infinite;
}

.rotate {
  animation: rotate 2s linear infinite;
}
</style>
