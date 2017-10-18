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

Tuy nhiên, vì đây là một prop literal, giá trị của nó được truyền xuống dưới dạng chuỗi `"1"` thay vì một con số thật sự. Muốn truyền xuống một giá trị kiểu number trong JavaScript, chúng ta cần dùng `v-bind` để cho giá trị của nó được xem như một biểu thức JavaScript:


``` html
<!-- cách này sẽ truyền xuống một con số thật sự -->
<comp v-bind:some-prop="1"></comp>
```

### Luồng dữ liệu một chiều

Tất cả các prop tạo ra một ràng buộc (binding) **đi xuống một chiều** giữa thuộc tính của cha và thuộc tính của con. Khi thuộc tính của cha được cập nhật, sự thay đổi này sẽ được truyền xuống dưới, nhưng không xảy ra điều ngược lại. Điều này ngăn không cho component con vô tình thay đổi trạng thái của cha (và làm luồng dữ liệu trở nên rối loạn).

Thêm vào đó, mỗi khi component cha được cập nhật, toàn bộ prop trong component con sẽ được refresh với giá trị mới nhất. Điều này có nghĩa là bạn **không nên** thay đổi prop bên trong component con. Nếu bạn vẫn cố làm, Vue sẽ hiện cảnh báo trong console.

Thông thường thì có hai trường hợp bạn muốn thay đổi một prop:

1. Prop này được dùng để truyền một giá trị ban đầu, sau đó component con muốn dùng giá trị này như một thuộc tính dữ liệu cục bộ.
2. Prop được truyền xuống dưới dạng giá trị thô (raw value) và cần được chuyển đổi

Cách làm đúng cho hai trường hợp trên là:

1. Định nghĩa một thuộc tính dữ liệu cục bộ và dùng giá trị ban đầu của prop làm giá trị ban đầu của thuộc tính này:

  ``` js
  props: ['initialCounter'],
  data: function () {
    return { counter: this.initialCounter }
  }
  ```

2. Đĩnh nghĩa một [computed property](computed.html) dựa trên giá trị của prop:

  ``` js
  props: ['size'],
  computed: {
    normalizedSize: function () {
      return this.size.trim().toLowerCase()
    }
  }
  ```

<p class="tip">Lưu ý là object và array trong JavaScript được truyền bằng tham chiếu, vì vậy nếu prop là một array hay object, thay đổi array hay object này bên trong component con cũng sẽ ảnh hưởng đến component cha.</p>

### Kiểm chứng prop

Component có thể chỉ định một số yêu cầu (requirement) cho prop. Nếu có yêu cầu nào không được thỏa mãn, Vue sẽ cảnh báo. Điều này trở nên đặc biệt hữu ích khi component bạn đang viết là để cho người khác dùng.

Thay vì định nghĩa các prop dưới dạng một mảng chứa tên prop, bạn có thể dùng một object chứa các yêu cầu kiểm chứng (validation requirement).

``` js
Vue.component('example', {
  props: {
    // kiểm tra kiểu dữ liệu cơ bản (`null` chấp nhận tất cả các kiểu)
    propA: Number,
    // chấp nhận một số kiểu dữ liệu cùng lúc
    propB: [String, Number],
    // một chuỗi bắt buộc
    propC: {
      type: String,
      required: true
    },
    // một con số với giá trị mặc định
    propD: {
      type: Number,
      default: 100
    },
    // giá trị mặc định cho object/array nên được trả về 
    // từ một hàm factory
    propE: {
      type: Object,
      default: function () {
        return { message: 'Xin chào' }
      }
    },
    // hàm kiểm tra tùy biến
    propF: {
      validator: function (value) {
        return value > 10
      }
    }
  }
})
```

Thuộc tính `type` có thể là một trong các hàm dựng native sau:

- String
- Number
- Boolean
- Function
- Object
- Array
- Symbol

Ngoài ra, `type` cũng có thể là một hàm dựng tùy biến (custom constructor), và Vue sẽ so sánh bằng lệnh `instanceof`.

