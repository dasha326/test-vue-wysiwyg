<template>
    <main>
        <div class="container">
            <div class="buttons">
                <button class="btn btn-primary" @click="goBack" :disabled="textBefore.length === 0">
                    <SvgIcon name="arrow-left"/>
                </button>
                <button class="btn btn-primary" @click="goNext" :disabled="textNext.length === 0">
                    <SvgIcon name="arrow-right"/>
                </button>
                <button class="btn btn-primary" @click="addTitle">
                    <SvgIcon name="t"/>
                </button>
                <button class="btn btn-primary" @click="addParagraph">
                    <SvgIcon name="tt"/>
                </button>
                <button class="btn btn-primary" @click="addParagraph">
                    <SvgIcon name="img"/>
                </button>
                <button class="btn btn-primary" @click="addParagraph">Скопировать HTML</button>
            </div>
            <pre contenteditable="true" v-html="textValue" @blur="changeText($event.target)" @focus="rememberText"/>
        </div>
    </main>
</template>

<script lang="ts">
import {defineComponent, ref} from "vue";
import text from "@/mock/text";
import SvgIcon from "@/components/SvgIcon.vue";

export default defineComponent({
    name: 'App',
    components: {SvgIcon},
    setup(){
        const textValue = ref(text);
        const textBefore = ref<string[]>([]);
        const textBeforeList = textBefore.value;
        const textNext = ref<string[]>([]);
        const textNextList = textNext.value;

        function goBack(){
            const lastTextBefore = textBeforeList[textBeforeList.length - 1];
            textNextList.push(textValue.value);
            textValue.value = lastTextBefore;
            textBeforeList.pop();
        }
        function goNext(){
            const lastTextNext = textNextList[textNextList.length - 1];
            textBeforeList.push(textValue.value);
            textValue.value = lastTextNext;
            textNextList.pop();
        }

        function markupText(tag:string) {
            const select = document.getSelection();
            if(select !== null) {
                textValue.value = textValue.value.replace(select.toString(), `<${tag}>${select.toString()}</${tag}>`);
            }
        }
        function addParagraph() {
            markupText('p');
        }
        function addTitle() {
            markupText('h2');
        }

        function changeText(event:HTMLElement) {
           textValue.value = event.innerHTML
        }

        function rememberText() {
            if(textBeforeList[textBeforeList.length - 1] !== textValue.value) {
                textBeforeList.push(textValue.value);
            }
        }
        return{
            textValue,
            textBefore,
            textNext,
            goBack,
            goNext,
            addParagraph,
            addTitle,
            changeText,
            rememberText
        }
    }
});
</script>

<style lang="scss">
body {
    background-color: $bg-primary;
    color: $color-secondary;
    margin: 50px 0;
    font-family: 'Roboto', Arial, sans-serif;
}

.container {
    max-width: 739px;
    margin-left: auto;
    margin-right: auto;
}
.content{
    margin-top: 50px;
}
pre{
    white-space: pre-wrap;
    margin-top: 31px;
    font-family: $base-font-family;
}
p{
    margin: 0 0 15px;
}
.btn{
    display: inline-block;
    border-radius: $border-radius;
    padding: 10px;
    border: 0;
    margin-right: 12px;
    min-width: 42px;
    cursor: pointer;
    &:disabled{
        opacity: .5;
    }
    &-primary{
        background-color: $bg-secondary;
        color: $color-primary;
    }
}
</style>
