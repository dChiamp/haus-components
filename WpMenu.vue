<template>
  <ul :class="classes">
    <slot name="before" />

    <wp-menu-item
      v-for="(item, i) in menuItems"
      :key="i"
      class="menu-item"
      :item="item"
      :prepend-paths="prependPaths"
      @menu-interacted="menuInteracted();"
    />

    <slot name="after" />
  </ul>
</template>

<script>
import _kebabCase from "lodash/kebabCase";
import _get from "lodash/get";

export default {
  props: {
    locationName: {
      type: String,
      default: ""
    },
    items: {
      type: Array,
      default: () => []
    },
    prependPaths: {
      type: String,
      default: ""
    }
  },
  computed: {
    classes() {
      return ["wp-menu", `location-${_kebabCase(this.locationName)}`];
    },
    menuItems() {
      let items = this.items;

      // Use menu from store if name provided
      if (this.locationName) {
        items = _get(
          this,
          `$store.state.menus.locations.${this.locationName}`,
          []
        );
      }

      return items;
    }
  },
  methods: {
    menuInteracted(event) {
      this.$emit("menu-interacted", event);
    }
  }
};
</script>
