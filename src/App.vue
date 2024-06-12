<template>
  <app-movable-list
      v-model="list"
  >
  </app-movable-list>
</template>

<script setup>
import AppMovableList from './components/organisms/app-movable-list/AppMovableList.vue';
import {ref} from "vue";

const list = ref([
  [],
  [
    {id: 1, name: "Element 1"},
    {id: 2, name: "Element 2"},
    {id: 3, name: "Element 3"},
    {id: 4, name: "Element 4"},
    {id: 5, name: "Element 5"},
  ],
  [],
  [],
]);

</script>

<style scoped>

</style>
