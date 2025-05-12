<template>
  <div>
    <canvas ref="canvas"></canvas>
    <div ref="circleContainer" class="circle-container">
      <v-col>
        <v-row justify="center" align="center">
          <v-col cols="auto" class="px-4">
            <CircleImage :img-src="storyPartThree" :visibility="true" badge-number="3"/>
          </v-col>
          <v-col cols="auto" class="px-4">
            <CircleImage :img-src="storyPartFour" :visibility="true" badge-number="4"/>
          </v-col>
          <v-col cols="auto" class="px-4">
            <CircleImage :img-src="storyPartFive" :visibility="true" badge-number="5"/>
          </v-col>
        </v-row>
        <v-row justify="center" align="center">
          <v-col cols="auto" class="px-4">
            <CircleImage :img-src="storyPartTwo" :visibility="true" badge-number="2"/>
          </v-col>
          <v-col cols="auto" class="px-4">
            <CircleImage :img-src="storyPartTwo" :visibility="false"/>
          </v-col>
          <v-col cols="auto" class="px-4">
            <CircleImage :img-src="storyPartSix" :visibility="true" badge-number="6"/>
          </v-col>
        </v-row>
        <v-row justify="center" align="center">
          <v-col cols="auto" class="px-4">
            <CircleImage :img-src="storyPartOne" :visibility="true" badge-number="1"/>
          </v-col>
          <v-col cols="auto" class="px-4">
            <CircleImage :img-src="storyPartOne" :visibility="false"/>
          </v-col>
          <v-col cols="auto" class="px-4">
            <CircleImage :img-src="storyPartSevenNew" :visibility="true" badge-number="7"/>
          </v-col>
        </v-row>
      </v-col>
    </div>
  </div>
  <div class="buddha-container">
    <GlowingBuddha />
  </div>
</template>

<script lang="ts">
import CircleImage from '@/components/StoryComponent.vue';
import GlowingBuddha from '@/components/BuddhaComponent.vue';
import StoryPartOne from '@/assets/StoryParts/Story_Part_1.jpg';
import StoryPartTwo from '@/assets/StoryParts/Story_Part_2.jpg';
import StoryPartThree from '@/assets/StoryParts/Story_Part_3.jpg';
import StoryPartFour from '@/assets/StoryParts/Story_Part_4.jpg';
import StoryPartFive from '@/assets/StoryParts/Story_Part_5.jpg';
import StoryPartSix from '@/assets/StoryParts/Story_Part_6.jpg';
import StoryPartSeven from '@/assets/StoryParts/Story_Part_7.jpg';
import StoryPartSevenNew from '@/assets/StoryParts/Story_Part_7_New.jpg';
import { tr } from 'vuetify/locale'

export default {
  name: 'BaseLayout',
  computed: {
    tr() {
      return tr
    }
  },
  components: {
    GlowingBuddha,
    CircleImage,
  },
  data() {
    return {
      canvas: null as HTMLCanvasElement | null,
      circleContainer: null as HTMLDivElement | null,
      storyPartOne: StoryPartOne,
      storyPartTwo: StoryPartTwo,
      storyPartThree: StoryPartThree,
      storyPartFour: StoryPartFour,
      storyPartFive: StoryPartFive,
      storyPartSix: StoryPartSix,
      storyPartSeven: StoryPartSeven,
      storyPartSevenNew: StoryPartSevenNew,
    };
  },
  methods: {
    centerCircleContainer() {
      if (!this.circleContainer || !this.canvas) return;
      const w = window.innerWidth;
      const h = window.innerHeight;
      const container = this.circleContainer;

      container.style.left = `${w / 2.05}px`;
      container.style.top = `${h / 2}px`;
    },
  },
  mounted() {
    this.canvas = this.$refs.canvas as HTMLCanvasElement;
    this.circleContainer = this.$refs.circleContainer as HTMLDivElement;

    const ctx = this.canvas?.getContext('2d');
    if (this.canvas) {
      this.canvas.width = window.innerWidth;
      this.canvas.height = window.innerHeight;
    }

    //this.zoom()
    this.centerCircleContainer();
    window.addEventListener('resize', this.centerCircleContainer);
  },
  beforeUnmount() {
    window.removeEventListener('resize', this.centerCircleContainer);
  },
};
</script>

<style scoped>
canvas {
  position: absolute;
  top: 0;
  left: 0;
  z-index: 0;
}

.circle-container {
  position: absolute;
  transform: translate(-50%, -50%);
  z-index: 1;
}

.buddha-container {
  z-index: 100 !important;
}
</style>
