<template>
  <div :class="classes">
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
    />
  </div>
</template>

<script>
import Player from "@vimeo/player";

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
    }
  },
  data() {
    return {
      player: null,
      hasLoaded: false,
      height: 720,
      width: 1280,
      title: ""
    };
  },
  computed: {
    classes() {
      return ["video-stage", { "has-loaded": this.hasLoaded }];
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
  mounted() {
    this.initVimeoPlayer();
  },
  beforeDestroy() {
    this.destroyVimeoPlayer();
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

      // Set loaded class when player is ready
      await this.player.ready().then((this.hasLoaded = true));

      // Set Attributes
      this.player.getVideoWidth().then(width => {
        this.width = width;
      });
      this.player.getVideoHeight().then(height => {
        this.width = height;
      });
      this.player.getVideoTitle().then(title => {
        this.title = title;
      });

      // Set events
      this.player.on("play", this.onPlay);
      this.player.on("pause", this.onPause);
      this.player.on("ended", this.onEnded);
    },
    destroyVimeoPlayer() {
      // Remove events
      this.player.off("play", this.onPlay);
      this.player.off("pause", this.onPause);
      this.player.off("ended", this.onEnded);
    }
  }
};
</script>

<style lang="scss">
.video-stage {
  position: relative;

  .iframe {
    position: absolute;
    top: 0;
    left: 0;
    height: 100%;
    width: 100%;
    opacity: 0;
    transition: opacity 0.4s ease-in-out;
  }

  // Loaded state
  &.has-loaded .iframe {
    opacity: 1;
  }
}
</style>
