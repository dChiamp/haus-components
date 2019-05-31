<template>
  <div ref="stage" :class="classes">
    <slot name="top" />

    <iframe
      ref="iframe"
      class="iframe"
      :src="iFrameSrc"
      :width="width"
      :height="height"
      frameborder="0"
      allow="autoplay; fullscreen"
      allowfullscreen
      :playsinline="playsinline"
      :title="title"
      :style="iframeStyles"
    />

    <slot name="bottom" />
  </div>
</template>

<script>
import Vue from "vue";
import Player from "@vimeo/player";
import { cover, contain } from "intrinsic-scale";

export default {
  props: {
    src: {
      type: String,
      default: ""
    },
    playsinline: {
      type: Boolean,
      default: true
    },
    autoplay: {
      type: Boolean,
      default: true
    },
    color: {
      type: String,
      default: "ffffff"
    },
    muted: {
      type: Boolean,
      default: false
    },
    mode: {
      type: String,
      default: "css"
    }
  },
  data() {
    return {
      player: null,
      hasLoaded: false,
      height: 720,
      width: 1280,
      styleHeight: "",
      styleWidth: "",
      title: ""
    };
  },
  computed: {
    classes() {
      return [
        "video-stage",
        { "has-loaded": this.hasLoaded },
        `mode-${this.mode}`
      ];
    },
    iframeStyles() {
      return {
        height: `${this.styleHeight}px`,
        width: `${this.styleWidth}px`
      };
    },
    isIframe() {
      return String(this.src).includes("<iframe");
    },
    isVimeo() {
      return String(this.src).includes("vimeo.com");
    },
    iFrameSrc() {
      let url = this.src;

      if (this.isVimeo) {
        url = url.replace(
          "https://vimeo.com/",
          "https://player.vimeo.com/video/"
        );
      }

      // Build out base props
      return `${url}?byline=0&portrait=0&autoplay=${
        this.autoplay
      }&playsinline=${this.playsinline}&color=${this.color}`;
    }
  },
  watch: {
    hasLoaded() {
      // Size video when video is loaded
      Vue.nextTick(this.sizeVideo);
    }
  },
  mounted() {
    this.initVimeoPlayer();
    window.addEventListener("resize", this.sizeVideo);
  },
  beforeDestroy() {
    this.destroyVimeoPlayer();
    window.addEventListener("resize", this.sizeVideo);
  },
  methods: {
    onPlay() {
      this.$emit("play", ...arguments);
    },
    onPause() {
      this.$emit("pause", ...arguments);
    },
    onEnded() {
      this.$emit("ended", ...arguments);
    },
    async initVimeoPlayer() {
      // init player
      this.player = new Player(this.$refs.iframe, {
        muted: this.muted,
        playsinline: this.playsinline
      });

      // Set Attributes
      await this.player.getVideoWidth().then(width => {
        this.width = width;
      });
      await this.player.getVideoHeight().then(height => {
        this.height = height;
      });
      await this.player.getVideoTitle().then(title => {
        this.title = title;
      });

      // Set loaded class when player is ready and sized
      await this.player.ready().then(() => {
        this.hasLoaded = true;

        // Set events
        this.player.on("play", this.onPlay);
        this.player.on("pause", this.onPause);
        this.player.on("ended", this.onEnded);
      });
    },
    destroyVimeoPlayer() {
      // Remove events
      if (this.player) {
        this.player.off("play", this.onPlay);
        this.player.off("pause", this.onPause);
        this.player.off("ended", this.onEnded);
      }
    },
    sizeVideo() {
      // Abort if mode not "fit-to-parent"
      if (this.mode !== "fit-to-parent") {
        return false;
      }

      let $stage = this.$refs.stage;
      let heightOffset = 0;

      // Return if no stage
      if (!$stage) {
        return false;
      }

      // Total height of all slot elements (only if auto is true)
      heightOffset = Object.keys(this.$slots).reduce((acc, key) => {
        const slotItems = this.$slots[key] || [];
        const slotHeight = slotItems.reduce((acc, item) => {
          const rect = item.elm.getBoundingClientRect();
          const height = rect.height || 0;
          return acc + height;
        }, 0);
        return slotHeight;
      }, 0);

      // Figure out "contain" height and set it on video
      let { width, height, x, y } = contain(
        $stage.offsetWidth,
        $stage.offsetHeight + heightOffset,
        this.width,
        this.height
      );

      // Set style heights
      this.styleHeight = height;
      this.styleWidth = width;
    }
  }
};
</script>

<style lang="scss">
.video-stage {
  position: relative;

  .iframe {
    opacity: 0;
    transition: opacity 0.4s ease-in-out;
  }

  // Loaded state
  &.has-loaded .iframe {
    opacity: 1;
  }

  // Sizing modes
  &.mode-css {
    position: absolute;
    top: 0;
    left: 0;
    height: 100%;
    width: 100%;
  }
}
</style>
