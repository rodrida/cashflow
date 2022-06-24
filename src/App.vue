<template>
  <!-- Es un componente de Vue que recibe 2 slot's -->
  <!-- Con # también se puede especificar el nombre del slot a utilizar -->
  <Suspence>
    <template #default>
      <Home />
    </template>
    <!-- Mientras no haya Home carga al componente SplashScreen -->
    <template #fallback>
      <SplashScreen />
    </template>
  </Suspence>
</template>

<script>
import SplashScreen from "@/components/SplashScreen.vue";
import { defineAsyncComponent } from "vue";

export default {
  components: {
    SplashScreen,
    Home: defineAsyncComponent(
      () =>
        new Promise((resolve) => {
          setTimeout(() => {
            resolve(import("./components/Home.vue"));
          }, 2500);
        })
    ),
  },
};
</script>

<style>
html,
body,
.app {
  min-height: 100vh;
  margin: 0;
  font-family: Arial, Helvetica, sans-serif;
}

* {
  --brand-green: #04b500;
  --brand-blue: #0689b0;
}
</style>
