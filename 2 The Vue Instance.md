# 2 The Vue Instance

Views in Vue are composed of **nested** instances of Vue objects, made by `new Vue`. All Vue objects are identical instructure, but the root instance has some specific properties. For example, a hierarchy might look like this:

```
Root Instance
└─ TodoList
   ├─ TodoItem
   │  ├─ DeleteTodoButton
   │  └─ EditTodoButton
   └─ TodoListFooter
      ├─ ClearTodosButton
      └─ TodoListStatistics
```

Vue is extremely similar to the MVVM model, so convention is to call the object that we create as `vm`

## Data in Vue

`data` in the Vue object holds all the attributes that are meant to be reactive - i.e. the view should update to match these attributes. But we cannot add properties to the Vue object and expect them to be reactive. **Properties must be present when the instance was created (null is OK)**. Additionally, objects are passed **by reference**

```javascript
// Our data object
var data = { a: 1 }

// The object is added to a Vue instance
var vm = new Vue({
  data: data
})

// Getting the property on the instance
// returns the one from the original data
vm.a == data.a // => true

// Setting the property on the instance
// also affects the original data
vm.a = 2
data.a // => 2

// ... and vice-versa
data.a = 3
vm.a // => 3

// Adding a new prop will do nothing for reactivity
vm.b = 'hi' // Will not trigger a view reload

// If you freeze an object, it will no longer trigger view reloads
Object.freeze(data)
```

Notice that we can retrieve attributes directly from the data object, so `vm.a` instead of `vm.data.a`. Vue exposes its instance properties and some special instance methods, but these must be prefixed with `$` to indicate relationship with Vue:

```javascript
var data = { a: 1 }
var vm = new Vue({
  el: '#example',
  data: data
})

vm.$data === data // => true
vm.$el === document.getElementById('example') // => true

// $watch is an instance method
vm.$watch('a', function (newValue, oldValue) {
  // This callback will be called when `vm.a` changes
})
```



## Instance Lifecycle

[A diagram of the instance lifecycle](https://vuejs.org/images/lifecycle.png?_sw-precache=6f2c97f045ba988851b02056c01c8d62)

In certain parts of the instance lifecycle, `hooks` are run - these are functions that are called during certain parts of the instance lifecycle. We **MUST** declare these functions as `function () { }` instead of using arrow syntax `() => {}`. This is because we need to be able to access Vue properties using `this`.

**beforeCreate**

- Called when Vue is initialized, but before injection into HTML and reactivity

**created**

- Called after Vue is injected and reactivity is active

**beforeMount**

- Called before Vue is mounted to an HTML element, after the render function is compiled

**mounted**

- Called after Vue is mounted to an HTML element

**beforeUpdate / updated**

- Called before / after a change occurs in the data (causing change in the HTML DOM)

**beforeDestroy**

- Called when `vm.$destroy()` is called - but before watchers, components, and event listeners are torn down

**destroyed**

- Called after Vue instance is destroyed