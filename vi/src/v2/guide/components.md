---
title: Components
type: guide
order: 11
---
## What are Components?

Components are one of the most powerful features of Vue. They help you extend basic HTML elements to encapsulate reusable code. At a high level, components are custom elements that Vue's compiler attaches behavior to. In some cases, they may also appear as a native HTML element extended with the special `is` attribute.

## Using Components

### Global Registration

We've learned in the previous sections that we can create a new Vue instance with:

```js
new Vue({
  el: '#some-element',
  // options
})
```

To register a global component, you can use `Vue.component(tagName, options)`. For example:

```js
Vue.component('my-component', {
  // options
})
```

<p class="tip">
  Note that Vue does not enforce the [W3C rules](https://www.w3.org/TR/custom-elements/#concepts) for custom tag names (all-lowercase, must contain a hyphen) though following this convention is considered good practice.
</p>

Once registered, a component can be used in an instance's template as a custom element, `<my-component></my-component>`. Make sure the component is registered **before** you instantiate the root Vue instance. Here's the full example:

```html
<div id="example">
  <my-component></my-component>
</div>
```

```js
// register
Vue.component('my-component', {
  template: '<div>A custom component!</div>'
})

// create a root instance
new Vue({
  el: '#example'
})
```

Which will render:

```html
<div id="example">
  <div>A custom component!</div>
</div>
```

{% raw %}

<div id="example" class="demo">
  <my-component></my-component>
</div> 

<script>
Vue.component('my-component', {
  template: '<div>A custom component!</div>'
})
new Vue({ el: '#example' })
</script>

 

{% endraw %}

### Local Registration

You don't have to register every component globally. You can make a component available only in the scope of another instance/component by registering it with the `components` instance option:

```js
var Child = {
  template: '<div>A custom component!</div>'
}

new Vue({
  // ...
  components: {
    // <my-component> will only be available in parent's template
    'my-component': Child
  }
})
```

The same encapsulation applies for other registerable Vue features, such as directives.

### DOM Template Parsing Caveats

When using the DOM as your template (e.g. using the `el` option to mount an element with existing content), you will be subject to some restrictions that are inherent to how HTML works, because Vue can only retrieve the template content **after** the browser has parsed and normalized it. Most notably, some elements such as `<ul>`, `<ol>`, `<table>` and `<select>` have restrictions on what elements can appear inside them, and some elements such as `<option>` can only appear inside certain other elements.

This will lead to issues when using custom components with elements that have such restrictions, for example:

```html
<table>
  <my-row>...</my-row>
</table>
```

The custom component `<my-row>` will be hoisted out as invalid content, thus causing errors in the eventual rendered output. A workaround is to use the `is` special attribute:

```html
<table>
  <tr is="my-row"></tr>
</table>
```

**It should be noted that these limitations do not apply if you are using string templates from one of the following sources**:

- `<script type="text/x-template">`
- JavaScript inline template strings
- `.vue` components

Therefore, prefer using string templates whenever possible.

### `data` Must Be a Function

Most of the options that can be passed into the Vue constructor can be used in a component, with one special case: `data` must be a function. In fact, if you try this:

```js
Vue.component('my-component', {
  template: '<span>{{ message }}</span>',
  data: {
    message: 'hello'
  }
})
```

Then Vue will halt and emit warnings in the console, telling you that `data` must be a function for component instances. It's good to understand why the rules exist though, so let's cheat.

```html
<div id="example-2">
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
</div>
```

```js
var data = { counter: 0 }

Vue.component('simple-counter', {
  template: '<button v-on:click="counter += 1">{{ counter }}</button>',
  // data is technically a function, so Vue won't
  // complain, but we return the same object
  // reference for each component instance
  data: function () {
    return data
  }
})

new Vue({
  el: '#example-2'
})
```

{% raw %}

<div id="example-2" class="demo">
  <simple-counter></simple-counter> <simple-counter></simple-counter> <simple-counter></simple-counter>
</div> 

<script>
var data = { counter: 0 }
Vue.component('simple-counter', {
  template: '<button v-on:click="counter += 1">{{ counter }}</button>',
  data: function () {
    return data
  }
})
new Vue({
  el: '#example-2'
})
</script>

 

{% endraw %}

Since all three component instances share the same `data` object, incrementing one counter increments them all! Ouch. Let's fix this by instead returning a fresh data object:

```js
data: function () {
  return {
    counter: 0
  }
}
```

Now all our counters each have their own internal state:

{% raw %}

<div id="example-2-5" class="demo">
  <my-component></my-component> <my-component></my-component> <my-component></my-component>
</div> 

<script>
Vue.component('my-component', {
  template: '<button v-on:click="counter += 1">{{ counter }}</button>',
  data: function () {
    return {
      counter: 0
    }
  }
})
new Vue({
  el: '#example-2-5'
})
</script>

 

{% endraw %}

### Composing Components

Components are meant to be used together, most commonly in parent-child relationships: component A may use component B in its own template. They inevitably need to communicate to one another: the parent may need to pass data down to the child, and the child may need to inform the parent of something that happened in the child. However, it is also very important to keep the parent and the child as decoupled as possible via a clearly-defined interface. This ensures each component's code can be written and reasoned about in relative isolation, thus making them more maintainable and potentially easier to reuse.

In Vue, the parent-child component relationship can be summarized as **props down, events up**. The parent passes data down to the child via **props**, and the child sends messages to the parent via **events**. Let's see how they work next.

<p style="text-align: center;">
  <img style="width: 300px;" src="/images/props-events.png" alt="props down, events up" />
</p>

## Props

### Passing Data with Props

Every component instance has its own **isolated scope**. This means you cannot (and should not) directly reference parent data in a child component's template. Data can be passed down to child components using **props**.

A prop is a custom attribute for passing information from parent components. A child component needs to explicitly declare the props it expects to receive using the [`props` option](../api/#props):

```js
Vue.component('child', {
  // declare the props
  props: ['message'],
  // like data, the prop can be used inside templates and
  // is also made available in the vm as this.message
  template: '<span>{{ message }}</span>'
})
```

Then we can pass a plain string to it like so:

```html
<child message="hello!"></child>
```

Result:

{% raw %}

<div id="prop-example-1" class="demo">
  <child message="hello!"></child>
</div> 

<script>
new Vue({
  el: '#prop-example-1',
  components: {
    child: {
      props: ['message'],
      template: '<span>{{ message }}</span>'
    }
  }
})
</script>

 

{% endraw %}

### camelCase vs. kebab-case

HTML attributes are case-insensitive, so when using non-string templates, camelCased prop names need to use their kebab-case (hyphen-delimited) equivalents:

```js
Vue.component('child', {
  // camelCase in JavaScript
  props: ['myMessage'],
  template: '<span>{{ myMessage }}</span>'
})
```

```html
<!-- kebab-case in HTML -->
<child my-message="hello!"></child>
```

Again, if you're using string templates, then this limitation does not apply.

### Dynamic Props

Similar to binding a normal attribute to an expression, we can also use `v-bind` for dynamically binding props to data on the parent. Whenever the data is updated in the parent, it will also flow down to the child:

```html
<div>
  <input v-model="parentMsg">
  <br>
  <child v-bind:my-message="parentMsg"></child>
</div>
```

You can also use the shorthand syntax for `v-bind`:

```html
<child :my-message="parentMsg"></child>
```

Result:

{% raw %}

<div id="demo-2" class="demo">
  <input v-model="parentMsg" /> <br /> <child v-bind:my-message="parentMsg"></child>
</div> 

<script>
new Vue({
  el: '#demo-2',
  data: {
    parentMsg: 'Message from parent'
  },
  components: {
    child: {
      props: ['myMessage'],
      template: '<span>{{myMessage}}</span>'
    }
  }
})
</script>

 

{% endraw %}

If you want to pass all the properties in an object as props, you can use `v-bind` without an argument (`v-bind` instead of `v-bind:prop-name`). For example, given a `todo` object:

```js
todo: {
  text: 'Learn Vue',
  isComplete: false
}
```

Then:

```html
<todo-item v-bind="todo"></todo-item>
```

Will be equivalent to:

```html
<todo-item
  v-bind:text="todo.text"
  v-bind:is-complete="todo.isComplete"
></todo-item>
```

### Literal vs. Dynamic

A common mistake beginners tend to make is attempting to pass down a number using the literal syntax:

```html
<!-- this passes down a plain string "1" -->
<comp some-prop="1"></comp>
```

However, since this is a literal prop, its value is passed down as a plain string `"1"` instead of an actual number. If we want to pass down an actual JavaScript number, we need to use `v-bind` so that its value is evaluated as a JavaScript expression:

```html
<!-- this passes down an actual number -->
<comp v-bind:some-prop="1"></comp>
```

### One-Way Data Flow

All props form a **one-way-down** binding between the child property and the parent one: when the parent property updates, it will flow down to the child, but not the other way around. This prevents child components from accidentally mutating the parent's state, which can make your app's data flow harder to understand.

In addition, every time the parent component is updated, all props in the child component will be refreshed with the latest value. This means you should **not** attempt to mutate a prop inside a child component. If you do, Vue will warn you in the console.

There are usually two cases where it's tempting to mutate a prop:

1. The prop is used to pass in an initial value; the child component wants to use it as a local data property afterwards.

2. The prop is passed in as a raw value that needs to be transformed.

The proper answer to these use cases are:

1. Define a local data property that uses the prop's initial value as its initial value:
    
    ```js
props: ['initialCounter'],
data: function () {
return { counter: this.initialCounter }
}
```

2. Define a computed property that is computed from the prop's value:
    
    ```js
props: ['size'],
computed: {
normalizedSize: function () {
  return this.size.trim().toLowerCase()
}
}
```

<p class="tip">
  Note that objects and arrays in JavaScript are passed by reference, so if the prop is an array or object, mutating the object or array itself inside the child **will** affect parent state.
</p>

### Prop Validation

It is possible for a component to specify requirements for the props it is receiving. If a requirement is not met, Vue will emit warnings. This is especially useful when you are authoring a component that is intended to be used by others.

Instead of defining the props as an array of strings, you can use an object with validation requirements:

```js
Vue.component('example', {
  props: {
    // basic type check (`null` means accept any type)
    propA: Number,
    // multiple possible types
    propB: [String, Number],
    // a required string
    propC: {
      type: String,
      required: true
    },
    // a number with default value
    propD: {
      type: Number,
      default: 100
    },
    // object/array defaults should be returned from a
    // factory function
    propE: {
      type: Object,
      default: function () {
        return { message: 'hello' }
      }
    },
    // custom validator function
    propF: {
      validator: function (value) {
        return value > 10
      }
    }
  }
})
```

The `type` can be one of the following native constructors:

- String
- Number
- Boolean
- Function
- Object
- Array
- Symbol

In addition, `type` can also be a custom constructor function and the assertion will be made with an `instanceof` check.

When prop validation fails, Vue will produce a console warning (if using the development build). Note that props are validated **before** a component instance is created, so within `default` or `validator` functions, instance properties such as from `data`, `computed`, or `methods` will not be available.

## Non-Prop Attributes

A non-prop attribute is an attribute that is passed to a component, but does not have a corresponding prop defined.

While explicitly defined props are preferred for passing information to a child component, authors of component libraries can't always foresee the contexts in which their components might be used. That's why components can accept arbitrary attributes, which are added to the component's root element.

For example, imagine we're using a 3rd-party `bs-date-input` component with a Bootstrap plugin that requires a `data-3d-date-picker` attribute on the `input`. We can add this attribute to our component instance:

```html
<bs-date-input data-3d-date-picker="true"></bs-date-input>
```

And the `data-3d-date-picker="true"` attribute will automatically be added to the root element of `bs-date-input`.

### Replacing/Merging with Existing Attributes

Imagine this is the template for `bs-date-input`:

```html
<input type="date" class="form-control">
```

To specify a theme for our date picker plugin, we might need to add a specific class, like this:

```html
<bs-date-input
  data-3d-date-picker="true"
  class="date-picker-theme-dark"
></bs-date-input>
```

In this case, two different values for `class` are defined:

- `form-control`, which is set by the component in its template
- `date-picker-theme-dark`, which is passed to the component by its parent

For most attributes, the value provided to the component will replace the value set by the component. So for example, passing `type="large"` will replace `type="date"` and probably break it! Fortunately, the `class` and `style` attributes are a little smarter, so both values are merged, making the final value: `form-control date-picker-theme-dark`.

## Custom Events

We have learned that the parent can pass data down to the child using props, but how do we communicate back to the parent when something happens? This is where Vue's custom event system comes in.

### Using `v-on` with Custom Events

Every Vue instance implements an [events interface](../api/#Instance-Methods-Events), which means it can:

- Listen to an event using `$on(eventName)`
- Trigger an event using `$emit(eventName)`

<p class="tip">
  Note that Vue's event system is different from the browser's [EventTarget API](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget). Though they work similarly, `$on` and `$emit` are __not__ aliases for `addEventListener` and `dispatchEvent`.
</p>

In addition, a parent component can listen to the events emitted from a child component using `v-on` directly in the template where the child component is used.

<p class="tip">
  You cannot use `$on` to listen to events emitted by children. You must use `v-on` directly in the template, as in the example below.
</p>

Here's an example:

```html
<div id="counter-event-example">
  <p>{{ total }}</p>
  <button-counter v-on:increment="incrementTotal"></button-counter>
  <button-counter v-on:increment="incrementTotal"></button-counter>
</div>
```

```js
Vue.component('button-counter', {
  template: '<button v-on:click="incrementCounter">{{ counter }}</button>',
  data: function () {
    return {
      counter: 0
    }
  },
  methods: {
    incrementCounter: function () {
      this.counter += 1
      this.$emit('increment')
    }
  },
})

new Vue({
  el: '#counter-event-example',
  data: {
    total: 0
  },
  methods: {
    incrementTotal: function () {
      this.total += 1
    }
  }
})
```

{% raw %}

<div id="counter-event-example" class="demo">
  <p>
    {{ total }}
  </p>
  
  <button-counter v-on:increment="incrementTotal"></button-counter> <button-counter v-on:increment="incrementTotal"></button-counter> </div> 

<script>
Vue.component('button-counter', {
  template: '<button v-on:click="incrementCounter">{{ counter }}</button>',
  data: function () {
    return {
      counter: 0
    }
  },
  methods: {
    incrementCounter: function () {
      this.counter += 1
      this.$emit('increment')
    }
  }
})
new Vue({
  el: '#counter-event-example',
  data: {
    total: 0
  },
  methods: {
    incrementTotal: function () {
      this.total += 1
    }
  }
})
</script>

 
  
  <p>
    {% endraw %}
  </p>
  
  <p>
    In this example, it's important to note that the child component is still completely decoupled from what happens outside of it. All it does is report information about its own activity, just in case a parent component might care.
  </p>
  
  <h3>
    Binding Native Events to Components
  </h3>
  
  <p>
    There may be times when you want to listen for a native event on the root element of a component. In these cases, you can use the <code>.native</code> modifier for <code>v-on</code>. For example:
  </p>
  
  <pre><code class="html">&lt;my-component v-on:click.native="doTheThing"&gt;&lt;/my-component&gt;
</code></pre>
  
  <h3>
    <code>.sync</code> Modifier
  </h3>
  
  <blockquote>
    <p>
      2.3.0+
    </p>
  </blockquote>
  
  <p>
    In some cases we may need "two-way binding" for a prop - in fact, in Vue 1.x this is exactly what the <code>.sync</code> modifier provided. When a child component mutates a prop that has <code>.sync</code>, the value change will be reflected in the parent. This is convenient, however it leads to maintenance issues in the long run because it breaks the one-way data flow assumption: the code that mutates child props are implicitly affecting parent state.
  </p>
  
  <p>
    This is why we removed the <code>.sync</code> modifier when 2.0 was released. However, we've found that there are indeed cases where it could be useful, especially when shipping reusable components. What we need to change is <strong>making the code in the child that affects parent state more consistent and explicit.</strong>
  </p>
  
  <p>
    In 2.3.0+ we re-introduced the <code>.sync</code> modifier for props, but this time it is only syntax sugar that automatically expands into an additional <code>v-on</code> listener:
  </p>
  
  <p>
    The following
  </p>
  
  <pre><code class="html">&lt;comp :foo.sync="bar"&gt;&lt;/comp&gt;
</code></pre>
  
  <p>
    is expanded into:
  </p>
  
  <pre><code class="html">&lt;comp :foo="bar" @update:foo="val =&gt; bar = val"&gt;&lt;/comp&gt;
</code></pre>
  
  <p>
    For the child component to update <code>foo</code>'s value, it needs to explicitly emit an event instead of mutating the prop:
  </p>
  
  <pre><code class="js">this.$emit('update:foo', newValue)
</code></pre>
  
  <h3>
    Form Input Components using Custom Events
  </h3>
  
  <p>
    Custom events can also be used to create custom inputs that work with <code>v-model</code>. Remember:
  </p>
  
  <pre><code class="html">&lt;input v-model="something"&gt;
</code></pre>
  
  <p>
    is syntactic sugar for:
  </p>
  
  <pre><code class="html">&lt;input
  v-bind:value="something"
  v-on:input="something = $event.target.value"&gt;
</code></pre>
  
  <p>
    When used with a component, it instead simplifies to:
  </p>
  
  <pre><code class="html">&lt;custom-input
  :value="something"
  @input="value =&gt; { something = value }"&gt;
&lt;/custom-input&gt;
</code></pre>
  
  <p>
    So for a component to work with <code>v-model</code>, it should (these can be configured in 2.2.0+):
  </p>
  
  <ul>
    <li>
      accept a <code>value</code> prop
    </li>
    <li>
      emit an <code>input</code> event with the new value
    </li>
  </ul>
  
  <p>
    Let's see it in action with a simple currency input:
  </p>
  
  <pre><code class="html">&lt;currency-input v-model="price"&gt;&lt;/currency-input&gt;
</code></pre>
  
  <pre><code class="js">Vue.component('currency-input', {
  template: '\
    &lt;span&gt;\
      $\
      &lt;input\
        ref="input"\
        v-bind:value="value"\
        v-on:input="updateValue($event.target.value)"&gt;\
    &lt;/span&gt;\
  ',
  props: ['value'],
  methods: {
    // Instead of updating the value directly, this
    // method is used to format and place constraints
    // on the input's value
    updateValue: function (value) {
      var formattedValue = value
        // Remove whitespace on either side
        .trim()
        // Shorten to 2 decimal places
        .slice(
          0,
          value.indexOf('.') === -1
            ? value.length
            : value.indexOf('.') + 3
        )
      // If the value was not already normalized,
      // manually override it to conform
      if (formattedValue !== value) {
        this.$refs.input.value = formattedValue
      }
      // Emit the number value through the input event
      this.$emit('input', Number(formattedValue))
    }
  }
})
</code></pre>
  
  <p>
    {% raw %}
  </p>
  
  <div id="currency-input-example" class="demo">
    <currency-input v-model="price"></currency-input>
  </div> 

<script>
Vue.component('currency-input', {
  template: '\
    <span>\
      $\
      <input\
        ref="input"\
        v-bind:value="value"\
        v-on:input="updateValue($event.target.value)"\
      >\
    </span>\
  ',
  props: ['value'],
  methods: {
    updateValue: function (value) {
      var formattedValue = value
        .trim()
        .slice(
          0,
          value.indexOf('.') === -1
            ? value.length
            : value.indexOf('.') + 3
        )
      if (formattedValue !== value) {
        this.$refs.input.value = formattedValue
      }
      this.$emit('input', Number(formattedValue))
    }
  }
})
new Vue({
  el: '#currency-input-example',
  data: { price: '' }
})
</script>

 
  
  <p>
    {% endraw %}
  </p>
  
  <p>
    The implementation above is pretty naive though. For example, users are allowed to enter multiple periods and even letters sometimes - yuck! So for those that want to see a non-trivial example, here's a more robust currency filter:
  </p> <iframe width="100%" height="300" src="https://jsfiddle.net/chrisvfritz/1oqjojjx/embedded/result,html,js" allowfullscreen="allowfullscreen" frameborder="0" mark="crwd-mark"></iframe> 
  
  <h3>
    Customizing Component <code>v-model</code>
  </h3>
  
  <blockquote>
    <p>
      New in 2.2.0+
    </p>
  </blockquote>
  
  <p>
    By default, <code>v-model</code> on a component uses <code>value</code> as the prop and <code>input</code> as the event, but some input types such as checkboxes and radio buttons may want to use the <code>value</code> prop for a different purpose. Using the <code>model</code> option can avoid the conflict in such cases:
  </p>
  
  <pre><code class="js">Vue.component('my-checkbox', {
  model: {
    prop: 'checked',
    event: 'change'
  },
  props: {
    checked: Boolean,
    // this allows using the `value` prop for a different purpose
    value: String
  },
  // ...
})
</code></pre>
  
  <pre><code class="html">&lt;my-checkbox v-model="foo" value="some value"&gt;&lt;/my-checkbox&gt;
</code></pre>
  
  <p>
    The above will be equivalent to:
  </p>
  
  <pre><code class="html">&lt;my-checkbox
  :checked="foo"
  @change="val =&gt; { foo = val }"
  value="some value"&gt;
&lt;/my-checkbox&gt;
</code></pre>
  
  <p class="tip">
    Note that you still have to declare the `checked` prop explicitly.
  </p>
  
  <h3>
    Non Parent-Child Communication
  </h3>
  
  <p>
    Sometimes two components may need to communicate with one-another but they are not parent/child to each other. In simple scenarios, you can use an empty Vue instance as a central event bus:
  </p>
  
  <pre><code class="js">var bus = new Vue()
</code></pre>
  
  <pre><code class="js">// in component A's method
bus.$emit('id-selected', 1)
</code></pre>
  
  <pre><code class="js">// in component B's created hook
bus.$on('id-selected', function (id) {
  // ...
})
</code></pre>
  
  <p>
    In more complex cases, you should consider employing a dedicated <a href="state-management.html">state-management pattern</a>.
  </p>
  
  <h2>
    Content Distribution with Slots
  </h2>
  
  <p>
    When using components, it is often desired to compose them like this:
  </p>
  
  <pre><code class="html">&lt;app&gt;
  &lt;app-header&gt;&lt;/app-header&gt;
  &lt;app-footer&gt;&lt;/app-footer&gt;
&lt;/app&gt;
</code></pre>
  
  <p>
    There are two things to note here:
  </p>
  
  <ol>
    <li>
      <p>
        The <code>&lt;app&gt;</code> component does not know what content it will receive. It is decided by the component using <code>&lt;app&gt;</code>.
      </p>
    </li>
    
    <li>
      <p>
        The <code>&lt;app&gt;</code> component very likely has its own template.
      </p>
    </li>
  </ol>
  
  <p>
    To make the composition work, we need a way to interweave the parent "content" and the component's own template. This is a process called <strong>content distribution</strong> (or "transclusion" if you are familiar with Angular). Vue.js implements a content distribution API that is modeled after the current <a href="https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md">Web Components spec draft</a>, using the special <code>&lt;slot&gt;</code> element to serve as distribution outlets for the original content.
  </p>
  
  <h3>
    Compilation Scope
  </h3>
  
  <p>
    Before we dig into the API, let's first clarify which scope the contents are compiled in. Imagine a template like this:
  </p>
  
  <pre><code class="html">&lt;child-component&gt;
  {{ message }}
&lt;/child-component&gt;
</code></pre>
  
  <p>
    Should the <code>message</code> be bound to the parent's data or the child data? The answer is the parent. A simple rule of thumb for component scope is:
  </p>
  
  <blockquote>
    <p>
      Everything in the parent template is compiled in parent scope; everything in the child template is compiled in child scope.
    </p>
  </blockquote>
  
  <p>
    A common mistake is trying to bind a directive to a child property/method in the parent template:
  </p>
  
  <pre><code class="html">&lt;!-- does NOT work --&gt;
&lt;child-component v-show="someChildProperty"&gt;&lt;/child-component&gt;
</code></pre>
  
  <p>
    Assuming <code>someChildProperty</code> is a property on the child component, the example above would not work. The parent's template is not aware of the state of a child component.
  </p>
  
  <p>
    If you need to bind child-scope directives on a component root node, you should do so in the child component's own template:
  </p>
  
  <pre><code class="js">Vue.component('child-component', {
  // this does work, because we are in the right scope
  template: '&lt;div v-show="someChildProperty"&gt;Child&lt;/div&gt;',
  data: function () {
    return {
      someChildProperty: true
    }
  }
})
</code></pre>
  
  <p>
    Similarly, distributed content will be compiled in the parent scope.
  </p>
  
  <h3>
    Single Slot
  </h3>
  
  <p>
    Parent content will be <strong>discarded</strong> unless the child component template contains at least one <code>&lt;slot&gt;</code> outlet. When there is only one slot with no attributes, the entire content fragment will be inserted at its position in the DOM, replacing the slot itself.
  </p>
  
  <p>
    Anything originally inside the <code>&lt;slot&gt;</code> tags is considered <strong>fallback content</strong>. Fallback content is compiled in the child scope and will only be displayed if the hosting element is empty and has no content to be inserted.
  </p>
  
  <p>
    Suppose we have a component called <code>my-component</code> with the following template:
  </p>
  
  <pre><code class="html">&lt;div&gt;
  &lt;h2&gt;I'm the child title&lt;/h2&gt;
  &lt;slot&gt;
    This will only be displayed if there is no content
    to be distributed.
  &lt;/slot&gt;
&lt;/div&gt;
</code></pre>
  
  <p>
    And a parent that uses the component:
  </p>
  
  <pre><code class="html">&lt;div&gt;
  &lt;h1&gt;I'm the parent title&lt;/h1&gt;
  &lt;my-component&gt;
    &lt;p&gt;This is some original content&lt;/p&gt;
    &lt;p&gt;This is some more original content&lt;/p&gt;
  &lt;/my-component&gt;
&lt;/div&gt;
</code></pre>
  
  <p>
    The rendered result will be:
  </p>
  
  <pre><code class="html">&lt;div&gt;
  &lt;h1&gt;I'm the parent title&lt;/h1&gt;
  &lt;div&gt;
    &lt;h2&gt;I'm the child title&lt;/h2&gt;
    &lt;p&gt;This is some original content&lt;/p&gt;
    &lt;p&gt;This is some more original content&lt;/p&gt;
  &lt;/div&gt;
&lt;/div&gt;
</code></pre>
  
  <h3>
    Named Slots
  </h3>
  
  <p>
    <code>&lt;slot&gt;</code> elements have a special attribute, <code>name</code>, which can be used to further customize how content should be distributed. You can have multiple slots with different names. A named slot will match any element that has a corresponding <code>slot</code> attribute in the content fragment.
  </p>
  
  <p>
    There can still be one unnamed slot, which is the <strong>default slot</strong> that serves as a catch-all outlet for any unmatched content. If there is no default slot, unmatched content will be discarded.
  </p>
  
  <p>
    For example, suppose we have an <code>app-layout</code> component with the following template:
  </p>
  
  <pre><code class="html">&lt;div class="container"&gt;
  &lt;header&gt;
    &lt;slot name="header"&gt;&lt;/slot&gt;
  &lt;/header&gt;
  &lt;main&gt;
    &lt;slot&gt;&lt;/slot&gt;
  &lt;/main&gt;
  &lt;footer&gt;
    &lt;slot name="footer"&gt;&lt;/slot&gt;
  &lt;/footer&gt;
&lt;/div&gt;
</code></pre>
  
  <p>
    Parent markup:
  </p>
  
  <pre><code class="html">&lt;app-layout&gt;
  &lt;h1 slot="header"&gt;Here might be a page title&lt;/h1&gt;

  &lt;p&gt;A paragraph for the main content.&lt;/p&gt;
  &lt;p&gt;And another one.&lt;/p&gt;

  &lt;p slot="footer"&gt;Here's some contact info&lt;/p&gt;
&lt;/app-layout&gt;
</code></pre>
  
  <p>
    The rendered result will be:
  </p>
  
  <pre><code class="html">&lt;div class="container"&gt;
  &lt;header&gt;
    &lt;h1&gt;Here might be a page title&lt;/h1&gt;
  &lt;/header&gt;
  &lt;main&gt;
    &lt;p&gt;A paragraph for the main content.&lt;/p&gt;
    &lt;p&gt;And another one.&lt;/p&gt;
  &lt;/main&gt;
  &lt;footer&gt;
    &lt;p&gt;Here's some contact info&lt;/p&gt;
  &lt;/footer&gt;
&lt;/div&gt;
</code></pre>
  
  <p>
    The content distribution API is a very useful mechanism when designing components that are meant to be composed together.
  </p>
  
  <h3>
    Scoped Slots
  </h3>
  
  <blockquote>
    <p>
      New in 2.1.0+
    </p>
  </blockquote>
  
  <p>
    A scoped slot is a special type of slot that functions as a reusable template (that can be passed data to) instead of already-rendered-elements.
  </p>
  
  <p>
    In a child component, pass data into a slot as if you are passing props to a component:
  </p>
  
  <pre><code class="html">&lt;div class="child"&gt;
  &lt;slot text="hello from child"&gt;&lt;/slot&gt;
&lt;/div&gt;
</code></pre>
  
  <p>
    In the parent, a <code>&lt;template&gt;</code> element with a special attribute <code>scope</code> must exist, indicating that it is a template for a scoped slot. The value of <code>scope</code> is the name of a temporary variable that holds the props object passed from the child:
  </p>
  
  <pre><code class="html">&lt;div class="parent"&gt;
  &lt;child&gt;
    &lt;template scope="props"&gt;
      &lt;span&gt;hello from parent&lt;/span&gt;
      &lt;span&gt;{{ props.text }}&lt;/span&gt;
    &lt;/template&gt;
  &lt;/child&gt;
&lt;/div&gt;
</code></pre>
  
  <p>
    If we render the above, the output will be:
  </p>
  
  <pre><code class="html">&lt;div class="parent"&gt;
  &lt;div class="child"&gt;
    &lt;span&gt;hello from parent&lt;/span&gt;
    &lt;span&gt;hello from child&lt;/span&gt;
  &lt;/div&gt;
&lt;/div&gt;
</code></pre>
  
  <p>
    A more typical use case for scoped slots would be a list component that allows the component consumer to customize how each item in the list should be rendered:
  </p>
  
  <pre><code class="html">&lt;my-awesome-list :items="items"&gt;
  &lt;!-- scoped slot can be named too --&gt;
  &lt;template slot="item" scope="props"&gt;
    &lt;li class="my-fancy-item"&gt;{{ props.text }}&lt;/li&gt;
  &lt;/template&gt;
&lt;/my-awesome-list&gt;
</code></pre>
  
  <p>
    And the template for the list component:
  </p>
  
  <pre><code class="html">&lt;ul&gt;
  &lt;slot name="item"
    v-for="item in items"
    :text="item.text"&gt;
    &lt;!-- fallback content here --&gt;
  &lt;/slot&gt;
&lt;/ul&gt;
</code></pre>
  
  <h2>
    Dynamic Components
  </h2>
  
  <p>
    You can use the same mount point and dynamically switch between multiple components using the reserved <code>&lt;component&gt;</code> element and dynamically bind to its <code>is</code> attribute:
  </p>
  
  <pre><code class="js">var vm = new Vue({
  el: '#example',
  data: {
    currentView: 'home'
  },
  components: {
    home: { /* ... */ },
    posts: { /* ... */ },
    archive: { /* ... */ }
  }
})
</code></pre>
  
  <pre><code class="html">&lt;component v-bind:is="currentView"&gt;
  &lt;!-- component changes when vm.currentView changes! --&gt;
&lt;/component&gt;
</code></pre>
  
  <p>
    If you prefer, you can also bind directly to component objects:
  </p>
  
  <pre><code class="js">var Home = {
  template: '&lt;p&gt;Welcome home!&lt;/p&gt;'
}

var vm = new Vue({
  el: '#example',
  data: {
    currentView: Home
  }
})
</code></pre>
  
  <h3>
    <code>keep-alive</code>
  </h3>
  
  <p>
    If you want to keep the switched-out components in memory so that you can preserve their state or avoid re-rendering, you can wrap a dynamic component in a <code>&lt;keep-alive&gt;</code> element:
  </p>
  
  <pre><code class="html">&lt;keep-alive&gt;
  &lt;component :is="currentView"&gt;
    &lt;!-- inactive components will be cached! --&gt;
  &lt;/component&gt;
&lt;/keep-alive&gt;
</code></pre>
  
  <p>
    Check out more details on <code>&lt;keep-alive&gt;</code> in the <a href="../api/#keep-alive">API reference</a>.
  </p>
  
  <h2>
    Misc
  </h2>
  
  <h3>
    Authoring Reusable Components
  </h3>
  
  <p>
    When authoring components, it's good to keep in mind whether you intend to reuse it somewhere else later. It's OK for one-off components to be tightly coupled, but reusable components should define a clean public interface and make no assumptions about the context it's used in.
  </p>
  
  <p>
    The API for a Vue component comes in three parts - props, events, and slots:
  </p>
  
  <ul>
    <li>
      <p>
        <strong>Props</strong> allow the external environment to pass data into the component
      </p>
    </li>
    <li>
      <p>
        <strong>Events</strong> allow the component to trigger side effects in the external environment
      </p>
    </li>
    <li>
      <p>
        <strong>Slots</strong> allow the external environment to compose the component with extra content.
      </p>
    </li>
  </ul>
  
  <p>
    With the dedicated shorthand syntaxes for <code>v-bind</code> and <code>v-on</code>, the intents can be clearly and succinctly conveyed in the template:
  </p>
  
  <pre><code class="html">&lt;my-component
  :foo="baz"
  :bar="qux"
  @event-a="doThis"
  @event-b="doThat"
&gt;
  &lt;img slot="icon" src="..."&gt;
  &lt;p slot="main-text"&gt;Hello!&lt;/p&gt;
&lt;/my-component&gt;
</code></pre>
  
  <h3>
    Child Component Refs
  </h3>
  
  <p>
    Despite the existence of props and events, sometimes you might still need to directly access a child component in JavaScript. To achieve this you have to assign a reference ID to the child component using <code>ref</code>. For example:
  </p>
  
  <pre><code class="html">&lt;div id="parent"&gt;
  &lt;user-profile ref="profile"&gt;&lt;/user-profile&gt;
&lt;/div&gt;
</code></pre>
  
  <pre><code class="js">var parent = new Vue({ el: '#parent' })
// access child component instance
var child = parent.$refs.profile
</code></pre>
  
  <p>
    When <code>ref</code> is used together with <code>v-for</code>, the ref you get will be an array containing the child components mirroring the data source.
  </p>
  
  <p class="tip">
    `$refs` are only populated after the component has been rendered, and it is not reactive. It is only meant as an escape hatch for direct child manipulation - you should avoid using `$refs` in templates or computed properties.
  </p>
  
  <h3>
    Async Components
  </h3>
  
  <p>
    In large applications, we may need to divide the app into smaller chunks and only load a component from the server when it's actually needed. To make that easier, Vue allows you to define your component as a factory function that asynchronously resolves your component definition. Vue will only trigger the factory function when the component actually needs to be rendered and will cache the result for future re-renders. For example:
  </p>
  
  <pre><code class="js">Vue.component('async-example', function (resolve, reject) {
  setTimeout(function () {
    // Pass the component definition to the resolve callback
    resolve({
      template: '&lt;div&gt;I am async!&lt;/div&gt;'
    })
  }, 1000)
})
</code></pre>
  
  <p>
    The factory function receives a <code>resolve</code> callback, which should be called when you have retrieved your component definition from the server. You can also call <code>reject(reason)</code> to indicate the load has failed. The <code>setTimeout</code> here is for demonstration; how to retrieve the component is up to you. One recommended approach is to use async components together with <a href="https://webpack.js.org/guides/code-splitting/">Webpack's code-splitting feature</a>:
  </p>
  
  <pre><code class="js">Vue.component('async-webpack-example', function (resolve) {
  // This special require syntax will instruct Webpack to
  // automatically split your built code into bundles which
  // are loaded over Ajax requests.
  require(['./my-async-component'], resolve)
})
</code></pre>
  
  <p>
    You can also return a <code>Promise</code> in the factory function, so with Webpack 2 + ES2015 syntax you can do:
  </p>
  
  <pre><code class="js">Vue.component(
  'async-webpack-example',
  () =&gt; import('./my-async-component')
)
</code></pre>
  
  <p>
    When using <a href="components.html#Local-Registration">local registration</a>, you can also directly provide a function that returns a <code>Promise</code>:
  </p>
  
  <pre><code class="js">new Vue({
  // ...
  components: {
    'my-component': () =&gt; import('./my-async-component')
  }
})
</code></pre>
  
  <p class="tip">
    If you're a <strong>Browserify</strong> user that would like to use async components, its creator has unfortunately [made it clear](https://github.com/substack/node-browserify/issues/58#issuecomment-21978224) that async loading "is not something that Browserify will ever support." Officially, at least. The Browserify community has found [some workarounds](https://github.com/vuejs/vuejs.org/issues/620), which may be helpful for existing and complex applications. For all other scenarios, we recommend using Webpack for built-in, first-class async support.
  </p>
  
  <h3>
    Advanced Async Components
  </h3>
  
  <blockquote>
    <p>
      New in 2.3.0+
    </p>
  </blockquote>
  
  <p>
    Starting in 2.3.0+ the async component factory can also return an object of the following format:
  </p>
  
  <pre><code class="js">const AsyncComp = () =&gt; ({
  // The component to load. Should be a Promise
  component: import('./MyComp.vue'),
  // A component to use while the async component is loading
  loading: LoadingComp,
  // A component to use if the load fails
  error: ErrorComp,
  // Delay before showing the loading component. Default: 200ms.
  delay: 200,
  // The error component will be displayed if a timeout is
  // provided and exceeded. Default: Infinity.
  timeout: 3000
})
</code></pre>
  
  <p>
    Note that when used as a route component in <code>vue-router</code>, these properties will be ignored because async components are resolved upfront before the route navigation happens. You also need to use <code>vue-router</code> 2.4.0+ if you wish to use the above syntax for route components.
  </p>
  
  <h3>
    Component Naming Conventions
  </h3>
  
  <p>
    When registering components (or props), you can use kebab-case, camelCase, or PascalCase.
  </p>
  
  <pre><code class="js">// in a component definition
components: {
  // register using kebab-case
  'kebab-cased-component': { /* ... */ },
  // register using camelCase
  'camelCasedComponent': { /* ... */ },
  // register using PascalCase
  'PascalCasedComponent': { /* ... */ }
}
</code></pre>
  
  <p>
    Within HTML templates though, you have to use the kebab-case equivalents:
  </p>
  
  <pre><code class="html">&lt;!-- always use kebab-case in HTML templates --&gt;
&lt;kebab-cased-component&gt;&lt;/kebab-cased-component&gt;
&lt;camel-cased-component&gt;&lt;/camel-cased-component&gt;
&lt;pascal-cased-component&gt;&lt;/pascal-cased-component&gt;
</code></pre>
  
  <p>
    When using <em>string</em> templates however, we're not bound by HTML's case-insensitive restrictions. That means even in the template, you can reference your components using:
  </p>
  
  <ul>
    <li>
      kebab-case
    </li>
    <li>
      camelCase or kebab-case if the component has been defined using camelCase
    </li>
    <li>
      kebab-case, camelCase or PascalCase if the component has been defined using PascalCase
    </li>
  </ul>
  
  <pre><code class="js">components: {
  'kebab-cased-component': { /* ... */ },
  camelCasedComponent: { /* ... */ },
  PascalCasedComponent: { /* ... */ }
}
</code></pre>
  
  <pre><code class="html">&lt;kebab-cased-component&gt;&lt;/kebab-cased-component&gt;

&lt;camel-cased-component&gt;&lt;/camel-cased-component&gt;
&lt;camelCasedComponent&gt;&lt;/camelCasedComponent&gt;

&lt;pascal-cased-component&gt;&lt;/pascal-cased-component&gt;
&lt;pascalCasedComponent&gt;&lt;/pascalCasedComponent&gt;
&lt;PascalCasedComponent&gt;&lt;/PascalCasedComponent&gt;
</code></pre>
  
  <p>
    This means that the PascalCase is the most universal <em>declaration convention</em> and kebab-case is the most universal <em>usage convention</em>.
  </p>
  
  <p>
    If your component isn't passed content via <code>slot</code> elements, you can even make it self-closing with a <code>/</code> after the name:
  </p>
  
  <pre><code class="html">&lt;my-component/&gt;
</code></pre>
  
  <p>
    Again, this <em>only</em> works within string templates, as self-closing custom elements are not valid HTML and your browser's native parser will not understand them.
  </p>
  
  <h3>
    Recursive Components
  </h3>
  
  <p>
    Components can recursively invoke themselves in their own template. However, they can only do so with the <code>name</code> option:
  </p>
  
  <pre><code class="js">name: 'unique-name-of-my-component'
</code></pre>
  
  <p>
    When you register a component globally using <code>Vue.component</code>, the global ID is automatically set as the component's <code>name</code> option.
  </p>
  
  <pre><code class="js">Vue.component('unique-name-of-my-component', {
  // ...
})
</code></pre>
  
  <p>
    If you're not careful, recursive components can also lead to infinite loops:
  </p>
  
  <pre><code class="js">name: 'stack-overflow',
template: '&lt;div&gt;&lt;stack-overflow&gt;&lt;/stack-overflow&gt;&lt;/div&gt;'
</code></pre>
  
  <p>
    A component like the above will result in a "max stack size exceeded" error, so make sure recursive invocation is conditional (i.e. uses a <code>v-if</code> that will eventually be <code>false</code>).
  </p>
  
  <h3>
    Circular References Between Components
  </h3>
  
  <p>
    Let's say you're building a file directory tree, like in Finder or File Explorer. You might have a <code>tree-folder</code> component with this template:
  </p>
  
  <pre><code class="html">&lt;p&gt;
  &lt;span&gt;{{ folder.name }}&lt;/span&gt;
  &lt;tree-folder-contents :children="folder.children"/&gt;
&lt;/p&gt;
</code></pre>
  
  <p>
    Then a <code>tree-folder-contents</code> component with this template:
  </p>
  
  <pre><code class="html">&lt;ul&gt;
  &lt;li v-for="child in children"&gt;
    &lt;tree-folder v-if="child.children" :folder="child"/&gt;
    &lt;span v-else&gt;{{ child.name }}&lt;/span&gt;
  &lt;/li&gt;
&lt;/ul&gt;
</code></pre>
  
  <p>
    When you look closely, you'll see that these components will actually be each other's descendent <em>and</em> ancestor in the render tree - a paradox! When registering components globally with <code>Vue.component</code>, this paradox is resolved for you automatically. If that's you, you can stop reading here.
  </p>
  
  <p>
    However, if you're requiring/importing components using a <strong>module system</strong>, e.g. via Webpack or Browserify, you'll get an error:
  </p>
  
  <pre><code>Failed to mount component: template or render function not defined.
</code></pre>
  
  <p>
    To explain what's happening, let's call our components A and B. The module system sees that it needs A, but first A needs B, but B needs A, but A needs B, etc, etc. It's stuck in a loop, not knowing how to fully resolve either component without first resolving the other. To fix this, we need to give the module system a point at which it can say, "A needs B <em>eventually</em>, but there's no need to resolve B first."
  </p>
  
  <p>
    In our case, let's make that point the <code>tree-folder</code> component. We know the child that creates the paradox is the <code>tree-folder-contents</code> component, so we'll wait until the <code>beforeCreate</code> lifecycle hook to register it:
  </p>
  
  <pre><code class="js">beforeCreate: function () {
  this.$options.components.TreeFolderContents = require('./tree-folder-contents.vue')
}
</code></pre>
  
  <p>
    Problem solved!
  </p>
  
  <h3>
    Inline Templates
  </h3>
  
  <p>
    When the <code>inline-template</code> special attribute is present on a child component, the component will use its inner content as its template, rather than treating it as distributed content. This allows more flexible template-authoring.
  </p>
  
  <pre><code class="html">&lt;my-component inline-template&gt;
  &lt;div&gt;
    &lt;p&gt;These are compiled as the component's own template.&lt;/p&gt;
    &lt;p&gt;Not parent's transclusion content.&lt;/p&gt;
  &lt;/div&gt;
&lt;/my-component&gt;
</code></pre>
  
  <p>
    However, <code>inline-template</code> makes the scope of your templates harder to reason about. As a best practice, prefer defining templates inside the component using the <code>template</code> option or in a <code>template</code> element in a <code>.vue</code> file.
  </p>
  
  <h3>
    X-Templates
  </h3>
  
  <p>
    Another way to define templates is inside of a script element with the type <code>text/x-template</code>, then referencing the template by an id. For example:
  </p>
  
  <pre><code class="html">&lt;script type="text/x-template" id="hello-world-template"&gt;
  &lt;p&gt;Hello hello hello&lt;/p&gt;
&lt;/script&gt;
</code></pre>
  
  <pre><code class="js">Vue.component('hello-world', {
  template: '#hello-world-template'
})
</code></pre>
  
  <p>
    These can be useful for demos with large templates or in extremely small applications, but should otherwise be avoided, because they separate templates from the rest of the component definition.
  </p>
  
  <h3>
    Cheap Static Components with <code>v-once</code>
  </h3>
  
  <p>
    Rendering plain HTML elements is very fast in Vue, but sometimes you might have a component that contains <strong>a lot</strong> of static content. In these cases, you can ensure that it's only evaluated once and then cached by adding the <code>v-once</code> directive to the root element, like this:
  </p>
  
  <pre><code class="js">Vue.component('terms-of-service', {
  template: '\
    &lt;div v-once&gt;\
      &lt;h1&gt;Terms of Service&lt;/h1&gt;\
      ... a lot of static content ...\
    &lt;/div&gt;\
  '
})
</code></pre>