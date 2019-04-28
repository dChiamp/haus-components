<template>
    <div :class="classes">
        <div
            class="sizer"
            :style="sizerStyles"
        >
            <img
                v-if="parsedSrc"
                class="image"
                :src="parsedSrc"
                :style="imgStyles"
            >
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
        }
    },
    computed: {
        classes() {
            return ["responsive-image", `mode-${this.mode}`]
        },
        aspectPadding() {
            return (
                this.aspectRatio || (this.parsedHeight / this.parsedWidth) * 100
            )
        },
        parsedHeight() {
            // default to defined height
            if (this.height) {
                return parseInt(this.height)
            }
            return this.image.mediaDetails.height
        },
        parsedWidth() {
            // default to defined width
            if (this.width) {
                return parseInt(this.width)
            }
            return this.image.mediaDetails.width
        },
        parsedSrc() {
            return this.src || this.image.sourceUrl
        },
        sizerStyles() {
            let styles = {}
            if (this.mode == "intrinsic-ratio") {
                styles = {
                    paddingBottom: `${this.aspectPadding}%`
                }
            }
            return styles
        },
        imgStyles() {
            return {
                objectFit: this.objectFit
            }
        }
    }
}
</script>

<style lang="scss">
.responsive-image {
    position: relative;

    .sizer {
        .image {
            width: 100%;
            height: 100%;
            position: absolute;
            top: 0;
            left: 0;
        }
    }
}
</style>
