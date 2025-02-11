---
title: React v0.9 RC
author: [sophiebits]
---

We're almost ready to release React v0.9! We're posting a release candidate so that you can test your apps on it; we'd much prefer to find show-stopping bugs now rather than after we release.

The release candidate is available for download from the CDN:

- **React**  
  Dev build with warnings: <https://fb.me/react-0.9.0-rc1.js>  
  Minified build for production: <https://fb.me/react-0.9.0-rc1.min.js>
- **React with Add-Ons**  
  Dev build with warnings: <https://fb.me/react-with-addons-0.9.0-rc1.js>  
  Minified build for production: <https://fb.me/react-with-addons-0.9.0-rc1.min.js>
- **In-Browser JSX transformer**  
  <https://fb.me/JSXTransformer-0.9.0-rc1.js>

We've also published version `0.9.0-rc1` of the `react` and `react-tools` packages on npm and the `react` package on bower.

Please try these builds out and [file an issue on GitHub](https://github.com/facebook/react/issues/new) if you see anything awry.

## Upgrade Notes {#upgrade-notes}

In addition to the changes to React core listed below, we've made a small change to the way JSX interprets whitespace to make things more consistent. With this release, space between two components on the same line will be preserved, while a newline separating a text node from a tag will be eliminated in the output. Consider the code:

```html
<div>
  Monkeys: {listOfMonkeys} {submitButton}
</div>
```

In v0.8 and below, it was transformed to the following:

```javascript
React.DOM.div(null, ' Monkeys: ', listOfMonkeys, submitButton);
```

In v0.9, it will be transformed to this JS instead:

```javascript{2,3}
React.DOM.div(null, 'Monkeys:', listOfMonkeys, ' ', submitButton);
```

We believe this new behavior is more helpful and eliminates cases where unwanted whitespace was previously added.

In cases where you want to preserve the space adjacent to a newline, you can write a JS string like `{"Monkeys: "}` in your JSX source. We've included a script to do an automated codemod of your JSX source tree that preserves the old whitespace behavior by adding and removing spaces appropriately. You can [install jsx_whitespace_transformer from npm](https://github.com/facebook/react/blob/master/npm-jsx_whitespace_transformer/README.md) and run it over your source tree to modify files in place. The transformed JSX files will preserve your code's existing whitespace behavior.

## Changelog {#changelog}

### React Core {#react-core}

#### Breaking Changes {#breaking-changes}

- The lifecycle methods `componentDidMount` and `componentDidUpdate` no longer receive the root node as a parameter; use `this.getDOMNode()` instead
- Whenever a prop is equal to `undefined`, the default value returned by `getDefaultProps` will now be used instead
- `React.unmountAndReleaseReactRootNode` was previously deprecated and has now been removed
- `React.renderComponentToString` is now synchronous and returns the generated HTML string
- Full-page rendering (that is, rendering the `<html>` tag using React) is now supported only when starting with server-rendered markup
- On mouse wheel events, `deltaY` is no longer negated
- When prop types validation fails, a warning is logged instead of an error thrown (with the production build of React, the type checks are now skipped for performance)
- On `input`, `select`, and `textarea` elements, `.getValue()` is no longer supported; use `.getDOMNode().value` instead
- `this.context` on components is now reserved for internal use by React

#### New Features {#new-features}

- React now never rethrows errors, so stack traces are more accurate and Chrome's purple break-on-error stop sign now works properly
- Added a new tool for profiling React components and identifying places where defining `shouldComponentUpdate` can give performance improvements
- Added support for SVG tags `defs`, `linearGradient`, `polygon`, `radialGradient`, `stop`
- Added support for more attributes:
  - `noValidate` and `formNoValidate` for forms
  - `property` for Open Graph `<meta>` tags
  - `sandbox`, `seamless`, and `srcDoc` for `<iframe>` tags
  - `scope` for screen readers
  - `span` for `<colgroup>` tags
- Added support for defining `propTypes` in mixins
- Added `any`, `arrayOf`, `component`, `oneOfType`, `renderable`, `shape` to `React.PropTypes`
- Added support for `statics` on component spec for static component methods
- On all events, `.currentTarget` is now properly set
- On keyboard events, `.key` is now polyfilled in all browsers for special (non-printable) keys
- On clipboard events, `.clipboardData` is now polyfilled in IE
- On drag events, `.dataTransfer` is now present
- Added support for `onMouseOver` and `onMouseOut` in addition to the existing `onMouseEnter` and `onMouseLeave` events
- Added support for `onLoad` and `onError` on `<img>` elements
- Added support for `onReset` on `<form>` elements
- The `autoFocus` attribute is now polyfilled consistently on `input`, `select`, and `textarea`

#### Bug Fixes {#bug-fixes}

- React no longer adds an `__owner__` property to each component's `props` object; passed-in props are now never mutated
- When nesting top-level components (e.g., calling `React.renderComponent` within `componentDidMount`), events now properly bubble to the parent component
- Fixed a case where nesting top-level components would throw an error when updating
- Passing an invalid or misspelled propTypes type now throws an error
- On mouse enter/leave events, `.target`, `.relatedTarget`, and `.type` are now set properly
- On composition events, `.data` is now properly normalized in IE9 and IE10
- CSS property values no longer have `px` appended for the unitless properties `columnCount`, `flex`, `flexGrow`, `flexShrink`, `lineClamp`, `order`, `widows`
- Fixed a memory leak when unmounting children with a `componentWillUnmount` handler
- Fixed a memory leak when `renderComponentToString` would store event handlers
- Fixed an error that could be thrown when removing form elements during a click handler
- `key` values containing `.` are now supported
- Shortened `data-reactid` values for performance
- Components now always remount when the `key` property changes
- Event handlers are attached to `document` only when necessary, improving performance in some cases
- Events no longer use `.returnValue` in modern browsers, eliminating a warning in Chrome
- `scrollLeft` and `scrollTop` are no longer accessed on document.body, eliminating a warning in Chrome
- General performance fixes, memory optimizations, improvements to warnings and error messages

### React with Addons {#react-with-addons}

- `React.addons.TransitionGroup` was renamed to `React.addons.CSSTransitionGroup`
- `React.addons.TransitionGroup` was added as a more general animation wrapper
- `React.addons.cloneWithProps` was added for cloning components and modifying their props
- Bug fix for adding back nodes during an exit transition for CSSTransitionGroup
- Bug fix for changing `transitionLeave` in CSSTransitionGroup
- Performance optimizations for CSSTransitionGroup
- On checkbox `<input>` elements, `checkedLink` is now supported for two-way binding

### JSX Compiler and react-tools Package {#jsx-compiler-and-react-tools-package}

- Whitespace normalization has changed; now space between two tags on the same line will be preserved, while newlines between two tags will be removed
- The `react-tools` npm package no longer includes the React core libraries; use the `react` package instead.
- `displayName` is now added in more cases, improving error messages and names in the React Dev Tools
- Fixed an issue where an invalid token error was thrown after a JSX closing tag
- `JSXTransformer` now uses source maps automatically in modern browsers
- `JSXTransformer` error messages now include the filename and problematic line contents when a file fails to parse
