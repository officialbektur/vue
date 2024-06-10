<template>
    <button v-for="(item, index) in count" @click.prevent="onEditValue(index)">{{ items[index].value }}</button>
</template>

<script >
    export default {
        props: {
            modelValue: Array,
            count: Number
        },
        data() {
            return {
                items: this.modelValue
            }
        },
        methods: {
            onEditValue(index) {
                ++this.items[index].value
                this.$emit('update:modelValue', this.items)
            }
        },
    }
</script>

<style scoped>
</style>
