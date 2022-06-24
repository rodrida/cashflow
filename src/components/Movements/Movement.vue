<template>
  <div class="movement">
    <div class="content">
      <h4>{{ title }}</h4>
      <p>{{ description }}</p>
    </div>
    <div class="action">
      <img src="@/assets/trash-icon.svg" alt="borrar" @click="remove" />
      <p :class="{ red: isNegative, green: !isNegative }">
        {{ amountCurrency }}
      </p>
    </div>
  </div>
</template>

<script setup>
import { toRefs, defineProps, defineEmits, computed } from "vue";

// Función de js puro para el formato de la moneda
const currencyFormatter = new Intl.NumberFormat("es-MX", {
  style: "currency",
  currency: "MXN",
});

const props = defineProps({
  id: {
    type: Number,
  },
  title: {
    type: String,
  },
  description: {
    type: String,
  },
  amount: {
    type: Number,
  },
});

const { id, title, description, amount } = toRefs(props);

const amountCurrency = computed(() => currencyFormatter.format(amount.value));

// Esta función computada me permite definir el color dependiendo del valor de amount
const isNegative = computed(() => amount.value < 0);

// En la constante emit obtenemos el evento remove
const emit = defineEmits(["remove"]);

const remove = () => {
  // Emitimos el evento remove hacia el componente padre (Index.vue) y mandamos el id
  emit("remove", id.value);
};
</script>

<style scoped>
.movement {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  padding: 16px;
  background-color: #e6f9ff;
  border-radius: 8px;
  box-sizing: border-box;
}

.movement .content {
  width: 100%;
}

.movement .action {
  display: flex;
  justify-content: space-between;
  align-items: flex-end;
  flex-direction: column;
}
h4,
p {
  margin: 0;
  padding: 0;
}

h4 {
  margin-bottom: 8px;
}

.movement .action img {
  margin-bottom: 16px;
}

.red {
  color: red;
}

.green {
  color: green;
}
</style>
