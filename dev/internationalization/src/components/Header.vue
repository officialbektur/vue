<template>
  <select v-model="$i18n.locale">
    <option
      v-for="locale in $i18n.availableLocales"
      :key="locale"
      :value="locale"
    >
      {{ locale }}
    </option>
  </select>
</template>

<script setup lang="ts"></script>
