<template>
  <div class="video-stage">
    <iframe
      class="iframe"
      :src="iFrameSrc"
      width="1280"
      height="720"
      frameborder="0"
      allow="autoplay; fullscreen"
      allowfullscreen
    />
  </div>
</template>

<script>
export default {
  props: {
    src: {
      type: String,
      default: ""
    }
  },
  computed: {
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

      return url;
    }
  },
  methods: {
    // async fetchOembed() {
    //     let html = ""
    //
    //     switch (true) {
    //         case this.isVimeo:
    //             this.loadingEmbed = true
    //             const base = "https://vimeo.com/api/oembed.json"
    //             const encoded = encodeURIComponent(this.src)
    //             const response = await fetch(`${base}?url=${encoded}`).then(
    //                 r => r.json()
    //             )
    //             this.loadingEmbed = false
    //             html = response && response.html ? response.html : ""
    //             break
    //     }
    //     return html
    // }
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
    object-fit: contain;
  }
}
</style>
