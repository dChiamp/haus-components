<script>
import _throttle from "lodash/throttle"

// Define default settings
let checker
let settings = {
    value: {
        offset: 0,
        throttle: 30
    },
    modifiers: {
        once: false
    }
}

// Setup stuff
const initInView = (binding) => {
    // Save new settings to settings var for convenience
    Object.assign(settings, binding)

    checker = _throttle(
        () => checkViewport(el, settings.value.offset, settings.modifiers.once),
        settings.value.throttle
    )

    window.addEventListener("scroll", checker)
    window.addEventListener("resize", checker)
}

// This function runs on scroll and resize, it checks if an element is in view
const checkViewport = (el, offset, once) => {
    const hasClass = el.classList.contains("in-view")

    // If once and el already has class we have nothing to do
    if (once && hasClass) return

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

// Cleanup events
const destroyInView = () => {
    window.removeEventListener("scroll", checker)
    window.removeEventListener("resize", checker)
}

// The Vue directive config
export default {
    bind(el, binding) {
        initInView(binding)
    },
    unbind(el) {
        destroyInView()
    },
    update(el, binding) {
        // If a setting has changed, restart
        if(binding.oldValue !== binding.value) {
            destroyInView()
            initInView(binding)
        }
    },
    inserted(el) {
        // Check if in view now that element is ready
        checkViewport(el, settings.value.offset, settings.modifiers.once)
    }
}
</script>
