---
title: 'React v0.4.0'
author: [zpao]
---

Over the past 2 months we've been taking feedback and working hard to make React even better. We fixed some bugs, made some under-the-hood improvements, and added several features that we think will improve the experience developing with React. Today we're proud to announce the availability of React v0.4!

This release could not have happened without the support of our growing community. Since launch day, the community has contributed blog posts, questions to the [Google Group](https://groups.google.com/group/reactjs), and issues and pull requests on GitHub. We've had contributions ranging from documentation improvements to major changes to React's rendering. We've seen people integrate React into the tools they're using and the products they're building, and we're all very excited to see what our budding community builds next!

React v0.4 has some big changes. We've also restructured the documentation to better communicate how to use React. We've summarized the changes below and linked to documentation where we think it will be especially useful.

When you're ready, [go download it](/docs/installation.html)!

### React {#react}

- Switch from using `id` attribute to `data-reactid` to track DOM nodes. This allows you to integrate with other JS and CSS libraries more easily.
- Support for more DOM elements and attributes (e.g., `<canvas>`)
- Improved server-side rendering APIs. `React.renderComponentToString(<component>, callback)` allows you to use React on the server and generate markup which can be sent down to the browser.
- `prop` improvements: validation and default values. [Read our blog post for details...](/blog/2013/07/11/react-v0-4-prop-validation-and-default-values.html)
- Support for the `key` prop, which allows for finer control over reconciliation. [Read the docs for details...](/docs/multiple-components.html)
- Removed `React.autoBind`. [Read our blog post for details...](/blog/2013/07/02/react-v0-4-autobind-by-default.html)
- Improvements to forms. We've written wrappers around `<input>`, `<textarea>`, `<option>`, and `<select>` in order to standardize many inconsistencies in browser implementations. This includes support for `defaultValue`, and improved implementation of the `onChange` event, and circuit completion. [Read the docs for details...](/docs/forms.html)
- We've implemented an improved synthetic event system that conforms to the W3C spec.
- Updates to your component are batched now, which may result in a significantly faster re-render of components. `this.setState` now takes an optional callback as its second parameter. If you were using `onClick={this.setState.bind(this, state)}` previously, you'll want to make sure you add a third parameter so that the event is not treated as the callback.

### JSX {#jsx}

- Support for comment nodes `<div>{/* this is a comment and won't be rendered */}</div>`
- Children are now transformed directly into arguments instead of being wrapped in an array
  E.g. `<div><Component1/><Component2/></div>` is transformed into `React.DOM.div(null, Component1(null), Component2(null))`.
  Previously this would be transformed into `React.DOM.div(null, [Component1(null), Component2(null)])`.
  If you were using React without JSX previously, your code should still work.

### react-tools {#react-tools}

- Fixed a number of bugs when transforming directories
- No longer re-write `require()`s to be relative unless specified
