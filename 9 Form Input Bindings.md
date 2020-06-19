# 9 Form Input Bindings

`v-model` provides a two way binding, which is useful for handling form data. `v-model` adapts based n the input used:

- text and textarea elements use `value` property and `input` event;
- checkboxes and radiobuttons use `checked` property and `change` event;
- select fields use `value` as a prop and `change` as an event.

For example, we can create a list of checkboxes an bind it to an array:

```html
<input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
<label for="jack">Jack</label>
<input type="checkbox" id="john" value="John" v-model="checkedNames">
<label for="john">John</label>
<input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
<label for="mike">Mike</label>
<br>
<span>Checked names: {{ checkedNames }}</span>
```

```javascript
new Vue({
  el: '...',
  data: {
    checkedNames: []
  }
})
```

Note that the `value` holds the item names. So when you check a specific checkbox, for example, the first one, "Jack" will be appended to `checkedNames`. 

**Important**: For `<select>` elements, if the initial data state does not match any option, the rendered element will be in an `unselected` state, creating undefined behavior. So it is always recommended to hava a default, disabled option:

```html
<select v-model="selected">
  <option disabled value="">Please select one</option>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
<span>Selected: {{ selected }}</span>
```

```javascript
new Vue({
  el: '...',
  data: {
    selected: ''
  }
})
```

This behavior can be combined with `v-for` to have dynamic options. This is an example:

```html
<select v-model="selected">
  <option v-for="option in options" v-bind:value="option.value">
    {{ option.text }}
  </option>
</select>
<span>Selected: {{ selected }}</span>
```

```javascript
new Vue({
  el: '...',
  data: {
    selected: 'A',
    options: [
      { text: 'One', value: 'A' },
      { text: 'Two', value: 'B' },
      { text: 'Three', value: 'C' }
    ]
  }
})
```

We can then add/remove from `options` to change the options available to the user. There's a lot more options available for form bindings. A comprehensive list can be found [here](https://vuejs.org/v2/guide/forms.html). Vue also supports using `v-model` in custom inputs (i.e. defined as components)

### Modifiers

Some helpful `v-model` postfixes are available:

- **.lazy**: Usually, `v-model` syncs to the viewmodel after each `input` event. Adding `v-model.lazy` causes the viewmodel to sync after `change` events
- **.number**: Vue will return the value as a number using `parseFloat()`, or the original input string if this was unsuccessful
- **.trim**: Vue will trim whitespace from the input