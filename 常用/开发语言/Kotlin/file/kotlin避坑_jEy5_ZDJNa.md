### 1 方法入参是常量，不可修改
```kotlin
/**
* Kotlin 入参是常量
*/
fun print(a: Int = 1, b: String = "") {
// a = 10; // 错误：Val cannot be reassigned!!!
}
```
### 2 不要 Companion 、INSTANCE ？
```kotlin
// Java 访问 Kotlin 中定义的静态变量以及静态方法，需要 Companion 。例如：
// Main.kt
class Main {
    companion object {
        val EMPTY = ""
        fun isEmpty(string: String = EMPTY) {
            //todo code
        }
        @JvmField
        val FULL_NUMBER = "1234567890"
        @JvmStatic
        fun isNumber(string: String = FULL_NUMBER) {
        //todo code
        }
    }
}

// Test.java
class Test {
    public static void main(String[] args) {
        // Java 访问 Kotlin 中的常量
        Keng.Companion.getEMPTY();
        Keng.Companion.isEmpty("");
        KengInstance.INSTANCE.getEMPTY();
        // Java 访问 Kotlin 中带有 JvmField 修饰的常量，无需 Companion
        String FULL_NUMBER = Keng.FULL_NUMBER;
        // Java 访问 Kotlin 中带有 JvmStatic 修饰的方法，无需 Companion
        Keng.isNumber("");
    }
}
/*
不想使用 Companion ， @JvmField 、 @JvmStatic 注解了解一下。
    在 Kotlin object Main{...} 定义的静态对象依旧适用。
    这些注解，特别推荐在Kotlin中使用，它们让Java与Kotlin互操，如丝般顺滑，没有任何一点点改变，就当什么
都没发生过一样。
*/
```
### 3 Java 重载，在 Kotlin 中怎么巧妙过渡一下？
```java
/**
Kotlin 调用 Kotlin 中的方法，如果有默认参数，是可以不传递参数的。Java 与 Kotlin 互操中，好像还是需要
传？
例如： isNumber(string: String = FULL_NUMBER) ：
 */
// Test.java
class Test {
    public static void main(String[] args) {
        Keng.isNumber("");// 必须要传递个参数
    }
}

// 能不能让 Java 也享受到 Kotlin 默认参数的快乐？
// Test.java
class Test {
    public static void main(String[] args) {
        // JvmOverloads 注解的作用，默认实现了 重载 特性
        Keng.isNumberWithOverLoads();
        Keng.isNumberWithOverLoads("");
    }
}
```
### 4 Kotlin 中的判空姿势
```kotlin
fun testNullableString() {
    var nullableString: String? = null
    var nullableStringLength = nullableString?.length
}
```

###  is、as` 中的坑
```kotlin
// obj is String 之后，作用域之中，类型就已经转换了，有点类似 java 的多态
fun testAsIs() {
    var obj: Any? = null
    if (obj is String) {// 方法体内的作用域，obj 就是 String
        var length = obj.length
    }
}

// as`的两种不推荐写法，会抛出异常：`TypeCastException: null cannot be cast to non-null type kotlin.String
//错误写法1，text不是String或为空时，会报异常
var strAble1 = text as String
//错误写法2，text不是String时，同样会报异常
var strAble2 = text as String?

// as 的推荐写法：
//正确写法，转换失败自动转换为空对象
var strAble = text as? String
```

### 9 also 关键字
```kotlin
// also 是 Kotlin 标准库中的一个高阶函数，其作用是在函数块执行的同时，将接收者对象作为参数传入函数块中，最后返回接收者对象本身。
while (bufferReader.readLine().also({ line = it }) != null) {
    // do something
}
/*
// 等同Java代码
while ((line = bufferReader.readLine()) != null) {
    // do something
}
*/

// 使用 also 可以在一些需要执行一些额外操作的场景中很方便地使用，比如：
val list = mutableListOf(1, 2, 3)
    .also {
        println("list before add: $it")
        it.add(4)
        println("list after add: $it")
    }
println("list at last: $list")

/*
结果: 
list before add: [1, 2, 3]
list after add: [1, 2, 3, 4]
list at last: [1, 2, 3, 4]
从输出结果可以看到，also 函数在 MutableList 上执行函数块时，能够将 MutableList 作为函数参数传递进去，执行函数块并输出结果，然后返回 MutableList 本身。
所以 also 函数的作用是辅助执行一些附加操作，并且可以保证最后返回接收者对象，使用 also 可以在代码中更方便地进行调试等操作。
*/
```

### 10 takeIf 关键字
```kotlin
// 原代码
if (someObject != null && status) {
    doThis()
}
// 最佳实践
someObject?.takeIf{ status }?.apply{ doThis() }
// takeIf 是 Kotlin 标准库中的一个扩展函数，其作用是判断对象是否满足某种条件，如果满足条件，则返回对象本身，否则返回 null。
// takeIf 函数接收一个 Lambda 表达式作为参数，这个 Lambda 表达式需要返回一个 Boolean 类型的值，
// 表示对象是否满足某种条件。如果 Lambda 表达式返回 true，则 takeIf 函数返回接收者对象本身；否则返回 null。
val str = "hello"
val result = str.takeIf { it.contains("e") }
println(result) // 输出 hello

val str2 = "world"
val result2 = str2.takeIf { it.contains("e") }
println(result2) // 输出 null

//  takeIf 函数可以用于在满足某个条件时执行一些操作，比如：
val text = "hello"
val result = text.takeIf { it.length <= 10 }
    ?.let { "text length is ${it.length}" }
    ?: "text length is too long"
println(result) // 输出 "text length is 5"

```

### 11 单例模式的写法
```kotlin
//kotlin实现
class Singleton private constructor() {
    companion object {
         val instance: Singleton by lazy(mode = LazyThreadSafetyMode.SYNCHRONIZED) {
        Singleton()
    }
    }
}
```