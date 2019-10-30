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

If use slots with video stage, you'll probably want to set it's mode prop to be `fit-to-parent`. If you do this, be aware of the markup that video-stage wraps around the slot. It's uses this markup to figure out the height of the elements in the slot. Often times using `position: absolute` will mean the slot has `height: 0`, so be aware that the iFrame will be sized bigger than you want if you do that.

## Carousel Scrolljack

Has a `before`, `default` and `after` slot.

`Default` slot is normally going to be a long list of images or blocks of some sort.

Be aware anything that you put into the `default` slot cannot be higher than 100vh, otherwise it will never count as "in-view". The current version of this is made to work with elements no taller than 80vh generally and will be positioned against the bottom of the window.

It's possible to tweak this code to make it work when at the top or middle, but Drew will update this when that is actually needed.

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
