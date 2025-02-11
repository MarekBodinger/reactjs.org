---
title: 'React v0.13.1'
author: [zpao]
---

It's been less than a week since we shipped v0.13.0 but it's time to do another quick release. We just released v0.13.1 which contains bugfixes for a number of small issues.

Thanks all of you who have been upgrading your applications and taking the time to report issues. And a huge thank you to those of you who submitted pull requests for the issues you found! 2 of the 6 fixes that went out today came from people who aren't on the core team!

The release is now available for download:

- **React**  
  Dev build with warnings: <https://fb.me/react-0.13.1.js>  
  Minified build for production: <https://fb.me/react-0.13.1.min.js>
- **React with Add-Ons**  
  Dev build with warnings: <https://fb.me/react-with-addons-0.13.1.js>  
  Minified build for production: <https://fb.me/react-with-addons-0.13.1.min.js>
- **In-Browser JSX transformer**  
  <https://fb.me/JSXTransformer-0.13.1.js>

We've also published version `0.13.1` of the `react` and `react-tools` packages on npm and the `react` package on bower.

---

## Changelog {#changelog}

### React Core {#react-core}

#### Bug Fixes {#bug-fixes}

- Don't throw when rendering empty `<select>` elements
- Ensure updating `style` works when transitioning from `null`

### React with Add-Ons {#react-with-add-ons}

#### Bug Fixes {#bug-fixes-1}

- TestUtils: Don't warn about `getDOMNode` for ES6 classes
- TestUtils: Ensure wrapped full page components (`<html>`, `<head>`, `<body>`) are treated as DOM components
- Perf: Stop double-counting DOM components

### React Tools {#react-tools}

#### Bug Fixes {#bug-fixes-2}

- Fix option parsing for `--non-strict-es6module`
