<template>
    <component :is="element">
        <component
            is="span"
            v-for="(line, i) in lines"
            :key="i"
            :class="['line', `line-${i + 1}`]"
            v-html="line"
        />
    </component>
</template>

<script>
export default {
    props: {
        text: {
            type: String,
            default: ""
        },
        separator: {
            type: String,
            default: " &#8211; "
        },
        element: {
            type: String,
            default: "span"
        }
    },
    computed: {
        lines() {
            return this.text.split(this.separator)
        }
    }
}
</script>
