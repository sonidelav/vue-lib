<template>
    <div class="text-editable" :class="{ 'focused' : isFocused }">
        <component
            ref="input"
            :contenteditable="true"
            tabindex="1"
            :is="tag"
            class="content-input"
            @keydown.stop.prevent.enter="onEnterDown"
            @blur="onBlur"
            @focus="onFocus"
            v-text="value">
        </component>
    </div>
</template>

<script>
export default {
    name: "TextEditable",
    props: {
        tag: { required: false, default: 'span', type: String },
        value: { required: false, default: null, type: String }
    },
    data:() => ({
        prevText: null,
        inputValue: null,
        isFocused: false,
    }),
    methods: {
        onEnterDown(e) {
            this.$refs['input'].blur();
        },
        onBlur(e) {
            this.$emit('blur', e);
            const value = e.target.innerText.trim();
            this.$emit('input', value);
            this.isFocused = false;
        },
        onFocus(e) {
            this.isFocused = true;
            this.prevText = this.value;

            const selectAllContents = function(element) {
                // select all text in contenteditable
                // see http://stackoverflow.com/a/6150060/145346
                let range, selection;
                if (document.body.createTextRange) {
                    range = document.body.createTextRange();
                    range.moveToElementText(element);
                    range.select();
                } else if (window.getSelection) {
                    selection = window.getSelection();
                    range = document.createRange();
                    range.selectNodeContents(element);
                    selection.removeAllRanges();
                    selection.addRange(range);
                }
            }
            selectAllContents(e.target);
            this.$emit('focus', e);
        }
    }
}
</script>

<style lang="scss">
.text-editable {
    cursor: pointer;

    &.focused {
        cursor: text;
        .content-input {
            background-color: rgba(#FFF, 0.2);
            border: solid 1px #ddd;
            padding: 2px 4px;
            &:focus, &:hover, &:focus-visible {
                outline: none;
            }
        }
    }
}
</style>
