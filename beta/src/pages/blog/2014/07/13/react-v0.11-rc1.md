---
title: React v0.11 RC
author: [zpao]
---

It's that time again… we're just about ready to ship a new React release! v0.11 includes a wide array of bug fixes and features. We highlighted some of the most important changes below, along with the full changelog.

The release candidate is available for download from the CDN:

- **React**  
  Dev build with warnings: <https://fb.me/react-0.11.0-rc1.js>  
  Minified build for production: <https://fb.me/react-0.11.0-rc1.min.js>
- **React with Add-Ons**  
  Dev build with warnings: <https://fb.me/react-with-addons-0.11.0-rc1.js>  
  Minified build for production: <https://fb.me/react-with-addons-0.11.0-rc1.min.js>
- **In-Browser JSX transformer**  
  <https://fb.me/JSXTransformer-0.11.0-rc1.js>

We've also published version `0.11.0-rc1` of the `react` and `react-tools` packages on npm and the `react` package on bower.

Please try these builds out and [file an issue on GitHub](https://github.com/facebook/react/issues/new) if you see anything awry.

## `getDefaultProps` {#getdefaultprops}

Starting in React 0.11, `getDefaultProps()` is called only once when `React.createClass()` is called, instead of each time a component is rendered. This means that `getDefaultProps()` can no longer vary its return value based on `this.props` and any objects will be shared across all instances. This change improves performance and will make it possible in the future to do PropTypes checks earlier in the rendering process, allowing us to give better error messages.

## Rendering to `null` {#rendering-to-null}

Since React's release, people have been using work arounds to "render nothing". Usually this means returning an empty `<div/>` or `<span/>`. Some people even got clever and started returning `<noscript/>` to avoid extraneous DOM nodes. We finally provided a "blessed" solution that allows developers to write meaningful code. Returning `null` is an explicit indication to React that you do not want anything rendered. Behind the scenes we make this work with a `<noscript>` element, though in the future we hope to not put anything in the document. In the mean time, `<noscript>` elements do not affect layout in any way, so you can feel safe using `null` today!

```js
// Before
render: function() {
  if (!this.state.visible) {
    return <span/>;
  }
  // ...
}

// After
render: function() {
  if (!this.state.visible) {
    return null;
  }
  // ...
}
```

## JSX Namespacing {#jsx-namespacing}

Another feature request we've been hearing for a long time is the ability to have namespaces in JSX. Given that JSX is just JavaScript, we didn't want to use XML namespacing. Instead we opted for a standard JS approach: object property access. Instead of assigning variables to access components stored in an object (such as a component library), you can now use the component directly as `<Namespace.Component/>`.

```js
// Before
var UI = require('UI');
var UILayout = UI.Layout;
var UIButton = UI.Button;
var UILabel = UI.Label;

render: function() {
  return <UILayout><UIButton /><UILabel>text</UILabel></UILayout>;
}

// After
var UI = require('UI');

render: function() {
  return <UI.Layout><UI.Button /><UI.Label>text</UI.Label></UI.Layout>;
}
```

## Improved keyboard event normalization {#improved-keyboard-event-normalization}

Keyboard events now contain a normalized `e.key` value according to the [DOM Level 3 Events spec](http://www.w3.org/TR/DOM-Level-3-Events/#keys-special), allowing you to write simpler key handling code that works in all browsers, such as:

```js
handleKeyDown: function(e) {
  if (e.key === 'Enter') {
    // Handle enter key
  } else if (e.key === ' ') {
    // Handle spacebar
  } else if (e.key === 'ArrowLeft') {
    // Handle left arrow
  }
},
```

Keyboard and mouse events also now include a normalized `e.getModifierState()` that works consistently across browsers.

## Changelog {#changelog}

### React Core {#react-core}

#### Breaking Changes {#breaking-changes}

- `getDefaultProps()` is now called once per class and shared across all instances

#### New Features {#new-features}

- Rendering to `null`
- Keyboard events include normalized `e.key` and `e.getModifierState()` properties
- New normalized `onBeforeInput` event
- `React.Children.count` has been added as a helper for counting the number of children

#### Bug Fixes {#bug-fixes}

- Re-renders are batched in more cases
- Events: `e.view` properly normalized
- Added Support for more HTML attributes (`coords`, `crossOrigin`, `download`, `hrefLang`, `mediaGroup`, `muted`, `scrolling`, `shape`, `srcSet`, `start`, `useMap`)
- Improved SVG support
  - Changing `className` on a mounted SVG component now works correctly
  - Added support for elements `mask` and `tspan`
  - Added support for attributes `dx`, `dy`, `fillOpacity`, `fontFamily`, `fontSize`, `markerEnd`, `markerMid`, `markerStart`, `opacity`, `patternContentUnits`, `patternUnits`, `preserveAspectRatio`, `strokeDasharray`, `strokeOpacity`
- CSS property names with vendor prefixes (`Webkit`, `ms`, `Moz`, `O`) are now handled properly
- Duplicate keys no longer cause a hard error; now a warning is logged (and only one of the children with the same key is shown)
- `img` event listeners are now unbound properly, preventing the error "Two valid but unequal nodes with the same `data-reactid`"
- Added explicit warning when missing polyfills

### React With Addons {#react-with-addons}

- PureRenderMixin
- Perf: a new set of tools to help with performance analysis
- Update: New `$apply` command to transform values
- TransitionGroup bug fixes with null elements, Android

### React NPM Module {#react-npm-module}

- Now includes the pre-built packages under `dist/`.
- `envify` is properly listed as a dependency instead of a peer dependency

### JSX {#jsx}

- Added support for namespaces, eg `<Components.Checkbox />`
- JSXTransformer
  - Enable the same `harmony` features available in the command line with `<script type="text/jsx;harmony=true">`
  - Scripts are downloaded in parallel for more speed. They are still executed in order (as you would expect with normal script tags)
  - Fixed a bug preventing sourcemaps from working in Firefox

### React Tools Module {#react-tools-module}

- Improved readme with usage and API information
- Improved ES6 transforms available with `--harmony` option
- Added `--source-map-inline` option to the `jsx` executable
- New `transformWithDetails` API which gives access to the raw sourcemap data
