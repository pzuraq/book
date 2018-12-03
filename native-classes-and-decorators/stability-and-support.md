# Stability and Support

Use of native classes is officially public API in Ember as of 3.6, with a [polyfill which works on 3.4 and 3.5](https://github.com/pzuraq/ember-native-class-polyfill). However, Ember does _not_ provide any decorators as of yet, since decorators are still only stage 2 in the TC39 process. This means that they could still change significantly between stages 2 and 3.

Native classes _should not_ be used without decorators. Without them, it is very difficult to write classes in an ergonomic way. This includes Typescript, and patterns such as:

```js
class MyComponent extends Component.extend({
  computedProp: computed(/*...*/),
}) {
  nativeMethod() {}
}
```

These patterns will be difficult to codemod and update automatically in the future, and should be avoided if possible. If you are using native classes today, the most future proof way to use them is with decorators. Otherwise, continue to use the standard Ember object-model, and when decorators stabilize the [native class codemod (currently WIP)](https://github.com/scalvert/ember-es6-class-codemod) will be readily available for you to automatically convert in the future.

## Decorators Support

[Ember Decorators](https://github.com/ember-decorators/ember-decorators) is being used in production apps today, and the library is fully tested in both Javascript and Typescript. The project aims to maintain a stable API as decorators and class fields evolve through the TC39 process. This means that it will not make major breaking changes to its APIs _unless_ absolutely required by changes in the underlying spec.

## Browser Support

Both Ember and Ember Decorators support native class usage IE11+ and the last two versions of every major browser. For IE11, classes will be transpiled. This is handled by the standard Ember tooling for targets.
