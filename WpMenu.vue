<template>
  <ul :class="classes">
    <li v-for="(item, i) in menuItems" :key="i" class="menu-item">
      <nuxt-link
        :to="makeUrlRelative(item.url)"
        @click.native="menuInteraction"
        v-html="item.label"
      />

      <ul v-if="item.childItems.nodes.length" class="sub-menu">
        <li
          v-for="(subItem, i) in item.childItems.nodes"
          :key="`sub-${i}`"
          class="sub-item menu-item"
        >
          <nuxt-link
            :to="makeUrlRelative(subItem.url)"
            @click.native="menuInteraction"
            v-html="subItem.label"
          />
        </li>
      </ul>
    </li>
  </ul>
</template>

<script>
import _kebabCase from "lodash/kebabCase";
import _get from "lodash/get";

export default {
  props: {
    locationName: {
      type: String,
      default: "",
      required: true
    }
  },
  computed: {
    classes() {
      return ["wp-menu", `location-${_kebabCase(this.locationName)}`];
    },
    menuItems() {
      return _get(
        this,
        `$store.state.menus.locations.${this.locationName}`,
        []
      );
    }
  },
  methods: {
    makeUrlRelative(url) {
      return url.replace(this.$store.state.apiUrl, "");
    },
    menuInteraction() {
      this.$emit("interacted");
    }
  }
};
</script>
