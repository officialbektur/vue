<template>
  <div class="post">
      {{t('title')}}<br>
      {{t('post')}}
  </div>
        <div
                v-for="locale in $i18n.availableLocales"
                :key="locale"
                :value="locale"
                @click.prevent="changeLocal(locale)"
        >
            {{ locale }}
        </div>
</template>

<script setup type="ts">
import { useI18n } from 'vue-i18n';

const { t, locale } = useI18n();


const changeLocal = (loc) => {
    locale.value = loc
}
</script>