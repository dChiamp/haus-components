_NOTE: THIS IS CURRENTLY A BIG WORK IN PROGRESS_

Reusable components and directives for Vue. Designed for [Stackhaus](https://github.com/funkhaus/stackhaus) - a Graph QL powered frontend tech stack built on Nuxt, using Apollo.

`haus-components` saves you the trouble of rewriting common components like images, video players, svgs, and several other elements. If you're looking at writing a custom component, check if one already exists here first - and if it doesn't, feel free to [contribute](#contributing)!

# Table of Contents

1.  [Installation](#installation)
1.  [Components](#components)

    1.  [smart-link](#smart-link)
    1.  [responsive-image](#responsive-image)
    1.  [wp-content](#wp-content)
    1.  [split-text](#wp-content)
    1.  [wp-menu](#wp-menu)

## Video Stage

If use slots with video stage, you'll probably want to set it's mode prop to be `fit-to-parent`. If you do this, be aware of the markup that video-stage wraps around the slot. It's uses this markup to figure out the height of the elements in the slot. Often times using `position: absolute` will mean the slot has `height: 0`, so be aware that the iFrame will be sized bigger than you want if you do that. Same with adding a margin to things in the slots, it will work better with paddings.

## Carousel Scrolljack

Has a `before`, `default` and `after` slot.

`Default` slot is normally going to be a long list of images or blocks of some sort.

Be aware anything that you put into the `default` slot cannot be higher than 100vh, otherwise it will never count as "in-view". The current version of this is made to work with elements no taller than 80vh generally and will be positioned against the bottom of the window.

It's possible to tweak this code to make it work when at the top or middle, but Drew will update this when that is actually needed.

## IntersectionObserver and InView directives

Both these directives operate similarly. You should use `v-intersection-observer` if you don't need precision, like for a grid that animates in each block. You should only use `v-in-view` for things that need to be very precise, like something that is supposed to pin to a specific part of the screen on scroll.

Both directives have a `once` modifier, so `v-in-view.once` or `v-intersection-observer.once` will only run once, which is good for animating in something on scroll. Both auto add classes when in and out of view.

### v-in-view
`v-in-view` emits `in-view` and `out-view` events, and toggles `in-view` as a class.

Takes an `offset` and `throttle` setting as an object-literals, like `v-in-view="{offset: 0, throttle: 30}"`. `offset` is can be used to make the "in-view" events happen early or late. `throttle` is the amount of milliseconds to throttle the in-view detection. A lower number will mean higher precision, but less performance.

### v-intersection-observer
`v-intersection-observer` emits a `has-intersected` event, that contains a [IntersectionObserverEntry](https://developer.mozilla.org/en-US/docs/Web/API/IntersectionObserverEntry) object as payload that contains a lot of useful data.

Often you'll want to check that event payload for `event.isIntersecting` to see if the element has come into view, or has left view. `event.intersectionRatio` and `event.boundingClientRect` are useful to see how much the element has come into view.

Takes an object for setting as an object-literal, like `v-intersection-observer="rootMargin: '0px', threshold: 1.0}"`. See [this for an explanation on those settings](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API).

[It is important to read this](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) to understand the options for intersection observers, the way `rootMargin` works is important to understand (often times you'll want this to be `-100px -100px --100px -100px` for example, or to change threshold). You can also set `root` if the default of document root isn't what you want.

## TODO list

TODO improvements:

1.  Add Storybook UI to this
1.  Improve documentation
1.  Make this an NPM module?
1.  The slideshow-list component on MPC is the base of a really good background slideshow. Add that to this. Need to make it more generic, with slots probably.
1.  Get ESLINT working on this repo. Spacing is all wrong.
1.  Get v-in-view working if it loads in browser view.
1.  Allow a stagger setting on v-in-view
1.  Add toggle-height from MPC. But perhaps add `height:auto` after CSS animation complete on open?
