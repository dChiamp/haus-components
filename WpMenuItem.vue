<template>
    <li :class="classes">
        <a
            v-if="!isRelative"
            target="_blank"
            :href="item.url"
            @click.native="menuInteracted"
            v-html="item.label"
        />

        <nuxt-link
            v-if="isRelative"
            :to="getPath"
            @click.native="menuInteracted"
            v-html="item.label"
        />

        <!-- TODO Make prop for sub-menus to be drop downs -->
        <ul
            v-if="hasSubMenu"
            class="sub-menu"
        >
            <wp-menu-item
                v-for="(subItem, i) in getChildren"
                :key="`sub-${i}`"
                class="menu-item sub-menu-item"
                :item="subItem"
                :prepend-paths="prependPaths"
                @menu-interacted="menuInteracted()"
            />
        </ul>
    </li>
</template>

<script>
import _kebabCase from "lodash/kebabCase"
import _get from "lodash/get"

export default {
    props: {
        item: {
            type: Object,
            default: () => {}
        },
        prependPaths: {
            type: String,
            default: ""
        }
    },
    computed: {
        classes() {
            return [
                "wp-menu-item menu-item sub-menu-item",
                {
                    "is-realtive": this.isRelative
                },
                { "has-sub-menu": this.hasSubMenu }
            ]
        },
        getChildren() {
            return _get(this, "item.childItems.nodes", [])
        },
        hasSubMenu() {
            return this.getChildren.length
        },
        isRelative() {
            return this.item.target !== "_blank"
        },
        relativeUrl() {
            return this.item.url.replace(this.$store.state.siteMeta.apiUrl, "")
        },
        getPath() {
            return `${this.prependPaths}${this.relativeUrl}`
        }
    },
    methods: {
        menuInteracted(event) {
            this.$emit("menu-interacted", event)
        }
    }
}
</script>
