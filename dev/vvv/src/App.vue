<script>
import HelloWorld from './components/HelloWorld.vue'
import HelloWorld2 from './components/HelloWorld2.vue'

export default {
    data() {
        return {
            items: [
                {
                    value: 4
                },
                {
                    value: 12
                },
                {
                    value: 212
                },
                {
                    value: 445
                },
            ]
        }
    },
    components: {
        HelloWorld,
        HelloWorld2
    }
}
</script>

<template>
  <HelloWorld v-model="items" :count="4"/>
  <HelloWorld2 v-for="item in items" :item="item"/>

    {{items}}
</template>

<style scoped>
</style>
