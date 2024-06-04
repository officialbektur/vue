<script lang="ts">
import My2 from '@/components/my2.vue';
import myMixin from "./../mixins/myMixin"
import My1 from "@/components/my1.vue";
import TheWelcome from "@/components/TheWelcome.vue";
export default {
    components: {
        TheWelcome,
        My1,
        My2,
    },
    mixins: [myMixin],
    created() {
    },
}
</script>

<template>
  <main>
    <TheWelcome />
      <div>
          <My1></My1>
      </div>
      <div>
          <My2></My2>
      </div>
  </main>
</template>
