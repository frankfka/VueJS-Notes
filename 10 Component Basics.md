# 10 Component Basics

Vue components are essentially nested Vue objects, with all the properties such as `data`, `computed`, `watch`, `methods`, etc. with the exception of root-specific options, such as `el`. 

We can define a component such as the following:

```javascript
// Define a new component called button-counter
Vue.component('button-counter', {
  data: function () {
    return {
      count: 0
    }
  },
  template: '<button v-on:click="count++">You clicked me {{ count }} times.</button>'
})
```

Following this, we can just use it as a regular HTML element:

```html
<div id="components-demo">
  <button-counter></button-counter>
</div>
```

Each time `button-counter` is created, a new instance with its own state is created. 

Notice that the `data` property is actually a function that returns the corresponding `data` object. This is so that a new instance can be created on the creation of each component. 

Notice that when you declare a new component via `Vue.component(..)`. We are registering a *global* component with Vue, which can be used in any root view and subcomponent of the root view. More on the details of registration later.

## Component Props

`Props` are data properties that we pass to component views. Notice that the `data` attribute of each component has a specific initial state. Thus, any items that have presence in the superview's data needs to be passed as props.

You declare the props required during component registration:

```javascript
Vue.component('blog-post', {
  props: ['title'],
  template: '<h3>{{ title }}</h3>'
})
```

We can then pass props as an HTML attribute:

```html
<blog-post title="My journey with Vue"></blog-post>
```

Alternatively, we can use `v-bind` to pass in the `title` attribute, this allows us to, for example, iterate through a list and pass a dynamic title attribute:

```html
<blog-post
  v-for="post in posts"
  v-bind:key="post.id"
  v-bind:title="post.title"
></blog-post>
```

In a more practical example, we would usually want multiple elements in a blog post, for example, the template might be:

```html
<div class="blog-post">
  <h3>{{ title }}</h3>
  <div v-html="content"></div>
</div>
```

Notice that we **wrap all the sub-elements in a div** - this is because each component can only have **one single root element**. 

To pass multiple elements as props, it is more standard to pass a single object:

```javascript
Vue.component('blog-post', {
  props: ['post'],
  template: `
    <div class="blog-post">
      <h3>{{ post.title }}</h3>
      <div v-html="post.content"></div>
    </div>
  `
})

// An example prop
{
  'title': 'Title',
  'content': 'Some content'
}
```



## Component Event Listeners

Consider the following:

```javascript
new Vue({
  el: '#blog-posts-events-demo',
  data: {
    posts: [/* ... */],
    postFontSize: 1
  }
})
```

```html
<div id="blog-posts-events-demo">
  <div :style="{ fontSize: postFontSize + 'em' }">
    <blog-post
      v-for="post in posts"
      v-bind:key="post.id"
      v-bind:post="post"
    ></blog-post>
  </div>
</div>
```

This uses the `blog-post` component that we define as the following:

```javascript
Vue.component('blog-post', {
  props: ['post'],
  template: `
    <div class="blog-post">
      <h3>{{ post.title }}</h3>
      <button>
        Enlarge text
      </button>
      <div v-html="post.content"></div>
    </div>
  `
})
```

Notice that in addition to the content, we have added a `button` to enlarge the text. The overall `fontSize` is controlled by the outder div, which holds `postFontSize`. We would like, on the press of the button, to increment this property.

To resolve this, we create a **listener** in the superview (i.e. `blog-posts-event-demo`) and an **emitter** in the `blog-post`:

```html
<blog-post
  ...
  v-on:enlarge-text="postFontSize += 0.1"
></blog-post>
```

Using `v-on`, we create a listener for the event `enlarge-text`. We then emit this event within `blog-post`:

```html
<button v-on:click="$emit('enlarge-text')">
  Enlarge text
</button>
```

## v-model with Components

When using a native HTML `input` element, using `v-model`:

```html
<input v-model="searchText">
```

This is a two-way binding, which is the same as:

```html
<input
  v-bind:value="searchText"
  v-on:input="searchText = $event.target.value"
>
```

Essentially, Vue is watching for changes in the value of the input. Now say we define a custom input component. Using `v-model` on the component:

```html
<custom-input v-model="searchText"></custom-input>
```

is equivalent to:

```html
<custom-input
  v-bind:value="searchText"
  v-on:input="searchText = $event"
></custom-input>
```

This means that for the custom input to work properly, we must define the following **within** the custom component:

```javascript
Vue.component('custom-input', {
  props: ['value'],
  template: `
    <input
      v-bind:value="value"
      v-on:input="$emit('input', $event.target.value)"
    >
  `
})
```

This is pretty much emitting the `input` action when the encapsulated input changes.

## Slots

Recall in HTML, we have content **within** a component:

```html
<p>
  Some Content
</p>
```

This is also achievable with Vue, using `slot`. 

```html
<alert-box>
  Something bad happened.
</alert-box>
```

```javascript
Vue.component('alert-box', {
  template: `
    <div class="demo-alert-box">
      <strong>Error!</strong>
      <slot></slot>
    </div>
  `
})
```

The `<slot>` will be replaced with "Something bad happened."

## Dynamic Components

Vue offers the opportunity to dynamically switch between components using the native `component` element and using `v-bind:is` directive:

```html
<!-- Component changes when currentTabComponent changes -->
<component v-bind:is="currentTabComponent"></component>
```

Here, `currentTabComponent` is either:

- the name of a registered component (i.e. `alert-box` in the above example)

- The component's options object: i.e. an *unregistered component object* that has all the properties required:

  ```javascript
  {
    template: "<div>Home component</div>"
  }
  ```

## Native HTML With Components

Native HTML elements such as `table`, `ul` etc. limit the type of the inner element. Similarly, elements such as `li` limit the type of the outer element. So, something like this wouldn't work:

```html
<table>
  <blog-post-row></blog-post-row>
</table>
```

This is because `table` can only hold `tr`, etc. As a work around, we use Vue's `is` attribute:

```html
<table>
  <tr is="blog-post-row"></tr>
</table>
```

You can think of this as "type casting" the custom Vue component into a `tr`.

