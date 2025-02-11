---
title: 'React v15.0 Release Candidate'
author: [zpao]
---

Sorry for the small delay in releasing this. As we said, we've been busy binge-watching House of Cards. That scene in the last episode where Francis and Claire Underwood <abbr title="You didn't think we would actually spoil anything did you?">████████████████████████████████████</abbr>. WOW!

But now we're ready, so without further ado, we're shipping a release candidate for React v15 now. As a reminder, [we're switching to major versions](/blog/2016/02/19/new-versioning-scheme.html) to indicate that we have been using React in production for a long time. This 15.0 release follows our previous 0.14 version and we'll continue to follow semver like we've been doing since 2013. It's also worth noting that [we no longer actively support Internet Explorer 8](/blog/2016/01/12/discontinuing-ie8-support.html). We believe React will work in its current form there but we will not be prioritizing any efforts to fix new issues that only affect IE8.

Please try it out before we publish the final release. Let us know if you run into any problems by filing issues on our [GitHub repo](https://github.com/facebook/react).

## Upgrade Guide {#upgrade-guide}

Like always, we have a few breaking changes in this release. We know changes can be painful (the Facebook codebase has over 15,000 React components), so we always try to make changes gradually in order to minimize the pain.

If your code is free of warnings when running under React 0.14, upgrading should be easy. The bulk of changes in this release are actually behind the scenes, impacting the way that React interacts with the DOM. The other substantial change is that React now supports the full range of SVG elements and attributes. Beyond that we have a large number of incremental improvements and additional warnings aimed to aid developers. We've also laid some groundwork in the core to bring you some new capabilities in future releases.

See the changelog below for more details.

## Installation {#installation}

We recommend using React from `npm` and using a tool like browserify or webpack to build your code into a single bundle. To install the two packages:

- `npm install --save react@15.0.0-rc.1 react-dom@15.0.0-rc.1`

Remember that by default, React runs extra checks and provides helpful warnings in development mode. When deploying your app, set the `NODE_ENV` environment variable to `production` to use the production build of React which does not include the development warnings and runs significantly faster.

If you can’t use `npm` yet, we provide pre-built browser builds for your convenience, which are also available in the `react` package on bower.

- **React**  
  Dev build with warnings: <https://fb.me/react-15.0.0-rc.1.js>  
  Minified build for production: <https://fb.me/react-15.0.0-rc.1.min.js>
- **React with Add-Ons**  
  Dev build with warnings: <https://fb.me/react-with-addons-15.0.0-rc.1.js>  
  Minified build for production: <https://fb.me/react-with-addons-15.0.0-rc.1.min.js>
- **React DOM** (include React in the page before React DOM)  
  Dev build with warnings: <https://fb.me/react-dom-15.0.0-rc.1.js>  
  Minified build for production: <https://fb.me/react-dom-15.0.0-rc.1.min.js>

## Changelog {#changelog}

### Major changes {#major-changes}

- #### `document.createElement` is in and `data-reactid` is out

  There were a number of large changes to our interactions with the DOM. One of the most noticeable changes is that we no longer set the `data-reactid` attribute for each DOM node. While this will make it much more difficult to know if a website is using React, the advantage is that the DOM is much more lightweight. This change was made possible by us switching to use `document.createElement` on initial render. Previously we would generate a large string of HTML and then set `node.innerHTML`. At the time, this was decided to be faster than using `document.createElement` for the majority of cases and browsers that we supported. Browsers have continued to improve and so overwhelmingly this is no longer true. By using `createElement` we can make other parts of React faster. The ids were used to map back from events to the original React component, meaning we had to do a bunch of work on every event, even though we cached this data heavily. As we've all experienced, caching and in particularly invalidating caches, can be error prone and we saw many hard to reproduce issues over the years as a result. Now we can build up a direct mapping at render time since we already have a handle on the node.

- #### No more extra `<span>`s

  Another big change with our DOM interaction is how we render text blocks. Previously you may have noticed that React rendered a lot of extra `<span>`s. Eg, in our most basic example on the home page we render `<div>Hello {this.props.name}</div>`, resulting in markup that contained 2 `<span>`s. Now we'll render plain text nodes interspersed with comment nodes that are used for demarcation. This gives us the same ability to update individual pieces of text, without creating extra nested nodes. Very few people have depended on the actual markup generated here so it's likely you are not impacted. However if you were targeting these `<span>`s in your CSS, you will need to adjust accordingly. You can always render them explicitly in your components.

- #### Rendering `null` now uses comment nodes

  We've also made use of these comment nodes to change what `null` renders to. Rendering to `null` was a feature we added in React v0.11 and was implemented by rendering `<noscript>` elements. By rendering to comment nodes now, there's a chance some of your CSS will be targeting the wrong thing, specifically if you are making use of `:nth-child` selectors. This, along with the other changes mentioned above, have always been considered implementation details of how React targets the DOM. We believe they are safe changes to make without going through a release with warnings detailing the subtle differences as they are details that should not be depended upon. Additionally, we have seen that these changes have improved React performance for many typical applications.

- #### Improved SVG support

  All SVG tags and attributes are now fully supported. (Uncommon SVG tags are not present on the `React.DOM` element helper, but JSX and `React.createElement` work on all tag names.) All SVG attributes match their original capitalization and hyphenation as defined in the specification (ex: `gradientTransform` must be camel-cased but `clip-path` should be hyphenated).

### Breaking changes {#breaking-changes}

It's worth calling out the DOM structure changes above again, in particular the change from `<span>`s. In the course of updating the Facebook codebase, we found a very small amount of code that was depending on the markup that React generated. Some of these cases were integration tests like WebDriver which were doing very specific XPath queries to target nodes. Others were simply tests using `ReactDOM.renderToStaticMarkup` and comparing markup. Again, there were a very small number of changes that had to be made, but we don't want anybody to be blindsided. We encourage everybody to run their test suites when upgrading and consider alternative approaches when possible. One approach that will work for some cases is to explicitly use `<span>`s in your `render` method.

These deprecations were introduced in v0.14 with a warning and the APIs are now removed.

- Deprecated APIs removed from `React`, specifically `findDOMNode`, `render`, `renderToString`, `renderToStaticMarkup`, and `unmountComponentAtNode`.
- Deprecated APIs removed from `React.addons`, specifically `batchedUpdates` and `cloneWithProps`.
- Deprecated APIs removed from component instances, specifically `setProps`, `replaceProps`, and `getDOMNode`.

### New deprecations, introduced with a warning {#new-deprecations-introduced-with-a-warning}

Each of these changes will continue to work as before with a new warning until the release of React 16 so you can upgrade your code gradually.

- `LinkedStateMixin` and `valueLink` are now deprecated due to very low popularity. If you need this, you can use a wrapper component that implements the same behavior: [react-linked-input](https://www.npmjs.com/package/react-linked-input).

### New helpful warnings {#new-helpful-warnings}

- If you use a minified copy of the _development_ build, React DOM kindly encourages you to use the faster production build instead.
- React DOM: When specifying a unit-less CSS value as a string, a future version will not add `px` automatically. This version now warns in this case (ex: writing `style={{width: '300'}}`. (Unitless _number_ values like `width: 300` are unchanged.)
- Synthetic Events will now warn when setting and accessing properties (which will not get cleared appropriately), as well as warn on access after an event has been returned to the pool.
- Elements will now warn when attempting to read `ref` and `key` from the props.
- React DOM now attempts to warn for mistyped event handlers on DOM elements (ex: `onclick` which should be `onClick`)

### Notable bug fixes {#notable-bug-fixes}

- Fixed multiple small memory leaks
- Input events are handled more reliably in IE 10 and IE 11; spurious events no longer fire when using a placeholder.
- React DOM now supports the `cite` and `profile` HTML attributes.
- React DOM now supports the `onAnimationStart`, `onAnimationEnd`, `onAnimationIteration`, `onTransitionEnd`, and `onInvalid` events. Support for `onLoad` has been added to `object` elements.
- `Object.is` is used in a number of places to compare values, which leads to fewer false positives, especially involving `NaN`. In particular, this affects the `shallowCompare` add-on.
- React DOM now defaults to using DOM attributes instead of properties, which fixes a few edge case bugs. Additionally the nullification of values (ex: `href={null}`) now results in the forceful removal, no longer trying to set to the default value used by browsers in the absence of a value.
