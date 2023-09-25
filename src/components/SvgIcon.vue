<template>
    <svg :class="className">
        <title v-if="title">{{ title }}</title>
        <use :xlink:href="iconPath" xmlns:xlink="http://www.w3.org/1999/xlink"/>
    </svg>
</template>

<script lang="ts">
import {computed, defineComponent} from "vue";
export default defineComponent({
    name: 'SvgIcon',
    props: {
        name: {
            type: String,
            required: true
        },

        title: {
            type: String,
            default: null
        }
    },
    setup(props){
        const iconPath = computed(() => {
            let icon = require(`@/assets/icons/${props.name}.svg`);
            if (Object.prototype.hasOwnProperty.call(icon, 'default')) {
                icon = icon.default;
            }

            return icon.url;
        });
        const className = computed(() => `svg-icon svg-icon--${props.name}`);

        return{
            iconPath,
            className
        }
    }
});
</script>

<style>
.svg-icon {
    fill: currentColor;
    height: 24px;
    width: 24px;
}
</style>
