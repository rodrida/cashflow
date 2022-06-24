<template>
  <div>
    <svg
      @touchstart="tap"
      @touchmove="tap"
      @touchend="untap"
      viwBox="0,0 300 200"
    >
      <line
        stroke="#c4c4c4"
        stroke-width="2"
        x1="0"
        :y1="zero"
        x2="300"
        :y2="zero"
      ></line>
      <polyline
        fill="none"
        stroke="#0689b0"
        stroke-width="2"
        :points="points"
      ></polyline>
      <line
        v-show="showPointer"
        stroke="#04b500"
        stroke-width="2"
        :x1="pointer"
        y1="0"
        :x2="pointer"
        y2="200"
      ></line>
    </svg>
    <p>Últimos 30 días</p>
  </div>
</template>

<script setup>
import { ref, toRefs, defineProps, defineEmits, computed, watch } from "vue";

const props = defineProps({
  amounts: {
    type: Array,
    default: () => [],
  },
});

const { amounts } = toRefs(props);

const amountToPixeles = (amount) => {
  // Math te devuelve de una lista el elemento mínimo y el elemento máximo
  const min = Math.min(...amounts.value);
  const max = Math.max(...amounts.value);

  const amountAbs = amount + Math.abs(min);
  const minmax = Math.abs(max) + Math.abs(min);

  return 200 - ((amountAbs * 100) / minmax) * 2;
};

// Esta función me permite saber cuando estoy llegando a cero pesos
const zero = computed(() => {
  return amountToPixeles(0);
});

const points = computed(() => {
  // Primero encontrar el total de elementos
  const total = amounts.value.length;
  // El total me sirve para saber entre cuantos puntos vamos a dividir la gráfica
  // Retornamos los puntos que tendra la gráfica
  return amounts.value.reduce((points, amount, i) => {
    // x es cada uno de los elementos que iteramos
    const x = (300 / total) * (i + 1);
    // y es siempre el monto a pixeles
    const y = amountToPixeles(amount);
    // Retornamos la cadena final
    return `${points} ${x},${y}`;
  }, `0, ${amountToPixeles(amounts.value.length ? amounts.value[0] : 0)}`);
});

const showPointer = ref(false);
const pointer = ref(0);

// Definimos el evento select, para cada que se seleccione un evento en pantalla
const emit = defineEmits(["select"]);

// watch recibe la variable que va a escuchar
watch(pointer, (value) => {
  const index = Math.ceil(value / (300 / amounts.value.length));
  if (index < 0 || index > amounts.value.length) return;
  // propagamos el monto con el evento select
  emit("select", amounts.value[index - 1]);
});

const tap = ({ target, touches }) => {
  // Para aparecer la línea vertical
  showPointer.value = true;
  // Obtiene el tamaño de la imagen SVG
  const elementWidth = target.getBoundingClientRect().width;
  // Obtiene la coordenada x de nuestro elemento HTML
  const elementX = target.getBoundingClientRect().x;
  // Obtiene la coordenada x donde se hizo touch
  const touchX = touches[0].clientX;
  pointer.value = ((touchX - elementX) * 300) / elementWidth;
};

const untap = () => {
  // Para desaparecer la línea vertical
  showPointer.value = false;
};
</script>

<style scoped>
svg {
  width: 100%;
}

p {
  text-align: center;
}
</style>
