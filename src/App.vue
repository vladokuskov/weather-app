<script setup lang="ts">
import { RouterView } from 'vue-router'

import NavBar from '@/components/NavBar.vue'
</script>

<template>
  <div class="main-wrapper">
    <div class="content-wrapper">
      <NavBar />

      <RouterView />
    </div>

  </div>
</template>

<style scoped>
.main-wrapper {
  width: 100%;
  /* max and min values are from TT */
  max-width: 1200px;
  min-width: 360px;
  margin: 0 auto;
  padding: 0 .5rem;
  margin-bottom: 3rem;
}

.content-wrapper {
  width: 100%;
  display: flex;
  flex-direction: column;
  max-width: 840px;
  margin: 0 auto;
}



footer {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: flex-start;
  padding: .5rem 0;
}
</style>
