<template>
    <nuxt-link
        v-if="isRelative || isInternal"
        class="smart-link"
        :to="parsedTo"
    >
        <slot />
    </nuxt-link>

    <a
        v-else-if="to"
        :href="to"
        :target="target"
        class="smart-link"
    >
        <slot />
    </a>

    <component
        :is="element"
        v-else
        class="smart-link"
    >
        <slot />
    </component>
</template>

<script>
import _get from "lodash/get"
export default {
    props: {
        to: {
            type: String,
            default: ""
        },
        target: {
            type: String,
            default: "_self"
        },
        element: {
            type: String,
            default: "div"
        }
    },
    computed: {
         frontendUrl() {
            return this.$store.state.siteMeta.frontendUrl || ""
        },
        isInternal() {
            // wp-content in url means probably a download link, so open in new window
            if (this.to.includes("wp-content")) {
                return false
            }

            return this.to.includes(this.frontendUrl)
        },
        isRelative() {
            let result = false
            switch (true) {
                case this.target == "_blank":
                    // If open in new window, then render an a-tag
                    result = false
                    break
                case this.to && String(this.to).indexOf(".") === 0:
                case this.to && String(this.to).indexOf("/") === 0:
                    // return true if we start with a slash
                    result = true
            }
            return result
        },
        parsedTo() {
            let url = this.to

            // Replace all these things
            const replaceThese = [
                _get(this, "$store.state.siteMeta.frontendUrl", ""),
                _get(this, "$store.state.siteMeta.backendUrl", ""),
                _get(this, "$store.state.siteMeta.host", "")
            ]
            replaceThese.forEach(element => {
                url = url.replace(element, "")
            })
            
            return url.startsWith("/") ? url : `/${url}`
        }
    }
}
</script>
