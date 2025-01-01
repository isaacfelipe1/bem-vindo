<template>
  <div id="app">
    <div v-if="!isMidnight" class="countdown" :style="{ backgroundImage: `url(${countdownBackgroundImage})` }">
      <div class="countdown-container">
        <img src="https://www.lojaapolo.com.br/3466-thickbox_default/jogo-taca-flute-gastro-champanhe-220ml-bohemia-6-unidades.jpg" alt="Taça" class="glass left" />
        <div class="countdown-content">
          <h1>Contagem regressiva para o Ano Novo:</h1>
          <h2>{{ timeLeft }}</h2>
          <button @click="simulateMidnight" v-if="!isMidnight">Test Fireworks</button>
        </div>
        <img src="https://www.lojaapolo.com.br/3466-thickbox_default/jogo-taca-flute-gastro-champanhe-220ml-bohemia-6-unidades.jpg" alt="Taça" class="glass right" />
      </div>
    </div>
    <div v-else class="fireworks" :style="{ backgroundImage: `url(${backgroundImage})` }">
      <canvas ref="fireworksCanvas"></canvas>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      isMidnight: false,
      timeLeft: '',
      interval: null,
    };
  },
  mounted() {
    this.startCountdown();
    window.addEventListener('resize', this.resizeCanvas);
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.resizeCanvas);
  },
  methods: {
    startCountdown() {
      this.interval = setInterval(() => {
        const now = new Date();
        const midnight = new Date();
        midnight.setHours(24, 0, 0, 0);

        const diff = midnight - now;
        if (diff <= 0) {
          clearInterval(this.interval);
          this.isMidnight = true;
          this.$nextTick(() => {
            this.startFireworks();
          });
        } else {
          const hours = String(Math.floor(diff / 1000 / 60 / 60)).padStart(2, '0');
          const minutes = String(Math.floor((diff / 1000 / 60) % 60)).padStart(2, '0');
          const seconds = String(Math.floor((diff / 1000) % 60)).padStart(2, '0');
          this.timeLeft = `${hours}:${minutes}:${seconds}`;
        }
      }, 1000);
    },
    simulateMidnight() {
      clearInterval(this.interval);
      this.isMidnight = true;
      this.$nextTick(() => {
        this.startFireworks();
      });
    },
    startFireworks() {
      const canvas = this.$refs.fireworksCanvas;

      if (!canvas) {
        console.error('Canvas element not found after DOM update.');
        return;
      }

      const ctx = canvas.getContext('2d');

      if (!ctx) {
        console.error('Canvas context not found.');
        return;
      }

      this.resizeCanvas();

      const fireworks = [];

      const createFirework = () => {
        const x = Math.random() * canvas.width;
        const y = Math.random() * canvas.height / 2;
        const color = `hsl(${Math.random() * 360}, 100%, 50%)`;

        for (let i = 0; i < 50; i++) {
          fireworks.push({
            x,
            y,
            dx: (Math.random() - 0.5) * 4,
            dy: (Math.random() - 0.5) * 4,
            color,
            size: Math.random() * 3 + 1,
            life: 100,
          });
        }
      };

      const render = () => {
        ctx.clearRect(0, 0, canvas.width, canvas.height);

        fireworks.forEach((f, index) => {
          ctx.beginPath();
          ctx.arc(f.x, f.y, f.size, 0, Math.PI * 2);
          ctx.fillStyle = f.color;
          ctx.fill();

          f.x += f.dx;
          f.y += f.dy;
          f.size *= 0.96;
          f.life -= 1;

          if (f.life <= 0) fireworks.splice(index, 1);
        });

        if (Math.random() < 0.3) createFirework();
        requestAnimationFrame(render);
      };

      createFirework();
      render();
    },
    resizeCanvas() {
      const canvas = this.$refs.fireworksCanvas;
      if (canvas) {
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;
      }
    },
  },
};
</script>

<style>
body {
  margin: 0;
  overflow: hidden;
  font-family: Arial, sans-serif;
}

#app {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background: black;
  color: white;
  text-align: center;
}

.countdown-container {
  display: flex;
  align-items: center;
  justify-content: center;
}

.countdown-content {
  text-align: center;
}

.glass {
  width: auto;
  height: 70%;
}

.glass.left {
  margin-right: 20px;
}

.glass.right {
  margin-left: 20px;
}

.countdown {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background-size: cover;
  background-position: center;
  height: 100%;
  width: 100%;
}

.countdown h1 {
  font-size: 2rem;
}

.countdown h2 {
  font-size: 3rem;
  font-weight: bold;
}

button {
  font-size: 1rem;
  padding: 0.5rem 1rem;
  margin-top: 1rem;
  background: #ff4500;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background 0.3s;
}

button:hover {
  background: #ff6347;
}

.fireworks {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-size: cover;
  background-position: center;
}

canvas {
  display: block;
}

/* Responsividade para dispositivos menores */
@media (max-width: 768px) {
  .countdown-container {
    flex-direction: column;
  }

  .glass {
    width: 25vw;
    max-width: 100px;
    height: auto;
    margin: 10px 0;
  }

  .countdown h1 {
    font-size: 1.5rem;
  }

  .countdown h2 {
    font-size: 2rem;
  }

  button {
    font-size: 0.9rem;
    padding: 0.4rem 0.8rem;
  }
}

@media (max-width: 480px) {
  .countdown h1 {
    font-size: 1.2rem;
  }

  .countdown h2 {
    font-size: 1.5rem;
  }

  .glass {
    width: 35vw;
    max-width: 80px;
  }

  button {
    font-size: 0.8rem;
    padding: 0.3rem 0.6rem;
  }
}
</style>
