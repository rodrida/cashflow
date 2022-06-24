<template>
  <Layout>
    <template #header>
      <Header></Header>
    </template>
    <template #resume>
      <Resume
        :totalLabel="'Ahorro total'"
        :label="label"
        :totalAmount="totalAmount"
        :amount="amount"
      >
        <template #graphic>
          <Graphic :amounts="amounts" @select="select"></Graphic>
        </template>
        <template #action>
          <Action @create="create"></Action>
        </template>
      </Resume>
    </template>
    <template #movements>
      <Movements :movements="movements" @remove="remove"></Movements>
    </template>
  </Layout>
</template>

<script>
import Layout from "./Layout.vue";
import Header from "./Header.vue";
import Resume from "./Resume/Index.vue";
import Action from "./Action.vue";
import Movements from "./Movements/Index.vue";
import Graphic from "./Resume/Graphic.vue";
import { computed } from "@vue/runtime-core";

export default {
  components: {
    Layout,
    Header,
    Resume,
    Action,
    Movements,
    Graphic,
  },
  data() {
    return {
      label: null,
      amount: null,
      movements: [],
    };
  },
  computed: {
    amounts() {
      // Primero vamos a filtrar los últimos 30 días
      // Con filter filtramos las fechas y con map obtenemos el monto
      const lastDays = this.movements
        .filter((m) => {
          // Primero obtenemos la fecha de hoy
          const today = new Date();
          // Obtenemos la fecha de hace 30 días
          const oldDate = today.setDate(today.getDate() - 30);

          // Retornamos verdadero o falso dependiendo si nuestra fecha es mayor a los 30 días
          return m.time > oldDate;
        })
        .map((m) => m.amount);

      // Retornamos cada movimiento sumandole los movimientos anteriores
      return lastDays.map((m, i) => {
        // i + 1 para incluir el movimiento actual
        const lastMovements = lastDays.slice(0, i + 1);

        return lastMovements.reduce((suma, movement) => {
          return suma + movement;
        }, 0);
      });
    },
    totalAmount() {
      return this.movements.reduce((suma, m) => {
        return suma + m.amount;
      }, 0);
    },
  },
  mounted() {
    const movements = JSON.parse(localStorage.getItem("movements"));

    // Validamos si lo que recibimos en movements es una lista (Array)
    if (Array.isArray(movements)) {
      this.movements = movements?.map((m) => {
        return { ...m, time: new Date(m.time) };
      });
    }
  },
  methods: {
    create(movement) {
      this.movements.push(movement);
      this.save();
    },
    remove(id) {
      const index = this.movements.findIndex((m) => m.id === id);
      this.movements.splice(index, 1);
      this.save();
    },
    save() {
      localStorage.setItem("movements", JSON.stringify(this.movements));
    },
    select(el) {
      console.log(el);
      this.amount = el;
    },
  },
};
</script>

<style></style>
