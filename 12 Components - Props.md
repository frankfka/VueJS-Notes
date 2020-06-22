# 12 Components - Props

We can declare prop names as camelCase, but as HTML is case insensitive, we must use their kebab-case equivalent in native HTML. However, this does **not** apply to string templates (i.e. inside other components)

```javascript
Vue.component('blog-post', {
  // camelCase in JavaScript
  props: ['postTitle'],
  template: '<h3>{{ postTitle }}</h3>'
})
```

```html
<!-- kebab-case in HTML -->
<blog-post post-title="hello!"></blog-post>
```

Recall that props are usually passed as an array. **However, it is common practice to pass an object.** This object links the prop names to their types:

```javascript
props: {
  title: String,
  likes: Number,
  isPublished: Boolean,
  commentIds: Array,
  author: Object,
  callback: Function,
  contactsPromise: Promise // or any other constructor
}
```

## Passing Props

Recall that props can be passed with an HTML attribute:

```html
<blog-post title="My journey with Vue"></blog-post>
```

Chances are, these will be dynamic, so you can use `v-bind:propName`:

```html
<!-- Dynamically assign the value of a variable -->
<blog-post v-bind:title="post.title"></blog-post>

<!-- Dynamically assign the value of a complex expression -->
<blog-post
  v-bind:title="post.title + ' by ' + post.author.name"
></blog-post>
```

Notice that the first method - passing as an attribute - will **only** work for strings. To pass numbers, booleans, etc, we'll need to use `v-bind` even if the value is static:

```html
<!-- Even though `42` is static, we need v-bind to tell Vue that -->
<!-- this is a JavaScript expression rather than a string.       -->
<blog-post v-bind:likes="42"></blog-post>

<!-- Dynamically assign to the value of a variable. -->
<blog-post v-bind:likes="post.likes"></blog-post>
```

Using `v-bind` without the `:propName` is useful for passing **all the props** as an object. This is most likely what you'll do:

```javascript
post: {
  id: 1,
  title: 'My Journey with Vue'
}
```

```html
<blog-post v-bind="post"></blog-post>
```

The above is the same as:

```html
<blog-post
  v-bind:id="post.id"
  v-bind:title="post.title"
></blog-post>
```

## Data Flow

**v-bind is one way data flow.**

This means you **cannot** modify props. As **objects and arrays** are passed by reference, any changes to them within the child component will **not work.** Here are some cases where we might be tempted to do so:

1. Using parent state as an initial state: simply create a child data element that uses the passed in prop:

   ```javascript
   props: ['initialCounter'],
   data: function () {
     return {
       counter: this.initialCounter
     }
   }
   ```

2. A data value is passed in that needs to be transformed before displaying: create a computed variable:

   ```javascript
   props: ['size'],
   computed: {
     normalizedSize: function () {
       return this.size.trim().toLowerCase()
     }
   }
   ```

## Prop Validation

Recall that we went over an example of validating prop types by declaring props as an object. We can expand on this prop validation like so:

```javascript
Vue.component('my-component', {
  props: {
    // Basic type check (`null` and `undefined` values will pass any type validation)
    propA: Number,
    // Multiple possible types
    propB: [String, Number],
    // Required string
    propC: {
      type: String,
      required: true
    },
    // Number with a default value
    propD: {
      type: Number,
      default: 100
    },
    // Object with a default value
    propE: {
      type: Object,
      // Object or array defaults must be returned from
      // a factory function
      default: function () {
        return { message: 'hello' }
      }
    },
    // Custom validator function
    propF: {
      validator: function (value) {
        // The value must match one of these strings
        return ['success', 'warning', 'danger'].indexOf(value) !== -1
      }
    }
  }
})
```

Validation occurs **before** the component is created, so `data` and `computed` properties, as well as all other instance properties, will not be accessible within validation functions.

Type checks are dependent on the **constructors**. Thes are available for native JS types:

- String
- Number
- Boolean
- Array
- Object
- Date
- Function
- Symbol

But custom types can also be validated, given that they have a constructor such as:

```javascript
function Person (firstName, lastName) {
  this.firstName = firstName
  this.lastName = lastName
}
```

## Non-Prop Attributes

These are attributes passed to a component **without a corresponding prop.** This is meant for reusability of components - we can pass **more** than we intended to. Any attributes passed to the base component will be added to the root element. In this example, `data-date-picker` is set to `activated`:

```html
<bootstrap-date-input data-date-picker="activated"></bootstrap-date-input>
```

Passing additional attributes will **overwrite** any possible existing attributes. However, this is not the case for **class** and **style**: these will be merged.

We can disable the default behavior of inheriting attributes by setting `inheritAttrs: false` (Note: this does **not** apply to `class` and `style`. But the given attributes are still accessible through `$attrs`. This allows us to decide where the given attributes are applied:

```javascript
Vue.component('base-input', {
  inheritAttrs: false,
  props: ['label', 'value'],
  template: `
    <label>
      {{ label }}
      <input
        v-bind="$attrs"
        v-bind:value="value"
        v-on:input="$emit('input', $event.target.value)"
      >
    </label>
  `
})
```

```html
<base-input
  v-model="username"
  required
  placeholder="Enter your username"
></base-input>
```

In the above example, notice the `required` and `placeholder` are forwarded to the nested `input` element, instead of being applied on the outer `<label`. 