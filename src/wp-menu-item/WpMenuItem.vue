<template>
    <li :class="classes">
        <slot>
            <!--  TODO: add filter to removeOrigin?-->
            <nuxt-link
                v-if="!isExternalLink"
                @click.native="menuInteraction"
                :class="itemClasses"
                :to="relativePath | removeTrailingSlash"
                v-html="item.label"
                :target="item.target"
            />

            <a
                v-else
                :class="itemClasses"
                @click.native="menuInteraction"
                :href="relativePath | removeTrailingSlash"
                target="_blank"
                v-html="item.label"
            />

            <ul class="children" v-if="children.length">
                <wp-menu-item
                    v-for="(child, i) in children"
                    :class="itemClasses"
                    :item="child"
                    :key="i"
                    :forceRouterLink="forceRouterLink"
                />
            </ul>
        </slot>
    </li>
</template>

<script>
import _get from 'lodash/get'
import _pick from 'lodash/pick'
const backendURL = 'http://stackhaus-backend.flywheelsites.com'
// TODO: replace backend url, filter relative path
export default {
    name: 'menu-item',
    filters: {
        removeTrailingSlash(val) {
            return val == '/' ? val : val.replace(/\/$/, '')
        }
    },
    props: {
        item: {
            type: Object,
            default: () => {
                return {}
            }
        },
        forceRouterLink: {
            type: Boolean,
            default: false
        }
    },
    computed: {
        isExternalLink() {
            return this.item.url.includes(backendURL) ? false : true
        },
        relativePath() {
            // TODO: better way to filter url
            /* Remove backend url*/
            return this.item.url.split(backendURL).pop()
        },
        children() {
            let kids = _get(this.item.childItems, 'edges', [])

            return kids.map(i => {
                return i.node
            })
        },
        isActive() {
            return (
                this.$nuxt.$route.path.replace(/\/*$/, '') == this.relativePath
            )
        },
        isParent() {
            // remove trailing slash
            const strippedSlash = this.$nuxt.$route.path.replace(/\/$/g, '')
            // remove last directory from current route
            const parentRoute = strippedSlash.replace(/\/[^\/]*$/g, '')
            return parentRoute == this.item.url
        },
        isAncestor() {
            return (
                (!this.isActive &&
                    this.$nuxt.$route.path.includes(this.item.url)) ||
                this.isParent ||
                this.item.url == '/'
            )
        },
        hasSubMenu() {
            // return Object.keys(this.item.children).length > 0
            // return this.item.childItems.edges.length > 0

            let kids = _get(this.item.childItems, 'edges', false)
            return kids.length ? true : false
        },
        isFrontPage() {
            // return this.item.url == '/'
            return this.relativePath == '/'
        },
        classes() {
            return [
                'menu-item',
                `menu-item-${this.item.menuItemId || 'none'}`,
                { 'menu-item-has-children': this.hasSubMenu }
            ]
        },
        itemClasses() {
            return [
                { 'current-menu-ancestor': this.isAncestor },
                { 'menu-item-is-front-page': this.isFrontPage }
            ]
        }
    },
    methods: {
        menuInteraction() {
            this.$emit('menu-interaction')
        }
    }
}
</script>
