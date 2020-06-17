# 7 List Rendering

### For Ranges

The most basic `v-for` can be used in a range:

```html
<div>
  <span v-for="n in 10">{{ n }} </span>
</div>
```

This will print numbers from 1 to 10

### For Lists

We use the `v-for` directive to transform a Javascript array to a sequence of elements.

```html
<ul id="example-1">
  <li v-for="item in items" :key="item.message">
    {{ item.message }}
  </li>
</ul>
```

```javascript
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```

This will create two list items: Foo and Bar. We can additionally retrieve the index in the array using: `v-for="(item, index) in items"`

### For Objects

We can also use `v-for` to iterate through the keys and values of an object. 

```html
<ul id="v-for-object" class="demo">
  <li v-for="value in object">
    {{ value }}
  </li>
</ul>
```

```javascript
new Vue({
  el: '#v-for-object',
  data: {
    object: {
      title: 'How to do lists in Vue',
      author: 'Jane Doe',
      publishedAt: '2016-04-10'
    }
  }
})
```

This will render a list of all the values: "How to do lists in Vue", "Jane Doe", and "2016-04-10"

To retrieve the keys, we can use: `v-for="(value, name) in object"`

### Keys with V-For

Vue has some default behavior when the ordering of the list changes. In general, it's good practice to give each array item a `key` value - based on the objects that we are iterating through:

```html
<div v-for="item in items" v-bind:key="item.id">
  <!-- content -->
</div>
```

Here, each `item` has an `id` attribute to identify a specific item within the array. These keys should be primitives - such as strings or numerics.

### Detecting Changes in Arrays

The following array operations, when performed on the underlying data object, will trigger view changes:

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

In cases where we need to replace the entire array, for example: 

```javascript
example1.items = example1.items.filter(function (item) {
  return item.message.match(/Foo/)
})
```

Vue will re-render the content, and it's smart enough to maximize the reuse of some elements. So don't be afraid of performance implications with replacing arrays.

### Computed Properties / Methods on Arrays

`v-for` will work with computed properties, which is useful when we want to maintain the underlying data. for example:

```html
<li v-for="n in evenNumbers">{{ n }}</li>
```

```javascript
data: {
  numbers: [ 1, 2, 3, 4, 5 ]
},
computed: {
  evenNumbers: function () {
    return this.numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

There may be cases where we can't use a computed property. We can then use a `method`:

```html
<ul v-for="set in sets">
  <li v-for="n in even(set)">{{ n }}</li>
</ul>
```

```javascript
data: {
  sets: [[ 1, 2, 3, 4, 5 ], [6, 7, 8, 9, 10]]
},
methods: {
  even: function (numbers) {
    return numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

Notice that this can't be easily done with a computed property.

### Templating `V-For`

Recall with `v-if` that we can toggle a block with the `template` HTML element. We can do something similar with `v-for`, causing a block to be iterated once per loop:

```html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

### Note on `v-for` with `v-if`

While v-for can work with v-if, as shown [here](https://vuejs.org/v2/guide/list.html#v-for-with-v-if), We should use alternate solutions as demonstrated in the [style guide](https://vuejs.org/v2/style-guide/#Avoid-v-if-with-v-for-essential)

### With Components

Say you define a component `my-component` to be displayed once per `item` given an array of `items`. We can use this:

```html
<my-component v-for="item in items" :key="item.id"></my-component>
```

But `my-component` will **not** have access to the `item` object. To give access, explicitly bind the `item` object like so:

```html
<my-component
  v-for="(item, index) in items"
  v-bind:item="item"
  v-bind:index="index"
  v-bind:key="item.id"
></my-component>
```

A quick note: `key` is required for components.

