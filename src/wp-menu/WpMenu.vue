<template>
    <ul :class="['menu', targetSlug]">
        <wp-menu-item
            v-for="(item, i) in menuItems"
            :key="i"
            :item="item"
            :forceRouterLink="forceRouterLink"
        >
            <slot name="menu-item" :menu-item="item" :index="i" />
        </wp-menu-item>
    </ul>
</template>

<script>
import _kebabCase from 'lodash/kebabCase'
import _camelCase from 'lodash/camelCase'
import _get from 'lodash/get'
import _find from 'lodash/find'

export default {
    props: {
        slug: {
            type: String,
            default: ''
        },
        // TODO: rename menus not as camel case
        locationName: {
            type: String,
            default: 'Main Menu'
        },
        forceRouterLink: {
            type: Boolean,
            default: false
        }
    },
    components: {
        // TODO: load components better
        'menu-item': require('./WpMenuItem')
    },
    computed: {
        targetSlug() {
            return this.slug || _kebabCase(this.locationName)
        },
        formattedName() {
            return _camelCase(this.locationName)
        },
        menuItems() {
            const menus = _get(this.$store.state, 'menus', [])
            // console.log('menus', menus)

            /*/
            // find first menu that matches the given slug
            let menu = menus.find(singleMenu => {
                return singleMenu.slug == this.tar
                getSlug
            })
            /*/
            // TODO: match menu by slug or name? (cant get slug from wpGraphql)
            let menu = menus[this.formattedName]

            // fall back to first menu
            if (menu === undefined) {
                menu = _get(menus, '[0]')
            }

            // return menu ? menu.items : false
            return menu
        }
    }
}
</script>

<style lang="scss">
.menu {
    list-style-type: none;
    padding: 0;
    margin: 0;
    padding: 10px;

    .menu-item {
        // display: inline;
        // text-align: right;
        list-style-type: none;
        text-transform: uppercase;
        font-size: 16px;

        .children {
            // display: inline;
            // text-align: right;
            text-transform: uppercase;
            font-size: 16px;
        }
    }
}
</style>
