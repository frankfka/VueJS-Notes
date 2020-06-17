# 6 Conditional Rendering

Conditionally renders an HTML block - if the parameter is falsy, then the block will not be rendered. We can optionally also specify `v-else` afterwards to have a fallback render. `v-else` **must** be right after a `v-if` or a `v-else-if` statement, or else it will have no effect:

```html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```

It's important to know that `v-if` can only be attached on a single HTML element. To toggle a block of elements, we wrap it in a `template` HTML element:

```html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

Although we've defined `template` in HTML - nothing will be rendered in the resulting HTML.

Vue does some magic to **reuse** instead of completely replace elements. This means that for the following example:

```html
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address">
</template>
```

When you toggle `loginType`, the value in the `input` is actually kept. This may be preferred behavior in some cases, but we can also tell Vue to **not reuse** elements by giving them a unique key:

```html
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username" key="username-input">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address" key="email-input">
</template>
```

In the above example, the value in the input is cleared when we change `loginType`, because the different `input` elements have different keys

Alternatively to `v-if`, we can use `v-show`. The difference is that **v-show will render the HTML but hide it, whereas v-if will create/destroy elements**. This means that we should do the following:

- Toggle something very often: use `v-show`
- Otherwise: use `v-if` 