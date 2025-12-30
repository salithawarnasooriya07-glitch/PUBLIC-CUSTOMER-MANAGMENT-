<template>
  <q-layout view="hHh lpR fFf" class="bg-white font-inter">
    <q-header class="bg-white text-dark q-py-sm" style="border-bottom: 1px solid #f0f0f0">
      <q-toolbar>
        <q-btn flat dense round icon="menu" aria-label="Menu" @click="toggleLeftDrawer" v-if="$q.screen.lt.md" />

        <q-toolbar-title class="text-weight-bold text-h5 tracking-wide">
          bossofict<span class="text-accent-green">.</span>
        </q-toolbar-title>

        <q-space />

        <div class="gt-sm row items-center q-gutter-md">
          <q-btn flat no-caps label="Home" class="text-weight-medium" />
          <q-btn flat no-caps label="Courses" class="text-weight-medium" />
          <q-btn flat no-caps label="About" class="text-weight-medium" />
          <q-btn unelevated color="dark" text-color="white" label="Login" no-caps class="q-px-lg rounded-btn" />
        </div>
      </q-toolbar>
    </q-header>

    <q-drawer v-model="leftDrawerOpen" bordered side="left" class="bg-white" v-if="$q.screen.lt.md">
      <q-list padding class="text-dark">
        <q-item clickable v-ripple>
          <q-item-section>Home</q-item-section>
        </q-item>
        <q-item clickable v-ripple>
          <q-item-section>Courses</q-item-section>
        </q-item>
        <q-item clickable v-ripple>
          <q-item-section>About</q-item-section>
        </q-item>
        <q-item clickable v-ripple>
          <q-item-section>Login</q-item-section>
        </q-item>
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script setup>
import { ref } from 'vue'

const leftDrawerOpen = ref(false)

function toggleLeftDrawer() {
  leftDrawerOpen.value = !leftDrawerOpen.value
}
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;500;600;700&display=swap');

.font-inter {
  font-family: 'Outfit', sans-serif;
}

.text-accent-green {
  color: #10B981;
}

.tracking-wide {
  letter-spacing: -0.5px;
}

.rounded-btn {
  border-radius: 8px;
}

body {
  background: #ffffff;
}
</style>
