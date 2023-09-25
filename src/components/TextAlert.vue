<template>
    <div class="alert">{{text}}</div>
</template>

<script lang="ts">
import {defineComponent} from "vue";

export default defineComponent({
    name: 'TextAlert',
    props:{
        text: String
    }
});
</script>

<style scoped lang="scss">
    .alert{
        position: fixed;
        top: 10px;
        right: 10px;
        background-color: $bg-secondary;
        color: $color-primary;
        padding: 10px;
        border-radius: $border-radius;
        font-size: 15px;
    }
</style>