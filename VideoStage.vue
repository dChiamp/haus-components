<template>
  <div class="video-stage">{{ fetchOembed() }}</div>
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
    }
  },
  methods: {
    async fetchOembed() {
      let html = "";

      switch (true) {
        case this.isVimeo:
          this.loadingEmbed = true;
          const base = "https://vimeo.com/api/oembed.json";
          const encoded = encodeURIComponent(this.src);
          const response = await fetch(`${base}?url=${encoded}`).then(r =>
            r.json()
          );
          this.loadingEmbed = false;
          html = response && response.html ? response.html : "";
          break;
      }
      return html;
    }
  }
};
</script>

<style lang="scss">
.video-stage {
}
</style>
