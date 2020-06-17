# 5 Class + Style Bindings

Often, we want to bind **class lists** and **inline styles**, Vue has special behavior when using `v-bind` with these HTML attributes.

## Binding Classes

The base directive is `v-bind:class`

### Using Objects

Starting with an example, if we define the following:

```html
<div
  class="static"
  v-bind:class="{ active: isActive, 'text-danger': hasError }"
></div>
```

And the following data structure:

```javascript
data: {
  isActive: true,
  hasError: false
}
```

Then we get the following. Notice that any predefined classes in the div are kept as well ("static"):

```html
<div class="static active"></div>
```

We can encapsulate `isActive` and `hasError` with their respective HTML classes:

```javascript
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

Here, `classObject` is a computed property, bindable to HTML like so:

```html
<div v-bind:class="classObject"></div>
```

### Using Arrays

To bind multiple classes, we can declare an array:

```html
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
```

```javascript
data: {
	isActive: true,
  activeClass: 'active',
  errorClass: 'text-danger'
}
```

Notice the use of a ternary expression to conditionally show `activeClass` - i.e. `active`. In this case, `errorClass` (i.e. `text-danger`) will always be applied. We can also nest objects within these arrays:

```html
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

### Behavior with Vue Components

Given a component like so:

```javascript
Vue.component('my-component', {
  template: '<p class="foo bar">Hi</p>'
})
```

Notice the classes `foo` and `bar` already applied. If we use this component:

```html
<my-component v-bind:class="{ active: isActive }"></my-component>
```

And `isActive` is truthy, then the final rendered HTML is:

```html
<p class="foo bar active">Hi</p>
```

**Thus, any additional classes declared on components will be added to the class list, not replacing the root template class list**



## Binding Inline Styles

The base directive is `v-bind:style`

We can declare CSS almost as is. We can reference CSS property names either like `fontSize` or `"font-size"` - Notice the use of quotes with the kebab-case variant. This is almost identical to the options given in binding classes, so we can do something like this:

```html
<div v-bind:style="styleObject"></div>
```

```javascript
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

As with binding classes, we can use arrays as well - this is useful when we want to compose HTML with different base CSS classes:

```html
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

In the case of vendor prefixes, such as:

```
-webkit-transition: all 4s ease;
-moz-transition: all 4s ease;
-ms-transition: all 4s ease;
-o-transition: all 4s ease;
transition: all 4s ease;
```

Vue will automatically inject the browser-specific variants, so all we need to do is define:

```javascript
data: {
	transition: "width 2s"
}
```

For prefixed values, we can pass an array of possible values:

```html
<div v-bind:style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

In this case, for browsers that do not support the `-webkit` and `-ms` options, `display: flex` will be rendered. 