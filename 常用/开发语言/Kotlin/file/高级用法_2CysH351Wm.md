### Kotlin 写 Gradle 脚本是一种什么体验？
```kotlin
1. 将 单引号 替换成 双引号
2. 修改 Gradle 文件扩展名
    1. builde.gradle --> build.gradle.kts
    2. settings.gradle --> settings.gradle.kts
    3. Sync 走起！
3. 遇到无法解决的报错怎么办？
    
```
### let, also, with, apply 使用与区别
```text
这四个函数 (let, also, with, apply) 的作用都是针对对象进行操作，但它们之间还是有一些细微的区别的。

let
作用：允许在操作对象的同时执行额外的代码，最后返回 Lambda 表达式的结果。
使用场景：通常用于需要在对象上执行一些操作，并将结果用于其他计算的场景，例如绑定 View，计算结果等。
函数签名：fun <T, R> T.let(block: (T) ->: R R)
also
作用：允许执行一些链式调用之后的操作，并返回对象本身。
使用场景：通常用于调试、日志记录、与其它函数链式调用的场景。
函数签名：fun <T> T.also(block: (T) -> Unit): T
with
作用：允许不断地操作一个指定对象，不用反复引用该对象。
使用场景：通常用于操作同一个对象的一连串操作，并将操作集合在一起。
函数签名：fun <T, R> with(receiver: T, block: T.() -> R): R
apply
作用：允许在对象上执行一些链式调用，最后返回对象本身。
使用场景：通常用于对象的初始化或者配置对象的一系列属性。
函数签名：fun <T> T.apply(block: T.() -> Unit): T
其中，let 和 also 都是在一个对象上执行链式调用，但是它们返回的结果不一样；with 可以避免反复引用同一个对象，而 apply 可以用于对象的初始化。

具体来说，可以通过下面的示例代码更好地理解这四个函数在实际开发中的使用。
```
```kotlin
这四个函数 (`let`, `also`, `with`, `apply`) 的作用都是针对对象进行操作，但它们之间还是有一些细微的区别的。

1. `let`
   * 作用：允许在操作对象的同时执行额外的代码，最后返回 Lambda 表达式的结果。
   * 使用场景：通常用于需要在对象上执行一些操作，并将结果用于其他计算的场景，例如绑定 View，计算结果等。
   * 函数签名：`fun <T, R> T.let(block: (T) -> R): R`
2. `also`
   * 作用：允许执行一些链式调用之后的操作，并返回对象本身。
   * 使用场景：通常用于调试、日志记录、与其它函数链式调用的场景。
   * 函数签名：`fun <T> T.also(block: (T) -> Unit): T`
3. `with`
   * 作用：允许不断地操作一个指定对象，不用反复引用该对象。
   * 使用场景：通常用于操作同一个对象的一连串操作，并将操作集合在一起。
   * 函数签名：`fun <T, R> with(receiver: T, block: T.() -> R): R`
4. `apply`
   * 作用：允许在对象上执行一些链式调用，最后返回对象本身。
   * 使用场景：通常用于对象的初始化或者配置对象的一系列属性。
   * 函数签名：`fun <T> T.apply(block: T.() -> Unit): T`

其中，`let` 和 `also` 都是在一个对象上执行链式调用，但是它们返回的结果不一样；`with` 可以避免反复引用同一个对象，而 `apply` 可以用于对象的初始化。

具体来说，可以通过下面的示例代码更好地理解这四个函数在实际开发中的使用。

```kotlin
// let
val text: String? = "Kotlin Programming Language"
val result = text?.let {
    it.length * 2 // 计算字符串长度乘以 2 的结果作为返回值
}
println(result) // 输出 54

// also
val list = mutableListOf<Int>(1, 2, 3)
val newList = list.also {
    it.add(4) // 在原列表上添加一个新元素
}.map { it * it } // 对列表中的元素进行平方运算
println(newList) // 输出 [1, 4, 9, 16]

// with
val person = Person("ZhangSan", 20, "Beijing")
with(person) {
    name = "LiSi" // 用 with 代替 person.xxx 的写法
    age = 30    // 可以避免反复引用同一个对象
    city = "Shanghai"
}
println(person) // 输出: Person(name=LiSi, age=30, city=Shanghai)

