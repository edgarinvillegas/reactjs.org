---
id: hello-world
title: Hello World
permalink: docs/hello-world.html
prev: cdn-links.html
next: introducing-jsx.html
redirect_from:
  - "docs/"
  - "docs/index.html"
  - "docs/getting-started.html"
  - "docs/getting-started-ko-KR.html"
  - "docs/getting-started-zh-CN.html"
---

The easiest way to get started with React is to use [this Hello World example code on CodePen](https://codepen.io/edgarinvillegas/pen/PeYVXN). You don't need to install anything; you can just open it in another tab and follow along as we go through examples. If you'd rather use a local development environment, check out the [Installation](/docs/try-react.html) section.

The smallest React example looks like this:

```typescript
ReactDOM.render(
  let id: string; // TODO: Just a ts example. Remove
  <h1>Hello, React with TS!</h1>,
  document.getElementById(id)
);
```

It renders a heading saying "Hello, world!" on the page.

The next few sections will gradually introduce you to using React. We will examine the building blocks of React apps: elements and components. Once you master them, you can create complex apps from small reusable pieces.

## A Note on TypeScript

React is a JavaScript library, and so we'll assume you have a basic understanding of the JavaScript language. If you don't feel very confident, we recommend [refreshing your JavaScript knowledge](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript) so you can follow along more easily.

React is usually used with ES6 syntax. We encourage you to get familiar with [arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions), [classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes), [template literals](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Template_literals), [`let`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let), and [`const`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const) statements. You can use the [Babel REPL](babel://es5-syntax-example) to check what ES6 code compiles to.

Typescript is the typed superset of ES6. It [has many benefits](https://blog.appdynamics.com/engineering/the-benefits-of-migrating-from-javascript-to-typescript/) and can work perfectly with React.
You can learn it on its [official website](http://www.typescriptlang.org/)
