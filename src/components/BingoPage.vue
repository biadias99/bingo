<template>
  <v-container class="pa-2 pa-sm-4 d-flex flex-column justify-space-between" style="min-height: 100vh;">
    <div>
      <v-row justify="center" v-if="ultimoNumero !== null">
        <h1 class="font-weight-bold text-center">
          Último número: {{ letraDoNumero(ultimoNumero) }} {{ ultimoNumero }}
        </h1>
      </v-row>

      <v-row class="mt-4" justify="center" dense>
        <v-col
          v-for="coluna in ['B', 'I', 'N', 'G', 'O']"
          :key="coluna"
          cols="6"
          sm="2"
          class="text-center"
        >
          <div class="text-subtitle-1 text-sm-h5 font-weight-bold mb-3">{{ coluna }}</div>
          <v-card
            v-for="num in numerosPorColuna[coluna]"
            :key="num"
            class="mb-2 ml-1"
            :color="numeroColor(num)"
            :elevation="ultimoNumero === num ? 12 : 2"
            height="5vh"
          >
            <v-card-text
              class="d-flex align-center justify-center pa-0"
              :class="{
                'text-white': numerosSorteados.includes(num),
                'font-weight-bold': ultimoNumero === num,
                'text-black': !numerosSorteados.includes(num)
              }"
              style="font-size: 3.75vh;"
            >
              {{ num }}
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>
    </div>

    <v-row justify="center" align="center" no-gutters>
      <v-col cols="12" sm="6" class="mb-2 mb-sm-0 pa-5">
        <v-btn block color="primary" size="x-large" @click="sortearNumero">🎲 Sortear Número</v-btn>
      </v-col>
      <v-col cols="12" sm="6" class="pa-5">
        <v-btn block color="error" size="x-large" @click="limparBingo">🧹 Limpar Bingo</v-btn>
      </v-col>
    </v-row>

    <v-overlay v-model="mostrarAlerta" class="overlayOpacity d-flex align-center justify-center" :scrim="true" persistent>
      <div class="text-center">
        <div class="text-h5 text-sm-h2 font-weight-bold text-white mb-2">
          🎉 NÚMERO SORTEADO: 🎉
        </div>
        <div
          class="font-weight-black text-yellow"
          :style="{ fontSize: isMobile ? '64px' : '120px' }"
        >
          {{ letraDoNumero(ultimoNumero) }} {{ ultimoNumero }}
        </div>
        <div class="d-flex justify-center">
          <v-img
            src="../assets/logo_dani.png"
            max-width="20vh"
          />
        </div>
      </div>
  </v-overlay>
  </v-container>
</template>

<script>
export default {
  data() {
    return {
      numerosSorteados: [],
      ultimoNumero: null,
      mostrarAlerta: false,
      numerosPorColuna: {
        B: Array.from({ length: 15 }, (_, i) => i + 1),
        I: Array.from({ length: 15 }, (_, i) => i + 16),
        N: Array.from({ length: 15 }, (_, i) => i + 31),
        G: Array.from({ length: 15 }, (_, i) => i + 46),
        O: Array.from({ length: 15 }, (_, i) => i + 61),
      },
    };
  },
  computed: {
    isMobile() {
      return window.innerWidth <= 600;
    },
  },
  created() {
    const salvos = localStorage.getItem('numerosSorteados');
    if (salvos) {
      this.numerosSorteados = JSON.parse(salvos);
      this.ultimoNumero = this.numerosSorteados[this.numerosSorteados.length - 1];
    }

    window.addEventListener('resize', this.updateIsMobile);
  },
  unmounted() {
    window.removeEventListener('resize', this.updateIsMobile);
  },
  methods: {
    sortearNumero() {
      const todosNumeros = Array.from({ length: 75 }, (_, i) => i + 1);
      const restantes = todosNumeros.filter(n => !this.numerosSorteados.includes(n));

      if (restantes.length === 0) {
        alert('Todos os números já foram sorteados!');
        return;
      }

      const novoNumero = restantes[Math.floor(Math.random() * restantes.length)];
      this.numerosSorteados.push(novoNumero);
      this.ultimoNumero = novoNumero;

      localStorage.setItem('numerosSorteados', JSON.stringify(this.numerosSorteados));
      this.mostrarSuperAlerta();
    },
    letraDoNumero(numero) {
      if (numero <= 15) return 'B';
      if (numero <= 30) return 'I';
      if (numero <= 45) return 'N';
      if (numero <= 60) return 'G';
      return 'O';
    },
    numeroColor(num) {
      if (this.ultimoNumero === num) return 'green darken-3';
      if (this.numerosSorteados.includes(num)) return 'green lighten-1';
      return 'grey lighten-3';
    },
    mostrarSuperAlerta() {
      this.mostrarAlerta = true;
      setTimeout(() => {
        this.mostrarAlerta = false;
      }, 10000);
    },
    limparBingo() {
      this.numerosSorteados = [];
      this.ultimoNumero = null;
      localStorage.removeItem('numerosSorteados');
    },
    updateIsMobile() {
      this.$forceUpdate();
    },
  },
};
</script>

<style scoped>
.overlayOpacity {
 opacity: 1 !important;
 background: black !important;
}
</style>