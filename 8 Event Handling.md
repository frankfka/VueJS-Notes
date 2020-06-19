# 8 Event Handling

At the very basic level, we can use `v-on` to run some function when a certain event happens:

```html
<div id="example-1">
  <button v-on:click="counter += 1">Add 1</button>
  <p>The button above has been clicked {{ counter }} times.</p>
</div>
```

```javascript
var example1 = new Vue({
  el: '#example-1',
  data: {
    counter: 0
  }
})
```

Notice that we passed a simple JS expression, but we can also pass the name of a predefined method, in this case, `greet`:

```javascript
var example2 = new Vue({
  ...
  // define methods under the `methods` object
  methods: {
    greet: function (event) {
      // `this` inside methods points to the Vue instance
      alert('Hello ' + this.name + '!')
      // `event` is the native DOM event
      if (event) {
        alert(event.target.tagName)
      }
    }
    ...
  }
})
```

Or, we can use a JS expression that **uses a defined Vue method**:

```html
<div id="example-3">
  <button v-on:click="say('hi')">Say hi</button>
  <button v-on:click="say('what')">Say what</button>
</div>
```

```javascript
new Vue({
  el: '#example-3',
  methods: {
    say: function (message) {
      alert(message)
    }
  }
})
```

The native Javascript event can be accessed with `$event`, to be passed to a function call if needed:

```html
<button v-on:click="warn('Form cannot be submitted yet.', $event)">
  Submit
</button>
```

The `warn` function now has access to the `onClick` Javascript event.

### Event Modifier Shorthands

**Vue modifiers** are postfixes for directives. In the case of `v-on`, often we want to call functions such as `preventDefault()`. We can use a modifier to avoid having this in our business logic:

- `.stop`
- `.prevent`
- `.capture`
- `.self`
- `.once` - The event will be triggered at most once
- `.passive` - Improves scrolling performance, especially mobile, but should **not be used with `.prevent`**

Some examples:

```html
<!-- the click event's propagation will be stopped -->
<a v-on:click.stop="doThis"></a>

<!-- the submit event will no longer reload the page -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- modifiers can be chained -->
<a v-on:click.stop.prevent="doThat"></a>

<!-- just the modifier -->
<form v-on:submit.prevent></form>

<!-- use capture mode when adding the event listener -->
<!-- i.e. an event targeting an inner element is handled here before being handled by that element -->
<div v-on:click.capture="doThis">...</div>

<!-- only trigger handler if event.target is the element itself -->
<!-- i.e. not from a child element -->
<div v-on:click.self="doThat">...</div>
```

### Key Modifiers

Vue allows easy listening to keyboard key actions. For example:

```html
<input v-on:keyup.page-down="onPageDown">
```

The `page-down` refers to a key name in the following [doc](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key/Key_Values). They must be converted to `kebab-case` - recall that browsers are not great with maintaining case, which is why this decision was made.

We can also use the `.exact` modifier (to maintain that only the specified keys are pressed, and nothing else) and chain these modifiers for sequences of keys.