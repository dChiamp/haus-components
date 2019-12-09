<script>
import _throttle from "lodash/throttle"

// Define default settings
let settings = {
    value: {
        offset: 0,
        once: true,
        throttle: 30
    }
}

// This function runs on scroll and resize, it checks if an element is in view
const checkViewport = (el, offset, fireOnce) => {
    const hasClass = el.classList.contains("in-view")

    // If fire once and el already has class we have nothing to do
    if (fireOnce && hasClass) return

    const boundingRect = el.getBoundingClientRect()

    // In view
    if (
        boundingRect.y + offset < window.innerHeight &&
        boundingRect.y + boundingRect.height > offset
    ) {
        if (!hasClass) {
            el.classList.add("in-view")
            // Create and dispatch event
            const event = new Event("in-view")
            el.dispatchEvent(event)
        }
    }

    //Out of view
    else if (hasClass) {
        // Create and dispatch event
        el.classList.remove("in-view")
        const event = new Event("out-view")
        el.dispatchEvent(event)
    }
}

// The Vue directive config
export default {
    bind(el, binding) {
        // Save new settings to settings var for convenience
        Object.assign(settings, binding)

        el.checkViewport = _throttle(
            () => checkViewport(el, settings.value.offset, settings.value.once),
            settings.value.throttle
        )

        window.addEventListener("scroll", el.checkViewport)
        window.addEventListener("resize", el.checkViewport)
    },
    unbind(el) {
        window.removeEventListener("scroll", el.checkViewport)
        window.removeEventListener("resize", el.checkViewport)
    },
    inserted(el) {
        checkViewport(el, settings.value.offset, settings.value.once)
    }
}
</script>
