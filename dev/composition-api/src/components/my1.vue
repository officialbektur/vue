<script lang="ts">
import myMixin from "./../mixins/myMixin"
export default {
    components: {
    },
    mixins: [myMixin],
    created() {
    },
}
</script>

<template>
    <div>
        <div>
            list 1 {{ list }}
        </div>
        <input type="text" v-model="list">
    </div>
</template>
