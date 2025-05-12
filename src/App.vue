<template>
  <div v-if="isDesktop" class="toolbar">
    <button class="toolbar-button left" @click="toggleAudio">
      {{ isPlaying ? 'Pause Audio' : 'Play Audio' }}
    </button>
    සස ජාතකය
    <button class="toolbar-button right" @click="openAbout">About</button>
  </div>

  <BaseLayout v-if="isDesktop"/>
  <v-card v-else
    class="mx-auto"
    prepend-icon="mdi-alert"
    width="400"
  >
    <template v-slot:title>
      <span class="font-weight-black">Display Size Warning!</span>
    </template>

    <v-card-text class="bg-surface-light pt-4">
      If you see this message, it means you are trying to watching our Vesak Thorana using a Mobile or Tablet Computer. To provide the maximum user experience, we limited the Vesak Thorana only for Desktop or laptop computers.
    </v-card-text>

    <v-card-text class="text-red font-weight-bold pt-4">
      Please use a Laptop or Desktop computer to experience the Vesak Thorana!
    </v-card-text>
  </v-card>
  <AboutModal :visible="showAbout" @close="closeAbout" />
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import BaseLayout from '@/components/BaseLayout.vue';
import AboutModal from '@/components/AboutModal.vue';

export default defineComponent({
  components: {
    BaseLayout,
    AboutModal,
  },
  data() {
    return {
      isDesktop: window.innerWidth >= 1366,
      audioPlayer: null as HTMLAudioElement | null,
      isPlaying: false,
      showAbout: false,
    };
  },
  mounted() {
    window.addEventListener('resize', this.updateScreenSize);
    if (this.isDesktop) {
      this.audioPlayerMount()
    }
  },
  beforeUnmount() {
    window.removeEventListener('resize', this.updateScreenSize);
    if (this.audioPlayer) {
      this.audioPlayer.pause();
    }
  },
  methods: {
    updateScreenSize() {
      this.isDesktop = window.innerWidth >= 1366;
    },
    audioPlayerMount() {
      const audio = new Audio(new URL('@/assets/Audio.mp3', import.meta.url).href);
      audio.loop = true;
      audio.load();

      audio.play()
        .then(() => {
          this.isPlaying = true;
        })
        .catch((err) => {
          console.warn('Autoplay blocked:', err);
        });

      this.audioPlayer = audio;
    },
    toggleAudio() {
      if (this.audioPlayer) {
        if (this.isPlaying) {
          this.audioPlayer.pause();
        } else {
          this.audioPlayer.play().catch(console.error);
        }
        this.isPlaying = !this.isPlaying;
      }
    },
    openAbout() {
      this.showAbout = true;
    },
    closeAbout() {
      this.showAbout = false;
    },
  },
});
</script>

<style>
body {
  background-image: url('@/assets/Thorana_BG.jpg');
  background-size: cover;
  background-position: center;
  background-repeat: repeat;
  margin: 0;
  padding: 0;

}
.mobile-message {
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 1.5rem;
  font-weight: bold;
  text-align: center;
  color: #FFF;
}
.toolbar {
  width: 100%;
  padding: 1rem;
  background-color: rgba(0, 0, 0, 0.6);
  color: white;
  font-size: 1.25rem;
  font-weight: bold;
  text-align: center;
  position: fixed;
  top: 0;
  left: 0;
  z-index: 1000;
}

.toolbar-button {
  background-color: rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.3);
  border-radius: 8px;
  color: #fff;
  font-size: 1rem;
  padding: 0.4rem 0.8rem;
  cursor: pointer;
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  transition: background-color 0.3s, transform 0.2s;
  backdrop-filter: blur(4px);
}

.toolbar-button:hover {
  background-color: rgba(255, 255, 255, 0.25);
  transform: translateY(-50%) scale(1.05);
}

.toolbar-button:active {
  transform: translateY(-50%) scale(0.95);
}

.toolbar-button.left {
  left: 1rem;
}

.toolbar-button.right {
  right: 1rem;
}

</style>
