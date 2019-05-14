<template>
  <div :class="classes">
    <div class="sizer" :style="sizerStyles">
      <img
        v-if="parsedSrc"
        class="media image"
        :src="parsedSrc"
        :style="mediaStyles"
        @load="setLoaded('image');"
      />
      <video
        v-if="parsedVideoUrl"
        class="media video"
        :src="parsedVideoUrl"
        :style="mediaStyles"
        :poster="parsedSrc"
        loop
        autoplay
        muted
        @canplay="setLoaded('video');"
      />
    </div>
    <slot />
  </div>
</template>

<script>
import Vue from "vue";
import _get from "lodash/get";

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
    },
    videoUrl: {
      type: String,
      default: ""
    }
  },
  data() {
    return {
      loadedStatus: {}
    };
  },
  computed: {
    classes() {
      return [
        "responsive-image",
        `mode-${this.mode}`,
        { "has-loaded": this.hasLoaded },
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
    parsedColor() {
      return this.color || _get(this, "image.acfImageMeta.primaryColor", "");
    },
    parsedVideoUrl() {
      return this.videoUrl || _get(this, "image.acfImageMeta.videoUrl", "");
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
      if (this.parsedColor) {
        styles = {
          backgroundColor: `${this.parsedColor}`
        };
      }

      return styles;
    },
    mediaStyles() {
      return {
        objectFit: this.objectFit
      };
    },
    hasLoaded() {
      // Handle if we have a video and an image
      return Object.values(this.loadedStatus).every(Boolean);
    }
  },
  watch: {
    // Update loaded state if new src set
    parsedVideoUrl(newVal, oldVal) {
      if (newVal) {
        Vue.set(this.loadedStatus, "video", false);
      }
    },
    // Update loaded state if new src set
    parsedSrc(newVal, oldVal) {
      if (newVal) {
        Vue.set(this.loadedStatus, "image", false);
      }
    }
  },
  mounted() {
    // Setup loaded state tracking
    if (this.parsedVideoUrl) {
      Vue.set(this.loadedStatus, "video", false);
    }
    if (this.parsedSrc) {
      Vue.set(this.loadedStatus, "image", false);
    }
  },
  methods: {
    setLoaded(type) {
      Vue.set(this.loadedStatus, type, true);
    }
  }
};
</script>

<style lang="scss">
.responsive-image {
  position: relative;

  .sizer {
    position: relative;
  }
  .media {
    width: 100%;
    height: 100%;
    position: absolute;
    top: 0;
    left: 0;
    opacity: 0;
    transition: opacity 0.4s ease-in-out;
    z-index: 10;
  }
  .video {
    z-index: 20;
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
  &.has-loaded .media {
    opacity: 1;
  }
}
</style>
