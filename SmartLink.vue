<template>
    <nuxt-link
        v-if="isRelative"
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
import config from "~/nuxt.config"

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
            let url = ""
            if (config.hasOwnProperty("apollo")) {
                // Get the backend URL from Apollo, and remove the Grapgh QL part
                url = config.apollo.clientConfigs.default.httpEndpoint.replace(
                    "/graphql",
                    ""
                )
            }

            return url
        },
        isRelative() {
            let result = false

            switch (true) {
            case this.target == "_blank":
                // If open in new window, then render an a-tag
                result = false
                break

            case this.to && String(this.to).indexOf("/") === 0:
                // return true if we start with a slash
                result = true
            }

            return result
        },
        parsedTo() {
            return this.to.replace(this.apiUrl, "")
        }
    }
}
</script>
