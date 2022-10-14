<script setup lang="ts">
import NavItem from "../components/NavItem.vue";
import NavIcon from "../components/NavIcon.vue";
import ToggleThemeButtom from "../components/ToggleThemeButtom.vue";
</script>
<template>
  <div class="fixed w-full bg-black-100/[0.1] backdrop-blur h-14 z-20">
    <div class="container mx-auto p-2 flex mt-0">
      <NavIcon />
      <NavItem />
      <NavItem />
      <NavItem />
      <div class="grow"></div>
      <ToggleThemeButtom />
    </div>
  </div>
</template>
