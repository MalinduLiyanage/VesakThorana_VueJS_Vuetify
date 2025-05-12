<template>
  <div class="container">
    <canvas ref="canvas"></canvas>
    <img ref="buddha" :src="buddhaImage" alt="Buddha Image" class="buddha" />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import buddhaImage from '@/assets/Buddha.png';

export default defineComponent({
  name: 'GlowingBuddha',
  data() {
    return {
      canvas: null as HTMLCanvasElement | null,
      ctx: null as CanvasRenderingContext2D | null,
      contextId: '2d',
      buddha: null as HTMLImageElement | null,
      buddhaImage: buddhaImage as string,
      w: 0 as number,
      h: 0 as number,
      numSquares: 40 as number,
      baseSize: 160 as number,
      rotationBaseSpeed: 0.006 as number,
      time: 0 as number,
      buddhaOffsetY: 190 as number,
      buddhaOffsetX: 8 as number
    };
  },
  mounted() {
    this.canvas = this.$refs.canvas as HTMLCanvasElement;
    this.ctx = this.canvas.getContext(this.contextId) as CanvasRenderingContext2D;
    this.buddha = this.$refs.buddha as HTMLImageElement;

    this.resize();
    this.positionBuddha();
    window.addEventListener('resize', this.handleResize);
    this.animate();
  },
  beforeUnmount() {
    window.removeEventListener('resize', this.handleResize);
  },
  methods: {
    resize() {
      this.w = this.canvas!.width = window.innerWidth;
      this.h = this.canvas!.height = window.innerHeight;
    },
    centerX(): number {
      return this.w / 2;
    },
    centerY(): number {
      return this.h / 2;
    },
    positionBuddha() {
      this.buddha!.style.left = `${this.centerX() + this.buddhaOffsetX}px`;
      this.buddha!.style.top = `${this.centerY() + this.buddhaOffsetY}px`;
    },
    handleResize() {
      this.resize();
      this.positionBuddha();
    },
    drawSquare(x: number, y: number, size: number, angle: number, color: string, glow: number, visible: boolean = true) {
      if (!visible) return;

      this.ctx!.save();
      this.ctx!.translate(x, y);
      this.ctx!.rotate(angle);
      this.ctx!.beginPath();
      this.ctx!.moveTo(-size / 2, -size / 2);
      this.ctx!.lineTo(size / 2, -size / 2);
      this.ctx!.lineTo(size / 2, size / 2);
      this.ctx!.lineTo(-size / 2, size / 2);
      this.ctx!.closePath();

      this.ctx!.strokeStyle = color;
      this.ctx!.shadowColor = color;
      this.ctx!.shadowBlur = glow;
      this.ctx!.lineWidth = 1.5;
      this.ctx!.stroke();
      this.ctx!.restore();
    },
    animate() {
      this.ctx!.clearRect(0, 0, this.w, this.h);
      this.ctx!.globalCompositeOperation = 'lighter';

      for (let i = 0; i < this.numSquares; i++) {
        const progress = i / this.numSquares;
        const phase = i * 0.3;

        const scanPos = Math.sin(this.time * 2 + phase);
        const isVisible = Math.abs(scanPos) > 0.2;

        const glow = 30 + 30 * Math.sin(this.time * 3 + i);
        const size = this.baseSize + Math.sin(this.time * 2 + i) * 25;
        const angle = this.time * (1 + Math.sin(i * 0.2 + this.time * 1.5) * 0.5) + i * 0.1;
        const hue = (this.time * 50 + i * 10) % 360;
        const color = `hsl(${hue}, 100%, 65%)`;

        this.drawSquare(this.centerX(), this.centerY(), size, angle, color, glow, isVisible);
      }

      this.time += this.rotationBaseSpeed;
      if (this.time > 20) {
        this.time = 0;
      }
      requestAnimationFrame(this.animate);
    }
  }
});
</script>

<style scoped>
.container {
  background-color: black;
}

canvas {
  display: block;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 0;
}

.buddha {
  position: absolute;
  width: 400px;
  transform: translate(-50%, -50%);
  z-index: 1;
  pointer-events: none;
}
</style>