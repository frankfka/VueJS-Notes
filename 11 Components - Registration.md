# 11 Components - Registration

## Naming

When registering, pass the name as the first parameter:

```javascript
Vue.component('my-component-name', { /* ... */ })
```

We can use PascalCase as well - this means that we can use `<my-component-name>` in HTML DOM, and `MyComponentName` in any string templates:

```javascript
Vue.component('MyComponentName', { /* ... */ })
```

## Local Component Registration

Calling `Vue.component(...)` registers the component **globally**, which we do not always want. Instead, we can use local registration of components:

```javascript
var ComponentA = { /* ... */ }
var ComponentB = { /* ... */ }
new Vue({
  el: '#app',
  components: {
    'component-a': ComponentA,
    'component-b': ComponentB
  }
})
```

But this comes at the cost of components **not available in subcomponents**. So to use `component-a` in `component-b`, we need to do the following:

```javascript
var ComponentA = { /* ... */ }

var ComponentB = {
  components: {
    'component-a': ComponentA
  },
  // ...
}
```

Or with ES2015 modules, this is simpler:

```javascript
import ComponentA from './ComponentA.vue'

export default {
  components: {
    ComponentA // Note: this is equivalent to declaring ComponentA: ComponentA
  },
  // ...
}
```

## Components with Modules

It's likely that you'll be using Babel/Webpack (i.e. using import statements). It is convention to declare each individual component in their own file, often in a `components` directory. We could then do something like this for `ComponentB`:

```javascript
import ComponentA from './ComponentA'
import ComponentC from './ComponentC'

export default {
  components: {
    ComponentA,
    ComponentC
  },
  // ...
}
```

## Base Components

We often work with base components - ex. specific behavior for icons, inputs, buttons. Usually we want to globally register these. But global registration **must occur before the root Vue instance is created**.

See [this link](https://vuejs.org/v2/guide/components-registration.html#Automatic-Global-Registration-of-Base-Components) for an overview of how to import our base components at initialization.