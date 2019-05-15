<template>

    <component
        :is="element"
        v-text="displayNumber"
    />

</template>

<script>

import { tween } from 'popmotion'

export default {
    props: {
        from: {
            type: Number,
            default: 0
        },
        to: {
            type: Number,
            required: true
        },
        //duration is in milliseconds
        duration: {
            type: Number,
            required: false,
            default: 1000
        },
        element: {
            type: String,
            default: 'div'
        },
        startCount: {
            type: Boolean,
            required: true
        }
    },
    data() {
        return {
            displayNumber: this.from
        }
    },
    watch: {
        startCount(newVal) {
            if (newVal) {
                tween({
                    to: this.to,
                    from: this.from,
                    duration: this.duration
                }).pipe(parseInt).start(val => this.displayNumber = val)
            }
        }
    }
}
</script>
