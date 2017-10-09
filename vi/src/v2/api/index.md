---
type: api
---
## Global Config

`Vue.config` is an object containing Vue's global configurations. You can modify its properties listed below before bootstrapping your application:

### silent

- **Type:** `boolean`

- **Default:** `false`

- **Usage:**
  
  ```js
Vue.config.silent = true
```

Suppress all Vue logs and warnings.

### optionMergeStrategies

- **Type:** `{ [key: string]: Function }`

- **Default:** `{}`

- **Usage:**
  
  ```js
Vue.config.optionMergeStrategies._my_option = function (parent, child, vm) {
  return child + 1
}

const Profile = Vue.extend({
  _my_option: 1
})

// Profile.options._my_option = 2
```

Define custom merging strategies for options.

The merge strategy receives the value of that option defined on the parent and child instances as the first and second arguments, respectively. The context Vue instance is passed as the third argument.

- **See also:** [Custom Option Merging Strategies](../guide/mixins.html#Custom-Option-Merge-Strategies)

### devtools

- **Type:** `boolean`

- **Default:** `true` (`false` in production builds)

- **Usage:**
  
  ```js
// make sure to set this synchronously immediately after loading Vue
Vue.config.devtools = true
```

Configure whether to allow [vue-devtools](https://github.com/vuejs/vue-devtools) inspection. This option's default value is `true` in development builds and `false` in production builds. You can set it to `true` to enable inspection for production builds.

### errorHandler

- **Type:** `Function`

- **Default:** `undefined`

- **Usage:**
  
  ```js
Vue.config.errorHandler = function (err, vm, info) {
  // handle error
  // `info` is a Vue-specific error info, e.g. which lifecycle hook
  // the error was found in. Only available in 2.2.0+
}
```

Assign a handler for uncaught errors during component render function and watchers. The handler gets called with the error and the Vue instance.

> In 2.2.0+, this hook also captures errors in component lifecycle hooks. Also, when this hook is `undefined`, captured errors will be logged with `console.error` instead of crashing the app.
> 
> In 2.4.0+ this hook also captures errors thrown inside Vue custom event handlers.
> 
> [Sentry](https://sentry.io), an error tracking service, provides [official integration](https://sentry.io/for/vue/) using this option.

### warnHandler

> New in 2.4.0+

- **Type:** `Function`

- **Default:** `undefined`

- **Usage:**
  
  ```js
Vue.config.warnHandler = function (msg, vm, trace) {
  // `trace` is the component hierarchy trace
}
```

Assign a custom handler for runtime Vue warnings. Note this only works during development and is ignored in production.

### ignoredElements

- **Type:** `Array<string>`

- **Default:** `[]`

- **Usage:**
  
  ```js
Vue.config.ignoredElements = [
  'my-custom-web-component', 'another-web-component'
]
```

Make Vue ignore custom elements defined outside of Vue (e.g., using the Web Components APIs). Otherwise, it will throw a warning about an `Unknown custom element`, assuming that you forgot to register a global component or misspelled a component name.

### keyCodes

- **Type:** `{ [key: string]: number | Array<number> }`

- **Default:** `{}`

- **Usage:**
  
  ```js
Vue.config.keyCodes = {
  v: 86,
  f1: 112,
  // camelCase won`t work
  mediaPlayPause: 179,
  // instead you can use kebab-case with double quotation marks
  "media-play-pause": 179,
  up: [38, 87]
}
```

```html
<input type="text" @keyup.media-play-pause="method">
```

Define custom key alias(es) for `v-on`.

### performance

> New in 2.2.0+

- **Type:** `boolean`

- **Default:** `false (from 2.2.3+)`

- **Usage**:
  
  Set this to `true` to enable component init, compile, render and patch performance tracing in the browser devtool timeline. Only works in development mode and in browsers that support the [performance.mark](https://developer.mozilla.org/en-US/docs/Web/API/Performance/mark) API.

### productionTip

> New in 2.2.0+

- **Type:** `boolean`

- **Default:** `true`

- **Usage**:
  
  Set this to `false` to prevent the production tip on Vue startup.

## Global API

<h3 id="Vue-extend">Vue.extend( options )</h3>

- **Arguments:**
  
  - `{Object} options`

- **Usage:**
  
  Create a "subclass" of the base Vue constructor. The argument should be an object containing component options.
  
  The special case to note here is the `data` option - it must be a function when used with `Vue.extend()`.
  
  ```html
<div id="mount-point"></div>
```

```js
// create constructor
var Profile = Vue.extend({
  template: '<p>{{firstName}} {{lastName}} aka {{alias}}</p>',
  data: function () {
    return {
      firstName: 'Walter',
      lastName: 'White',
      alias: 'Heisenberg'
    }
  }
})
// create an instance of Profile and mount it on an element
new Profile().$mount('#mount-point')
```

Will result in:

```html
<p>Walter White aka Heisenberg</p>
```

- **See also:** [Components](../guide/components.html)

<h3 id="Vue-nextTick">Vue.nextTick( [callback, context] )</h3>

- **Arguments:**
  
  - `{Function} [callback]`
  - `{Object} [context]`

- **Usage:**
  
  Defer the callback to be executed after the next DOM update cycle. Use it immediately after you've changed some data to wait for the DOM update.
  
  ```js
// modify data
vm.msg = 'Hello'
// DOM not updated yet
Vue.nextTick(function () {
  // DOM updated
})
```

> New in 2.1.0+: returns a Promise if no callback is provided and Promise is supported in the execution environment. Please note that Vue does not come with a Promise polyfill, so if you target browsers that don't support Promises natively (looking at you, IE), you will have to provide a polyfill yourself.

- **See also:** [Async Update Queue](../guide/reactivity.html#Async-Update-Queue)

<h3 id="Vue-set">Vue.set( target, key, value )</h3>

- **Arguments:**
  
  - `{Object | Array} target`
  - `{string | number} key`
  - `{any} value`

- **Returns:** the set value.

- **Usage:**
  
  Set a property on an object. If the object is reactive, ensure the property is created as a reactive property and trigger view updates. This is primarily used to get around the limitation that Vue cannot detect property additions.
  
  **Note the object cannot be a Vue instance, or the root data object of a Vue instance.**

- **See also:** [Reactivity in Depth](../guide/reactivity.html)

<h3 id="Vue-delete">Vue.delete( target, key )</h3>

- **Arguments:**
  
  - `{Object | Array} target`
  - `{string | number} key/index`
  
  > Only in 2.2.0+: Also works with Array + index.

- **Usage:**
  
  Delete a property on an object. If the object is reactive, ensure the deletion triggers view updates. This is primarily used to get around the limitation that Vue cannot detect property deletions, but you should rarely need to use it.

<p class="tip">
  The target object cannot be a Vue instance, or the root data object of a Vue instance.
</p>

- **See also:** [Reactivity in Depth](../guide/reactivity.html)

<h3 id="Vue-directive">Vue.directive( id, [definition] )</h3>

- **Arguments:**
  
  - `{string} id`
  - `{Function | Object} [definition]`

- **Usage:**
  
  Register or retrieve a global directive.
  
  ```js
// register
Vue.directive('my-directive', {
  bind: function () {},
  inserted: function () {},
  update: function () {},
  componentUpdated: function () {},
  unbind: function () {}
})

// register (function directive)
Vue.directive('my-directive', function () {
  // this will be called as `bind` and `update`
})

// getter, return the directive definition if registered
var myDirective = Vue.directive('my-directive')
```

- **See also:** [Custom Directives](../guide/custom-directive.html)

<h3 id="Vue-filter">Vue.filter( id, [definition] )</h3>

- **Arguments:**
  
  - `{string} id`
  - `{Function} [definition]`

- **Usage:**
  
  Register or retrieve a global filter.
  
  ```js
// register
Vue.filter('my-filter', function (value) {
  // return processed value
})

// getter, return the filter if registered
var myFilter = Vue.filter('my-filter')
```

<h3 id="Vue-component">Vue.component( id, [definition] )</h3>

- **Arguments:**
  
  - `{string} id`
  - `{Function | Object} [definition]`

- **Usage:**
  
  Register or retrieve a global component. Registration also automatically sets the component's `name` with the given `id`.
  
  ```js
// register an extended constructor
Vue.component('my-component', Vue.extend({ /* ... */ }))

// register an options object (automatically call Vue.extend)
Vue.component('my-component', { /* ... */ })

// retrieve a registered component (always return constructor)
var MyComponent = Vue.component('my-component')
```

- **See also:** [Components](../guide/components.html)

<h3 id="Vue-use">Vue.use( plugin )</h3>

- **Arguments:**
  
  - `{Object | Function} plugin`

- **Usage:**
  
  Install a Vue.js plugin. If the plugin is an Object, it must expose an `install` method. If it is a function itself, it will be treated as the install method. The install method will be called with Vue as the argument.
  
  When this method is called on the same plugin multiple times, the plugin will be installed only once.

- **See also:** [Plugins](../guide/plugins.html)

<h3 id="Vue-mixin">Vue.mixin( mixin )</h3>

- **Arguments:**
  
  - `{Object} mixin`

- **Usage:**
  
  Apply a mixin globally, which affects every Vue instance created afterwards. This can be used by plugin authors to inject custom behavior into components. **Not recommended in application code**.

- **See also:** [Global Mixin](../guide/mixins.html#Global-Mixin)

<h3 id="Vue-compile">Vue.compile( template )</h3>

- **Arguments:**
  
  - `{string} template`

- **Usage:**
  
  Compiles a template string into a render function. **Only available in the full build.**
  
  ```js
var res = Vue.compile('<div><span>{{ msg }}</span></div>')

new Vue({
  data: {
    msg: 'hello'
  },
  render: res.render,
  staticRenderFns: res.staticRenderFns
})
```

- **See also:** [Render Functions](../guide/render-function.html)

<h3 id="Vue-version">Vue.version</h3>

- **Details**: Provides the installed version of Vue as a string. This is especially useful for community plugins and components, where you might use different strategies for different versions.

- **Usage**:
  
  ```js
var version = Number(Vue.version.split('.')[0])

if (version === 2) {
  // Vue v2.x.x
} else if (version === 1) {
  // Vue v1.x.x
} else {
  // Unsupported versions of Vue
}
```

## Options / Data

### data

- **Type:** `Object | Function`

- **Restriction:** Only accepts `Function` when used in a component definition.

- **Details:**
  
  The data object for the Vue instance. Vue will recursively convert its properties into getter/setters to make it "reactive". **The object must be plain**: native objects such as browser API objects and prototype properties are ignored. A rule of thumb is that data should just be data - it is not recommended to observe objects with their own stateful behavior.
  
  Once observed, you can no longer add reactive properties to the root data object. It is therefore recommended to declare all root-level reactive properties upfront, before creating the instance.
  
  After the instance is created, the original data object can be accessed as `vm.$data`. The Vue instance also proxies all the properties found on the data object, so `vm.a` will be equivalent to `vm.$data.a`.
  
  Properties that start with `_` or `$` will **not** be proxied on the Vue instance because they may conflict with Vue's internal properties and API methods. You will have to access them as `vm.$data._property`.
  
  When defining a **component**, `data` must be declared as a function that returns the initial data object, because there will be many instances created using the same definition. If we use a plain object for `data`, that same object will be **shared by reference** across all instances created! By providing a `data` function, every time a new instance is created we can call it to return a fresh copy of the initial data.
  
  If required, a deep clone of the original object can be obtained by passing `vm.$data` through `JSON.parse(JSON.stringify(...))`.

- **Example:**
  
  ```js
var data = { a: 1 }

// direct instance creation
var vm = new Vue({
  data: data
})
vm.a // => 1
vm.$data === data // => true

// must use function when in Vue.extend()
var Component = Vue.extend({
  data: function () {
    return { a: 1 }
  }
})
```

<p class="tip">
  Note that __you should not use an arrow function with the `data` property__ (e.g. `data: () => { return { a: this.myProp }}`). The reason is arrow functions bind the parent context, so `this` will not be the Vue instance as you expect and `this.myProp` will be undefined.
</p>

- **See also:** [Reactivity in Depth](../guide/reactivity.html)

### props

- **Type:** `Array<string> | Object`

- **Details:**
  
  A list/hash of attributes that are exposed to accept data from the parent component. It has an Array-based simple syntax and an alternative Object-based syntax that allows advanced configurations such as type checking, custom validation and default values.

- **Example:**
  
  ```js
// simple syntax
Vue.component('props-demo-simple', {
  props: ['size', 'myMessage']
})

// object syntax with validation
Vue.component('props-demo-advanced', {
  props: {
    // type check
    height: Number,
    // type check plus other validations
    age: {
      type: Number,
      default: 0,
      required: true,
      validator: function (value) {
        return value >= 0
      }
    }
  }
})
```

- **See also:** [Props](../guide/components.html#Props)

### propsData

- **Type:** `{ [key: string]: any }`

- **Restriction:** only respected in instance creation via `new`.

- **Details:**
  
  Pass props to an instance during its creation. This is primarily intended to make unit testing easier.

- **Example:**
  
  ```js
var Comp = Vue.extend({
  props: ['msg'],
  template: '<div>{{ msg }}</div>'
})

var vm = new Comp({
  propsData: {
    msg: 'hello'
  }
})
```

### computed

- **Type:** `{ [key: string]: Function | { get: Function, set: Function } }`

- **Details:**
  
  Computed properties to be mixed into the Vue instance. All getters and setters have their `this` context automatically bound to the Vue instance.

<p class="tip">
  Note that __you should not use an arrow function to define a computed property__ (e.g. `aDouble: () => this.a * 2`). The reason is arrow functions bind the parent context, so `this` will not be the Vue instance as you expect and `this.a` will be undefined.
</p>

    Computed properties are cached, and only re-computed on reactive dependency changes. Note that if a certain dependency is out of the instance's scope (i.e. not reactive), the computed property will __not__ be updated.
    

- **Example:**
  
  ```js
var vm = new Vue({
  data: { a: 1 },
  computed: {
    // get only
    aDouble: function () {
      return this.a * 2
    },
    // both get and set
    aPlus: {
      get: function () {
        return this.a + 1
      },
      set: function (v) {
        this.a = v - 1
      }
    }
  }
})
vm.aPlus   // => 2
vm.aPlus = 3
vm.a       // => 2
vm.aDouble // => 4
```

- **See also:** [Computed Properties](../guide/computed.html)

### methods

- **Type:** `{ [key: string]: Function }`

- **Details:**
  
  Methods to be mixed into the Vue instance. You can access these methods directly on the VM instance, or use them in directive expressions. All methods will have their `this` context automatically bound to the Vue instance.

<p class="tip">
  Note that __you should not use an arrow function to define a method__ (e.g. `plus: () => this.a++`). The reason is arrow functions bind the parent context, so `this` will not be the Vue instance as you expect and `this.a` will be undefined.
</p>

- **Example:**
  
  ```js
var vm = new Vue({
  data: { a: 1 },
  methods: {
    plus: function () {
      this.a++
    }
  }
})
vm.plus()
vm.a // 2
```

- **See also:** [Event Handling](../guide/events.html)

### watch

- **Type:** `{ [key: string]: string | Function | Object }`

- **Details:**
  
  An object where keys are expressions to watch and values are the corresponding callbacks. The value can also be a string of a method name, or an Object that contains additional options. The Vue instance will call `$watch()` for each entry in the object at instantiation.

- **Example:**
  
  ```js
var vm = new Vue({
  data: {
    a: 1,
    b: 2,
    c: 3
  },
  watch: {
    a: function (val, oldVal) {
      console.log('new: %s, old: %s', val, oldVal)
    },
    // string method name
    b: 'someMethod',
    // deep watcher
    c: {
      handler: function (val, oldVal) { /* ... */ },
      deep: true
    }
  }
})
vm.a = 2 // => new: 2, old: 1
```

<p class="tip">
  Note that __you should not use an arrow function to define a watcher__ (e.g. `searchQuery: newValue => this.updateAutocomplete(newValue)`). The reason is arrow functions bind the parent context, so `this` will not be the Vue instance as you expect and `this.updateAutocomplete` will be undefined.
</p>

- **See also:** [Instance Methods / Data - vm.$watch](#vm-watch)

## Options / DOM

### el

- **Type:** `string | HTMLElement`

- **Restriction:** only respected in instance creation via `new`.

- **Details:**
  
  Provide the Vue instance an existing DOM element to mount on. It can be a CSS selector string or an actual HTMLElement.
  
  After the instance is mounted, the resolved element will be accessible as `vm.$el`.
  
  If this option is available at instantiation, the instance will immediately enter compilation; otherwise, the user will have to explicitly call `vm.$mount()` to manually start the compilation.

<p class="tip">
  The provided element merely serves as a mounting point. Unlike in Vue 1.x, the mounted element will be replaced with Vue-generated DOM in all cases. It is therefore not recommended to mount the root instance to `` or ``.</p> 
  
  <p class="tip">
    If neither `render` function nor `template` option is present, the in-DOM HTML of the mounting DOM element will be extracted as the template. In this case, Runtime + Compiler build of Vue should be used.
  </p>
  
  <ul>
    <li>
      <strong>See also:</strong> <ul>
        <li>
          <a href="../guide/instance.html#Lifecycle-Diagram">Lifecycle Diagram</a>
        </li>
        <li>
          <a href="../guide/installation.html#Runtime-Compiler-vs-Runtime-only">Runtime + Compiler vs. Runtime-only</a>
        </li>
      </ul>
    </li>
  </ul>
  
  <h3>
    template
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>string</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        A string template to be used as the markup for the Vue instance. The template will <strong>replace</strong> the mounted element. Any existing markup inside the mounted element will be ignored, unless content distribution slots are present in the template.
      </p>
      <p>
        If the string starts with <code>#</code> it will be used as a querySelector and use the selected element's innerHTML as the template string. This allows the use of the common <code>&lt;script type="x-template"&gt;</code> trick to include templates.
      </p>
    </li>
  </ul>
  
  <p class="tip">
    From a security perspective, you should only use Vue templates that you can trust. Never use user-generated content as your template.
  </p>
  
  <p class="tip">
    If render function is present in the Vue option, the template will be ignored.
  </p>
  
  <ul>
    <li>
      <strong>See also:</strong> <ul>
        <li>
          <a href="../guide/instance.html#Lifecycle-Diagram">Lifecycle Diagram</a>
        </li>
        <li>
          <a href="../guide/components.html#Content-Distribution-with-Slots">Content Distribution with Slots</a>
        </li>
      </ul>
    </li>
  </ul>
  
  <h3>
    render
  </h3>
  
  <pre><code>- **Type:** `(createElement: () =&gt; VNode) =&gt; VNode`

- **Details:**

  An alternative to string templates allowing you to leverage the full programmatic power of JavaScript. The render function receives a `createElement` method as it's first argument used to create `VNode`s.

  If the component is a functional component, the render function also receives an extra argument `context`, which provides access to contextual data since functional components are instance-less.

  &lt;p class="tip"&gt;The `render` function has priority over the render function compiled from `template` option or in-DOM HTML template of the mounting element which is specified by the `el` option.&lt;/p&gt;

- **See also:** [Render Functions](../guide/render-function.html)
</code></pre>
  
  <h3>
    renderError
  </h3>
  
  <blockquote>
    <p>
      New in 2.2.0+
    </p>
  </blockquote>
  
  <pre><code>- **Type:** `(createElement: () =&gt; VNode, error: Error) =&gt; VNode`

- **Details:**

  **Only works in development mode.**

  Provide an alternative render output when the default `render` function encounters an error. The error will be passed to `renderError` as the second argument. This is particularly useful when used together with hot-reload.

- **Example:**

  ``` js
  new Vue({
    render (h) {
      throw new Error('oops')
    },
    renderError (h, err) {
      return h('pre', { style: { color: 'red' }}, err.stack)
    }
  }).$mount('#app')
  ```

- **See also:** [Render Functions](../guide/render-function.html)
</code></pre>
  
  <h2>
    Options / Lifecycle Hooks
  </h2>

<p class="tip">All lifecycle hooks automatically have their `this` context bound to the instance, so that you can access data, computed properties, and methods. This means __you should not use an arrow function to define a lifecycle method__ (e.g. `created: () => this.fetchTodos()`). The reason is arrow functions bind the parent context, so `this` will not be the Vue instance as you expect and `this.fetchTodos` will be undefined.</p>

  
  <h3>
    beforeCreate
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Function</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Called synchronously immediately after the instance has been initialized, before data observation and event/watcher setup.
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/instance.html#Lifecycle-Diagram">Lifecycle Diagram</a>
      </p>
    </li>
  </ul>
  
  <h3>
    created
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Function</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Called synchronously after the instance is created. At this stage, the instance has finished processing the options which means the following have been set up: data observation, computed properties, methods, watch/event callbacks. However, the mounting phase has not been started, and the <code>$el</code> property will not be available yet.
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/instance.html#Lifecycle-Diagram">Lifecycle Diagram</a>
      </p>
    </li>
  </ul>
  
  <h3>
    beforeMount
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Function</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Called right before the mounting begins: the <code>render</code> function is about to be called for the first time.
      </p>
      <p>
        <strong>This hook is not called during server-side rendering.</strong>
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/instance.html#Lifecycle-Diagram">Lifecycle Diagram</a>
      </p>
    </li>
  </ul>
  
  <h3>
    mounted
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Function</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Called after the instance has been mounted, where <code>el</code> is replaced by the newly created <code>vm.$el</code>. If the root instance is mounted to an in-document element, <code>vm.$el</code> will also be in-document when <code>mounted</code> is called.
      </p>
      <p>
        Note that <code>mounted</code> does <strong>not</strong> guarantee that all child components have also been mounted. If you want to wait until the entire view has been rendered, you can use <a href="#vm-nextTick">vm.$nextTick</a> inside of <code>mounted</code>:
      </p>
      <pre><code class="js">mounted: function () {
  this.$nextTick(function () {
    // Code that will run only after the
    // entire view has been rendered
  })
}
</code></pre>
      <p>
        <strong>This hook is not called during server-side rendering.</strong>
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/instance.html#Lifecycle-Diagram">Lifecycle Diagram</a>
      </p>
    </li>
  </ul>
  
  <h3>
    beforeUpdate
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Function</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Called when the data changes, before the virtual DOM is re-rendered and patched.
      </p>
      <p>
        You can perform further state changes in this hook and they will not trigger additional re-renders.
      </p>
      <p>
        <strong>This hook is not called during server-side rendering.</strong>
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/instance.html#Lifecycle-Diagram">Lifecycle Diagram</a>
      </p>
    </li>
  </ul>
  
  <h3>
    updated
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Function</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Called after a data change causes the virtual DOM to be re-rendered and patched.
      </p>
      <p>
        The component's DOM will have been updated when this hook is called, so you can perform DOM-dependent operations here. However, in most cases you should avoid changing state inside the hook. To react to state changes, it's usually better to use a <a href="#computed">computed property</a> or <a href="#watch">watcher</a> instead.
      </p>
      <p>
        Note that <code>updated</code> does <strong>not</strong> guarantee that all child components have also been re-rendered. If you want to wait until the entire view has been re-rendered, you can use <a href="#vm-nextTick">vm.$nextTick</a> inside of <code>updated</code>:
      </p>
      <pre><code class="js">updated: function () {
  this.$nextTick(function () {
    // Code that will run only after the
    // entire view has been re-rendered
  })
}
</code></pre>
      <p>
        <strong>This hook is not called during server-side rendering.</strong>
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/instance.html#Lifecycle-Diagram">Lifecycle Diagram</a>
      </p>
    </li>
  </ul>
  
  <h3>
    activated
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Function</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Called when a kept-alive component is activated.
      </p>
      <p>
        <strong>This hook is not called during server-side rendering.</strong>
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong>
      </p>
      <ul>
        <li>
          <a href="#keep-alive">Built-in Components - keep-alive</a>
        </li>
        <li>
          <a href="../guide/components.html#keep-alive">Dynamic Components - keep-alive</a>
        </li>
      </ul>
    </li>
  </ul>
  
  <h3>
    deactivated
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Function</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Called when a kept-alive component is deactivated.
      </p>
      <p>
        <strong>This hook is not called during server-side rendering.</strong>
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong>
      </p>
      <ul>
        <li>
          <a href="#keep-alive">Built-in Components - keep-alive</a>
        </li>
        <li>
          <a href="../guide/components.html#keep-alive">Dynamic Components - keep-alive</a>
        </li>
      </ul>
    </li>
  </ul>
  
  <h3>
    beforeDestroy
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Function</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Called right before a Vue instance is destroyed. At this stage the instance is still fully functional.
      </p>
      <p>
        <strong>This hook is not called during server-side rendering.</strong>
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/instance.html#Lifecycle-Diagram">Lifecycle Diagram</a>
      </p>
    </li>
  </ul>
  
  <h3>
    destroyed
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Function</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Called after a Vue instance has been destroyed. When this hook is called, all directives of the Vue instance have been unbound, all event listeners have been removed, and all child Vue instances have also been destroyed.
      </p>
      <p>
        <strong>This hook is not called during server-side rendering.</strong>
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/instance.html#Lifecycle-Diagram">Lifecycle Diagram</a>
      </p>
    </li>
  </ul>
  
  <h2>
    Options / Assets
  </h2>
  
  <h3>
    directives
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Object</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        A hash of directives to be made available to the Vue instance.
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/custom-directive.html">Custom Directives</a>
      </p>
    </li>
  </ul>
  
  <h3>
    filters
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Object</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        A hash of filters to be made available to the Vue instance.
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="#Vue-filter"><code>Vue.filter</code></a>
      </p>
    </li>
  </ul>
  
  <h3>
    components
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Object</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        A hash of components to be made available to the Vue instance.
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/components.html">Components</a>
      </p>
    </li>
  </ul>
  
  <h2>
    Options / Composition
  </h2>
  
  <h3>
    parent
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Vue instance</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Specify the parent instance for the instance to be created. Establishes a parent-child relationship between the two. The parent will be accessible as <code>this.$parent</code> for the child, and the child will be pushed into the parent's <code>$children</code> array.
      </p>
    </li>
  </ul>
  
  <p class="tip">
    Use `$parent` and `$children` sparingly - they mostly serve as an escape-hatch. Prefer using props and events for parent-child communication.
  </p>
  
  <h3>
    mixins
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Array&lt;Object&gt;</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        The <code>mixins</code> option accepts an array of mixin objects. These mixin objects can contain instance options like normal instance objects, and they will be merged against the eventual options using the same option merging logic in <code>Vue.extend()</code>. e.g. If your mixin contains a created hook and the component itself also has one, both functions will be called.
      </p>
      <p>
        Mixin hooks are called in the order they are provided, and called before the component's own hooks.
      </p>
    </li>
    <li>
      <p>
        <strong>Example:</strong>
      </p>
      <pre><code class="js">var mixin = {
  created: function () { console.log(1) }
}
var vm = new Vue({
  created: function () { console.log(2) },
  mixins: [mixin]
})
// =&gt; 1
// =&gt; 2
</code></pre>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/mixins.html">Mixins</a>
      </p>
    </li>
  </ul>
  
  <h3>
    extends
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Object | Function</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Allows declaratively extending another component (could be either a plain options object or a constructor) without having to use <code>Vue.extend</code>. This is primarily intended to make it easier to extend between single file components.
      </p>
      <p>
        This is similar to <code>mixins</code>, the difference being that the component's own options takes higher priority than the source component being extended.
      </p>
    </li>
    <li>
      <p>
        <strong>Example:</strong>
      </p>
      <pre><code class="js">var CompA = { ... }

// extend CompA without having to call `Vue.extend` on either
var CompB = {
  extends: CompA,
  ...
}
</code></pre>
    </li>
  </ul>
  
  <h3>
    provide / inject
  </h3>
  
  <blockquote>
    <p>
      New in 2.2.0+
    </p>
  </blockquote>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong>
      </p>
      <ul>
        <li>
          <strong>provide:</strong> <code>Object | () =&gt; Object</code>
        </li>
        <li>
          <strong>inject:</strong> <code>Array&lt;string&gt; | { [key: string]: string | Symbol }</code>
        </li>
      </ul>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
    </li>
  </ul>
  
  <p class="tip">
    `provide` and `inject` are primarily provided for advanced plugin / component library use cases. It is NOT recommended to use them in generic application code.
  </p>
  
  <pre><code>This pair of options are used together to allow an ancestor component to serve as a dependency injector for its all descendants, regardless of how deep the component hierarchy is, as long as they are in the same parent chain. If you are familiar with React, this is very similar to React's context feature.

The `provide` option should be an object or a function that returns an object. This object contains the properties that are available for injection into its descendants. You can use ES2015 Symbols as keys in this object, but only in environments that natively support `Symbol` and `Reflect.ownKeys`.

The `inject` options should be either an Array of strings or an object where the keys stand for the local binding name, and the value being the key (string or Symbol) to search for in available injections.

&gt; Note: the `provide` and `inject` bindings are NOT reactive. This is intentional. However, if you pass down an observed object, properties on that object do remain reactive.
</code></pre>
  
  <ul>
    <li>
      <p>
        <strong>Example:</strong>
      </p>
      <pre><code class="js">var Provider = {
  provide: {
    foo: 'bar'
  },
  // ...
}

var Child = {
  inject: ['foo'],
  created () {
    console.log(this.foo) // =&gt; "bar"
  }
  // ...
}
</code></pre>
      <p>
        With ES2015 Symbols, function <code>provide</code> and object <code>inject</code>:
      </p>
      <pre><code class="js">const s = Symbol()

const Provider = {
  provide () {
    return {
      [s]: 'foo'
    }
  }
}

const Child = {
  inject: { s },
  // ...
}
</code></pre>
      <blockquote>
        <p>
          The next 2 examples work with Vue 2.2.1+. Below that version, injected values were resolved after the <code>props</code> and the <code>data</code> initialization.
        </p>
      </blockquote>
      
      <p>
        Using an injected value as the default for a prop:
      </p>
      <pre><code class="js">const Child = {
  inject: ['foo'],
  props: {
    bar: {
      default () {
        return this.foo
      }
    }
  }
}
</code></pre>
      <p>
        Using an injected value as data entry:
      </p>
      <pre><code class="js">const Child = {
  inject: ['foo'],
  data () {
    return {
      bar: this.foo
    }
  }
}
</code></pre>
    </li>
  </ul>
  
  <h2>
    Options / Misc
  </h2>
  
  <h3>
    name
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>string</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Restriction:</strong> only respected when used as a component option.
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Allow the component to recursively invoke itself in its template. Note that when a component is registered globally with <code>Vue.component()</code>, the global ID is automatically set as its name.
      </p>
      <p>
        Another benefit of specifying a <code>name</code> option is debugging. Named components result in more helpful warning messages. Also, when inspecting an app in the <a href="https://github.com/vuejs/vue-devtools">vue-devtools</a>, unnamed components will show up as <code>&lt;AnonymousComponent&gt;</code>, which isn't very informative. By providing the <code>name</code> option, you will get a much more informative component tree.
      </p>
    </li>
  </ul>
  
  <h3>
    delimiters
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Array&lt;string&gt;</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Default:</strong> <code>{% raw %}["{{", "}}"]{% endraw %}</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Restrictions:</strong> This option is only available in the full build, with in-browser compilation.
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Change the plain text interpolation delimiters.
      </p>
    </li>
    <li>
      <p>
        <strong>Example:</strong>
      </p>
      <pre><code class="js">new Vue({
  delimiters: ['${', '}']
})

// Delimiters changed to ES6 template string style
</code></pre>
    </li>
  </ul>
  
  <h3>
    functional
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>boolean</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Causes a component to be stateless (no <code>data</code>) and instanceless (no <code>this</code> context). They are only a <code>render</code> function that returns virtual nodes making them much cheaper to render.
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/render-function.html#Functional-Components">Functional Components</a>
      </p>
    </li>
  </ul>
  
  <h3>
    model
  </h3>
  
  <blockquote>
    <p>
      New in 2.2.0
    </p>
  </blockquote>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>{ prop?: string, event?: string }</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Allows a custom component to customize the prop and event used when it's used with <code>v-model</code>. By default, <code>v-model</code> on a component uses <code>value</code> as the prop and <code>input</code> as the event, but some input types such as checkboxes and radio buttons may want to use the <code>value</code> prop for a different purpose. Using the <code>model</code> option can avoid the conflict in such cases.
      </p>
    </li>
    <li>
      <p>
        <strong>Example:</strong>
      </p>
      <pre><code class="js">Vue.component('my-checkbox', {
  model: {
    prop: 'checked',
    event: 'change'
  },
  props: {
    // this allows using the `value` prop for a different purpose
    value: String,
    // use `checked` as the prop which take the place of `value`
    checked: {
      type: Number,
      default: 0
    }
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
    </li>
  </ul>
  
  <h3>
    inheritAttrs
  </h3>
  
  <blockquote>
    <p>
      New in 2.4.0+
    </p>
  </blockquote>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>boolean</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Default:</strong> <code>true</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        By default, parent scope attribute bindings that are not recognized as props will "fallthrough" and be applied to the root element of the child component as normal HTML attributes. When authoring a component that wraps a target element or another component, this may not always be the desired behavior. By setting <code>inheritAttrs</code> to <code>false</code>, this default behavior can be disabled. The attributes are available via the <code>$attrs</code> instance property (also new in 2.4) and can be explicitly bound to a non-root element using <code>v-bind</code>.
      </p>
      <p>
        Note: this option does <strong>not</strong> affect <code>class</code> and <code>style</code> bindings.
      </p>
    </li>
  </ul>
  
  <h3>
    comments
  </h3>
  
  <blockquote>
    <p>
      New in 2.4.0+
    </p>
  </blockquote>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>boolean</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Default:</strong> <code>false</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Restrictions:</strong> This option is only available in the full build, with in-browser compilation.
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        When set to <code>true</code>, will preserve and render HTML comments found in templates. The default behavior is discarding them.
      </p>
    </li>
  </ul>
  
  <h2>
    Instance Properties
  </h2>
  
  <h3>
    vm.$data
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Object</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        The data object that the Vue instance is observing. The Vue instance proxies access to the properties on its data object.
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="#data">Options / Data - data</a>
      </p>
    </li>
  </ul>
  
  <h3>
    vm.$props
  </h3>
  
  <blockquote>
    <p>
      New in 2.2.0+
    </p>
  </blockquote>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Object</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        An object representing the current props a component has received. The Vue instance proxies access to the properties on its props object.
      </p>
    </li>
  </ul>
  
  <h3>
    vm.$el
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>HTMLElement</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Read only</strong>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        The root DOM element that the Vue instance is managing.
      </p>
    </li>
  </ul>
  
  <h3>
    vm.$options
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Object</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Read only</strong>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        The instantiation options used for the current Vue instance. This is useful when you want to include custom properties in the options:
      </p>
      <pre><code class="js">new Vue({
  customOption: 'foo',
  created: function () {
    console.log(this.$options.customOption) // =&gt; 'foo'
  }
})
</code></pre>
    </li>
  </ul>
  
  <h3>
    vm.$parent
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Vue instance</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Read only</strong>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        The parent instance, if the current instance has one.
      </p>
    </li>
  </ul>
  
  <h3>
    vm.$root
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Vue instance</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Read only</strong>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        The root Vue instance of the current component tree. If the current instance has no parents this value will be itself.
      </p>
    </li>
  </ul>
  
  <h3>
    vm.$children
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Array&lt;Vue instance&gt;</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Read only</strong>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        The direct child components of the current instance. <strong>Note there's no order guarantee for <code>$children</code>, and it is not reactive.</strong> If you find yourself trying to use <code>$children</code> for data binding, consider using an Array and <code>v-for</code> to generate child components, and use the Array as the source of truth.
      </p>
    </li>
  </ul>
  
  <h3>
    vm.$slots
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>{ [name: string]: ?Array&lt;VNode&gt; }</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Read only</strong>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Used to programmatically access content <a href="../guide/components.html#Content-Distribution-with-Slots">distributed by slots</a>. Each <a href="../guide/components.html#Named-Slots">named slot</a> has its own corresponding property (e.g. the contents of <code>slot="foo"</code> will be found at <code>vm.$slots.foo</code>). The <code>default</code> property contains any nodes not included in a named slot.
      </p>
      <p>
        Accessing <code>vm.$slots</code> is most useful when writing a component with a <a href="../guide/render-function.html">render function</a>.
      </p>
    </li>
    <li>
      <p>
        <strong>Example:</strong>
      </p>
      <pre><code class="html">&lt;blog-post&gt;
  &lt;h1 slot="header"&gt;
    About Me
  &lt;/h1&gt;

  &lt;p&gt;Here's some page content, which will be included in vm.$slots.default, because it's not inside a named slot.&lt;/p&gt;

  &lt;p slot="footer"&gt;
    Copyright 2016 Evan You
  &lt;/p&gt;

  &lt;p&gt;If I have some content down here, it will also be included in vm.$slots.default.&lt;/p&gt;.
&lt;/blog-post&gt;
</code></pre>
      <pre><code class="js">Vue.component('blog-post', {
  render: function (createElement) {
    var header = this.$slots.header
    var body   = this.$slots.default
    var footer = this.$slots.footer
    return createElement('div', [
      createElement('header', header),
      createElement('main', body),
      createElement('footer', footer)
    ])
  }
})
</code></pre>
    </li>
    <li>
      <p>
        <strong>See also:</strong>
      </p>
      <ul>
        <li>
          <a href="#slot-1"><code>&lt;slot&gt;</code> Component</a>
        </li>
        <li>
          <a href="../guide/components.html#Content-Distribution-with-Slots">Content Distribution with Slots</a>
        </li>
        <li>
          <a href="../guide/render-function.html#Slots">Render Functions - Slots</a>
        </li>
      </ul>
    </li>
  </ul>
  
  <h3>
    vm.$scopedSlots
  </h3>
  
  <blockquote>
    <p>
      New in 2.1.0+
    </p>
  </blockquote>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>{ [name: string]: props =&gt; VNode | Array&lt;VNode&gt; }</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Read only</strong>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Used to programmatically access <a href="../guide/components.html#Scoped-Slots">scoped slots</a>. For each slot, including the <code>default</code> one, the object contains a corresponding function that returns VNodes.
      </p>
      <p>
        Accessing <code>vm.$scopedSlots</code> is most useful when writing a component with a <a href="../guide/render-function.html">render function</a>.
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong>
      </p>
      <ul>
        <li>
          <a href="#slot-1"><code>&lt;slot&gt;</code> Component</a>
        </li>
        <li>
          <a href="../guide/components.html#Scoped-Slots">Scoped Slots</a>
        </li>
        <li>
          <a href="../guide/render-function.html#Slots">Render Functions - Slots</a>
        </li>
      </ul>
    </li>
  </ul>
  
  <h3>
    vm.$refs
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>Object</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Read only</strong>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        An object that holds child components that have <code>ref</code> registered.
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong>
      </p>
      <ul>
        <li>
          <a href="../guide/components.html#Child-Component-Refs">Child Component Refs</a>
        </li>
        <li>
          <a href="#ref">Special Attributes - ref</a>
        </li>
      </ul>
    </li>
  </ul>
  
  <h3>
    vm.$isServer
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>boolean</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Read only</strong>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Whether the current Vue instance is running on the server.
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/ssr.html">Server-Side Rendering</a>
      </p>
    </li>
  </ul>
  
  <h3>
    vm.$attrs
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>{ [key: string]: string }</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Read only</strong>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Contains parent-scope attribute bindings (except for <code>class</code> and <code>style</code>) that are not recognized (and extracted) as props. When a component doesn't have any declared props, this essentially contains all parent-scope bindings (except for <code>class</code> and <code>style</code>), and can be passed down to an inner component via <code>v-bind="$attrs"</code> - useful when creating higher-order components.
      </p>
    </li>
  </ul>
  
  <h3>
    vm.$listeners
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Type:</strong> <code>{ [key: string]: Function | Array&lt;Function&gt; }</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Read only</strong>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Contains parent-scope <code>v-on</code> event listeners (without <code>.native</code> modifiers). This can be passed down to an inner component via <code>v-on="$listeners"</code> - useful when creating higher-order components.
      </p>
    </li>
  </ul>
  
  <h2>
    Instance Methods / Data
  </h2>

<h3 id="vm-watch">vm.$watch( expOrFn, callback, [options] )</h3>

  
  <ul>
    <li>
      <p>
        <strong>Arguments:</strong>
      </p>
      <ul>
        <li>
          <code>{string | Function} expOrFn</code>
        </li>
        <li>
          <code>{Function | Object} callback</code>
        </li>
        <li>
          <code>{Object} [options]</code> <ul>
            <li>
              <code>{boolean} deep</code>
            </li>
            <li>
              <code>{boolean} immediate</code>
            </li>
          </ul>
        </li>
      </ul>
    </li>
    <li>
      <p>
        <strong>Returns:</strong> <code>{Function} unwatch</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        Watch an expression or a computed function on the Vue instance for changes. The callback gets called with the new value and the old value. The expression only accepts dot-delimited paths. For more complex expressions, use a function instead.
      </p>
    </li>
  </ul>

<p class="tip">Note: when mutating (rather than replacing) an Object or an Array, the old value will be the same as new value because they reference the same Object/Array. Vue doesn't keep a copy of the pre-mutate value.</p>

  
  <ul>
    <li>
      <p>
        <strong>Example:</strong>
      </p>
      <pre><code class="js">// keypath
vm.$watch('a.b.c', function (newVal, oldVal) {
  // do something
})

// function
vm.$watch(
  function () {
    return this.a + this.b
  },
  function (newVal, oldVal) {
    // do something
  }
)
</code></pre>
      <p>
        <code>vm.$watch</code> returns an unwatch function that stops firing the callback:
      </p>
      <pre><code class="js">var unwatch = vm.$watch('a', cb)
// later, teardown the watcher
unwatch()
</code></pre>
    </li>
    <li>
      <p>
        <strong>Option: deep</strong>
      </p>
      <p>
        To also detect nested value changes inside Objects, you need to pass in <code>deep: true</code> in the options argument. Note that you don't need to do so to listen for Array mutations.
      </p>
      <pre><code class="js">vm.$watch('someObject', callback, {
  deep: true
})
vm.someObject.nestedValue = 123
// callback is fired
</code></pre>
    </li>
    <li>
      <p>
        <strong>Option: immediate</strong>
      </p>
      <p>
        Passing in <code>immediate: true</code> in the option will trigger the callback immediately with the current value of the expression:
      </p>
      <pre><code class="js">vm.$watch('a', callback, {
  immediate: true
})
// `callback` is fired immediately with current value of `a`
</code></pre>
    </li>
  </ul>

<h3 id="vm-set">vm.$set( target, key, value )</h3>

  
  <ul>
    <li>
      <p>
        <strong>Arguments:</strong>
      </p>
      <ul>
        <li>
          <code>{Object | Array} target</code>
        </li>
        <li>
          <code>{string | number} key</code>
        </li>
        <li>
          <code>{any} value</code>
        </li>
      </ul>
    </li>
    <li>
      <p>
        <strong>Returns:</strong> the set value.
      </p>
    </li>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        This is the <strong>alias</strong> of the global <code>Vue.set</code>.
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="#Vue-set">Vue.set</a>
      </p>
    </li>
  </ul>

<h3 id="vm-delete">vm.$delete( target, key )</h3>

  
  <ul>
    <li>
      <p>
        <strong>Arguments:</strong>
      </p>
      <ul>
        <li>
          <code>{Object | Array} target</code>
        </li>
        <li>
          <code>{string | number} key</code>
        </li>
      </ul>
    </li>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        This is the <strong>alias</strong> of the global <code>Vue.delete</code>.
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="#Vue-delete">Vue.delete</a>
      </p>
    </li>
  </ul>
  
  <h2>
    Instance Methods / Events
  </h2>

<h3 id="vm-on">vm.$on( event, callback )</h3>

  
  <ul>
    <li>
      <p>
        <strong>Arguments:</strong>
      </p>
      <ul>
        <li>
          <code>{string | Array&lt;string&gt;} event</code> (array only supported in 2.2.0+)
        </li>
        <li>
          <code>{Function} callback</code>
        </li>
      </ul>
    </li>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        Listen for a custom event on the current vm. Events can be triggered by <code>vm.$emit</code>. The callback will receive all the additional arguments passed into these event-triggering methods.
      </p>
    </li>
    <li>
      <p>
        <strong>Example:</strong>
      </p>
      <pre><code class="js">vm.$on('test', function (msg) {
  console.log(msg)
})
vm.$emit('test', 'hi')
// =&gt; "hi"
</code></pre>
    </li>
  </ul>

<h3 id="vm-once">vm.$once( event, callback )</h3>

  
  <ul>
    <li>
      <p>
        <strong>Arguments:</strong>
      </p>
      <ul>
        <li>
          <code>{string} event</code>
        </li>
        <li>
          <code>{Function} callback</code>
        </li>
      </ul>
    </li>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        Listen for a custom event, but only once. The listener will be removed once it triggers for the first time.
      </p>
    </li>
  </ul>

<h3 id="vm-off">vm.$off( [event, callback] )</h3>

  
  <ul>
    <li>
      <p>
        <strong>Arguments:</strong>
      </p>
      <ul>
        <li>
          <code>{string | Array&lt;string&gt;} event</code> (array only supported in 2.2.2+)
        </li>
        <li>
          <code>{Function} [callback]</code>
        </li>
      </ul>
    </li>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        Remove custom event listener(s).
      </p>
      <ul>
        <li>
          <p>
            If no arguments are provided, remove all event listeners;
          </p>
        </li>
        <li>
          <p>
            If only the event is provided, remove all listeners for that event;
          </p>
        </li>
        <li>
          <p>
            If both event and callback are given, remove the listener for that specific callback only.
          </p>
        </li>
      </ul>
    </li>
  </ul>

<h3 id="vm-emit">vm.$emit( event, [...args] )</h3>

  
  <ul>
    <li>
      <p>
        <strong>Arguments:</strong>
      </p>
      <ul>
        <li>
          <code>{string} event</code>
        </li>
        <li>
          <code>[...args]</code>
        </li>
      </ul>
      <p>
        Trigger an event on the current instance. Any additional arguments will be passed into the listener's callback function.
      </p>
    </li>
  </ul>
  
  <h2>
    Instance Methods / Lifecycle
  </h2>

<h3 id="vm-mount">vm.$mount( [elementOrSelector] )</h3>

  
  <ul>
    <li>
      <p>
        <strong>Arguments:</strong>
      </p>
      <ul>
        <li>
          <code>{Element | string} [elementOrSelector]</code>
        </li>
        <li>
          <code>{boolean} [hydrating]</code>
        </li>
      </ul>
    </li>
    <li>
      <p>
        <strong>Returns:</strong> <code>vm</code> - the instance itself
      </p>
    </li>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        If a Vue instance didn't receive the <code>el</code> option at instantiation, it will be in "unmounted" state, without an associated DOM element. <code>vm.$mount()</code> can be used to manually start the mounting of an unmounted Vue instance.
      </p>
      <p>
        If <code>elementOrSelector</code> argument is not provided, the template will be rendered as an off-document element, and you will have to use native DOM API to insert it into the document yourself.
      </p>
      <p>
        The method returns the instance itself so you can chain other instance methods after it.
      </p>
    </li>
    <li>
      <p>
        <strong>Example:</strong>
      </p>
      <pre><code class="js">var MyComponent = Vue.extend({
  template: '&lt;div&gt;Hello!&lt;/div&gt;'
})

// create and mount to #app (will replace #app)
new MyComponent().$mount('#app')

// the above is the same as:
new MyComponent({ el: '#app' })

// or, render off-document and append afterwards:
var component = new MyComponent().$mount()
document.getElementById('app').appendChild(component.$el)
</code></pre>
    </li>
    <li>
      <p>
        <strong>See also:</strong>
      </p>
      <ul>
        <li>
          <a href="../guide/instance.html#Lifecycle-Diagram">Lifecycle Diagram</a>
        </li>
        <li>
          <a href="../guide/ssr.html">Server-Side Rendering</a>
        </li>
      </ul>
    </li>
  </ul>

<h3 id="vm-forceUpdate">vm.$forceUpdate()</h3>

  
  <ul>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        Force the Vue instance to re-render. Note it does not affect all child components, only the instance itself and child components with inserted slot content.
      </p>
    </li>
  </ul>

<h3 id="vm-nextTick">vm.$nextTick( [callback] )</h3>

  
  <ul>
    <li>
      <p>
        <strong>Arguments:</strong>
      </p>
      <ul>
        <li>
          <code>{Function} [callback]</code>
        </li>
      </ul>
    </li>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        Defer the callback to be executed after the next DOM update cycle. Use it immediately after you've changed some data to wait for the DOM update. This is the same as the global <code>Vue.nextTick</code>, except that the callback's <code>this</code> context is automatically bound to the instance calling this method.
      </p>
      <blockquote>
        <p>
          New in 2.1.0+: returns a Promise if no callback is provided and Promise is supported in the execution environment. Please note that Vue does not come with a Promise polyfill, so if you target browsers that don't support Promises natively (looking at you, IE), you will have to provide a polyfill yourself.
        </p>
      </blockquote>
    </li>
    
    <li>
      <p>
        <strong>Example:</strong>
      </p>
      <pre><code class="js">new Vue({
  // ...
  methods: {
    // ...
    example: function () {
      // modify data
      this.message = 'changed'
      // DOM is not updated yet
      this.$nextTick(function () {
        // DOM is now updated
        // `this` is bound to the current instance
        this.doSomethingElse()
      })
    }
  }
})
</code></pre>
    </li>
    <li>
      <p>
        <strong>See also:</strong>
      </p>
      <ul>
        <li>
          <a href="#Vue-nextTick">Vue.nextTick</a>
        </li>
        <li>
          <a href="../guide/reactivity.html#Async-Update-Queue">Async Update Queue</a>
        </li>
      </ul>
    </li>
  </ul>

<h3 id="vm-destroy">vm.$destroy()</h3>

  
  <ul>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        Completely destroy a vm. Clean up its connections with other existing vms, unbind all its directives, turn off all event listeners.
      </p>
      <p>
        Triggers the <code>beforeDestroy</code> and <code>destroyed</code> hooks.
      </p>
    </li>
  </ul>
  
  <p class="tip">
    In normal use cases you shouldn't have to call this method yourself. Prefer controlling the lifecycle of child components in a data-driven fashion using `v-if` and `v-for`.
  </p>
  
  <ul>
    <li>
      <strong>See also:</strong> <a href="../guide/instance.html#Lifecycle-Diagram">Lifecycle Diagram</a>
    </li>
  </ul>
  
  <h2>
    Directives
  </h2>
  
  <h3>
    v-text
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Expects:</strong> <code>string</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Updates the element's <code>textContent</code>. If you need to update the part of <code>textContent</code>, you should use <code>{% raw %}{{ Mustache }}{% endraw %}</code> interpolations.
      </p>
    </li>
    <li>
      <p>
        <strong>Example:</strong>
      </p>
      <pre><code class="html">&lt;span v-text="msg"&gt;&lt;/span&gt;
&lt;!-- same as --&gt;
&lt;span&gt;{{msg}}&lt;/span&gt;
</code></pre>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/syntax.html#Text">Data Binding Syntax - Interpolations</a>
      </p>
    </li>
  </ul>
  
  <h3>
    v-html
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Expects:</strong> <code>string</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Updates the element's <code>innerHTML</code>. <strong>Note that the contents are inserted as plain HTML - they will not be compiled as Vue templates</strong>. If you find yourself trying to compose templates using <code>v-html</code>, try to rethink the solution by using components instead.
      </p>
    </li>
  </ul>
  
  <p class="tip">
    Dynamically rendering arbitrary HTML on your website can be very dangerous because it can easily lead to [XSS attacks](https://en.wikipedia.org/wiki/Cross-site_scripting). Only use `v-html` on trusted content and **never** on user-provided content.
  </p>
  
  <ul>
    <li>
      <p>
        <strong>Example:</strong>
      </p>
      <pre><code class="html">&lt;div v-html="html"&gt;&lt;/div&gt;
</code></pre>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/syntax.html#Raw-HTML">Data Binding Syntax - Interpolations</a>
      </p>
    </li>
  </ul>
  
  <h3>
    v-show
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Expects:</strong> <code>any</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        Toggle's the element's <code>display</code> CSS property based on the truthy-ness of the expression value.
      </p>
      <p>
        This directive triggers transitions when its condition changes.
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/conditional.html#v-show">Conditional Rendering - v-show</a>
      </p>
    </li>
  </ul>
  
  <h3>
    v-if
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Expects:</strong> <code>any</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        Conditionally render the element based on the truthy-ness of the expression value. The element and its contained directives / components are destroyed and re-constructed during toggles. If the element is a <code>&lt;template&gt;</code> element, its content will be extracted as the conditional block.
      </p>
      <p>
        This directive triggers transitions when its condition changes.
      </p>
    </li>
  </ul>
  
  <p class="tip">
    When used together with v-if, v-for has a higher priority than v-if. See the <a href="../guide/list.html#v-for-with-v-if">list rendering guide</a> for details.
  </p>
  
  <ul>
    <li>
      <strong>See also:</strong> <a href="../guide/conditional.html">Conditional Rendering - v-if</a>
    </li>
  </ul>
  
  <h3>
    v-else
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Does not expect expression</strong>
      </p>
    </li>
    <li>
      <p>
        <strong>Restriction:</strong> previous sibling element must have <code>v-if</code> or <code>v-else-if</code>.
      </p>
    </li>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        Denote the "else block" for <code>v-if</code> or a <code>v-if</code>/<code>v-else-if</code> chain.
      </p>
      <pre><code class="html">&lt;div v-if="Math.random() &gt; 0.5"&gt;
  Now you see me
&lt;/div&gt;
&lt;div v-else&gt;
  Now you don't
&lt;/div&gt;
</code></pre>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/conditional.html#v-else">Conditional Rendering - v-else</a>
      </p>
    </li>
  </ul>
  
  <h3>
    v-else-if
  </h3>
  
  <blockquote>
    <p>
      New in 2.1.0+
    </p>
  </blockquote>
  
  <ul>
    <li>
      <p>
        <strong>Expects:</strong> <code>any</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Restriction:</strong> previous sibling element must have <code>v-if</code> or <code>v-else-if</code>.
      </p>
    </li>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        Denote the "else if block" for <code>v-if</code>. Can be chained.
      </p>
      <pre><code class="html">&lt;div v-if="type === 'A'"&gt;
  A
&lt;/div&gt;
&lt;div v-else-if="type === 'B'"&gt;
  B
&lt;/div&gt;
&lt;div v-else-if="type === 'C'"&gt;
  C
&lt;/div&gt;
&lt;div v-else&gt;
  Not A/B/C
&lt;/div&gt;
</code></pre>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/conditional.html#v-else-if">Conditional Rendering - v-else-if</a>
      </p>
    </li>
  </ul>
  
  <h3>
    v-for
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Expects:</strong> <code>Array | Object | number | string</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        Render the element or template block multiple times based on the source data. The directive's value must use the special syntax <code>alias in expression</code> to provide an alias for the current element being iterated on:
      </p>
      <pre><code class="html">&lt;div v-for="item in items"&gt;
  {{ item.text }}
&lt;/div&gt;
</code></pre>
      <p>
        Alternatively, you can also specify an alias for the index (or the key if used on an Object):
      </p>
      <pre><code class="html">&lt;div v-for="(item, index) in items"&gt;&lt;/div&gt;
&lt;div v-for="(val, key) in object"&gt;&lt;/div&gt;
&lt;div v-for="(val, key, index) in object"&gt;&lt;/div&gt;
</code></pre>
      <p>
        The default behavior of <code>v-for</code> will try to patch the elements in-place without moving them. To force it to reorder elements, you need to provide an ordering hint with the <code>key</code> special attribute:
      </p>
      <pre><code class="html">&lt;div v-for="item in items" :key="item.id"&gt;
  {{ item.text }}
&lt;/div&gt;
</code></pre>
    </li>
  </ul>
  
  <p class="tip">
    When used together with v-if, v-for has a higher priority than v-if. See the <a href="../guide/list.html#v-for-with-v-if">list rendering guide</a> for details.
  </p>
  
  <pre><code>The detailed usage for `v-for` is explained in the guide section linked below.
</code></pre>
  
  <ul>
    <li>
      <strong>See also:</strong> <ul>
        <li>
          <a href="../guide/list.html">List Rendering</a>
        </li>
        <li>
          <a href="../guide/list.html#key">key</a>
        </li>
      </ul>
    </li>
  </ul>
  
  <h3>
    v-on
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Shorthand:</strong> <code>@</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Expects:</strong> <code>Function | Inline Statement | Object</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Argument:</strong> <code>event</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Modifiers:</strong>
      </p>
      <ul>
        <li>
          <code>.stop</code> - call <code>event.stopPropagation()</code>.
        </li>
        <li>
          <code>.prevent</code> - call <code>event.preventDefault()</code>.
        </li>
        <li>
          <code>.capture</code> - add event listener in capture mode.
        </li>
        <li>
          <code>.self</code> - only trigger handler if event was dispatched from this element.
        </li>
        <li>
          <code>.{keyCode | keyAlias}</code> - only trigger handler on certain keys.
        </li>
        <li>
          <code>.native</code> - listen for a native event on the root element of component.
        </li>
        <li>
          <code>.once</code> - trigger handler at most once.
        </li>
        <li>
          <code>.left</code> - (2.2.0+) only trigger handler for left button mouse events.
        </li>
        <li>
          <code>.right</code> - (2.2.0+) only trigger handler for right button mouse events.
        </li>
        <li>
          <code>.middle</code> - (2.2.0+) only trigger handler for middle button mouse events.
        </li>
        <li>
          <code>.passive</code> - (2.3.0+) attaches a DOM event with <code>{ passive: true }</code>.
        </li>
      </ul>
    </li>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        Attaches an event listener to the element. The event type is denoted by the argument. The expression can be a method name, an inline statement, or omitted if there are modifiers present.
      </p>
      <p>
        Starting in 2.4.0+, <code>v-on</code> also supports binding to an object of event/listener pairs without an argument. Note when using the object syntax, it does not support any modifiers.
      </p>
      <p>
        When used on a normal element, it listens to <strong>native DOM events</strong> only. When used on a custom element component, it also listens to <strong>custom events</strong> emitted on that child component.
      </p>
      <p>
        When listening to native DOM events, the method receives the native event as the only argument. If using inline statement, the statement has access to the special <code>$event</code> property: <code>v-on:click="handle('ok', $event)"</code>.
      </p>
    </li>
    <li>
      <p>
        <strong>Example:</strong>
      </p>
      <pre><code class="html">&lt;!-- method handler --&gt;
&lt;button v-on:click="doThis"&gt;&lt;/button&gt;

&lt;!-- object syntax (2.4.0+) --&gt;
&lt;button v-on="{ mousedown: doThis, mouseup: doThat }"&gt;&lt;/button&gt;

&lt;!-- inline statement --&gt;
&lt;button v-on:click="doThat('hello', $event)"&gt;&lt;/button&gt;

&lt;!-- shorthand --&gt;
&lt;button @click="doThis"&gt;&lt;/button&gt;

&lt;!-- stop propagation --&gt;
&lt;button @click.stop="doThis"&gt;&lt;/button&gt;

&lt;!-- prevent default --&gt;
&lt;button @click.prevent="doThis"&gt;&lt;/button&gt;

&lt;!-- prevent default without expression --&gt;
&lt;form @submit.prevent&gt;&lt;/form&gt;

&lt;!-- chain modifiers --&gt;
&lt;button @click.stop.prevent="doThis"&gt;&lt;/button&gt;

&lt;!-- key modifier using keyAlias --&gt;
&lt;input @keyup.enter="onEnter"&gt;

&lt;!-- key modifier using keyCode --&gt;
&lt;input @keyup.13="onEnter"&gt;

&lt;!-- the click event will be triggered at most once --&gt;
&lt;button v-on:click.once="doThis"&gt;&lt;/button&gt;
</code></pre>
      <p>
        Listening to custom events on a child component (the handler is called when "my-event" is emitted on the child):
      </p>
      <pre><code class="html">&lt;my-component @my-event="handleThis"&gt;&lt;/my-component&gt;

&lt;!-- inline statement --&gt;
&lt;my-component @my-event="handleThis(123, $event)"&gt;&lt;/my-component&gt;

&lt;!-- native event on component --&gt;
&lt;my-component @click.native="onClick"&gt;&lt;/my-component&gt;
</code></pre>
    </li>
    <li>
      <p>
        <strong>See also:</strong>
      </p>
      <ul>
        <li>
          <a href="../guide/events.html">Event Handling</a>
        </li>
        <li>
          <a href="../guide/components.html#Custom-Events">Components - Custom Events</a>
        </li>
      </ul>
    </li>
  </ul>
  
  <h3>
    v-bind
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Shorthand:</strong> <code>:</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Expects:</strong> <code>any (with argument) | Object (without argument)</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Argument:</strong> <code>attrOrProp (optional)</code>
      </p>
    </li>
    <li>
      <p>
        <strong>Modifiers:</strong>
      </p>
      <ul>
        <li>
          <code>.prop</code> - Bind as a DOM property instead of an attribute (<a href="https://stackoverflow.com/questions/6003819/properties-and-attributes-in-html#answer-6004028">what's the difference?</a>). If the tag is a component then <code>.prop</code> will set the property on the component's <code>$el</code>.
        </li>
        <li>
          <code>.camel</code> - (2.1.0+) transform the kebab-case attribute name into camelCase.
        </li>
        <li>
          <code>.sync</code> - (2.3.0+) a syntax sugar that expands into a <code>v-on</code> handler for updating the bound value.
        </li>
      </ul>
    </li>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        Dynamically bind one or more attributes, or a component prop to an expression.
      </p>
      <p>
        When used to bind the <code>class</code> or <code>style</code> attribute, it supports additional value types such as Array or Objects. See linked guide section below for more details.
      </p>
      <p>
        When used for prop binding, the prop must be properly declared in the child component.
      </p>
      <p>
        When used without an argument, can be used to bind an object containing attribute name-value pairs. Note in this mode <code>class</code> and <code>style</code> does not support Array or Objects.
      </p>
    </li>
    <li>
      <p>
        <strong>Example:</strong>
      </p>
      <pre><code class="html">&lt;!-- bind an attribute --&gt;
&lt;img v-bind:src="imageSrc"&gt;

&lt;!-- shorthand --&gt;
&lt;img :src="imageSrc"&gt;

&lt;!-- with inline string concatenation --&gt;
&lt;img :src="'/path/to/images/' + fileName"&gt;

&lt;!-- class binding --&gt;
&lt;div :class="{ red: isRed }"&gt;&lt;/div&gt;
&lt;div :class="[classA, classB]"&gt;&lt;/div&gt;
&lt;div :class="[classA, { classB: isB, classC: isC }]"&gt;

&lt;!-- style binding --&gt;
&lt;div :style="{ fontSize: size + 'px' }"&gt;&lt;/div&gt;
&lt;div :style="[styleObjectA, styleObjectB]"&gt;&lt;/div&gt;

&lt;!-- binding an object of attributes --&gt;
&lt;div v-bind="{ id: someProp, 'other-attr': otherProp }"&gt;&lt;/div&gt;

&lt;!-- DOM attribute binding with prop modifier --&gt;
&lt;div v-bind:text-content.prop="text"&gt;&lt;/div&gt;

&lt;!-- prop binding. "prop" must be declared in my-component. --&gt;
&lt;my-component :prop="someThing"&gt;&lt;/my-component&gt;

&lt;!-- pass down parent props in common with a child component --&gt;
&lt;child-component v-bind="$props"&gt;&lt;/child-component&gt;

&lt;!-- XLink --&gt;
&lt;svg&gt;&lt;a :xlink:special="foo"&gt;&lt;/a&gt;&lt;/svg&gt;
</code></pre>
      <p>
        The <code>.camel</code> modifier allows camelizing a <code>v-bind</code> attribute name when using in-DOM templates, e.g. the SVG <code>viewBox</code> attribute:
      </p>
      <pre><code class="html">&lt;svg :view-box.camel="viewBox"&gt;&lt;/svg&gt;
</code></pre>
      <p>
        <code>.camel</code> is not needed if you are using string templates, or compiling with <code>vue-loader</code>/<code>vueify</code>.
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong>
      </p>
      <ul>
        <li>
          <a href="../guide/class-and-style.html">Class and Style Bindings</a>
        </li>
        <li>
          <a href="../guide/components.html#Props">Components - Props</a>
        </li>
        <li>
          <a href="../guide/components.html#sync-Modifier">Components - <code>.sync</code> Modifier</a>
        </li>
      </ul>
    </li>
  </ul>
  
  <h3>
    v-model
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Expects:</strong> varies based on value of form inputs element or output of components
      </p>
    </li>
    <li>
      <p>
        <strong>Limited to:</strong>
      </p>
      <ul>
        <li>
          <code>&lt;input&gt;</code>
        </li>
        <li>
          <code>&lt;select&gt;</code>
        </li>
        <li>
          <code>&lt;textarea&gt;</code>
        </li>
        <li>
          components
        </li>
      </ul>
    </li>
    <li>
      <p>
        <strong>Modifiers:</strong>
      </p>
      <ul>
        <li>
          <a href="../guide/forms.html#lazy"><code>.lazy</code></a> - listen to <code>change</code> events instead of <code>input</code>
        </li>
        <li>
          <a href="../guide/forms.html#number"><code>.number</code></a> - cast input string to numbers
        </li>
        <li>
          <a href="../guide/forms.html#trim"><code>.trim</code></a> - trim input
        </li>
      </ul>
    </li>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        Create a two-way binding on a form input element or a component. For detailed usage and other notes, see the Guide section linked below.
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong>
      </p>
      <ul>
        <li>
          <a href="../guide/forms.html">Form Input Bindings</a>
        </li>
        <li>
          <a href="../guide/components.html#Form-Input-Components-using-Custom-Events">Components - Form Input Components using Custom Events</a>
        </li>
      </ul>
    </li>
  </ul>
  
  <h3>
    v-pre
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Does not expect expression</strong>
      </p>
    </li>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        Skip compilation for this element and all its children. You can use this for displaying raw mustache tags. Skipping large numbers of nodes with no directives on them can also speed up compilation.
      </p>
    </li>
    <li>
      <p>
        <strong>Example:</strong>
      </p>
      <p>
        <pre><code>html
&lt;span v-pre&gt;{{ this will not be compiled }}&lt;/span&gt;</code></pre>
      </p>
    </li>
  </ul>
  
  <h3>
    v-cloak
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Does not expect expression</strong>
      </p>
    </li>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        This directive will remain on the element until the associated Vue instance finishes compilation. Combined with CSS rules such as <code>[v-cloak] { display: none }</code>, this directive can be used to hide un-compiled mustache bindings until the Vue instance is ready.
      </p>
    </li>
    <li>
      <p>
        <strong>Example:</strong>
      </p>
      <pre><code class="css">[v-cloak] {
  display: none;
}
</code></pre>
      <pre><code class="html">&lt;div v-cloak&gt;
  {{ message }}
&lt;/div&gt;
</code></pre>
      <p>
        The <code>&lt;div&gt;</code> will not be visible until the compilation is done.
      </p>
    </li>
  </ul>
  
  <h3>
    v-once
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Does not expect expression</strong>
      </p>
    </li>
    <li>
      <p>
        <strong>Details:</strong>
      </p>
      <p>
        Render the element and component <strong>once</strong> only. On subsequent re-renders, the element/component and all its children will be treated as static content and skipped. This can be used to optimize update performance.
      </p>
      <pre><code class="html">&lt;!-- single element --&gt;
&lt;span v-once&gt;This will never change: {{msg}}&lt;/span&gt;
&lt;!-- the element have children --&gt;
&lt;div v-once&gt;
  &lt;h1&gt;comment&lt;/h1&gt;
  &lt;p&gt;{{msg}}&lt;/p&gt;
&lt;/div&gt;
&lt;!-- component --&gt;
&lt;my-component v-once :comment="msg"&gt;&lt;/my-component&gt;
&lt;!-- `v-for` directive --&gt;
&lt;ul&gt;
  &lt;li v-for="i in list" v-once&gt;{{i}}&lt;/li&gt;
&lt;/ul&gt;
</code></pre>
    </li>
    <li>
      <p>
        <strong>See also:</strong>
      </p>
      <ul>
        <li>
          <a href="../guide/syntax.html#Text">Data Binding Syntax - interpolations</a>
        </li>
        <li>
          <a href="../guide/components.html#Cheap-Static-Components-with-v-once">Components - Cheap Static Components with <code>v-once</code></a>
        </li>
      </ul>
    </li>
  </ul>
  
  <h2>
    Special Attributes
  </h2>
  
  <h3>
    key
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Expects:</strong> <code>number | string</code>
      </p>
      <p>
        The <code>key</code> special attribute is primarily used as a hint for Vue's virtual DOM algorithm to identify VNodes when diffing the new list of nodes against the old list. Without keys, Vue uses an algorithm that minimizes element movement and tries to patch/reuse elements of the same type in-place as much as possible. With keys, it will reorder elements based on the order change of keys, and elements with keys that are no longer present will always be removed/destroyed.
      </p>
      <p>
        Children of the same common parent must have <strong>unique keys</strong>. Duplicate keys will cause render errors.
      </p>
      <p>
        The most common use case is combined with <code>v-for</code>:
      </p>
      <pre><code class="html">&lt;ul&gt;
  &lt;li v-for="item in items" :key="item.id"&gt;...&lt;/li&gt;
&lt;/ul&gt;
</code></pre>
      <p>
        It can also be used to force replacement of an element/component instead of reusing it. This can be useful when you want to:
      </p>
      <ul>
        <li>
          Properly trigger lifecycle hooks of a component
        </li>
        <li>
          Trigger transitions
        </li>
      </ul>
      <p>
        For example:
      </p>
      <pre><code class="html">&lt;transition&gt;
  &lt;span :key="text"&gt;{{ text }}&lt;/span&gt;
&lt;/transition&gt;
</code></pre>
      <p>
        When <code>text</code> changes, the <code>&lt;span&gt;</code> will always be replaced instead of patched, so a transition will be triggered.
      </p>
    </li>
  </ul>
  
  <h3>
    ref
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Expects:</strong> <code>string</code>
      </p>
      <p>
        <code>ref</code> is used to register a reference to an element or a child component. The reference will be registered under the parent component's <code>$refs</code> object. If used on a plain DOM element, the reference will be that element; if used on a child component, the reference will be component instance:
      </p>
      <pre><code class="html">&lt;!-- vm.$refs.p will be the DOM node --&gt;
&lt;p ref="p"&gt;hello&lt;/p&gt;

&lt;!-- vm.$refs.child will be the child comp instance --&gt;
&lt;child-comp ref="child"&gt;&lt;/child-comp&gt;
</code></pre>
      <p>
        When used on elements/components with <code>v-for</code>, the registered reference will be an Array containing DOM nodes or component instances.
      </p>
      <p>
        An important note about the ref registration timing: because the refs themselves are created as a result of the render function, you cannot access them on the initial render - they don't exist yet! <code>$refs</code> is also non-reactive, therefore you should not attempt to use it in templates for data-binding.
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/components.html#Child-Component-Refs">Child Component Refs</a>
      </p>
    </li>
  </ul>
  
  <h3>
    slot
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Expects:</strong> <code>string</code>
      </p>
      <p>
        Used on content inserted into child components to indicate which named slot the content belongs to.
      </p>
      <p>
        For detailed usage, see the guide section linked below.
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/components.html#Named-Slots">Named Slots</a>
      </p>
    </li>
  </ul>
  
  <h3>
    is
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Expects:</strong> <code>string</code>
      </p>
      <p>
        Used for <a href="../guide/components.html#Dynamic-Components">dynamic components</a> and to work around <a href="../guide/components.html#DOM-Template-Parsing-Caveats">limitations of in-DOM templates</a>.
      </p>
      <p>
        For example:
      </p>
      <pre><code class="html">&lt;!-- component changes when currentView changes --&gt;
&lt;component v-bind:is="currentView"&gt;&lt;/component&gt;

&lt;!-- necessary because `&lt;my-row&gt;` would be invalid inside --&gt;
&lt;!-- a `&lt;table&gt;` element and so would be hoisted out      --&gt;
&lt;table&gt;
  &lt;tr is="my-row"&gt;&lt;/tr&gt;
&lt;/table&gt;
</code></pre>
      <p>
        For detailed usage, follow the links in the description above.
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong>
      </p>
      <ul>
        <li>
          <a href="../guide/components.html#Dynamic-Components">Dynamic Components</a>
        </li>
        <li>
          <a href="../guide/components.html#DOM-Template-Parsing-Caveats">DOM Template Parsing Caveats</a>
        </li>
      </ul>
    </li>
  </ul>
  
  <h2>
    Built-In Components
  </h2>
  
  <h3>
    component
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Props:</strong>
      </p>
      <ul>
        <li>
          <code>is</code> - string | ComponentDefinition | ComponentConstructor
        </li>
        <li>
          <code>inline-template</code> - boolean
        </li>
      </ul>
    </li>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        A "meta component" for rendering dynamic components. The actual component to render is determined by the <code>is</code> prop:
      </p>
      <pre><code class="html">&lt;!-- a dynamic component controlled by --&gt;
&lt;!-- the `componentId` property on the vm --&gt;
&lt;component :is="componentId"&gt;&lt;/component&gt;

&lt;!-- can also render registered component or component passed as prop --&gt;
&lt;component :is="$options.components.child"&gt;&lt;/component&gt;
</code></pre>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/components.html#Dynamic-Components">Dynamic Components</a>
      </p>
    </li>
  </ul>
  
  <h3>
    transition
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Props:</strong>
      </p>
      <ul>
        <li>
          <code>name</code> - string, Used to automatically generate transition CSS class names. e.g. <code>name: 'fade'</code> will auto expand to <code>.fade-enter</code>, <code>.fade-enter-active</code>, etc. Defaults to <code>"v"</code>.
        </li>
        <li>
          <code>appear</code> - boolean, Whether to apply transition on initial render. Defaults to <code>false</code>.
        </li>
        <li>
          <code>css</code> - boolean, Whether to apply CSS transition classes. Defaults to <code>true</code>. If set to <code>false</code>, will only trigger JavaScript hooks registered via component events.
        </li>
        <li>
          <code>type</code> - string, Specify the type of transition events to wait for to determine transition end timing. Available values are <code>"transition"</code> and <code>"animation"</code>. By default, it will automatically detect the type that has a longer duration.
        </li>
        <li>
          <code>mode</code> - string, Controls the timing sequence of leaving/entering transitions. Available modes are <code>"out-in"</code> and <code>"in-out"</code>; defaults to simultaneous.
        </li>
        <li>
          <code>enter-class</code> - string
        </li>
        <li>
          <code>leave-class</code> - string
        </li>
        <li>
          <code>appear-class</code> - string
        </li>
        <li>
          <code>enter-to-class</code> - string
        </li>
        <li>
          <code>leave-to-class</code> - string
        </li>
        <li>
          <code>appear-to-class</code> - string
        </li>
        <li>
          <code>enter-active-class</code> - string
        </li>
        <li>
          <code>leave-active-class</code> - string
        </li>
        <li>
          <code>appear-active-class</code> - string
        </li>
      </ul>
    </li>
    <li>
      <p>
        <strong>Events:</strong>
      </p>
      <ul>
        <li>
          <code>before-enter</code>
        </li>
        <li>
          <code>before-leave</code>
        </li>
        <li>
          <code>before-appear</code>
        </li>
        <li>
          <code>enter</code>
        </li>
        <li>
          <code>leave</code>
        </li>
        <li>
          <code>appear</code>
        </li>
        <li>
          <code>after-enter</code>
        </li>
        <li>
          <code>after-leave</code>
        </li>
        <li>
          <code>after-appear</code>
        </li>
        <li>
          <code>enter-cancelled</code>
        </li>
        <li>
          <code>leave-cancelled</code> (<code>v-show</code> only)
        </li>
        <li>
          <code>appear-cancelled</code>
        </li>
      </ul>
    </li>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        <code>&lt;transition&gt;</code> serve as transition effects for <strong>single</strong> element/component. The <code>&lt;transition&gt;</code> only applies the transition behavior to the wrapped content inside; it doesn't render an extra DOM element, or show up in the inspected component hierarchy.
      </p>
      <pre><code class="html">&lt;!-- simple element --&gt;
&lt;transition&gt;
  &lt;div v-if="ok"&gt;toggled content&lt;/div&gt;
&lt;/transition&gt;

&lt;!-- dynamic component --&gt;
&lt;transition name="fade" mode="out-in" appear&gt;
  &lt;component :is="view"&gt;&lt;/component&gt;
&lt;/transition&gt;

&lt;!-- event hooking --&gt;
&lt;div id="transition-demo"&gt;
  &lt;transition @after-enter="transitionComplete"&gt;
    &lt;div v-show="ok"&gt;toggled content&lt;/div&gt;
  &lt;/transition&gt;
&lt;/div&gt;
</code></pre>
      <pre><code class="js">new Vue({
  ...
  methods: {
    transitionComplete: function (el) {
      // for passed 'el' that DOM element as the argument, something ...
    }
  }
  ...
}).$mount('#transition-demo')
</code></pre>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/transitions.html">Transitions: Entering, Leaving, and Lists</a>
      </p>
    </li>
  </ul>
  
  <h3>
    transition-group
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Props:</strong>
      </p>
      <ul>
        <li>
          <code>tag</code> - string, defaults to <code>span</code>.
        </li>
        <li>
          <code>move-class</code> - overwrite CSS class applied during moving transition.
        </li>
        <li>
          exposes the same props as <code>&lt;transition&gt;</code> except <code>mode</code>.
        </li>
      </ul>
    </li>
    <li>
      <p>
        <strong>Events:</strong>
      </p>
      <ul>
        <li>
          exposes the same events as <code>&lt;transition&gt;</code>.
        </li>
      </ul>
    </li>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        <code>&lt;transition-group&gt;</code> serve as transition effects for <strong>multiple</strong> elements/components. The <code>&lt;transition-group&gt;</code> renders a real DOM element. By default it renders a <code>&lt;span&gt;</code>, and you can configure what element is should render via the <code>tag</code> attribute.
      </p>
      <p>
        Note every child in a <code>&lt;transition-group&gt;</code> must be <strong>uniquely keyed</strong> for the animations to work properly.
      </p>
      <p>
        <code>&lt;transition-group&gt;</code> supports moving transitions via CSS transform. When a child's position on screen has changed after an updated, it will get applied a moving CSS class (auto generated from the <code>name</code> attribute or configured with the <code>move-class</code> attribute). If the CSS <code>transform</code> property is "transition-able" when the moving class is applied, the element will be smoothly animated to its destination using the <a href="https://aerotwist.com/blog/flip-your-animations/">FLIP technique</a>.
      </p>
      <pre><code class="html">&lt;transition-group tag="ul" name="slide"&gt;
  &lt;li v-for="item in items" :key="item.id"&gt;
    {{ item.text }}
  &lt;/li&gt;
&lt;/transition-group&gt;
</code></pre>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/transitions.html">Transitions: Entering, Leaving, and Lists</a>
      </p>
    </li>
  </ul>
  
  <h3>
    keep-alive
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Props:</strong>
      </p>
      <ul>
        <li>
          <code>include</code> - string or RegExp or Array. Only components matched by this will be cached.
        </li>
        <li>
          <code>exclude</code> - string or RegExp or Array. Any component matched by this will not be cached.
        </li>
      </ul>
    </li>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        When wrapped around a dynamic component, <code>&lt;keep-alive&gt;</code> caches the inactive component instances without destroying them. Similar to <code>&lt;transition&gt;</code>, <code>&lt;keep-alive&gt;</code> is an abstract component: it doesn't render a DOM element itself, and doesn't show up in the component parent chain.
      </p>
      <p>
        When a component is toggled inside <code>&lt;keep-alive&gt;</code>, its <code>activated</code> and <code>deactivated</code> lifecycle hooks will be invoked accordingly.
      </p>
      <blockquote>
        <p>
          In 2.2.0+ and above, <code>activated</code> and <code>deactivated</code> will fire for all nested components inside a <code>&lt;keep-alive&gt;</code> tree.
        </p>
      </blockquote>
      
      <p>
        Primarily used with preserve component state or avoid re-rendering.
      </p>
      <pre><code class="html">&lt;!-- basic --&gt;
&lt;keep-alive&gt;
  &lt;component :is="view"&gt;&lt;/component&gt;
&lt;/keep-alive&gt;

&lt;!-- multiple conditional children --&gt;
&lt;keep-alive&gt;
  &lt;comp-a v-if="a &gt; 1"&gt;&lt;/comp-a&gt;
  &lt;comp-b v-else&gt;&lt;/comp-b&gt;
&lt;/keep-alive&gt;

&lt;!-- used together with `&lt;transition&gt;` --&gt;
&lt;transition&gt;
  &lt;keep-alive&gt;
    &lt;component :is="view"&gt;&lt;/component&gt;
  &lt;/keep-alive&gt;
&lt;/transition&gt;
</code></pre>
      <p>
        Note, <code>&lt;keep-alive&gt;</code> is designed for the case where it has one direct child component that is being toggled. It does not work if you have <code>v-for</code> inside it. When there are multiple conditional children, as above, <code>&lt;keep-alive&gt;</code> requires that only one child is rendered at a time.
      </p>
    </li>
    <li>
      <p>
        <strong><code>include</code> and <code>exclude</code></strong>
      </p>
      <blockquote>
        <p>
          New in 2.1.0+
        </p>
      </blockquote>
      
      <p>
        The <code>include</code> and <code>exclude</code> props allow components to be conditionally cached. Both props can be a comma-delimited string, a RegExp or an Array:
      </p>
      <pre><code class="html">&lt;!-- comma-delimited string --&gt;
&lt;keep-alive include="a,b"&gt;
  &lt;component :is="view"&gt;&lt;/component&gt;
&lt;/keep-alive&gt;

&lt;!-- regex (use `v-bind`) --&gt;
&lt;keep-alive :include="/a|b/"&gt;
  &lt;component :is="view"&gt;&lt;/component&gt;
&lt;/keep-alive&gt;

&lt;!-- Array (use `v-bind`) --&gt;
&lt;keep-alive :include="['a', 'b']"&gt;
  &lt;component :is="view"&gt;&lt;/component&gt;
&lt;/keep-alive&gt;
</code></pre>
      <p>
        The match is first checked on the component's own <code>name</code> option, then its local registration name (the key in the parent's <code>components</code> option) if the <code>name</code> option is not available. Anonymous components cannot be matched against.
      </p>
    </li>
  </ul>
  
  <p class="tip">
    `<keep-alive>` does not work with functional components because they do not have instances to be cached.
  </p>
  
  <ul>
    <li>
      <strong>See also:</strong> <a href="../guide/components.html#keep-alive">Dynamic Components - keep-alive</a>
    </li>
  </ul>
  
  <h3>
    slot
  </h3>
  
  <ul>
    <li>
      <p>
        <strong>Props:</strong>
      </p>
      <ul>
        <li>
          <code>name</code> - string, Used for named slot.
        </li>
      </ul>
    </li>
    <li>
      <p>
        <strong>Usage:</strong>
      </p>
      <p>
        <code>&lt;slot&gt;</code> serve as content distribution outlets in component templates. <code>&lt;slot&gt;</code> itself will be replaced.
      </p>
      <p>
        For detailed usage, see the guide section linked below.
      </p>
    </li>
    <li>
      <p>
        <strong>See also:</strong> <a href="../guide/components.html#Content-Distribution-with-Slots">Content Distribution with Slots</a>
      </p>
    </li>
  </ul>
  
  <h2>
    VNode Interface
  </h2>
  
  <ul>
    <li>
      Please refer to the <a href="https://github.com/vuejs/vue/blob/dev/src/core/vdom/vnode.js">VNode class declaration</a>.
    </li>
  </ul>
  
  <h2>
    Server-Side Rendering
  </h2>
  
  <ul>
    <li>
      Please refer to the <a href="https://github.com/vuejs/vue/tree/dev/packages/vue-server-renderer">vue-server-renderer package documentation</a>.
    </li>
  </ul>