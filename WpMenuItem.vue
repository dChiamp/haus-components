<template>
  <li :class="classes">
    <a
      v-if="!isRelative"
      target="_blank"
      :href="item.url"
      @click.native="menuInteraction();"
      v-html="item.label"
    />

    <nuxt-link
      v-if="isRelative"
      :to="getPath"
      @click.native="menuInteraction();"
      v-html="item.label"
    />

    <wp-menu v-if="hasSubMenu" class="sub-menu" :items="getChildren" />
  </li>
</template>

<script>
import _kebabCase from "lodash/kebabCase";
import _get from "lodash/get";

export default {
  props: {
    item: {
      type: Object,
      default: () => {}
    },
    pathSpliceFrom: {
      type: [Number, Boolean],
      default: false
    },
    pathSpliceTo: {
      type: [Number, Boolean],
      default: false
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
      ];
    },
    getChildren() {
      return _get(this, "item.childItems.nodes", []);
    },
    hasSubMenu() {
      return this.getChildren.length;
    },
    isRelative() {
      return this.item.target !== "_blank";
    },
    relativeUrl() {
      return this.item.url.replace(this.$store.state.apiUrl, "");
    },
    getPath() {
      let url = this.relativeUrl;

      if (this.pathSpliceFrom && this.pathSpliceTo) {
        // Only use if isRealtive is true in template
        let chunks = url.split("/");

        // Remove empty strings
        chunks = chunks.filter(Boolean);
        url = chunks.splice(this.pathSpliceFrom, this.pathSpliceTo).join("/");
      }

      return url;
    }
  },
  methods: {
    menuInteraction() {
      this.$emit("interacted");
    }
  }
};
</script>
