<template>
    <nuxt-link
        v-if="isRelative"
        :class="['anchor-div', `a-${replaceWith}`]"
        :to="href | removeOrigin"
    >
        <slot />
    </nuxt-link>
    <!--  TODO: debug timing of filter / render (link changes after click)-->
    <a
        v-else-if="href"
        :class="['anchor-div', `a-${replaceWith}`]"
        :href="href"
        :target="newWindow ? '_blank' : null"
        :rel="newWindow ? 'noopener noreferrer' : null"
    >
        <slot />
    </a>

    <component
        :is="replaceWith"
        v-else
        :class="['anchor-div', `a-${replaceWith}`]"
    >
        <slot />
    </component>
</template>

<script>
export default {
    name: 'a-div',
    props: {
        href: String,
        'new-window': {
            type: Boolean,
            default: true
        },
        'replace-with': {
            type: String,
            default: 'div'
        },
        'force-new': {
            type: Boolean,
            default: false
        }
    },
    computed: {
        localUrl() {
            //https://nuxtjs.org/faq/window-document-undefined/
            return process.client ? window.location.origin : false
        },
        isRelative() {
            // return false if we're forcing an outside link
            if (this.forceNew) return false

            // return true if we start with a slash
            if (this.href && String(this.href).indexOf('/') === 0) return true

            // return true if we use the same origin
            if (this.href.includes(this.localUrl)) return true

            return false
        }
    },
    filters: {
        removeOrigin(input) {
            // console.log('input')
            let localUrl = process.client ? window.location.origin : false
            return input.replace(localUrl, '')
        }
    }
}
</script>
