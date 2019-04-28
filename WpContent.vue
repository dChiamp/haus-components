<template>
    <div
        class="wp-content"
        v-html="html"
    />
</template>

<script>
export default {
    props: {
        html: {
            type: String,
            default: ""
        }
    }
}
</script>
