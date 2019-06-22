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
        apiUrl() {
            return (
                // I have both these URLs for backwards compatibility. Could remove second one soon.
                this.$store.state.siteMeta.apiUrl ||
                this.$store.state.apiUrl ||
                ""
            )
        },
        isInternal() {
            return this.to.includes(this.apiUrl)
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
            let host = _get(this, "$store.state.siteMeta.host", "")
            return this.to.replace(this.apiUrl, "").replace(host, "")
        }
    }
}
</script>
