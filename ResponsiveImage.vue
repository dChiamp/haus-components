<template>
  <div :class="classes">
    <div class="sizer" :style="sizerStyles">
      <img
        v-if="parsedSrc"
        class="image"
        :src="parsedSrc"
        :style="imgStyles"
        @load="setLoaded();"
      />
    </div>
    <slot />
  </div>
</template>

<script>
export default {
  props: {
    image: {
      type: Object,
      default: () => {}
    },
    height: {
      type: Number,
      default: null
    },
    width: {
      type: Number,
      default: null
    },
    src: {
      type: String,
      default: ""
    },
    aspectRatio: {
      type: Number,
      default: null
    },
    objectFit: {
      type: String,
      default: "cover"
    },
    mode: {
      type: String,
      default: "intrinsic-ratio"
    },
    backgroundColor: {
      type: String,
      default: ""
    }
  },
  data() {
    return {
      imageLoaded: false
    };
  },
  computed: {
    classes() {
      return [
        "responsive-image",
        `mode-${this.mode}`,
        { "has-loaded": this.imageLoaded },
        { "has-background-color": this.backgroundColor }
      ];
    },
    aspectPadding() {
      return this.aspectRatio || (this.parsedHeight / this.parsedWidth) * 100;
    },
    parsedHeight() {
      // default to defined height
      if (this.height) {
        return parseInt(this.height);
      }
      return this.image.mediaDetails.height;
    },
    parsedWidth() {
      // default to defined width
      if (this.width) {
        return parseInt(this.width);
      }
      return this.image.mediaDetails.width;
    },
    parsedSrc() {
      return this.src || this.image.sourceUrl;
    },
    sizerStyles() {
      let styles = {};

      // Set padding for size
      if (this.mode == "intrinsic-ratio") {
        styles = {
          paddingBottom: `${this.aspectPadding}%`
        };
      }

      // Set background color
      if (this.backgroundColor) {
        styles = {
          backgroundColor: `${this.backgroundColor}`
        };
      }

      return styles;
    },
    imgStyles() {
      return {
        objectFit: this.objectFit
      };
    }
  },
  methods: {
    setLoaded() {
      this.imageLoaded = true;
    }
  }
};
</script>

<style lang="scss">
.responsive-image {
  position: relative;

  .sizer {
    position: relative;

    .image {
      width: 100%;
      height: 100%;
      position: absolute;
      top: 0;
      left: 0;
      opacity: 0;
      transition: opacity 0.4s ease-in-out;
    }
  }

  // Modes
  &.mode-fullbleed {
    .sizer {
      width: 100%;
      height: 100%;
      top: 0;
      left: 0;
      position: absolute;
    }
  }

  // Loaded state
  &.has-loaded .image {
    opacity: 1;
  }
}
</style>