Khi prop không thỏa mãn một hay nhiều điều kiện đã đặt ra, Vue sẽ cảnh báo trong console (nếu bạn đang dùng [bản development](installation.html#Che-do-development-va-production)). Lưu ý rằng prop được kiểm chứng __trước khi__ đối tượng component được khởi tạo, vì vậy bên trong các hàm `default` hoặc `validator`, các thuộc tính đối tượng như `data`, `computed`, hay `methods` sẽ không khả dụng.

## Các thuộc tính non-prop

Thuộc tính non-prop là một thuộc tính được truyền vào component mà không có prop tương ứng được định nghĩa sẵn. 

Tuy props nên được định nghĩa một cách minh bạch bất cứ khi nào có thể, tác giả của các thư viện component không phải lúc nào cũng có thể thấy trước được ngữ cảnh mà component của mình được sử dụng. Đó là lí do component có thể nhận những giá trị "linh động" hơn, các giá trị này được thêm vào root của component.

Ví dụ, thử tưởng tượng chúng ta sử dụng component bên thứ ba gọi là `bs-date-input` với một plugin Bootstrap. Plugin này yêu cầu một thuộc tính tên là `data-3d-date-picker` trên `input`. Chúng ta có thể thêm thuộc tính này vào đối tượng component của chúng ta như sau:

``` html
<bs-date-input data-3d-date-picker="true"></bs-date-input>
```

Ở đây thuộc tính `data-3d-date-picker="true"` sẽ được tự động gắn vào phần tử root của `bs-date-input`.

### Thay thế / sáp nhập với các thuộc tính sẵn có

Ví dụ đây là template của `bs-date-input`:

``` html
<input type="date" class="form-control">
```

Để chỉ định một theme cho plugin date picker, có thể chúng ta sẽ phải thêm vào một `class` như sau:

``` html
<bs-date-input
  data-3d-date-picker="true"
  class="date-picker-theme-dark"
></bs-date-input>
```

Như vậy ở đây có đến hai giá trị cho thuộc tính `class`:

- `form-control`, gán trong template của component
- `date-picker-theme-dark`, truyền vào component con từ đối tượng cha

Đối với đa số các thuộc tính, giá trị truyền vào sẽ thay thế cho giá trị được gắn sẵn trong component, có nghĩa là truyền vào `type="large"` sẽ thay thế `type="date"` (và có thể là làm hỏng luôn chương trình!) May thay, các thuộc tính `class` và `style` thông minh hơn một chút vã sẽ sáp nhập (merge) các giá trị lại với nhau, tạo thành kết quả cuối cùng: `class="form-control date-picker-theme-dark"`.

## Các sự kiện tùy biến

Chúng ta đã biết rằng đối tượng cha có thể truyền dữ liệu xuống đối tượng con thông qua prop, nhưng nếu có gì đó xảy ra thì chúng ta làm thế nào để giao tiếp ngược lại từ đối tượng con lên đối tượng cha? Câu trả lời là hệ thống các sự kiện tùy biến (custom event) của Vue.

### Sử dụng `v-on` với các sự kiện tùy biến

Mỗi đối tượng Vue đều phát triển một [giao diện sự kiện](../api/#Instance-Methods-Events), có nghĩa là nó có thể:

- Lắng nghe một sự kiện với `$on(eventName)`
- Kích hoạt một sự kiện với `$emit(eventName)`

<p class="tip">Lưu ý rằng hệ thống sự kiện của Vue khác với [EventTarget API](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget) của trình duyệt. Tuy cách hoạt động có vẻ giống nhau, `$on` và `$emit` __không phải__ là alias của `addEventListener` và `dispatchEvent`.</p>

Thêm vào đó, một component cha có thể lắng nghe các sự kiện được component con phát ra bằng cách sử dụng `v-on` trực tiếp trên template nơi component con được nhúng vào.

<p class="tip">Bạn không thể dùng `$on` để lắng nghe sự kiện được component con phát ra. Thay vào đó, bạn phải dùng `v-on` trực tiếp trong template, như trong ví dụ dưới đây.</p>

Đây là một ví dụ: 

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

Trong ví dụ này, cần lưu ý rằng đối tượng con hoàn toàn không bị ràng buộc gì với thế giới bên ngoài. Nó chỉ làm đúng một việc là thông báo thông tin về hoạt động của chính mình – lắng nghe và xử lí thế nào hoàn toàn là việc của component cha.

### Bind sự kiện native vào component

Đôi khi bạn cũng muốn lắng nghe một sự kiện native trên phần tử root của component. Trong những trường hợp này, bạn có thể sử dụng modifier `.native` cho `v-on`. Ví dụ:

``` html
<my-component v-on:click.native="doTheThing"></my-component>
```

### Modifier `.sync`

> 2.3.0+

Trong một số trường hợp có thể chúng ta cần "two-way binding" (ràng buộc hai chiều) cho một prop - thật ra, trong 1.x đây chính xác là mục đích của modifier `.sync`. Khi component con thay đổi một prop có modifier `.sync`, giá trị ở parent cũng sẽ thay đổi theo. Điều này tiện thì có tiện nhưng về lâu dài sẽ làm cho việc bảo trì phần mềm gặp khó khăn vì nó phá vỡ luồng dữ liệu một chiều: code thay đổi prop của con cũng lẳng lặng làm ảnh hưởng đến trạng thái của cha. Đây chính là lí do chúng tôi quyết định bỏ modifier `.sync` khi ra mắt phiên bản 2.0. 

Tuy nhiên, modifier `.sync` như trên vẫn có giá trị trong một số trường hợp nhất định, đặc biệt là khi ship những component tái sử dụng được. Cái chúng ta cần ở đây là **làm cho những đoạn code trong component con ảnh hưởng đến trạng thái của component cha được minh bạch (explicit) và ổn định (consistent) hơn.**

Từ bản 2.3.0 trở đi, chúng tôi giới thiệu lại modifier `.sync` cho prop, nhưng lần này `.sync` chỉ là một syntactic sugar (cú pháp đẹp/dễ nhìn) tự động mở rộng thêm thành một listener `v-on`:

Đoạn code sau

``` html
<comp :foo.sync="bar"></comp>
```

sẽ được mở rộng ra thành:

``` html
<comp :foo="bar" @update:foo="val => bar = val"></comp>
```

Để có thể cập nhật giá trị của `foo`, component con phải phát ra một sự kiện một cách minh bạch thay vì trực tiếp thay đổi `foo`:

``` js
this.foo = 'baz' // cách làm sai, và Vue sẽ cảnh báo
this.$emit('update:foo', newValue) // OK
```

### Sử dụng sự kiện tùy biến với form input component

Các sự kiện tùy biến cũng có thể được dùng để tạo custom input hoạt động với `v-model`. Nhớ là:

``` html
<input v-model="something">
```

là syntactic sugar của:

``` html
<input
  v-bind:value="something"
  v-on:input="something = $event.target.value">
```

Khi sử dụng với một component, nó được đơn giản hóa thành:

``` html
<custom-input
  :value="something"
  @input="value => { something = value }">
</custom-input>
```

Vì thế để hoạt động với `v-model`, một component cần

- nhận một prop `value`
- $emit một sự kiện `input` với giá trị mới

(những giá trị này tùy chỉnh được từ phiên bản 2.2.0 trở đi):

Chúng ta hãy xem ví dụ sau:

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
    // Thay vì cập nhật giá trị trực tiếp, phương thức này
    // được dùng để format và đặt một số ràng buộc lên giá trị
    // của input
    updateValue: function (value) {
      var formattedValue = value
        // Bỏ khoảng trắng ở hai bên
        .trim()
        // Rút ngắn lại còn hai chữ số thập phân
        .slice(
          0,
          value.indexOf('.') === -1
            ? value.length
            : value.indexOf('.') + 3
        )
      // Nếu giá trị chưa được chuẩn hóa, ta ghi đè (override)
      // để bắt nó chuẩn không cần chỉnh
      if (formattedValue !== value) {
        this.$refs.input.value = formattedValue
      }
      // Phát ra sự kiện input
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

Ví dụ trên thật ra còn khá sơ sài, đơn cử như người dùng vẫn có thể nhập vào nhiều dấu chấm và đôi khi cả chữ cái. Sau đây là một ví dụ hoàn chỉnh hơn:

<iframe width="100%" height="300" src="//jsfiddle.net/phanan/1oqjojjx/1464/embedded/result,html,js/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

### Tùy biến `v-model` cho component

> 2.2.0+

Mặc định, `v-model` dùng cho một component sử dụng prop tên là `value` và sự kiện tên là `input`. Tuy nhiên, một số kiểu input như checkbox và radio button có thể dùng prop `value` vào mục đích khác. Trong những trường hợp như vậy, tùy chọn `model` sẽ giúp chúng ta tránh được xung đột:

``` js
Vue.component('my-checkbox', {
  model: {
    prop: 'checked',
    event: 'change'
  },
  props: {
    checked: Boolean,
    // ở đây chúng ta có thể sử dụng prop `value` 
    // vào một mục đích khác
    value: String
  },
  // ...
})
```

``` html
<my-checkbox v-model="foo" value="some value"></my-checkbox>
```

Đoạn code trên là tương đồng với đoạn dưới đây:

``` html
<my-checkbox
  :checked="foo"
  @change="val => { foo = val }"
  value="some value">
</my-checkbox>
```

<p class="tip">Lưu ý là bạn vẫn phải khai báo prop `checked` một cách minh bạch.</p>

### Giao tiếp giữa hai component không phải cha-con

Đôi lúc hai component cần giao tiếp với nhau nhưng lại không có mối quan hệ cha-con (component không trực tiếp chứa component kia). Trong những trường hợp đơn giản, bạn có thể dùng một đối tượng Vue rỗng để làm một event bus (nôm na là kênh truyền tải sự kiện).

``` js
var bus = new Vue()
```
``` js
// trong phương thức của component A
bus.$emit('id-selected', 1)
```
``` js
// trong hook `created` của component B
bus.$on('id-selected', function (id) {
  // ...
})
```

Trong những trường hợp phức tạp hơn, bạn nên xem xét sử dụng một [pattern quản lí trạng thái](state-management.html).

## Phân bố nội dung với slot

Khi dùng component, thông thường ta sẽ muốn kết hợp như sau:

``` html
<app>
  <app-header></app-header>
  <app-footer></app-footer>
</app>
```

Ở đây có hai điểm cần lưu ý:

1. Component `<app>` không biết nội dung nó nhận được là gì. Thay vào đó, nội dung của `<app>` được quyết định bởi các component con, trong trường hợp này là `<app-header>` và `<app-footer>`.
2. Thường thì component `<app>` có template riêng.

Để đạt được kết quả như trên, chúng ta cần có một cách để trộn lẫn nội dung và template của component cha. Quá trình này gọi là **phân bố nội dung** (content distribution, hay còn gọi là "transclusion" trong Angular). Vue.js phát triển một API phân bố nội dung dựa trên [bản quy tắc về Web Component](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md) hiện hành, sử dụng phần tử đặc biệt `<slot>` để làm các _outlet_ phân bố cho nội dung ban đầu.

### Scope khi biên dịch

Trước khi đi sâu vào API, trước tiên chúng ta phải làm rõ: nội dung được biên dịch trong scope nào? Tưởng tượng chúng ta có một template như sau:

``` html
<child-component>
  {{ message }}
</child-component>
```

Trong trường hợp này thì `message` nên là dữ liệu của component cha hay component con? Câu trả lời là component cha. Một quy tắc cơ bản về scope của component là:

> Cái gì trong template của cha thì được biên dịch trong scope của cha, cái gì trong template của con thì được biên dịch trong scope của con.

Một lỗi mà người dùng hay mắc phải là cố bind một directive vào một thuộc tính hay phương thức của component con trong template của cha:

``` html
<!-- cách này KHÔNG HOẠT ĐỘNG -->
<child-component v-show="someChildProperty"></child-component>
```

Giả định `someChildProperty` là một thuộc tính của component con, ví dụ trên sẽ không hoạt động. Template của component cha không biết gì về trạng thái của component con.

Nếu muốn bind các directive với scope con trên node gốc của một component, bạn nên làm thế trong template của chính component con:

``` js
Vue.component('child-component', {
  // cách này sẽ hoạt động, vì chúng ta đang ở đúng scope
  template: '<div v-show="someChildProperty">Child</div>',
  data: function () {
    return {
      someChildProperty: true
    }
  }
})
```

Tương tự như vậy, nội dung được phân bố sẽ được biên dịch trong scope của cha.

### Slot đơn lẻ

Nội dung của component cha sẽ **bị loại bỏ** trừ phi template của component con chứa ít nhất một `<slot>`. Khi chỉ có một slot và slot này không có thuộc tính gì, toàn bộ phần nội dung sẽ được chèn vào vị trí của slot trong DOM, thay thế cho slot đó.

Mọi thứ bên trong thẻ `<slot>` lúc ban đầu được xem như **nội dung dự phòng**. Nội dung dự phòng được biên dịch trong scope của component con và chỉ được hiển thị nếu phần tử host là rỗng và không có nội dung gì để chèn vào.

Giả sử ta có một component gọi là `child-component` với template như sau:

``` html
<div>
  <h2>Lời của con</h2>
  <slot>
    Dòng này sẽ chỉ được hiển thị nếu không có 
    nội dung nào được phân bố.
  </slot>
</div>
```

và một component cha sử dụng `child-component`:

``` html
<div>
  <h1>Lời của cha</h1>
  <p>
    “Theo cánh buồm đi mãi đến nơi xa,
    Sẽ có cây, có cửa, có nhà,
    Vẫn là đất nước của ta
    Những nơi đó cha chưa hề đi đến.”
  </p>
  <child-component>
    <p>
      “Cha mượn cho con buồm trắng nhé
      Để con đi”
    </p>
  </child-component>
</div>
```

Nội dung được render sẽ là:

``` html
<div>
  <h1>Lời của cha</h1>
  <p>
    “Theo cánh buồm đi mãi đến nơi xa,
    Sẽ có cây, có cửa, có nhà,
    Vẫn là đất nước của ta
    Những nơi đó cha chưa hề đi đến.”
  </p>
  <div>
    <h2>Lời của con</h2>
    <p>
      “Cha mượn cho con buồm trắng nhé
      Để con đi”
    </p>
  </div>
</div>
```

### Slot có tên

Các phần tử `<slot>` có một thuộc tính đặc biệt là `name` (tên). Thuộc tính này được dùng để tùy biến thêm về cách phân bố nội dung. Bạn có thể có nhiều slot với các tên khác nhau.  Một slot có tên sẽ khớp với bất kì phần tử nào có thuộc tính `slot` tương ứng nằm trong phần nội dung.

Ngoài ra chúng ta vẫn có thể dùng một slot không có tên để là **slot mặc định**. Những nội dung không khớp với bất kì slot nào sẽ được chèn vào slot này. Nếu trong template không có slot mặc định, bất cứ nội dung nào không khớp sẽ bị bỏ đi.

Ví dụ, giả sử chúng ta có một component gọi là `app-layout` với template như sau:

``` html
<div class="container">
  <header>
    <!-- đây là một slot có tên -->
    <slot name="header"></slot>
  </header>
  <main>
    <!-- 
      đây là slot mặc định, slot không tên,
      ta cũng có thể gọi là slot của Vũ Thành An
    -->
    <slot></slot>
  </main>
  <footer>
    <!-- đây lại là một slot có tên -->
    <slot name="footer"></slot>
  </footer>
</div>
```

HTML của cha trông như sau:

``` html
<app-layout>
  <!-- nội dung này sẽ được chèn vào slot "header" -->
  <h1 slot="header">Tiêu đề của trang</h1>

  <!-- nội dung này sẽ được chèn vào slot mặc định -->
  <p>Một đoạn nội dung.</p>
  <p>Thêm một đoạn nữa cho dài.</p>

  <!-- nội dung này sẽ được chèn vào slot "footer" -->
  <p slot="footer">Thông tin liên hệ</p>
</app-layout>
```

Kết quả cuối cùng sẽ là:

``` html
<div class="container">
  <header>
    <h1>Tiêu đề của trang</h1>
  </header>
  <main>
    <p>Một đoạn nội dung.</p>
    <p>Thêm một đoạn nữa cho dài.</p>
  </main>
  <footer>
    <p>Thông tin liên hệ</p>
  </footer>
</div>
```

API phân bố nội dung là một cơ chế rất mạnh khi biên soạn những component nên dùng chung với nhau.

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
