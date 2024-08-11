---
layout: post
title:  "Validate Property trong Vue.js"
---

Với Vue.js chúng ta có thể dễ dàng thêm các thuộc tính (property) vào component. Nhưng chưa dừng lại ở đó, Vue.js còn cung cấp cho chúng ta nhiều cách để control sâu hơn thuộc tính đó như định kiểu, validate dữ liệu, set giá trị mặc định.

### Định kiểu dữ liệu
Chúng ta có thể dễ dàng thêm kiểu dữ liệu cho thuộc tính bằng cách thêm từ khóa `type` cho nó. Ví dụ:
~~~html
<template>
  <h2>Tuổi của tôi là {{age}}</p>
</template>

<script>
export default {
  props: {
    age: {
      type: Number
    }
  }
}
</script>
~~~

Các kiểu dữ liệu được cho phép:
* `Object`: Chỉ chấp nhận dữ liệu đầu vào là Object, ví dụ :myProp="{key: 'value'}"
* `Array`: Chỉ chấp nhận dữ liệu đầu vào là Array, ví dụ :myProp="[1, 2, 3]"
* `String`: Chỉ chấp nhận dữ liệu đầu vào là String, ví dụ :myProp="Example"
* `Number`: Chỉ chấp nhận dữ liệu đầu vào là Number, ví dụ :myProp="103.4"
* `Boolean`: Chỉ chấp nhận dữ liệu đầu vào là Boolean, ví dụ :myProp="true"

### Giá trị mặc định
Chúng ta có thể bắt buộc thuộc tính phải được truyền vào bằng cách thêm giá trị `required: true` cho nó. Ví dụ:
~~~js
props: {
  age: {
    type: Number,
    required: true
  }
}
~~~

Thay vào đó, chúng ta cũng có thể set giá trị mặc định cho thuộc tính trong trường hợp không có giá trị truyền vào:
~~~js
props: {
  age: {
    type: Number,
    default: 43
  }
}
~~~

Thậm chí chúng ta có thể set giá trị mặc định là một function:
~~~js
props: {
  age: {
    type: Number,
    default: () => Math.random()
  }
}
~~~

### Validate dữ liệu
Chúng ta cũng có thể thực hiện validate dữ liệu ngay trong thuộc tính bằng cách thêm function `validator` cho nó. Function này nhận vào giá trị của thuộc tính và return true nếu hợp lệ, ngược lại sẽ return false. Ví dụ:
~~~js
props: {
  age: {
    type: Number,
    validator: value => {
      return value > 30
    }
  }
}
~~~

Bằng việc kết hợp những cách thức trên, chúng ta có thể dễ dàng quản lý dữ liệu truyền vào component và khiến cho chúng trở nên tường minh, dễ hiểu hơn.