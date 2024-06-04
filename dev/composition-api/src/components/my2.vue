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
            list 2 {{ list }}
        </div>
        <input type="text" v-model="list">
    </div>
</template>
