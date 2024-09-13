<template>
  <div id="app">
    <RetirementCalculator />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import RetirementCalculator from './components/RetirementCalculator.vue';

export default defineComponent({
  name: 'App',
  components: {
    RetirementCalculator,
  },
});
</script>
