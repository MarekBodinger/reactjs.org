---
title: 'React v15.0.1'
author: [zpao]
---

Yesterday afternoon we shipped v15.0.0 and quickly got some feedback about a couple of issues. We apologize for these problems and we've been working since then to make sure we get fixes into your hands as quickly as possible.

The first of these issues is related to the removal of an undocumented API. This API was added to enable [JSX Spread Attributes](/docs/jsx-spread.html) in our JS compile tools (react-tools, JSXTransformer) before `Object.assign` was standard. When we stopped supporting these tools last year, we kept the API there to catch the longer tail of people using those tools. Meanwhile we moved to using Babel and encouraged others to do the same. Babel will typically compile the spread use to an `_extends` helper, which will use `Object.assign`. We did not properly research other compilation tools before deciding to remove the API in v15. Specifically, TypeScript and coffee-react are two popular packages using `React.__spread`, as well as reactify which still makes use react-tools. In order to make sure that code compiled with these tools is not broken, we will be restoring the `React.__spread` API and adding a warning. It will be removed in the future so if you maintain a project making using of it, we encourage you to compile to `Object.assign` directly or a similar helper function.

The second issue resulted in cursor position being lost in controlled inputs. We merged a pull request earlier this week to fix a separate regression from v0.14. Our goal was to target `<option>` elements but we ended up targeting all interactions with `value` properties. Unfortunately we didn't test it as thoroughly as we thought. We backed out the offending change and fixed the issue in different way which doesn't have the same problem.

We apologize if you installed 15.0.0 and have encountered these issues yourselves.

As usual, you can get install the `react` package via npm or download a browser bundle.

- **React**  
  Dev build with warnings: <https://fb.me/react-15.0.1.js>  
  Minified build for production: <https://fb.me/react-15.0.1.min.js>
- **React with Add-Ons**  
  Dev build with warnings: <https://fb.me/react-with-addons-15.0.1.js>  
  Minified build for production: <https://fb.me/react-with-addons-15.0.1.min.js>
- **React DOM** (include React in the page before React DOM)  
  Dev build with warnings: <https://fb.me/react-dom-15.0.1.js>  
  Minified build for production: <https://fb.me/react-dom-15.0.1.min.js>

## Changelog {#changelog}

### React {#react}

- Restore `React.__spread` API to unbreak code compiled with some tools making use of this undocumented API. It is now officially deprecated.  
  <small>[@zpao](https://github.com/zpao) in [#6444](https://github.com/facebook/react/pull/6444)</small>

### ReactDOM {#reactdom}

- Fixed issue resulting in loss of cursor position in controlled inputs.  
  <small>[@sophiebits](https://github.com/sophiebits) in [#6449](https://github.com/facebook/react/pull/6449)</small>
