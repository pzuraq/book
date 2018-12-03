# Introduction

The Ember object-model has served Ember well over the years, and really was a solid foundation during the early years of the framework being built on ES5 only. Now that native syntax has finally almost caught up feature-wise though, switching to native classes will unlock a number of major benefits.

## What Are Native Classes?

Native classes are a feature that has existed in Javascript since ES2015. They are declared using the `class` keyword, and under the hood they are mostly syntactic sugar for standard prototypical inheritance. The syntax creates a set of norms for how declarative prototype definitions should work, and the end result is much easier to read and statically analyze.

The [MDN documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes) is a great starting point for learning the details of how classes work.

## What Are Decorators?

Decorators are an upcoming feature in Javascript that allow you to declaratively and transparently modify either a class field/accessor/method or a class itself.

In other words, a decorator is a function that wraps another function and modifies its behavior in some way. Sometimes decorators just add meta information, like annotations in Java, and other times they can add functionality. For instance, you may have a `@memoize` decorator that caches the value of a getter:

```js
class Foo {
  _count = 0;

  @memoize
  get count() {
    return ++this._count;
  }
}

let foo = new Foo();

foo.count; // 1
foo.count; // 1
```

This wouldn't be particularly useful though unless you had a way to invalidate the cache. Hey, that's just `@computed`! In fact, many constructs in Ember, like computed properties, are essentially decorators. They work the same as decorators do, but the syntax was created before decorators or even native classes were being proposed in JS, so we ended up with the Ember object-model.

## What is Ember Decorators

Ember Decorators is a project dedicated to exploring and unlocking usage of native classes in Ember.js. Its goal is to provide a set of decorators which can be used to write classes with every standard feature that is available in the standard Ember object-model, along with the transforms and build system required to polyfill and ship them today!

## Why do I need decorators to use native classes?

Native classes have been around since ES2015, so why haven't we been able to use them with Ember all this time? While `class` syntax has existed for a long time now, using it with Ember was difficult because it lacked a way to:

1. Declare properties and give them values, e.g.

  ```js
  Ember.Object.extend({
    foo: 123,
    classNames: ['is-active']
  });
  ```

2. Specify meta behavior declaritively for methods, getters, setters, and properties. This is something the Ember object-model has supported out-of-the-box since the early days with computed properties, observers, event listeners, and so on, e.g.

  ```js
  Ember.Object.extend({
    foo: computed('qux', {
      get() {
        return 123;
      }
    }),

    bar: observer('qux', function() {
      // do something
    }),

    baz: on('init', function() {
      // do something else
    })
  });
  ```

[Class fields](https://github.com/tc39/proposal-class-fields) and
[decorators](https://github.com/tc39/proposal-decorators) solve each of these problems respectively, and give us the tools to do a nearly 1-to-1 conversion from the old Ember objects to native classes. While it is technically possible to use native classes without class fields or decorators, the DX of doing this is incredibly low - its a long, error prone, and somewhat non-performant process.

## Benefits of Classes

1. **Integration with the Javascript community**

  The Ember object-model was never picked up outside of the Ember community, and the wider Javascript community has continued to evolve their own standards, libraries, and practices. As classes solidify into a first class feature of the language, they will continue to gain usage, with more and more supporting libraries, frameworks, and code written for them. Ember is all about shared solutions, and standard native classes will allow us to share a solution with the _entire_ Javascript community.

2. **Lower teaching cost**

  The Ember object-model is a stumbling block for new users of Ember because its not Just Javascript. It requires learning a class definition and inheritance structure, a mixin system, and a whole bunch of gotchas. As native classes grow in adoption, Javascript developers in general will become more familiar with them by _default_, and there will be many resources teaching them how classes, fields, and decorators work. All of this lowers the cost of hiring and training new developers.

3. **Better tooling from editors**

  Modern text editors like VSCode and Atom have been adding more and better support for things like autocompletion, method signature reference, go to definition, and general IDE like behavior. These features can increase developer productivity by a huge margin, making it easier to find docs, look up APIs, and catch errors and bad practices early with static analysis.

4. **Better tooling in general**

  Other tools will be able to leverage a common class framework, leading to even more shared solutions for tooling and other repetitive work. A great example of this is [ESDoc](https://esdoc.org/), which is the documenter that generates our API docs! It can infer much more about your classes than traditional documenters like JSDoc and YUIDoc because it reads native class syntax:

  ```js
  // YUIDoc

  /*
    A foo class

    @class Foo
    @extends Bar
  */
  const Foo = Bar.extend({
    /*
      A property

      @property prop1
    */
    prop1: 123,

    /*
      A method

      @method doSomething
    */
   doSomething() {}
  });

  // ESDoc

  /*
    A foo class
  */
  class Foo extends Bar {
    /* A property */
    prop1 = 123;

    /* A method */
    doSomething() {}
  }
  ```

  The two above examples would produce exactly the same documentation! ESDoc doesn't requires tags most of the time, allowing you to write code without worrying about maintenance.
