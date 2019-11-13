<template>
    <div class="progress-bar">
        <div
            v-for="(slide, i) in slides"
            :key="i"
            :class="barClasses(i)"
            @click="changeSlide(i)"
        >
            <div class="background">
                <div
                    class="progress"
                    :style="progressStyles(i)"
                />
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        slides: {
            type: Array,
            default: () => []
        },
        activeIndex: {
            type: Number,
            default: 0
        },
        duration: {
            type: Number,
            default: 5000
        },
        paused: {
            type: Boolean,
            default: false
        }
    },
    methods: {
        changeSlide(i) {
            this.$emit("goto", i)
        },
        barClasses(i) {
            return [
                "bar",
                { "is-active": this.activeIndex == i },
                { "has-completed": i < this.activeIndex }
            ]
        },
        progressStyles(i) {
            if (this.activeIndex == i && !this.paused) {
                return [
                    {
                        transition: `opacity 0.4s linear, transform ${this.duration}ms linear`
                    }
                ]
            }
        }
    }
}
</script>

<style lang="scss">
.progress-bar {
    width: 100%;

    display: flex;
    flex-direction: row;
    flex-wrap: nowrap;
    justify-content: center;
    align-content: center;
    align-items: center;

    .bar {
        flex: 1 1 auto;
        padding: 10px 0;
        cursor: pointer;
        margin: 0 10px;
        position: relative;
        overflow: hidden;
    }
    .background {
        background-color: rgba(#222222, 0.4);
    }
    .progress {
        width: 100%;
        height: 2px;
        background-color: rgba(#222222, 1);
        transform: translateX(-100%);
        opacity: 0;
        transition: opacity 0.4s;
    }

    // Active class
    .is-active {
        .progress {
            transform: translateX(0%);
            opacity: 1;
        }
    }
}
</style>
