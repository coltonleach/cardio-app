<script setup>
import { ref } from 'vue'
import Close from '@/assets/Close.vue'
import Hamburger from '@/assets/Hamburger.vue'

const menu = ref(false)
</script>
<template>
  <div class="menu-btn" @click="menu = !menu">
    <Close size="2rem" v-if="menu" />
    <Hamburger size="2rem" v-if="!menu" />
  </div>
  <div class="nav-fullscreen" :class="menu ? 'true' : ''">
    <a>Login</a>
    <a>FAQ</a>
    <a>General Info</a>
  </div>
</template>
<style lang="scss" scoped>
.menu-btn {
  z-index: 10;
  position: absolute;
  top: 0;
  left: 0;
  margin: 1rem;
}

.nav-fullscreen {
  z-index: 1;
  position: fixed;
  inset: 0;
  display: flex;
  opacity: 0;
  flex-direction: column;
  gap: 2rem;
  font-size: 2rem;
  background-color: var(--clr-nav);
  padding-top: 5rem;
  right: 100%;
  transition: right 500ms ease, opacity 100ms ease;

  &.true {
    opacity: 1;
    right: 0;
  }
}
</style>
