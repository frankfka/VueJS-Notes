# 14 Components - Slots

Recall that `slot` allows you to insert inner HTML content given to a custom component. For example:

Given the template for `navigation-link`:

```html
<a
  v-bind:href="url"
  class="nav-link"
>
  <slot></slot>
</a>
```

And the component constructor:

```html
<navigation-link url="/profile">
  <!-- Add a Font Awesome icon -->
  <span class="fa fa-user"></span>
  Your Profile
</navigation-link>
```

Vue will render:

```html
<a
  v-bind:href="url"
  class="nav-link"
>
  <span class="fa fa-user"></span>
  Your Profile
</a>
```



## Scope of Passed Data

**Everything in the parent template is compiled in parent scope; everything in the child template is compiled in the child scope.**

Data defined **inside** the slot is within slope, but data defined outside the slot content (ex. in the `navigation-link` scope) is not within scope. So this is correct:

```html
<navigation-link url="/profile">
  Logged in as {{ user.name }}
</navigation-link>
```

But this isn't:

```html
<navigation-link url="/profile">
  Clicking here will send you to: {{ url }}
  <!--
  The `url` will be undefined, because this content is passed
  _to_ <navigation-link>, rather than defined _inside_ the
  <navigation-link> component.
  -->
</navigation-link>
```



## Default Content

We can define a fallback if we insert innerHTML within the slot:

```html
<button type="submit">
  <slot>Submit</slot>
</button>
```

The default in this case is `Submit` - so if nothing is passed, `Submit` will be rendered.



## Multiple Slots

To have multiple slots in a component, we can give them different names. A slot without a name has the name `default`:

```html
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot>Implicitly named default!</slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

We can then insert into these names using `<template>`. Anything not in `template` is inserted in the default slot:

```html
<base-layout>
  <template v-slot:header>
    <h1>Here might be a page title</h1>
  </template>

  <template v-slot:default>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </template>

  <template v-slot:footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
```

This creates the following:

```html
<div class="container">
  <header>
    <h1>Here might be a page title</h1>
  </header>
  <main>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </main>
  <footer>
    <p>Here's some contact info</p>
  </footer>
</div>
```

Recall that `<template>` is never actually rendered.

Slot names can also be dynamic:

```html
<base-layout>
  <template v-slot:[dynamicSlotName]>
    ...
  </template>
</base-layout>
```

## Scoped Slots

Let's say that we define `<current-user>` to be:

```html
<span>
  <slot>{{ user.lastName }}</slot>
</span>
```

So the backup "default" is `user.lastName`. But let's say we want to render something different - such as the firstName:

```html
<current-user>
  {{ user.firstName }}
</current-user>
```

But this won't work, because only the `current-user` component has access to `user`, thus the parent doesn't have access to `user`. We can fix this using `slot props`:

In the component, bind the user:

```html
<span>
  <slot v-bind:user="user">
    {{ user.lastName }}
  </slot>
</span>
```

In the parent, we can access the `user` (contained in the scope of the component), using `slotProps` - we can choose this name to our liking:

```html
<current-user>
  <template v-slot:default="slotProps">
    {{ slotProps.user.firstName }}
  </template>
</current-user>
```

If we only have one slot, we can abbreviate this further - replacing `v-slot:default` with `v-slot`:

```html
<current-user v-slot="slotProps">
  {{ slotProps.user.firstName }}
</current-user>
```

Alternatively, we can also use shorthand syntax. Instead of using `v-slot:NAME`, we can just use `#NAME` - but we can't omit `default`

```html
<base-layout>
  <template #header>
    <h1>Here might be a page title</h1>
  </template>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <template #footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
```

But note that you can't do this with named slots. Instead, you can do something like this:

```html
<current-user>
  <template v-slot:default="slotProps">
    {{ slotProps.user.firstName }}
  </template>

  <template v-slot:other="otherSlotProps">
    ...
  </template>
</current-user>
```

In fact, we can use any valid JS expression in the value of `v-slot`:

- ES2015 destructuring to pull out one property:

  ```html
  <current-user v-slot="{ user }">
    {{ user.firstName }}
  </current-user>
  ```

- Renaming (ex. `user` to `person`:

  ```html
  <current-user v-slot="{ user: person }">
    {{ person.firstName }}
  </current-user>
  ```

- Fallback content:

  ```html
  <current-user v-slot="{ user = { firstName: 'Guest' } }">
    {{ user.firstName }}
  </current-user>
  ```



## Slot Props - An Example

Consider a component `<todo-list>`:

```html
<ul>
  <li
    v-for="todo in filteredTodos"
    v-bind:key="todo.id"
  >
    <!--
    We have a slot for each todo, passing it the
    `todo` object as a slot prop.
    -->
    <slot name="todo" v-bind:todo="todo">
      <!-- Fallback content -->
      {{ todo.text }}
    </slot>
  </li>
</ul>
```

Notice that the default fallback is `todo.text`, and that we are declaring a slot prop named `todo`. In the parent component, we might want to customize the appearance of each todo:

```html
<todo-list v-bind:todos="todos">
  <template v-slot:todo="{ todo }">
    <span v-if="todo.isComplete">✓</span>
    {{ todo.text }}
  </template>
</todo-list>
```

Notice the use of `template` - as the `<slot>` element can only have one root element