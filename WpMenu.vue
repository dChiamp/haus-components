<template>
  <ul :class="classes">
    <wp-menu-item
      v-for="(item, i) in menuItems"
      :key="i"
      class="menu-item"
      :item="item"
      :path-splice="pathSplice"
    />
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
    pathSplice: {
      type: Object,
      default() {
        return { from: 0, to: 99 };
      }
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
    getUrl(url) {
      let relativeUrl = this.makeUrlRelative(url);
      let chunks = relativeUrl.split("/");

      return chunks.splice(0, this.urlDepth).join("/");
    },
    makeUrlRelative(url) {
      return url.replace(this.$store.state.apiUrl, "");
    },
    menuInteraction() {
      this.$emit("interacted");
    }
  }
};
</script>
