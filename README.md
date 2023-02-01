# Symbol Predicates Proposal

## Status

Stage 2

Champions: _Ashley Claymore_, _Jordan Harband_

Authors: _Robin Ricard_, _Ashley Claymore_, _Jordan Harband_

## Motivation

A proposal to introduce ways to differentiate symbols.

This proposal adds the following predicates:

- `Symbol.isRegistered(symbol)`
- `Symbol.isWellKnown(symbol)`

Not all symbols are the same and knowing more about what they are can be useful, notably for libraries.

Knowing if a symbol is truly unique, is forgeable (registered) or is shared across realms (well-known), can be important depending on the use case.

For instance [Symbols as WeakMap keys] require symbols to not be registered.

[Symbols as WeakMap keys]: https://github.com/tc39/proposal-symbols-as-weakmap-keys

## Use Cases

You can detect in a library if a symbol can be used as a WeakMap key:

```js
function isWeakMapKey(key) {
  switch (typeof key): {
    case "object":
      return key !== null;
    case "function":
      return true;
    case "symbol":
      return !Symbol.isRegistered(sym);
  }
  return false;
}

isWeakMapKey({}); // true
isWeakMapKey(Symbol()); // true
isWeakMapKey("foo"); // false
isWeakMapKey(Symbol.for("foo")); // false
isWeakMapKey(Symbol.asyncIterator); // true
```

You can also find out if you are being given a truly unique symbol:

```js
const isUniqueSymbol = sym => typeof sym === "symbol" && !(Symbol.isRegistered(sym) || Symbol.isWellKnown(sym));

isUniqueSymbol(Symbol()); // true
isUniqueSymbol(Symbol.for("foo")); // false
isUniqueSymbol(Symbol.asyncIterator); // false
isUniqueSymbol({}); // false
```

## Description

### `Symbol.isRegistered(value)`

Takes an unknown value as the only argument, returns a boolean: `true` if the symbol is registered, `false` otherwise.

### `Symbol.isWellKnown(value)`

Takes an unknown value as the only argument, returns a boolean: `true` if the symbol is one of the well known symbols as defined by ECMA262 & ECMA402, `false` otherwise.

## Implementations

Note that the two predicate packages belo are not polyfills; but they precisely match the current spec text, so they represent valid implementations of the proposal.

- `Symbol.isRegistered(symbol)`: https://github.com/inspect-js/is-registered-symbol
- `Symbol.isWellKnown(symbol)`: https://github.com/inspect-js/is-well-known-symbol

---

## Maintain the proposal repo

  1. Make your changes to `spec.emu` (ecmarkup uses HTML syntax, but is not HTML, so I strongly suggest not naming it ".html")
  1. Any commit that makes meaningful changes to the spec, should run `npm run build` and commit the resulting output.
  1. Whenever you update `ecmarkup`, run `npm run build` and commit any changes that come from that dependency.

  [explainer]: https://github.com/tc39/how-we-work/blob/HEAD/explainer.md
