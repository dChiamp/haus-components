<template>
    <div
        v-in-view
        :class="classes"
        @in-view="inView"
    >
        <div
            ref="panel"
            class="panel"
        >
            <slot name="before" />

            <div
                ref="itemsWrapper"
                class="items-wrapper"
            >
                <div
                    ref="items"
                    class="items"
                    :style="itemsStyles"
                >
                    <slot />
                </div>
            </div>

            <slot name="after" />
        </div>

        <div
            class="sizer"
            :style="sizerStyles"
        />
    </div>
</template>

<script>
import _clamp from "lodash/clamp"

export default {
    props: {
        images: {
            type: Array,
            default: () => []
        }
    },
    data() {
        return {
            isInView: false,
            translateVal: 0,
            itemsWidth: null,
            panelHeight: null,
            itemsOffsetY: null,
            throttledScroll: null
        }
    },
    computed: {
        classes() {
            return ["carousel-scrolljack", { "in-view": this.isInView }]
        },
        itemsStyles() {
            return {
                transform: `translateX(-${this.translateVal}px)`
            }
        },
        sizerStyles() {
            let styles = {
                height: `${this.itemsWidth - this.$store.state.winWidth}px`
            }
            // Add padding to sizer when in view, to account for the items becoming fixed position.
            if (this.isInView) {
                styles.paddingTop = `${this.panelHeight}px`
            }
            return styles
        }
    },
    watch: {
        "$store.state.winWidth"(newVal, oldVal) {
            this.setHeight()
        },
        "$store.state.winHeight"(newVal, oldVal) {
            this.setHeight()
        }
    },
    mounted() {
        this.setHeight()
        this.initEvents()

        // Trigger scroll on mounted, just to run all the logic
        window.scroll()
    },
    destroyed() {
        this.destroyEvents()
    },
    methods: {
        initEvents() {
            window.addEventListener("scroll", this.onScroll)
        },
        destroyEvents() {
            window.removeEventListener("scroll", this.onScroll)
        },
        inView(isInView = true) {
            this.isInView = isInView
        },
        onScroll(e, i) {
            // Abort if not in view
            if (!this.isInView) {
                return
            }
            // Scroll items sideways
            this.translateVal = _clamp(
                this.$store.state.sTop - this.itemsOffsetY,
                0,
                this.itemsWidth
            )
            // If going past start, allow scrolling up again
            if (this.translateVal <= 0) {
                this.isInView = false
            }
            // If at end, allow scrolling down again
            if (this.translateVal >= this.itemsWidth) {
                this.isInView = false
            }
        },
        setHeight() {
            this.itemsWidth = this.$refs.items.clientWidth
            this.panelHeight = this.$refs.panel.clientHeight
            // Save the sTop of the itemsWrapper on resize, so we are efficient with calling getBoundingClientRect
            // I did it this way because `this.$store.state.sTop` is throttled and would be slightly different always
            const rect = this.$refs.itemsWrapper.getBoundingClientRect()
            this.itemsOffsetY =
                window.pageYOffset +
                rect.top -
                (this.$store.state.winHeight - rect.height)
        }
    }
}
</script>

<style lang="scss">
.carousel-scrolljack {
    position: relative;

    .items-wrapper {
        overflow: hidden;
    }
    .items {
        white-space: nowrap;
        width: fit-content;
        min-width: 100%;
    }
    // In view states
    &.in-view .panel {
        position: fixed;
        bottom: 0;
        left: 0;
        width: 100%;
    }
}
</style>
