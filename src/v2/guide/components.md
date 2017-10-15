---
title: Component
type: guide
order: 11
---

## Component là gì?

Component là một trong những tính năng mạnh nhất của Vue. Với component, chúng ta có thể mở rộng những phần tử HTML cơ bản để đóng gói (encapsulate) code có thể tái sử dụng. Nói một cách tổng quát, component là những phần tử web tùy biến (custom element) đã được trình biên dịch của Vue đính kèm các hành vi (behavior) vào. Trong một số trường hợp, component cũng có thể xuất hiện dưới dạng một phần tử HTML bình thường với một thuộc tính `is` đặc biệt.

## Sử dụng component

### Đăng kí ở cấp toàn cục

Ở các phần trước, chúng ta đã biết rằng một đối tượng Vue có thể được khởi tạo bằng cách sau:

``` js
new Vue({
  el: '#some-element',
  // các tùy chọn
})
```

Để đăng kí một component ở cấp toàn cục, bạn có thể dùng cú pháp `Vue.component(tagName, options)`. Ví dụ:

``` js
Vue.component('my-component', {
  // các tùy chọn
})
```

<p class="tip">Vue không bắt buộc phải theo [các quy ước W3C](https://www.w3.org/TR/custom-elements/#concepts) về tên thẻ tùy biến (viết thường toàn bộ, phải sử dụng dấu gạch ngang) tuy rằng bạn cũng nên tuân thủ các quy tắc này.</p>

Một khi đã đăng kí, component có thể được sử dụng dưới dạng một phần tử tùy biến trong template của một đối tượng Vue: `<my-component></my-component>`. Bạn nhớ đăng kí component **trước khi** khởi tạo đối tượng Vue gốc. Sau đây là ví dụ hoàn chỉnh:

``` html
<div id="example">
  <my-component></my-component>
</div>
```

``` js
// đăng kí
Vue.component('my-component', {
  template: '<div>Đây là một component!</div>'
})

// tạo đối tượng Vue gốc
new Vue({
  el: '#example'
})
```

Kết quả render ra sẽ là:

``` html
<div id="example">
  <div>Đây là một component!</div>
</div>
```

{% raw %}
<div id="example" class="demo">
  <my-component></my-component>
</div>
<script>
Vue.component('my-component', {
  template: '<div>Đây là một component!</div>'
})
new Vue({ el: '#example' })
</script>
{% endraw %}

### Đăng kí ở cấp cục bộ 

Bạn không nhất thiết phải đăng kí toàn bộ các compoent ở cấp toàn cục. Thay vào đó, bạn có thể đăng kí một component bằng cách dùng tùy chọn `components` khi khởi tạo một đối tượng Vue. Với cách làm này, chỉ đối tượng Vue này mới có thể truy xuất đến component vừa đăng kí.

``` js
var Child = {
  template: '<div>Đây là một component!</div>'
}

new Vue({
  // ...
  components: {
    // <my-component> chỉ hợp lệ trong template của đối tượng cha
    'my-component': Child
  }
})
```

Cách đóng gói (encapsulation) như trên cũng áp dụng với các tính năng có thể đăng kí của Vue, ví dụ như directive.

### Lưu ý về việc parse DOM template

Khi sử dụng DOM làm template (ví dụ dùng tùy chọn `el` để gắn một phần tử web đã có sẵn nội dung), bạn sẽ phải gặp phải một số hạn chế vốn có của HTML, vì Vue chỉ có thể nhận vào nội dung của template **sau khi** trình duyệt đã parse (phân tích) và normalize (bình thường hóa) template này. Đáng lưu ý nhất, bên trong các phần tử như `<ul>`, `<ol>`, `<table>` và `<select>` chúng ta chỉ có thể chứa một số phần tử nhất định (chẳng hạn `<ul>` chỉ chấp nhận `<li>`), trong khi đó các phần tử như `<option>` lại chỉ có thể được đặt trong một số phần tử nhất định khác như `<select>`, `<optgroup>`, hay `<datalist>`. 

  Điều này sẽ dẫn đến một số vấn đề khi dùng component với các phần tử có những hạn chế vừa nêu, ví dụ:

``` html
<table>
  <my-row>...</my-row>
</table>
```

Ở đây component `<my-row>` sẽ bị xem là một phần tử không hợp lệ bên trong `<table>` và bị đẩy ra ngoài (hoisted out), dẫn đến lỗi khi render. Để giải quyết vấn đề này, ta có thể dùng thuộc tính đặc biệt `is`:

``` html
<table>
  <tr is="my-row"></tr>
</table>
```

**Cũng cần lưu ý rằng những hạn chế nêu trên không tồn tại nếu bạn sử dụng string template từ một trong các nguồn sau**:

- `<script type="text/x-template">`
- Template string bên trong JavaScript 
- Component dạng `.vue` 

Vì thế, hãy dùng string template bất cứ khi nào có thể.

### `data` phải là một hàm

Đa số các tùy chọn có thể truyền vào hàm dựng của Vue đều có thể dùng trong component, với một trường hợp đặc biệt: `data` phải là một hàm. Nếu bạn thử chạy ví dụ sau:

``` js
Vue.component('my-component', {
  template: '<span>{{ message }}</span>',
  data: {
    message: 'Xin chào'
  }
})
```

thì Vue sẽ ngừng thực thi và hiển thị cảnh báo trong console, thông báo rằng `data` trong các đối tượng component thì `data` phải là một hàm. Dĩ nhiên nếu chúng ta hiểu lí do tại sao thì tốt hơn, nên hãy thử chơi gian một chút:

``` html
<div id="example-2">
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
</div>
```

``` js
var data = { counter: 0 }

Vue.component('simple-counter', {
  template: '<button v-on:click="counter += 1">{{ counter }}</button>',
  // ở đây `data` về nguyên tắc vẫn là một hàm, 
  // nên Vue sẽ không phàn nàn gì, nhưng ta sẽ
  // trả lại cùng một tham chiếu đến object `data`
  // cho mỗi đối tượng component
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
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
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

Vì cả ba đối tượng component dùng chung một object `data` object, tăng giá trị của một bộ đếm số sẽ tăng cả ba! Chúng ta sẽ sửa lại bằng cách trả về một object data mới cho mỗi component:

``` js
data: function () {
  return {
    counter: 0
  }
}
```

Bây giờ thì mỗi bộ đếm của chúng ta sẽ có một state (trạng thái) riêng:

{% raw %}
<div id="example-2-5" class="demo">
  <my-component></my-component>
  <my-component></my-component>
  <my-component></my-component>
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

### Biên soạn component

Component được tạo ra là để dùng chung với nhau, thường thấy nhất là trong những mối quan hệ cha-con: component A có thể dùng component B trong template của mình. Chắc chắn hai component này sẽ phải giao tiếp với nhau: đối tượng cha có thể sẽ cần truyền dữ liệu cho đối tượng con, và đối tượng con cũng cần thông báo cho đối tượng cha biết khi có điều gì đó xảy ra. Tuy nhiên, một điều cũng hết sức quan trọng là hai đối tượng cha-con này cũng cần được tách biệt đến mức có thể, thông qua một interface (giao diện) được định nghĩa rõ ràng via a clearly-defined interface. Điều này bảo đảm code của mỗi component được viết và quản lí một cách biệt lập, nhờ đó giúp việc bảo trì và tái sử dụng được dễ dàng hơn.

Trong Vue, mối quan hệ component cha-con có thể được tóm tắt thành **props down, events up** (thuộc tính xuống, sự kiện lên). Component cha truyền data xuống component con bằng **props**, và component con gửi thông điệp cho component cha bằng **sự kiện**. Tiếp theo đây chúng ta sẽ xem chúng hoạt động như thế nào.

<p style="text-align: center;">
  <img style="width: 300px;" src="/images/props-events.png" alt="props down, events up">
</p>

## Props

### Truyền dữ liệu với props

Mỗi đối tượng component có một scope được cô lập (isolated) riêng. Điều này có nghĩa là bạn không thể (và cũng không nên) truy xuất đến dữ liệu cha trong template của component con. Thay vào đó, dữ liệu có thể được truyền từ cha xuống con bằng cách sử dụng **props**.

Một `prop`là một thuộc tính tùy biến để truyền thông tin từ các component cha. Một component con cần phải khai báo một cách minh bạch (explicitly) các `prop` mà nó trông đợi được nhận bằng [tùy chọn `props`](../api/#props):

``` js
Vue.component('child', {
  // khai báo props
  props: ['message'],
  // giống như `data`, prop này có thể được dùng bên trong template
  // và cũng có thể được truy xuất bên trong vm bằng this.message
  template: '<span>{{ message }}</span>'
})
```

Sau đó chúng ta có thể truyền cho nó một chuỗi con như sau:

``` html
<child message="Xin chào!"></child>
```

Result:

{% raw %}
<div id="prop-example-1" class="demo">
  <child message="Xin chào!"></child>
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

Các thuộc tính HTML không phân biệt hoa thường, vì vậy khi sử dụng template không phải là string template, tên của prop (được viết theo camelCase JavaScript) nên được chuyển thành dạng kebab-case trong HTML. Để dễ hiểu hơn, bạn có thể xem ví dụ sau đây:

``` js
Vue.component('child', {
  // camelCase trong JavaScript
  props: ['myMessage'],
  template: '<span>{{ myMessage }}</span>'
})
```

``` html
<!-- kebab-case trong HTML -->
<child my-message="hello!"></child>
```

Một lần nữa, nếu bạn đang dùng string template thì sẽ không tồn tại hạn chế này.

### Prop động

Tương tự như việc bind một thuộc tính thông thường vào một expression, chúng ta cũng có thể dùng `v-bind` để bind động props vào dữ liệu trên component cha. Bất cứ khi nào được cập nhật trong component cha, dữ liệu cũng sẽ được truyền xuống component con:

``` html
<div>
  <input v-model="parentMsg">
  <br>
  <child v-bind:my-message="parentMsg"></child>
</div>
```

Bạn cũng có thể dùng cú pháp viết tắt của `v-bind`:

``` html
<child :my-message="parentMsg"></child>
```

Kết quả:

{% raw %}
<div id="demo-2" class="demo">
  <input v-model="parentMsg">
  <br>
  <child v-bind:my-message="parentMsg"></child>
</div>
<script>
new Vue({
  el: '#demo-2',
  data: {
    parentMsg: 'Con ơi nhớ lấy câu này'
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

Nếu muốn truyền toàn bộ thuộc tính của một object thành props, bạn có thể dùng `v-bind` không có tham số (`v-bind` thay vì `v-bind:prop-name`). Ví dụ, nếu chúng ta có một object `todo`:

``` js
todo: {
  text: 'Học Vue',
  isComplete: false
}
```

thì

``` html
<todo-item v-bind="todo"></todo-item>
```

là tương đương với

``` html
<todo-item
  v-bind:text="todo.text"
  v-bind:is-complete="todo.isComplete"
></todo-item>
```

### Prop động và prop literal

Một lỗi mà người mới học thường vấp phải là thử truyền xuống một con số sử dụng cú pháp literal (trực tiếp):

``` html
<!-- cách này sẽ truyền xuống chuỗi "1" -->
<comp some-prop="1"></comp>
```

Tuy nhiên, vì đây là một prop literal, giá trị của nó được truyền xuống dưới dạng chuỗi `"1"` thay vì một con số thật sự. Để truyền xuống một giá trị kiểu number trong JavaScript, chúng ta cần dùng `v-bind` để cho giá trị của nó được xem như một biểu thức JavaScript:

``` html
<!-- cách này sẽ truyền xuống một con số thật sự -->
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

  ``` js
  props: ['initialCounter'],
  data: function () {
    return { counter: this.initialCounter }
  }
  ```

2. Define a computed property that is computed from the prop's value:

  ``` js
  props: ['size'],
  computed: {
    normalizedSize: function () {
      return this.size.trim().toLowerCase()
    }
  }
  ```

<p class="tip">Note that objects and arrays in JavaScript are passed by reference, so if the prop is an array or object, mutating the object or array itself inside the child **will** affect parent state.</p>

### Prop Validation

It is possible for a component to specify requirements for the props it is receiving. If a requirement is not met, Vue will emit warnings. This is especially useful when you are authoring a component that is intended to be used by others.

Instead of defining the props as an array of strings, you can use an object with validation requirements:

``` js
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

When prop validation fails, Vue will produce a console warning (if using the development build). Note that props are validated __before__ a component instance is created, so within `default` or `validator` functions, instance properties such as from `data`, `computed`, or `methods` will not be available.

## Non-Prop Attributes

A non-prop attribute is an attribute that is passed to a component, but does not have a corresponding prop defined.

While explicitly defined props are preferred for passing information to a child component, authors of component libraries can't always foresee the contexts in which their components might be used. That's why components can accept arbitrary attributes, which are added to the component's root element.

For example, imagine we're using a 3rd-party `bs-date-input` component with a Bootstrap plugin that requires a `data-3d-date-picker` attribute on the `input`. We can add this attribute to our component instance:

``` html
<bs-date-input data-3d-date-picker="true"></bs-date-input>
```

And the `data-3d-date-picker="true"` attribute will automatically be added to the root element of `bs-date-input`.

### Replacing/Merging with Existing Attributes

Imagine this is the template for `bs-date-input`:

``` html
<input type="date" class="form-control">
```

To specify a theme for our date picker plugin, we might need to add a specific class, like this:

``` html
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

<p class="tip">Note that Vue's event system is different from the browser's [EventTarget API](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget). Though they work similarly, `$on` and `$emit` are __not__ aliases for `addEventListener` and `dispatchEvent`.</p>

In addition, a parent component can listen to the events emitted from a child component using `v-on` directly in the template where the child component is used.

<p class="tip">You cannot use `$on` to listen to events emitted by children. You must use `v-on` directly in the template, as in the example below.</p>

Here's an example:

``` html
<div id="counter-event-example">
  <p>{{ total }}</p>
  <button-counter v-on:increment="incrementTotal"></button-counter>
  <button-counter v-on:increment="incrementTotal"></button-counter>
</div>
```

``` js
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
  <p>{{ total }}</p>
  <button-counter v-on:increment="incrementTotal"></button-counter>
  <button-counter v-on:increment="incrementTotal"></button-counter>
</div>
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
{% endraw %}

In this example, it's important to note that the child component is still completely decoupled from what happens outside of it. All it does is report information about its own activity, just in case a parent component might care.

### Binding Native Events to Components

There may be times when you want to listen for a native event on the root element of a component. In these cases, you can use the `.native` modifier for `v-on`. For example:

``` html
<my-component v-on:click.native="doTheThing"></my-component>
```

### `.sync` Modifier

> 2.3.0+

In some cases we may need "two-way binding" for a prop - in fact, in Vue 1.x this is exactly what the `.sync` modifier provided. When a child component mutates a prop that has `.sync`, the value change will be reflected in the parent. This is convenient, however it leads to maintenance issues in the long run because it breaks the one-way data flow assumption: the code that mutates child props are implicitly affecting parent state.

This is why we removed the `.sync` modifier when 2.0 was released. However, we've found that there are indeed cases where it could be useful, especially when shipping reusable components. What we need to change is **making the code in the child that affects parent state more consistent and explicit.**

In 2.3.0+ we re-introduced the `.sync` modifier for props, but this time it is only syntax sugar that automatically expands into an additional `v-on` listener:

The following

``` html
<comp :foo.sync="bar"></comp>
```

is expanded into:

``` html
<comp :foo="bar" @update:foo="val => bar = val"></comp>
```

For the child component to update `foo`'s value, it needs to explicitly emit an event instead of mutating the prop:

``` js
this.$emit('update:foo', newValue)
```

### Form Input Components using Custom Events

Custom events can also be used to create custom inputs that work with `v-model`. Remember:

``` html
<input v-model="something">
```

is syntactic sugar for:

``` html
<input
  v-bind:value="something"
  v-on:input="something = $event.target.value">
```

When used with a component, it instead simplifies to:

``` html
<custom-input
  :value="something"
  @input="value => { something = value }">
</custom-input>
```

So for a component to work with `v-model`, it should (these can be configured in 2.2.0+):

- accept a `value` prop
- emit an `input` event with the new value

Let's see it in action with a simple currency input:

``` html
<currency-input v-model="price"></currency-input>
```

``` js
Vue.component('currency-input', {
  template: '\
    <span>\
      $\
      <input\
        ref="input"\
        v-bind:value="value"\
        v-on:input="updateValue($event.target.value)">\
    </span>\
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
```

{% raw %}
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
{% endraw %}

The implementation above is pretty naive though. For example, users are allowed to enter multiple periods and even letters sometimes - yuck! So for those that want to see a non-trivial example, here's a more robust currency filter:

<iframe width="100%" height="300" src="https://jsfiddle.net/chrisvfritz/1oqjojjx/embedded/result,html,js" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

### Customizing Component `v-model`

> New in 2.2.0+

By default, `v-model` on a component uses `value` as the prop and `input` as the event, but some input types such as checkboxes and radio buttons may want to use the `value` prop for a different purpose. Using the `model` option can avoid the conflict in such cases:

``` js
Vue.component('my-checkbox', {
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
```

``` html
<my-checkbox v-model="foo" value="some value"></my-checkbox>
```

The above will be equivalent to:

``` html
<my-checkbox
  :checked="foo"
  @change="val => { foo = val }"
  value="some value">
</my-checkbox>
```

<p class="tip">Note that you still have to declare the `checked` prop explicitly.</p>

### Non Parent-Child Communication

Sometimes two components may need to communicate with one-another but they are not parent/child to each other. In simple scenarios, you can use an empty Vue instance as a central event bus:

``` js
var bus = new Vue()
```
``` js
// in component A's method
bus.$emit('id-selected', 1)
```
``` js
// in component B's created hook
bus.$on('id-selected', function (id) {
  // ...
})
```

In more complex cases, you should consider employing a dedicated [state-management pattern](state-management.html).

## Content Distribution with Slots

When using components, it is often desired to compose them like this:

``` html
<app>
  <app-header></app-header>
  <app-footer></app-footer>
</app>
```

There are two things to note here:

1. The `<app>` component does not know what content it will receive. It is decided by the component using `<app>`.

2. The `<app>` component very likely has its own template.

To make the composition work, we need a way to interweave the parent "content" and the component's own template. This is a process called **content distribution** (or "transclusion" if you are familiar with Angular). Vue.js implements a content distribution API that is modeled after the current [Web Components spec draft](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md), using the special `<slot>` element to serve as distribution outlets for the original content.

### Compilation Scope

Before we dig into the API, let's first clarify which scope the contents are compiled in. Imagine a template like this:

``` html
<child-component>
  {{ message }}
</child-component>
```

Should the `message` be bound to the parent's data or the child data? The answer is the parent. A simple rule of thumb for component scope is:

> Everything in the parent template is compiled in parent scope; everything in the child template is compiled in child scope.

A common mistake is trying to bind a directive to a child property/method in the parent template:

``` html
<!-- does NOT work -->
<child-component v-show="someChildProperty"></child-component>
```

Assuming `someChildProperty` is a property on the child component, the example above would not work. The parent's template is not aware of the state of a child component.

If you need to bind child-scope directives on a component root node, you should do so in the child component's own template:

``` js
Vue.component('child-component', {
  // this does work, because we are in the right scope
  template: '<div v-show="someChildProperty">Child</div>',
  data: function () {
    return {
      someChildProperty: true
    }
  }
})
```

Similarly, distributed content will be compiled in the parent scope.

### Single Slot

Parent content will be **discarded** unless the child component template contains at least one `<slot>` outlet. When there is only one slot with no attributes, the entire content fragment will be inserted at its position in the DOM, replacing the slot itself.

Anything originally inside the `<slot>` tags is considered **fallback content**. Fallback content is compiled in the child scope and will only be displayed if the hosting element is empty and has no content to be inserted.

Suppose we have a component called `my-component` with the following template:

``` html
<div>
  <h2>I'm the child title</h2>
  <slot>
    This will only be displayed if there is no content
    to be distributed.
  </slot>
</div>
```

And a parent that uses the component:

``` html
<div>
  <h1>I'm the parent title</h1>
  <my-component>
    <p>This is some original content</p>
    <p>This is some more original content</p>
  </my-component>
</div>
```

The rendered result will be:

``` html
<div>
  <h1>I'm the parent title</h1>
  <div>
    <h2>I'm the child title</h2>
    <p>This is some original content</p>
    <p>This is some more original content</p>
  </div>
</div>
```

### Named Slots

`<slot>` elements have a special attribute, `name`, which can be used to further customize how content should be distributed. You can have multiple slots with different names. A named slot will match any element that has a corresponding `slot` attribute in the content fragment.

There can still be one unnamed slot, which is the **default slot** that serves as a catch-all outlet for any unmatched content. If there is no default slot, unmatched content will be discarded.

For example, suppose we have an `app-layout` component with the following template:

``` html
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

Parent markup:

``` html
<app-layout>
  <h1 slot="header">Here might be a page title</h1>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <p slot="footer">Here's some contact info</p>
</app-layout>
```

The rendered result will be:

``` html
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

The content distribution API is a very useful mechanism when designing components that are meant to be composed together.

### Scoped Slots

> New in 2.1.0+

A scoped slot is a special type of slot that functions as a reusable template (that can be passed data to) instead of already-rendered-elements.

In a child component, pass data into a slot as if you are passing props to a component:

``` html
<div class="child">
  <slot text="hello from child"></slot>
</div>
```

In the parent, a `<template>` element with a special attribute `scope` must exist, indicating that it is a template for a scoped slot. The value of `scope` is the name of a temporary variable that holds the props object passed from the child:

``` html
<div class="parent">
  <child>
    <template scope="props">
      <span>hello from parent</span>
      <span>{{ props.text }}</span>
    </template>
  </child>
</div>
```

If we render the above, the output will be:

``` html
<div class="parent">
  <div class="child">
    <span>hello from parent</span>
    <span>hello from child</span>
  </div>
</div>
```

A more typical use case for scoped slots would be a list component that allows the component consumer to customize how each item in the list should be rendered:

``` html
<my-awesome-list :items="items">
  <!-- scoped slot can be named too -->
  <template slot="item" scope="props">
    <li class="my-fancy-item">{{ props.text }}</li>
  </template>
</my-awesome-list>
```

And the template for the list component:

``` html
<ul>
  <slot name="item"
    v-for="item in items"
    :text="item.text">
    <!-- fallback content here -->
  </slot>
</ul>
```

## Dynamic Components

You can use the same mount point and dynamically switch between multiple components using the reserved `<component>` element and dynamically bind to its `is` attribute:

``` js
var vm = new Vue({
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
```

``` html
<component v-bind:is="currentView">
  <!-- component changes when vm.currentView changes! -->
</component>
```

If you prefer, you can also bind directly to component objects:

``` js
var Home = {
  template: '<p>Welcome home!</p>'
}

var vm = new Vue({
  el: '#example',
  data: {
    currentView: Home
  }
})
```

### `keep-alive`

If you want to keep the switched-out components in memory so that you can preserve their state or avoid re-rendering, you can wrap a dynamic component in a `<keep-alive>` element:

``` html
<keep-alive>
  <component :is="currentView">
    <!-- inactive components will be cached! -->
  </component>
</keep-alive>
```

Check out more details on `<keep-alive>` in the [API reference](../api/#keep-alive).

## Misc

### Authoring Reusable Components

When authoring components, it's good to keep in mind whether you intend to reuse it somewhere else later. It's OK for one-off components to be tightly coupled, but reusable components should define a clean public interface and make no assumptions about the context it's used in.

The API for a Vue component comes in three parts - props, events, and slots:

- **Props** allow the external environment to pass data into the component

- **Events** allow the component to trigger side effects in the external environment

- **Slots** allow the external environment to compose the component with extra content.

With the dedicated shorthand syntaxes for `v-bind` and `v-on`, the intents can be clearly and succinctly conveyed in the template:

``` html
<my-component
  :foo="baz"
  :bar="qux"
  @event-a="doThis"
  @event-b="doThat"
>
  <img slot="icon" src="...">
  <p slot="main-text">Hello!</p>
</my-component>
```

### Child Component Refs

Despite the existence of props and events, sometimes you might still need to directly access a child component in JavaScript. To achieve this you have to assign a reference ID to the child component using `ref`. For example:

``` html
<div id="parent">
  <user-profile ref="profile"></user-profile>
</div>
```

``` js
var parent = new Vue({ el: '#parent' })
// access child component instance
var child = parent.$refs.profile
```

When `ref` is used together with `v-for`, the ref you get will be an array containing the child components mirroring the data source.

<p class="tip">`$refs` are only populated after the component has been rendered, and it is not reactive. It is only meant as an escape hatch for direct child manipulation - you should avoid using `$refs` in templates or computed properties.</p>

### Async Components

In large applications, we may need to divide the app into smaller chunks and only load a component from the server when it's actually needed. To make that easier, Vue allows you to define your component as a factory function that asynchronously resolves your component definition. Vue will only trigger the factory function when the component actually needs to be rendered and will cache the result for future re-renders. For example:

``` js
Vue.component('async-example', function (resolve, reject) {
  setTimeout(function () {
    // Pass the component definition to the resolve callback
    resolve({
      template: '<div>I am async!</div>'
    })
  }, 1000)
})
```

The factory function receives a `resolve` callback, which should be called when you have retrieved your component definition from the server. You can also call `reject(reason)` to indicate the load has failed. The `setTimeout` here is for demonstration; how to retrieve the component is up to you. One recommended approach is to use async components together with [Webpack's code-splitting feature](https://webpack.js.org/guides/code-splitting/):

``` js
Vue.component('async-webpack-example', function (resolve) {
  // This special require syntax will instruct Webpack to
  // automatically split your built code into bundles which
  // are loaded over Ajax requests.
  require(['./my-async-component'], resolve)
})
```

You can also return a `Promise` in the factory function, so with Webpack 2 + ES2015 syntax you can do:

``` js
Vue.component(
  'async-webpack-example',
  () => import('./my-async-component')
)
```

When using [local registration](components.html#Local-Registration), you can also directly provide a function that returns a `Promise`:

``` js
new Vue({
  // ...
  components: {
    'my-component': () => import('./my-async-component')
  }
})
```

<p class="tip">If you're a <strong>Browserify</strong> user that would like to use async components, its creator has unfortunately [made it clear](https://github.com/substack/node-browserify/issues/58#issuecomment-21978224) that async loading "is not something that Browserify will ever support." Officially, at least. The Browserify community has found [some workarounds](https://github.com/vuejs/vuejs.org/issues/620), which may be helpful for existing and complex applications. For all other scenarios, we recommend using Webpack for built-in, first-class async support.</p>

### Advanced Async Components

> New in 2.3.0+

Starting in 2.3.0+ the async component factory can also return an object of the following format:

``` js
const AsyncComp = () => ({
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
```

Note that when used as a route component in `vue-router`, these properties will be ignored because async components are resolved upfront before the route navigation happens. You also need to use `vue-router` 2.4.0+ if you wish to use the above syntax for route components.

### Component Naming Conventions

When registering components (or props), you can use kebab-case, camelCase, or PascalCase.

``` js
// in a component definition
components: {
  // register using kebab-case
  'kebab-cased-component': { /* ... */ },
  // register using camelCase
  'camelCasedComponent': { /* ... */ },
  // register using PascalCase
  'PascalCasedComponent': { /* ... */ }
}
```

Within HTML templates though, you have to use the kebab-case equivalents:

``` html
<!-- always use kebab-case in HTML templates -->
<kebab-cased-component></kebab-cased-component>
<camel-cased-component></camel-cased-component>
<pascal-cased-component></pascal-cased-component>
```

When using _string_ templates however, we're not bound by HTML's case-insensitive restrictions. That means even in the template, you can reference your components using:

- kebab-case
- camelCase or kebab-case if the component has been defined using camelCase
- kebab-case, camelCase or PascalCase if the component has been defined using PascalCase

``` js
components: {
  'kebab-cased-component': { /* ... */ },
  camelCasedComponent: { /* ... */ },
  PascalCasedComponent: { /* ... */ }
}
```

``` html
<kebab-cased-component></kebab-cased-component>

<camel-cased-component></camel-cased-component>
<camelCasedComponent></camelCasedComponent>

<pascal-cased-component></pascal-cased-component>
<pascalCasedComponent></pascalCasedComponent>
<PascalCasedComponent></PascalCasedComponent>
```

This means that the PascalCase is the most universal _declaration convention_ and kebab-case is the most universal _usage convention_.

If your component isn't passed content via `slot` elements, you can even make it self-closing with a `/` after the name:

``` html
<my-component/>
```

Again, this _only_ works within string templates, as self-closing custom elements are not valid HTML and your browser's native parser will not understand them.

### Recursive Components

Components can recursively invoke themselves in their own template. However, they can only do so with the `name` option:

``` js
name: 'unique-name-of-my-component'
```

When you register a component globally using `Vue.component`, the global ID is automatically set as the component's `name` option.

``` js
Vue.component('unique-name-of-my-component', {
  // ...
})
```

If you're not careful, recursive components can also lead to infinite loops:

``` js
name: 'stack-overflow',
template: '<div><stack-overflow></stack-overflow></div>'
```

A component like the above will result in a "max stack size exceeded" error, so make sure recursive invocation is conditional (i.e. uses a `v-if` that will eventually be `false`).

### Circular References Between Components

Let's say you're building a file directory tree, like in Finder or File Explorer. You might have a `tree-folder` component with this template:

``` html
<p>
  <span>{{ folder.name }}</span>
  <tree-folder-contents :children="folder.children"/>
</p>
```

Then a `tree-folder-contents` component with this template:

``` html
<ul>
  <li v-for="child in children">
    <tree-folder v-if="child.children" :folder="child"/>
    <span v-else>{{ child.name }}</span>
  </li>
</ul>
```

When you look closely, you'll see that these components will actually be each other's descendent _and_ ancestor in the render tree - a paradox! When registering components globally with `Vue.component`, this paradox is resolved for you automatically. If that's you, you can stop reading here.

However, if you're requiring/importing components using a __module system__, e.g. via Webpack or Browserify, you'll get an error:

```
Failed to mount component: template or render function not defined.
```

To explain what's happening, let's call our components A and B. The module system sees that it needs A, but first A needs B, but B needs A, but A needs B, etc, etc. It's stuck in a loop, not knowing how to fully resolve either component without first resolving the other. To fix this, we need to give the module system a point at which it can say, "A needs B _eventually_, but there's no need to resolve B first."

In our case, let's make that point the `tree-folder` component. We know the child that creates the paradox is the `tree-folder-contents` component, so we'll wait until the `beforeCreate` lifecycle hook to register it:

``` js
beforeCreate: function () {
  this.$options.components.TreeFolderContents = require('./tree-folder-contents.vue')
}
```

Problem solved!

### Inline Templates

When the `inline-template` special attribute is present on a child component, the component will use its inner content as its template, rather than treating it as distributed content. This allows more flexible template-authoring.

``` html
<my-component inline-template>
  <div>
    <p>These are compiled as the component's own template.</p>
    <p>Not parent's transclusion content.</p>
  </div>
</my-component>
```

However, `inline-template` makes the scope of your templates harder to reason about. As a best practice, prefer defining templates inside the component using the `template` option or in a `template` element in a `.vue` file.

### X-Templates

Another way to define templates is inside of a script element with the type `text/x-template`, then referencing the template by an id. For example:

``` html
<script type="text/x-template" id="hello-world-template">
  <p>Hello hello hello</p>
</script>
```

``` js
Vue.component('hello-world', {
  template: '#hello-world-template'
})
```

These can be useful for demos with large templates or in extremely small applications, but should otherwise be avoided, because they separate templates from the rest of the component definition.

### Cheap Static Components with `v-once`

Rendering plain HTML elements is very fast in Vue, but sometimes you might have a component that contains **a lot** of static content. In these cases, you can ensure that it's only evaluated once and then cached by adding the `v-once` directive to the root element, like this:

``` js
Vue.component('terms-of-service', {
  template: '\
    <div v-once>\
      <h1>Terms of Service</h1>\
      ... a lot of static content ...\
    </div>\
  '
})
```
