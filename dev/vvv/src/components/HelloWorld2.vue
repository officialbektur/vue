<template>
    <button @click.prevent="onEditValue()">{{ item.value }}</button>
</template>

<script >
    export default {
        props: {
            item: Object,
        },
        data() {
            return {
            }
        },
        methods: {
            onEditValue() {
                ++this.item.value
            }
        },
    }
</script>

<style scoped>
</style>
