# 13 Components - Custom Events

Recall that we listen to events like the following:

```html
<my-component v-on:my-event="doSomething"></my-component>
```

Where the event we're listening for, in this case, is `my-event`. Because events are only used in HTML attributes, the convention is to use **kebab-case**. Event names aren't transformed by nature, so defining an event as `myEvent` is incorrect - as HTML will convert `v-on:myEvent` to `v-on:myevent` - making `myEvent` impossible to listen to. 

## Events & v-model

Declaring `v-model` - i.e. a two way binding - creates `value` as a prop and `input` as the emitted event when the value changes. That being said, this may conflict with `<input>` types - such as checkboxes - as we may want to use `value` for a different purpose (???). To fix this, we declare the `model` property in the Vue component:

```javascript
Vue.component('base-checkbox', {
  model: {
    prop: 'checked',
    event: 'change'
  },
  props: {
    checked: Boolean
  },
  template: `
    <input
      type="checkbox"
      v-bind:checked="checked"
      v-on:change="$emit('change', $event.target.checked)"
    >
  `
})
```

```html
<base-checkbox v-model="lovingVue"></base-checkbox>
```

Now, using `v-model` will result in the `lovingVue` data attribute being updated with whatever the checkbox state is.

## Native Events

We can listen to native HTML events on the **root element** (i.e. outermost) by using `v-on:focus.native` (i.e. the .native modifier). However, this is often not a good idea, as this means that we can't wrap the component input with another HTML element. For example:

```html
<label>
  {{ label }}
  <input
    v-bind="$attrs"
    v-bind:value="value"
    v-on:input="$emit('input', $event.target.value)"
  >
</label>
```

Then using `v-on:focus.native` would not work, as the outer `<label>` does not gain focus - only the input does. As a solution, Vue forwards a property called `$listeners`, which contains all listeners that are being called on the object. For example, if we define `v-on:focus` on the component, then the `$listeners` property would contain:

```javascript
{
  focus: function (event) { /* ... */ }
}
```

This means that we can use `v-on="$listeners"` in the component's `input` element to "forward" listened-to events from the proper HTML element. Further, elements such as `input` are inherently two-way bindings - this means that we would want to use `v-model`. To make this work, we declare a custom `inputListeners` computed property, which merges the native `$listeners` with a custom `input` event, allowing `v-model` two-way binding to work properly:

```javascript
Vue.component('base-input', {
  inheritAttrs: false,
  props: ['label', 'value'],
  computed: {
    inputListeners: function () {
      var vm = this
      // `Object.assign` merges objects together to form a new object
      return Object.assign({},
        // We add all the listeners from the parent
        this.$listeners,
        // Then we can add custom listeners or override the
        // behavior of some listeners.
        {
          // This ensures that the component works with v-model
          input: function (event) {
            vm.$emit('input', event.target.value)
          }
        }
      )
    }
  },
  template: `
    <label>
      {{ label }}
      <input
        v-bind="$attrs"
        v-bind:value="value"
        v-on="inputListeners"
      >
    </label>
  `
})
```

## Alternative to v-model: .sync

`v-model` two-way bindings may be hard to maintain - as children views can modify parent properties. The child view would emit `update` events:

```javascript
this.$emit('update:title', newTitle)
```

And the parent view could listen to `update` events:

```html
<text-document
  v-bind:title="doc.title"
  v-on:update:title="doc.title = $event"
></text-document>
```

Vue offers a quick shorthand for this - this `.sync` modifier can be applied to the name of any **property or object property**:

```html
<text-document v-bind.sync="doc"></text-document>
```

This does:

- Passes each property in `doc` as an individual prop
- Adds `v-on:update` listeners for each property