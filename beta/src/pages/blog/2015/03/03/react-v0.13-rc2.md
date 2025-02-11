---
title: 'React v0.13 RC2'
author: [sebmarkbage]
---

Thanks to everybody who has already been testing the release candidate. We've received some good feedback and as a result we're going to do a second release candidate. The changes are minimal. We haven't changed the behavior of any APIs we exposed in the previous release candidate. Here's a summary of the changes:

- Introduced a new API (`React.cloneElement`, see below for details).
- Fixed a bug related to validating `propTypes` when using the new `React.addons.createFragment` API.
- Improved a couple warning messages.
- Upgraded jstransform and esprima.

The release candidate is available for download:

- **React**  
  Dev build with warnings: <https://fb.me/react-0.13.0-rc2.js>  
  Minified build for production: <https://fb.me/react-0.13.0-rc2.min.js>
- **React with Add-Ons**  
  Dev build with warnings: <https://fb.me/react-with-addons-0.13.0-rc2.js>  
  Minified build for production: <https://fb.me/react-with-addons-0.13.0-rc2.min.js>
- **In-Browser JSX transformer**  
  <https://fb.me/JSXTransformer-0.13.0-rc2.js>

We've also published version `0.13.0-rc2` of the `react` and `react-tools` packages on npm and the `react` package on bower.

---

## React.cloneElement {#reactcloneelement}

In React v0.13 RC2 we will introduce a new API, similar to `React.addons.cloneWithProps`, with this signature:

```js
React.cloneElement(element, props, ...children);
```

Unlike `cloneWithProps`, this new function does not have any magic built-in behavior for merging `style` and `className` for the same reason we don't have that feature from `transferPropsTo`. Nobody is sure what exactly the complete list of magic things are, which makes it difficult to reason about the code and difficult to reuse when `style` has a different signature (e.g. in the upcoming React Native).

`React.cloneElement` is _almost_ equivalent to:

```js
<element.type {...element.props} {...props}>
  {children}
</element.type>
```

However, unlike JSX and `cloneWithProps`, it also preserves `ref`s. This means that if you get a child with a `ref` on it, you won't accidentally steal it from your ancestor. You will get the same `ref` attached to your new element.

One common pattern is to map over your children and add a new prop. There were many issues reported about `cloneWithProps` losing the ref, making it harder to reason about your code. Now following the same pattern with `cloneElement` will work as expected. For example:

```js
var newChildren = React.Children.map(this.props.children, function (child) {
  return React.cloneElement(child, {foo: true});
});
```

> Note: `React.cloneElement(child, { ref: 'newRef' })` _DOES_ override the `ref` so it is still not possible for two parents to have a ref to the same child, unless you use callback-refs.

This was a critical feature to get into React 0.13 since props are now immutable. The upgrade path is often to clone the element, but by doing so you might lose the `ref`. Therefore, we needed a nicer upgrade path here. As we were upgrading callsites at Facebook we realized that we needed this method. We got the same feedback from the community. Therefore we decided to make another RC before the final release to make sure we get this in.

We plan to eventually deprecate `React.addons.cloneWithProps`. We're not doing it yet, but this is a good opportunity to start thinking about your own uses and consider using `React.cloneElement` instead. We'll be sure to ship a release with deprecation notices before we actually remove it so no immediate action is necessary.
