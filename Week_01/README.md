学习笔记

## Object.create
```javaScript
Object.create(proto,[propertiesObject])
```
建一个新对象，使用现有的对象来提供新创建的对象的__proto__。
### 浅拷贝

就是继承原理。浅拷贝但是可以可以clone 一维数组，因为一维数组存的都是基本值，所以浅拷贝是没有问题的

### 在Vue和Vuex的源码中，作者都使用了Object.create(null)来初始化一个新对象。为什么不用更简洁的{}呢？

正常新创建的对象继承了Object自身的方法，如hasOwnProperty、toString等，在新对象上可以直接使用。使用Object.create()创建的对象__prop__指向null，原型链上没有任何属性，也就是没有继承Object的任何东西，此时如果我们调用o.toString()会报Uncaught TypeError的错误。

总结：
你需要一个非常干净且高度可定制的对象当作数据字典的时候；
想节省hasOwnProperty带来的一丢丢性能损失并且可以偷懒少些一点代码的时候



## 异步迭代器 for-await-of

Iterator 接口是一种数据遍历的协议，只要调用遍历器对象的next方法，就会得到一个对象，表示当前遍历指针所在的那个位置的信息。next方法返回的对象的结构是{value, done}，其中value表示当前的数据的值，done是一个布尔值，表示遍历是否结束。

一个定义了迭代行为的对象，比如在 for...of 中循环了哪些值。为了实现可迭代，一个对象必须实现 @@iterator 方法，这意味着这个对象（或其原型链中的一个对象）必须具有带 Symbol.iterator 键的属性, 返回{value, done}。序列中的下一个值和数据源的 "done" 状态必须已知，所以这种必须是同步的。

```javaScript
const justjavac = {
  [Symbol.iterator]: () => {
    const items = [`t`, `e` , `s`, `t`];
    return {
      next: () => ({
        done: items.length === 0,
        value: items.shift()
      })
    }
  }
}
[...justjavac];
// ["t", "e", "s", "t"]

Array.from(justjavac)
// ["t", "e", "s", "t"]

new Set(justjavac);
// {"t", "e", "s", "t"}

for (const item of justjavac) {
  console.log(item)
}
// t
// e
// s
// t
```

为了给异步数据源提供通用的数据访问协议，我们引入了 AsyncIterator 接口，异步迭代语句（for-await-of）和异步生成器函数。

一个异步迭代器就像一个迭代器，除了它的 next() 方法返回一个 { value, done } 的 promise。如上所述，我们必须返回迭代器结果的 promise，因为在迭代器方法返回时，迭代器的下一个值和“完成”状态可能未知。

```javaScript
const justjavac = {
  [Symbol.asyncIterator]: () => {
    const items = [`t`, `e`, `s`, `t`];
    return {
      next: () => Promise.resolve({
        done: items.length === 0,
        value: items.shift()
      })
    }
  }
}
(async function(){
  for await (const item of justjavac) {
    console.log(item)
  }
})();
```

