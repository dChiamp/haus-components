<template>
    <div
        ref="stage"
        :class="classes"
    >
        <div
            v-if="$slots.top"
            ref="top"
            class="slot slot-top"
        >
            <slot name="top" />
        </div>

        <iframe
            ref="iframe"
            class="iframe"
            :src="iFrameSrc"
            :width="width"
            :height="height"
            frameborder="0"
            allow="autoplay; fullscreen"
            allowfullscreen
            :playsinline="false"
            :title="title"
            :style="iframeStyles"
            loading="lazy"
        />

        <div
            v-if="$slots.default"
            ref="bottom"
            class="slot slot-bottom"
        >
            <slot />
        </div>
    </div>
</template>

<script>
import Vue from "vue"
import Player from "@vimeo/player"
import { contain } from "intrinsic-scale"
import _compact from "lodash/compact"

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
        }
    },
    computed: {
        classes() {
            return [
                "video-stage",
                { "has-loaded": this.hasLoaded },
                `mode-${this.mode}`
            ]
        },
        iframeStyles() {
            if (this.styleHeight || this.styleWidth) {
                return {
                    height: `${this.styleHeight}px`,
                    width: `${this.styleWidth}px`
                }
            }
        },
        isIframe() {
            return String(this.src).includes("<iframe")
        },
        isVimeo() {
            return String(this.src).includes("vimeo.com")
        },
        iFrameSrc() {
            let url = this.src

            if (this.isVimeo) {
                url = url.replace(
                    "https://vimeo.com/",
                    "https://player.vimeo.com/video/"
                )
            }

            // Build out base props
            return `${url}?byline=0&portrait=0&autoplay=${this.autoplay}&playsinline=${this.playsinline}&color=${this.color}`
        }
    },
    mounted() {
        this.initVimeoPlayer()
        window.addEventListener("resize", this.sizeVideo)
    },
    beforeDestroy() {
        this.destroyVimeoPlayer()
        window.addEventListener("resize", this.sizeVideo)
    },
    methods: {
        onPlay() {
            this.$emit("play", ...arguments)
        },
        onPause() {
            this.$emit("pause", ...arguments)
        },
        onEnded() {
            this.$emit("ended", ...arguments)
        },
        async initVimeoPlayer() {
            // init player
            this.player = new Player(this.$refs.iframe, {
                muted: this.muted,
                playsinline: this.playsinline
            })

            // Get and set player size and title
            const meta = await Promise.all([
                this.player.getVideoWidth(),
                this.player.getVideoHeight(),
                this.player.getVideoTitle()
            ])
            this.width = meta[0]
            this.height = meta[1]
            this.title = meta[2]
            this.sizeVideo()

            // Size, set events and loaded state
            await this.player.ready()
            this.sizeVideo()

            // Set events
            this.player.on("play", this.onPlay)
            this.player.on("pause", this.onPause)
            this.player.on("ended", this.onEnded)

            this.hasLoaded = true
            this.$emit("has-loaded")
        },
        destroyVimeoPlayer() {
            // Remove events
            if (this.player) {
                this.player.off("play", this.onPlay)
                this.player.off("pause", this.onPause)
                this.player.off("ended", this.onEnded)
            }
        },
        async sizeVideo() {
            await this.$nextTick()

            // Abort if mode not "fit-to-parent"
            if (this.mode !== "fit-to-parent") {
                return false
            }

            let $stage = this.$refs.stage
            let heightOffset = 0

            // Abort if no stage
            if (!$stage) {
                return false
            }

            // Measure each slot height
            let slots = _compact([
                this.$refs.top || false,
                this.$refs.bottom || false
            ])

            heightOffset = slots.reduce((acc, slot) => {
                // Now we are in each slot
                const rect = slot.getBoundingClientRect()
                const height = rect.height || 0
                return acc + height
            }, 0)

            // Figure out "contain" height and set it on video
            let { width, height, x, y } = contain(
                $stage.offsetWidth,
                $stage.offsetHeight - heightOffset,
                this.width,
                this.height
            )

            // Set style heights
            this.styleHeight = height
            this.styleWidth = width

            // Emit dimensions event
            this.$emit("resized", {
                width: width,
                height: height
            })
        }
    }
}
</script>

<style lang="scss">
.video-stage {
    position: relative;

    .iframe {
        opacity: 0;
        display: block;
        transition: opacity 0.4s ease-in-out;
    }
    .slot {
        overflow: hidden;
    }

    // Loaded state
    &.has-loaded .iframe {
        opacity: 1;
    }

    // Sizing modes
    &.mode-css .iframe {
        position: absolute;
        top: 0;
        left: 0;
        height: 100%;
        width: 100%;
    }
}
</style>
