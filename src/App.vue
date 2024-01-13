<script setup lang="ts">
import { RouterLink, RouterView } from "vue-router";
</script>

<template>
  <nav>
    <RouterLink to="/">Home</RouterLink>
    <RouterLink to="/about">About</RouterLink>
    <RouterLink to="/amisPage-table">amisPage-table</RouterLink>
    <RouterLink to="/amisPage-form">amisPage-form</RouterLink>
  </nav>
  <router-view></router-view>
</template>
