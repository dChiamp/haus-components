Reusable components and directives for Vue. Designed for [Stackhaus](https://github.com/funkhaus/stackhaus) - a Graph QL powered frontend tech stack built on Nuxt, using Apollo.

`haus-components` saves you the trouble of rewriting common components like images, video players, svgs, and several other elements. If you're looking at writing a custom component in Vuehaus, check if one already exists here first - and if it doesn't, feel free to [contribute](#contributing)!

# Table of Contents

1.  [Installation](#installation)
1.  [Components](#components)
    1.  [a-div](#a-div)
    1.  [hamburger-button](#hamburger-button)
    1.  [responsive-image](#responsive-image)
    1.  [wp-content](#wp-content)
    1.  [wp-menu](#wp-menu)

# Installation

`npm install haus-components --save`

When you register components:

```js
import ComponentName from 'haus-components/component-name'

// Global in main.js...
Vue.component('component-name', ComponentName)

// ...or local to a .vue file...
export default {
    components: {
        'component-name': ComponentName
    }
}

// ...or a global component one-liner (no `import` statement needed)
Vue.component('component-name', require('haus-components/component-name'))
```

# Components

## `a-div`

`<a-div :href="myString">Link text</a-div>`

Safe way to render internal, external, or missing links.

| Standalone | Enhanced by Vuehaus | SSR Capable |
| ---------- | ------------------- | ----------- |
| ✅         | ❌                  | ✅          |

**Props**

| Name           | Type      | Default | Description                                                                                                                         |
| -------------- | --------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `force-new`    | `Boolean` | `false` | Whether the link should always open in a new window.                                                                                |
| `href`         | `String`  | empty   | URL to link to. Handles same-origin URLs with `router-link`, external URLs with `a`, and falsey values with the `replace-with` tag. |
| `new-window`   | `Boolean` | `true`  | If component renders as an `<a>` tag, open in a new window.                                                                         |
| `replace-with` | `String`  | `'div'` | If the href isn't a link, render contents inside this tag instead.                                                                  |

**Classes**

| Name                  | Conditions | Notes                                       |
| --------------------- | ---------- | ------------------------------------------- |
| `anchor-div`          | Always     |                                             |
| `a-${ replace-with }` | Always     | Substitutes with the replace-with tag name. |

**Notes**

-   Renders content in a `<router-to>` tag if `href` starts with `/` or includes the same `location.origin`.
-   Renders content in an `<a>` tag if `href` is a truthy value not starting with `/`.
-   Renders content in a given tag (`replace-with`, default `div`) if `href` is falsey.

## `responsive-image`

`<responsive-image :src="sourceUrl" :object="serializedRestEasyObject"/>`

Creates and fades in an image. Adds a placeholder for the image with a given background-color to prevent content jumping when the image loads.

| Standalone | Enhanced by Vuehaus | SSR Capable |
| ---------- | ------------------- | ----------- |
| ✅         | ✅                  | ❌          |

**Props**

| Name          | Type             | Default         | Description                                                                                                                                                            |
| ------------- | ---------------- | --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `aspect`      | [String, Number] | `''`            | Aspect ratio of desired image, as a percentage. `aspect="56.25"` would evaluate to a 56.25% aspect ratio. Calculated from height and width if not explicitly stated.   |
| `color`       | String           | `'transparent'` | Background placeholder color. Any CSS-compatible color is valid.                                                                                                       |
| `fill-space`  | Boolean          | `false`         | When `true`, position absolutely and force the image to take up all available space; when `false`, use the image's natural aspect ratio.                               |
| `fit`         | String           | `'cover'`       | Object-fit value for the image - `cover` or `contain`.                                                                                                                 |
| `height`      | [String, Number] | `''`            | Natural image height in pixels.                                                                                                                                        |
| `html`        | String           | `''`            | Desired <img> element as an HTML string. Provides all the sizing, fade-in, etc. benefits of a regular responsive-image.                                                |
| `object`      | Object           | `{}`            | Serialized [Rest-Easy attachment](https://github.com/funkhaus/Rest-Easy#serializer-filters). Fills out the rest of these props automatically except `fit`.             |
| `poster`      | String           | `''`            | URL to [poster](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/video) for video. If `false`, no poster used; if blank, defaults to the parsed image source. |
| `respect-max` | Boolean          | `false`         | Whether the image will have a `max-width` and `max-height` based on its natural dimensions.                                                                            |
| `size`        | String           | `'full'`        | WordPress image size.                                                                                                                                                  |
| `src`         | String           | `''`            | Same functionality as `<img src="...">`.                                                                                                                               |
| `video-src`   | String           | `''`            | An optional video URL if the component should display a looping video rather than an image. Set to `null` to disable the video option.                                 |
| `volume`      | Number           | `0`             | Controls the volume when rendering a video. If set to 0 (or not set) the video will be muted and will be able to autoplay.                                             |
| `width`       | [String, Number] | `''`            | Natural image width in pixels.                                                                                                                                         |

**Slots**

| Name    | Location                                      |
| ------- | --------------------------------------------- |
| Default | Content inside of image-padding, after image. |

**Classes**

| Name               | Conditions                            | Notes                                                             |
| ------------------ | ------------------------------------- | ----------------------------------------------------------------- |
| `fill-space`       | if `fill-space` prop is set to `true` |
| `fit-${fit}`       | Always                                | Either `fit-cover` or `fit-contain`, depending on the `fit` prop. |
| `has-video`        | if `videoSrc` is truthy               |                                                                   |
| `loading`          | Image hasn't finished loading         |                                                                   |
| `responsive-image` | Always                                |                                                                   |
| `rsp-image-module` | Always                                |                                                                   |

**Notes**

-   If you add a link to an .mp4 video in the "Alt" field in WordPress, this element will create and render a video, using the provided image as the poster (see "Poster" under "Attributes" [here](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/video)).
-   Also works when `object` is set to an Advanced Custom Fields image array. Only provides `fullscreen` size and image alt in this case.
-   Works with [Focushaus](https://github.com/funkhaus/focushaus), if installed, to set a custom focal point for an image. Defaults to standard browser behavior, focal point in the center of the image.

## wp-menu

[Vuehaus](https://github.com/funkhaus/vuehaus)-specific component to build WordPress menus. Uses the `wp-menu-item` component internally.

### Props

| Name              |  Type   |    Default    | Description                                                                             |
| ----------------- | :-----: | :-----------: | --------------------------------------------------------------------------------------- |
| `slug`            | String  |     `''`      | The WordPress slug of the menu to use.                                                  |
| `name`            | String  | `'Main Menu'` | The WordPress name of the menu to use.                                                  |
| `forceRouterLink` | Boolean |     false     | Whether or not this wp-menu should force the use of `router-link`s instead of `a` tags. |

### Classes

-   `menu`
-   `${ slug }`: Either the `slug` prop or a kebab-cased version of the `name` prop, whichever is available.

### Slots

This component comes with an optional [scoped slot](https://vuejs.org/v2/guide/components.html#Scoped-Slots) that will allow you to override the default markup for a single menu item.

**menu-item:** Template for single menu item within component

This slot accepts 2 arguments, `menuItem`, which carries the serialized object of the single item that is being iterated over, and `index`, which carries the index of the current menu item. Here is an example of how you might use it to add an arrow icon next to the name of each element in the menu:

```vue
<wp-menu name="Main Menu">
    <li slot="menu-item" slot-scope="{ menuItem, index }">
        <router-link :to="menuItem.relativePath">
            <svg-image src="arrow.svg" />
            <span>{{ index }}. </span>
            <span v-html="menuItem.title" />
        </router-link>
    </li>
</wp-menu>
```

## `wp-content`

**Props**

-   `html`: String, default `''`. HTML that can function as a Vue template. The component will dynamically compile the contents, and inject the results into the vue component tree.
-   `unwrap`: Array, default `['p > img', 'p > iframe']`. Array of CSS selectors describing elements to unwrap from their parent nodes. (WordPress automatically wraps newlines in `<p>` tags, so this is a useful feature to remove standalone media from those `<p>` tags.)
-   `fitvids`: Boolean, default `true`. When set to true, the component will run fitvids on all content inside.
-   `replace`: Array, default `[]`. Array of objects containing:

    -   `selector` - String with CSS selector for elements to replace
    -   `callback` - Function describing what to replace old element with. Accepts one argument referring to the old element; should return a String or Node with the element's replacement.

    This feature lets you replace items in `wp-content`. For example:

    ```
    <wp-content :html="contentHtml" :replace="[ { selector: 'img', callback: el => `<p>Image with source ${ el.src }</p>` } ]"
    ```

    This setting would replace all `img` tags with a `p` containing the text 'Image with source [element's source]'. Useful for modifying wp-content HTML before it is rendered on the DOM.

**Classes**

-   `wp-content-placeholder`: only present when no html template is provided
-   `wp-content-rendered`: only present when an html template was provided and successfully rendered

**Notes**

-   This was built to allow components to be used with the the contents of a Wordpress post. It's very effective for that purpose, but more generally can be used to compile any dynamically loaded template and render it into the component tree. Be aware that this should only ever be used when the template going into the `html` prop is trusted.

# Directives

Directives are declared as attributes. Remember to prefix `v-` to the directive name (so `full-height` becomes `v-full-height`)!

To use haus-components directives in your Vue app:

```js
import directiveName from 'haus-components/v-directive-name'
Vue.directive('directive-name', directiveName)
```

To pass arguments to directives:

```
<my-component v-my-directive="{ key: 'value' }"/>
```

Directives may also have "modifiers" to go with them. Modifiers can be used like this:

```
<my-component v-my-directive.example />
```

## `hamburger-button`

```html
<hamburger-button/>

<hamburger-button>
    <svg-image src="custom-hamburger.svg" />
</hamburger-button>
```

Lightweight hamburger SVG with open and closed states. Requires Vuehaus.

| Standalone | Enhanced by Vuehaus | SSR Capable |
| ---------- | ------------------- | ----------- |
| ❌         | ✅                  | ✅          |

**Classes**

| Name        | Conditions                               | Notes |
| ----------- | ---------------------------------------- | ----- |
| `hamburger` | Always                                   |       |
| `activated` | when `$store.state.menuOpened` is `true` |       |

**Notes**

-   Calls Vuehaus's `'OPEN_MENU'` or `'CLOSE_MENU'` when clicked, depending on current `activated` state. Will also update correctly when menu is opened or closed elsewhere.
-   Default slot is a three-line hamburger icon that changes to an "X" when activated. The user can replace this with their own svg using the `svg-image` component.

# Testing

`npm test` to run the tests defined in the `tests` directory.

# Contributing

## Prep

We recommend using a single local install to develop for haus-components:

```sh
cd ~/your-misc-repo-directory
git clone https://github.com/funkhaus/haus-components
```

Then, in your Vuehaus instance:

```sh
npm link haus-components ~/your-misc-repo-directory/haus-components
```

This will create a symlink from Vuehaus's `npm_modules/haus-components` to the master haus-components install.

## New Components

Components each get a directory in the `/src` folder, with the following naming conventions:

```
src
+-- new-component
    +-- NewComponent.vue
```

When you're ready to build your component, run `npm run build` in `haus-components`.

## Documentation

Reusable components are only helpful if they're easy to reuse! Fill in this template to start your documentation, deleting any unnecessary sections.

("SSR Capable" refers to an element being renderable by a server - ie, the element does not rely on `document` or `window`.)

```md
## `component-name`

`<component-name :example-prop="123"> I'm an example ready to be copied and pasted into a Vue component. </component-name>`

Brief description.

| Standalone | Enhanced by Vuehaus | SSR Capable |
| ---------- | ------------------- | ----------- |
| ✅         | ✅                  | ❌          |

**Props**

| Name       | Type   | Default   | Description     |
| ---------- | ------ | --------- | --------------- |
| `propName` | `Type` | `default` | About this prop |

**Slots**

| Name        | Location                     |
| ----------- | ---------------------------- |
| `slot-name` | Slot location and extra info |

**Events**

| Name        | Signature    | Description      |
| ----------- | ------------ | ---------------- |
| `eventName` | `(response)` | About this event |

**Classes**

| Name        | Conditions                  | Notes                        |
| ----------- | --------------------------- | ---------------------------- |
| `className` | When applied, if applicable | Extra notes about this class |

**Notes**

-   Miscellaneous notes and information here. Why add this custom component? When can it be used?
```

## Tests

It's a good idea to include a test for your new component so that others can add onto it more easily.

Tests each get a .js file in the `tests` directory, named the same as the component they test. Here's the template to start writing a new test:

```js
import ComponentName from '../component-name'
import { mount } from '@vue/test-utils'
import tap from 'tap'

// mount component
let wrapper = mount(ComponentName)

tap.test('Component Name', t => {
    // Tests using Tap and wrapper here
    // ...

    t.end()
})
```

Note the following caveats:

1.  You'll need to require the component from its built .js file, not its .vue file. (Replacing the `ComponentName` and `component-name` in the template should do the trick.)
1.  haus-components uses the [node-tap](http://www.node-tap.org/) library to run its tests ([API](http://www.node-tap.org/api/)).

When you add a new feature to an existing component, be sure to document and test it!

---

**haus-components**

http://funkhaus.us
