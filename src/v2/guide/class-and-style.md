---
title: Przypisywanie klas oraz stylów
type: guide
order: 6
---

Dzięki możliwości przypisywania danych, które umożliwia nam Vue.js możemy manipulować klasami elementów oraz inline'owymi stylami. Ponieważ są to zwykłe atrybuty, możemy użyć `v-bind` żeby je obsłużyć. Tak naprawdę ostatecznie wynikiem takiego przypisania będzie ciąg znaków, wygenerowany przez instrukcje warunkowe. Jak wiadomo, operowanie na takich danych może powodować wiele problemów oraz generować błędy.

Właśnie dlatego twórcy Vue.js zadali sobie trud rozwiązania tego problemu i ułatwili programistom pracę poprzez stosowanie obiektów czy też tablic jako danych obsługujących przypisywanie klas oraz stylów.

## Przypisywanie klas HTML

### Składnia obiektu

Aby dynamicznie przełączać się między klasami, możemy przekazać obiekt do atrybutu `v-bind:class`: 

``` html
<div v-bind:class="{ active: isActive }"></div>
```

Powyższy kod oznacza, że klasa `active` zostanie dodana do elementu HTML tylko wtedy, gdy zmienna `isActive` zwróci wartość `true`.

You can have multiple classes toggled by having more fields in the object. In addition, the `v-bind:class` directive can also co-exist with the plain `class` attribute. So given the following template:

``` html
<div class="static"
     v-bind:class="{ active: isActive, 'text-danger': hasError }">
</div>
```

And the following data:

``` js
data: {
  isActive: true,
  hasError: false
}
```

It will render:

``` html
<div class="static active"></div>
```

When `isActive` or `hasError` changes, the class list will be updated accordingly. For example, if `hasError` becomes `true`, the class list will become `"static active text-danger"`.

The bound object doesn't have to be inline:

``` html
<div v-bind:class="classObject"></div>
```
``` js
data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
```

This will render the same result. We can also bind to a [computed property](computed.html) that returns an object. This is a common and powerful pattern:

``` html
<div v-bind:class="classObject"></div>
```
``` js
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal',
    }
  }
}
```

### Array Syntax

We can pass an array to `v-bind:class` to apply a list of classes:

``` html
<div v-bind:class="[activeClass, errorClass]">
```
``` js
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```

Which will render:

``` html
<div class="active text-danger"></div>
```

If you would like to also toggle a class in the list conditionally, you can do it with a ternary expression:

``` html
<div v-bind:class="[isActive ? activeClass : '', errorClass]">
```

This will always apply `errorClass`, but will only apply `activeClass` when `isActive` is `true`.

However, this can be a bit verbose if you have multiple conditional classes. That's why it's also possible to use the object syntax inside array syntax:

``` html
<div v-bind:class="[{ active: isActive }, errorClass]">
```

### With Components

> This section assumes knowledge of [Vue Components](components.html). Feel free to skip it and come back later.

When you use the `class` attribute on a custom component, those classes will be added to the component's root element. Existing classes on this element will not be overwritten.

For example, if you declare this component:

``` js
Vue.component('my-component', {
  template: '<p class="foo bar">Hi</p>'
})
```

Then add some classes when using it:

``` html
<my-component class="baz boo"></my-component>
```

The rendered HTML will be:

``` html
<p class="foo bar baz boo">Hi</p>
```

The same is true for class bindings:

``` html
<my-component v-bind:class="{ active: isActive }"></my-component>
```

When `isActive` is truthy, the rendered HTML will be:

``` html
<p class="foo bar active">Hi</p>
```

## Binding Inline Styles

### Object Syntax

The object syntax for `v-bind:style` is pretty straightforward - it looks almost like CSS, except it's a JavaScript object. You can use either camelCase or kebab-case (use quotes with kebab-case) for the CSS property names:

``` html
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```
``` js
data: {
  activeColor: 'red',
  fontSize: 30
}
```

It is often a good idea to bind to a style object directly so that the template is cleaner:

``` html
<div v-bind:style="styleObject"></div>
```
``` js
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

Again, the object syntax is often used in conjunction with computed properties that return objects.

### Array Syntax

The array syntax for `v-bind:style` allows you to apply multiple style objects to the same element:

``` html
<div v-bind:style="[baseStyles, overridingStyles]">
```

### Auto-prefixing

When you use a CSS property that requires [vendor prefixes](https://developer.mozilla.org/en-US/docs/Glossary/Vendor_Prefix) in `v-bind:style`, for example `transform`, Vue will automatically detect and add appropriate prefixes to the applied styles.

### Multiple Values

> 2.3.0+

Starting in 2.3 you can provide an array of multiple (prefixed) values to a style property, for example:

``` html
<div :style="{ display: ["-webkit-box", "-ms-flexbox", "flex"] }">
```
