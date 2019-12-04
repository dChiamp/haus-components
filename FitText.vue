<template lang="html">
    <div
        :id="`container${id}`"
        ref="container"
    >
        <component
            is="span"
            :id="`element${id}`"
            ref="element"
            class="fit-text-element"
            v-html="textWithBreaks"
        />
    </div>
</template>

<script>
import { createUUID } from "~/utils/tools"

export default {
    props: {
        text: {
            type: String,
            default: ""
        },
        // Ratio of the font size
        lineHeight: {
            type: Number,
            default: 1
        },
        elementType: {
            type: String,
            default: "span"
        }
    },
    data() {
        return {
            id: null,
            el: null,
            container: null
        }
    },
    computed: {
        textWithBreaks() {
            return this.text.split(" ").join("<br>")
        }
    },
    updated() {
        this.el = document.getElementById(`element${this.id}`)
        this.container = document.getElementById(`container${this.id}`)
        this.makeTextFit()
        this.$watch("$store.state.winWidth", this.makeTextFit)
    },
    mounted() {
        this.id = this.createUUID()
    },
    methods: {
        growText() {
            let newFontSize =
                parseInt(this.el.style.fontSize.slice(0, -2)) + 1 + "px"
            this.el.style.fontSize = newFontSize
        },
        shrinkText() {
            let newFontSize =
                parseInt(this.el.style.fontSize.slice(0, -2)) - 1 + "px"
            this.el.style.fontSize = newFontSize
        },
        makeTextFit() {
            this.el.style.fontSize = "10px"
            this.el.style.transition = "opacity 0.3s ease-in-out"

            // Iterator variable conditional prevents endless loops in case of missing element content
            let i = 0

            while (
                this.container.offsetWidth < this.el.offsetWidth &&
                i < 1000
            ) {
                this.shrinkText()
                i++
            }
            while (
                this.container.offsetWidth > this.el.offsetWidth &&
                i < 1000
            ) {
                this.growText()
                i++
            }
            this.el.style.opacity = 1
            this.el.style.lineHeight =
                (parseInt(this.el.style.fontSize.slice(0, -2)) + 1) *
                    this.lineHeight +
                "px"

            // Purely for debugging purposes. Detects runaway while loop.
            if (i >= 1000) {
                console.log(
                    "Check to ensure fitText element content exists",
                    this.el
                )
            }
        },
        createUUID() {
            var dt = new Date().getTime()
            var uuid = "xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx".replace(
                /[xy]/g,
                function(c) {
                    var r = (dt + Math.random() * 16) % 16 | 0
                    dt = Math.floor(dt / 16)
                    return (c == "x" ? r : (r & 0x3) | 0x8).toString(16)
                }
            )
            return uuid
        }
    }
}
</script>

<style lang="scss">
// Element opacity should initially be 0 to prevent snappy rerenders.
.fit-text-element {
    display: inline-block;
    margin: 0;
    opacity: 0;
    word-spacing: 100%;
}
</style>
