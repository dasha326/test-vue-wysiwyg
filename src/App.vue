<template>
    <main>
        <div class="container">
            <div class="buttons">
                <button class="btn btn-primary" @click="goBack" :disabled="textBefore.length === 0" aria-label="Кнопка назад" title="Назад">
                    <SvgIcon name="arrow-left"/>
                </button>
                <button class="btn btn-primary" @click="goNext" :disabled="textNext.length === 0" aria-label="Кнопка вперед" title="Вперед">
                    <SvgIcon name="arrow-right"/>
                </button>
                <button class="btn btn-primary" @click="addTitle" aria-label="Кнопка создания заголовока" title="Создать заголовок">
                    <SvgIcon name="t"/>
                </button>
                <button class="btn btn-primary" @click="addParagraph" aria-label="Кнопка создания параграфа" title="Создать параграф">
                    <SvgIcon name="tt"/>
                </button>
                <button class="btn btn-primary" @click="addImage" aria-label="Кнопка добавлении картинки" title="Добавить картинку">
                    <SvgIcon name="img"/>
                </button>
                <button class="btn btn-link" @click="copyHTML" aria-label="Кнопка копирования в буфер обмена" title="Скопировать в буфер обмена">Скопировать HTML</button>
            </div>
            <div class="content" contenteditable="true" v-html="textValue" @blur="changeText($event.target)" @focus="rememberText"/>
            <TextAlert v-if="copyAlert" text="Текст успешно скопирован"/>
            <TextAlert v-if="selectAlert" text="Выделите больше символов для добавления тега"/>
        </div>
    </main>
</template>

<script lang="ts">
import {defineComponent, ref} from "vue";
import text from "@/mock/text";
import SvgIcon from "@/components/SvgIcon.vue";
import TextAlert from "@/components/TextAlert.vue"

export default defineComponent({
    name: 'App',
    components: {SvgIcon, TextAlert},
    setup(){
        const copyAlert = ref<boolean>(false);
        const selectAlert = ref<boolean>(false);

        const textValue = ref<string>(text);
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

        function markupText(tag: string) {
            const select = window.getSelection();
            if(select !== null) {
                if(select.toString().length > 2) {
                    const parentTag = select.getRangeAt(0).startContainer.parentElement?.tagName;
                    if(parentTag && parentTag !== 'DIV') {
                        const previewTag = parentTag.toLowerCase();
                        textValue.value = textValue.value.replace(select.toString(),`</${previewTag}><${tag}>${select.toString()}</${tag}><${previewTag}>`);
                    } else {
                        textValue.value = textValue.value.replace(select.toString(), `<${tag}>${select.toString()}</${tag}>`);
                    }
                } else {
                    selectAlert.value = true;
                    setTimeout(() => selectAlert.value = false, 2000)
                }
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

        function addImage(){
            const url:string | null = prompt('Добавьте ссылку на изображение');
            const select = window.getSelection();
            if (url && select) {
                let image = document.createElement('img');
                image.src = url;
                select.getRangeAt(0).insertNode(image);
            }
        }

        async function copyHTML() {
            const select = document.getSelection();
            if(select !== null){
                const copyText = select.toString().length > 0 ? select.toString() : textValue.value;
                try {
                    await navigator.clipboard.writeText(copyText);
                    copyAlert.value = true;
                    setTimeout(() => copyAlert.value = false, 2000)
                } catch(err) {
                    return
                }
            }
        }

        return{
            textValue,
            textBefore,
            textNext,
            copyAlert,
            selectAlert,
            goBack,
            goNext,
            addParagraph,
            addTitle,
            changeText,
            rememberText,
            addImage,
            copyHTML
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
h1, h2, h3{
    font-weight: 400;
}
h2{
    margin-top: 46px;
    margin-bottom: 33px;
}
.container {
    max-width: 739px;
    margin-left: auto;
    margin-right: auto;
}
.content{
    margin-top: 31px;
}
p{
    margin: 0 0 15px;
}
img {
    display: block;
    max-width: 100%;
    margin: 31px 0;
}
.buttons{
    display: flex;
    align-items: center;
}
.btn{
    display: inline-block;
    border-radius: $border-radius;
    padding: 10px;
    border: 0;
    margin-right: 12px;
    min-width: 42px;
    font-size: 15px;
    &:not(:disabled){
        cursor: pointer;
    }
    &:disabled{
        opacity: .5;
    }
    &-primary{
        background-color: $bg-secondary;
        color: $color-primary;
        transition: color $trans-primary, background-color $trans-primary;
        &:not(:disabled):hover{
            background-color: $color-primary;
            color: $bg-primary;
        }
    }
    &-link{
        background-color: transparent;
        color: $color-primary;
        transition: color $trans-primary;
        &:hover{
            color: $color-secondary;
        }
    }
}
</style>
