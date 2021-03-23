<script lang="ts">
import Vue from 'vue';
// Uncomment import and local "components" registration if library is not registered globally.
// import { VueVglibSample } from '@/entry.esm';

export default Vue.extend({
  name: 'ServeDev',
  // components: {
  //  VueVglibSample,
  // }
});
</script>

<template>
  <div id="app">
    <vue-vglib-sample />
  </div>
</template>
