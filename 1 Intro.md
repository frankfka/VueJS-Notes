# Intro to Vue

Include this in `index.html` to get the dev version of Vue:

```html
<!-- development version, includes helpful console warnings -->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```

We can now define a div, with id `app`:

```html
<div id="app">
  {{ message }}
</div>
```

The `{{ }}` is similar to string interpolation - "template syntax". We can now use Javascript to interact with this div:

```javascript
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
```

The `el` attribute is the selector to find the div, `data` contains all the view data required to render the element. We can also use `directives` to bind to HTML elements

```html
<div id="app-2">
  <span v-bind:title="message">
    Hover your mouse over me for a few seconds
    to see my dynamically bound title!
  </span>
</div>
```

```javascript
var app2 = new Vue({
  el: '#app-2',
  data: {
    message: 'You loaded this page on ' + new Date().toLocaleString()
  }
})
```

Notice that the javascript binds to the element `#app-2`. The HTML attribute `v-bind` is prefixed with `v-` to indicate relationship with Vue. We tell Vue that the title of the `span` element is controlled by the `message` attribute in `data`

We can also conditionally show elements, using `v-if`: `<span v-if="seen">Now you see me</span>`

We can also have loops, using `v-for`:

```html
<div id="app-4">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>
```

```javascript
var app4 = new Vue({
  el: '#app-4',
  data: {
    todos: [
      { text: 'Learn JavaScript' },
      { text: 'Learn Vue' },
      { text: 'Build something awesome' }
    ]
  }
})
```

User actions, such as click actions, can also be captured. For example:

```html
<div id="app-5">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">Reverse Message</button>
</div>
```

```javascript
var app5 = new Vue({
  el: '#app-5',
  data: {
    message: 'Hello Vue.js!'
  },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('')
    }
  }
})
```

Notice that we define `methods` in addition to `data`. We then pass the function to the `v-on:click` html attribute. Note the use of `this.` to reference data elements within the methods.

For forms and related data, we can have a 2-way binding as shown:

```html
<div id="app-6">
  <p>{{ message }}</p>
  <input v-model="message">
</div>
```

```javascript
var app6 = new Vue({
  el: '#app-6',
  data: {
    message: 'Hello Vue!'
  }
})
```

This results in `Hello Vue` to be shown initially. If you change the value of the input, then the text automatically changes as well. Vue also supports composition via `components` like so:

```javascript
Vue.component('todo-item', {
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})

var app7 = new Vue({
  el: '#app-7',
  data: {
    groceryList: [
      { id: 0, text: 'Vegetables' },
      { id: 1, text: 'Cheese' },
      { id: 2, text: 'Whatever else humans are supposed to eat' }
    ]
  }
})
```

```html
<div id="app-7">
  <ol>
    <!--
      Now we provide each todo-item with the todo object
      it's representing, so that its content can be dynamic.
      We also need to provide each component with a "key",
      which will be explained later.
    -->
    <todo-item
      v-for="item in groceryList"
      v-bind:todo="item"
      v-bind:key="item.id"
    ></todo-item>
  </ol>
</div>
```

We've now created a custom element called `todo-item`. We want to create an item for each item in some `groceryList` array, binding the item itself to the todo. Each item will have a `text` attribute to be shown when rendering.