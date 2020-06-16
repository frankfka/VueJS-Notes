# 3 Template Syntax

## Interpolation

### Text

The simplest bindings can be created using `mustache syntax`: 

```html
<span>Message: {{ msg }}</span>
```

We can also freeze a binding (i.e. get data once and then don't react to subsequent changes), using `v-once`:

```html
<span v-once>This will never change: {{ msg }}</span>
```

### Raw HTML

To render raw HTML, we use the `v-html` directive:

```html
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```

This will inject `data.rawHtml` into the span.

### HTML Attributes

We can also inject raw HTML attributes using `v-bind`:

```html
<div v-bind:id="dynamicId"></div>
```

This allows the div to have a dynamicId, set by the data object.

For **booleans**, we must use something different:

```html
<button v-bind:disabled="isButtonDisabled">Button</button>
```

If `isButtonDisabled` is falsy, then the button will be disabled.

### Javascript Expressions

We can use Javscript inside Moustache syntax as well as calls to directives, but given that we are using only **one expression**

The following will work:

```html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div v-bind:id="'list-' + id"></div>
```

But this won't:

```html
<!-- this is a statement, not an expression: -->
{{ var a = 1 }}

<!-- flow control won't work either, use ternary expressions -->
{{ if (ok) { return message } }}
```

A quick note: the template functions are rendered in a **sandbox**, so we would have access to a [certain number of globals](https://github.com/vuejs/vue/blob/v2.6.10/src/core/instance/proxy.js#L9), but not user defined globals.

### Arguments

Notice that directives such as `v-bind` and `v-on` take an **argument** - ex. `v-bind:href` - `href` is the argument here.  These can be dynamic. For example:

```html
<a v-on:[eventname]="doSomething"> ... </a>
```

Notice the use of `[]`. Here, `data.eventName` can be something like `click` or `hover`. Also notice the use of **lowercased** names - browsers will automatically lowercase the attribute names, so if we have `eventName` as the dynamic argument. It won't work.

### Modifiers

Consider a directive such as `v-on:submit`. We can **modify** this directive by using dot syntax:

```html
<form v-on:submit.prevent="onSubmit"> ... </form>
```

Here, we apply the `prevent` modifier, which is equivalent for calling `event.preventDefault()` on the triggered event.



## Shorthands

Using directives via `v-bind`, etc. is useful for verbosity if only certain DOM elements are injected by Vue. However, for a lot of apps, it's useful to replace this with a shorter, more concise version. This is available for two directives:

### v-bind

```html
<!-- full syntax -->
<a v-bind:href="url"> ... </a>

<!-- shorthand -->
<a :href="url"> ... </a>

<!-- shorthand with dynamic argument (2.6.0+) -->
<a :[key]="url"> ... </a>
```

### v-on

```html
<!-- full syntax -->
<a v-on:click="doSomething"> ... </a>

<!-- shorthand -->
<a @click="doSomething"> ... </a>

<!-- shorthand with dynamic argument (2.6.0+) -->
<a @[event]="doSomething"> ... </a>
```