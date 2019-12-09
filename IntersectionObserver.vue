<script>
// Define default settings
let observer
let settings = {
    value: {
        root: null,
        rootMargin: '0px',
        threshold: 1.0
    },
    modifiers: {
        once: false
    }
}

// Creates Observer
const initObserver = () => {
    return new IntersectionObserver(
        onIntersection,
        settings.value
    )
}

// This is the function that runs when an Intersection occurs.
// NOTE Intersection happens when it goes out of view too!
// SEE https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API
// SEE https://developer.mozilla.org/en-US/docs/Web/API/IntersectionObserverEntry
const onIntersection = (entries, observer) => {
    entries.forEach(entry => {
        const hasClass = entry.target.classList.contains("has-intersected")

        // If "once" and el already has class we have nothing to do
        if (settings.modifiers.once && hasClass) return

        // Fire event with IntersectionObserverEntry object
        const event = new CustomEvent("has-intersected", { detail: entry })
        entry.target.dispatchEvent(event)

        // Add a class for convenience
        if(entry.isIntersecting) {
            entry.target.classList.add("has-intersected")
        } else {
            entry.target.remove.add("has-intersected")
        }
    })
}

// The Vue directive config
export default {
    bind(el, binding) {
        // Save new settings to settings var for convenience
        Object.assign(settings, binding)

        // Setup
        initObserver()
    },
    unbind(el) {
        observer.disconnect()
    },
    inserted(el) {
        observer.observe(el);
    }
}
</script>