// apply
val person = Person("ZhangSan", 20, "Beijing").apply {
    name = "LiSi" // 在对象初始化过程中修改对象属性
    age = 30
    city = "Shanghai"
}
println(person) // 输出: Person(name=LiSi, age=30, city=Shanghai)
```

### 函数类型(Function Type)是什么？
```kotlin
// 顾名思义：函数类型，就是函数的类型
// (Int, Int) ->Float
// ↑ ↑ ↑
fun add(a: Int, b: Int): Float { return (a+b).toFloat() }
/*
将函数的 参数类型 和 返回值类型 抽象出来后，就得到了 函数类型 。 (Int, Int) -> Float 就代表了 参数类型 是 两个 Int 返回值类型 为 Float 的函数类型。
*/
```
### 高阶函数是什么？
```kotlin
// 高阶函数是将函数用作参数或返回值的函数。
// 上面的话有点绕，直接看例子吧。如果将 Android 里点击事件的监听用 Kotlin 来实现，它就是一个典型的 高阶 函数 。
//                          函数作为参数的高阶函数
// ↓
fun setOnClickListener(l: (View) -> Unit) { ... }
```

###  Lambda 是什么？
```kotlin
// Lambda 可以理解为函数的 简写 。
fun onClick(v: View): Unit {  }
setOnClickListener(::onClick)
// 用 Lambda 表达式来替代函数引用
setOnClickListener({v: View -> })

```
### Lambda 表达式引发的8种写法
```kotlin
// 1. 原始代码，它的本质是用 object 关键字定义了一个 匿名内部类
image.setOnClickListener(object: View.OnClickListener {
    override fun onClick(v: View?) {
        gotoPreview(v)
    }
})

// 2. 删掉 object 关键字，它就是 Lambda 表达式了，因此它里面 override 的方法也要跟着删掉
image.setOnClickListener(View.OnClickListener { view: View? ->
    gotoPreview(view)
})

// 3. 由于 Kotlin 的 Lambda 表达式是不需要 SAM Constructor 的，所以它也可以被删掉
image.setOnClickListener({ view: View? ->
    gotoPreview(view)
})

// 4. Kotlin 支持 类型推导 ，所以 View? 可以被删掉：
image.setOnClickListener({ view ->
    gotoPreview(view)
})

//5. Kotlin Lambda 表达式只有一个参数的时候，它可以被写成 it 。
image.setOnClickListener({ it ->
    gotoPreview(it)
})

//6. n Lambda 的 it 是可以被省略的：
image.setOnClickListener({
    gotoPreview(it)
})

//7. Kotlin Lambda 作为函数的最后一个参数时，Lambda 可以被挪到外面：
image.setOnClickListener() {
    gotoPreview(it)
}

//8. 当 Kotlin 只有一个 Lambda 作为函数参数时， () 可以被省略：

image.setOnClickListener {
    gotoPreview(it)
}

```

### 函数类型，高阶函数，Lambda表达式三者之间的关系
```text
将函数的 参数类型 和 返回值类型 抽象出来后，就得到了 函数类型 。 (View) -> Unit 就代表了 参数类型 是
View 返回值类型 为 Unit 的函数类型。
如果一个函数的参数 或者 返回值的类型是函数类型，那这个函数就是 高阶函数 。很明显，我们刚刚就写了
一个高阶函数，只是它比较简单而已。
Lambda 就是函数的一种 简写
```

###  定义节点元素的接口
```kotlin
interface Element {
// 每个节点都需要实现 render 方法
fun render(builder: StringBuilder, indent: String): String
}
```

### 定义基础类
```kotlin
/**
* 每个节点都有 name，content: <title> Kotlin Jetpack In Action </title>
*/
open class BaseElement(val name: String, val content: String = "") : Element {
    // 每个节点，都会有很多子节点
    val children = ArrayList<Element>()
    // 存放节点参数：<img src= "" alt=""/>，里面的 src，alt
    val hashMap = HashMap<String, String>()
    /**
    * 拼接 Html: <title> Kotlin Jetpack In Action </title>
    */
    override fun render(builder: StringBuilder, indent: String): String {
      builder.append("$indent<$name>\n")
        if (content.isNotBlank()) {
            builder.append(" $indent$content\n")
        }
        children.forEach {
            it.render(builder, "$indent ")
        }
        builder.append("$indent</$name>\n")
        return builder.toString()
    }
}
```

### 泛型
```kotlin
// 函数的泛型参数
// ↓ ↓
fun <T> turnOn(obj: T){  }
fun <T> turnOff(obj: T){  }
```