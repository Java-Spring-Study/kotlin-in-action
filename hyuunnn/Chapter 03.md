## 컬렉션

코틀린은 자바에 구현된 컬렉션을 그대로 사용한다. 아래 코드를 보면 `hashSetOf`, `arrayListOf`, `hashMapOf` 등 `typealias`에 설정된 이름을 사용하고 있다.

```kotlin
// TypeAliases.kt
@SinceKotlin("1.1") public actual typealias ArrayList<E> = java.util.ArrayList<E>
@SinceKotlin("1.1") public actual typealias LinkedHashMap<K, V> = java.util.LinkedHashMap<K, V>
@SinceKotlin("1.1") public actual typealias HashMap<K, V> = java.util.HashMap<K, V>
@SinceKotlin("1.1") public actual typealias LinkedHashSet<E> = java.util.LinkedHashSet<E>
@SinceKotlin("1.1") public actual typealias HashSet<E> = java.util.HashSet<E>
```

```kotlin
public fun <T> hashSetOf(vararg elements: T): HashSet<T> = elements.toCollection(HashSet(mapCapacity(elements.size)))

public fun <T> arrayListOf(vararg elements: T): ArrayList<T> =
    if (elements.size == 0) ArrayList() else ArrayList(ArrayAsCollection(elements, isVarargs = true))

public fun <K, V> hashMapOf(vararg pairs: Pair<K, V>): HashMap<K, V> = HashMap<K, V>(mapCapacity(pairs.size)).apply { putAll(pairs) }
```

## 함수 호출 방법

### 파라미터명 명시 가능

```kotlin
joinToString(collection, separator = " ", prefix = " ", postfix = " ")
```

코틀린에서는 파라미터명을 명시하여 어떠한 값을 전달하는지 명확하게 표현할 수 있다.

자바에서는 파라미터명을 정의할 수 없기 때문에 IDE의 도움을 받거나 빌더 패턴을 사용해야 했다.

### 디폴트 파라미터

디폴트 파라미터 값을 정의할 수도 있다.

```kotlin
fun <T> joinToString(
    collection: Collection<T>,
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""
): String {
    val result = StringBuilder(prefix)
    for ((index, element) in this.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(element)
    }

    result.append(postfix)
    return result.toString()
}
```

디폴트 파라미터는 함수를 호출하는 쪽이 아니라 함수 선언 쪽에서 컴파일할 때 지정하지 않은 인자들이 디폴트 값을 적용 받는다.

코틀린 함수를 자바에서 호출하고 싶을 때 `@JvmOverloads`를 사용하면 된다.

자바는 디폴트 파라미터라는 기능이 없으므로 오버로딩한 함수들이 만들어진다.

```java
String joinToString(Collection<T> collection, String separator, String prefix, String postfix);
String joinToString(Collection<T> collection, String separator, String prefix);
String joinToString(Collection<T> collection, String separator);
String joinToString(Collection<T> collection);
```

### 정적인 유틸리티 클래스 지우기

자바에서는 모든 코드를 클래스의 메서드로 만들어야 한다. 그렇기 때문에 규모가 매우 작은 코드도 각각의 자바 파일로 만들어야 해서 무의미한 클래스가 많아진다. (`Collections`는 전형적인 정적 유틸리티 클래스이다.)

하지만 코틀린에서는 함수를 최상위 수준에 위치시키면 되며, 무의미한 클래스 생성이 필요 없다.

JVM에서 동작하기 위해서는 **클래스 안에 있는 코드**만 실행할 수 있기 때문에 컴파일할 때 새로운 클래스를 정의해준다. 이때 파일명을 기준으로 클래스가 만들어진다. (ex: `join.kt` -> `public class JoinKt {}` -> `JoinKt.joinToString(list, ",", ",", "")`)

이를 따로 지정하고 싶다면 `@JvmName`을 사용하면 된다. (파일 맨 앞, 패키지 이름 선언 이전에 위치해야 한다.)

## 확장 함수

```kotlin
public fun <T> List<T>.last(): T {
    if (isEmpty())
        throw NoSuchElementException("List is empty.")
    return this[lastIndex]
}
```

```kotlin
package strings 
fun String.lastChar(): Char = this.get(this.length - 1) // String: 수신 객체 타입(receiver type), this: 수신 객체(receiver object)
```

클래스 밖에 선언된 함수지만 클래스의 멤버 메서드인 것처럼 호출할 수 있다.

단지 추가하려는 함수 이름 앞에 확장할 클래스의 이름을 적으면 된다. 

책에서 사용되었던 `last()`도 확장 함수임을 확인할 수 있다.

확장 함수 내부에서는 일반적인 메서드와 동작에서는 차이가 없지만 (호출 방식이 동일하기 때문에 호출하는 쪽에서는 구분할 수 없다.), 확장 함수가 캡슐화를 깨지는 않으며 private, protected 멤버를 사용할 수도 없다. 

즉 `joinToString`을 별도의 함수가 아닌 `Collection`의 확장 함수로 만들 수 있다.

```kotlin
fun <T> Collection<T>.joinToString(
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""
): String {
    val result = StringBuilder(prefix)
    for ((index, element) in this.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(element)
    }

    result.append(postfix)
    return result.toString()
}

val list = listOf(1, 2, 3)
println(list.joinToString(separator = "; ", prefix = "(", postfix=")"))
```

### 확장 함수 임포트 방법

```kotlin
import strings.lastChar
val c = "Kotlin".lastChar()

import strings.lastChar as last
val c = "Kotlin".last()
```

확장 함수를 정의했더라도 임포트하지 않으면 사용할 수 없다. as 키워드를 사용하면 다른 이름으로 사용할 수 있으며, 파이썬에서 자주 사용되는 키워드이다.

### 자바에서 확장 함수 호출하는 방법

`StringUtil.kt`에 `lastChar` 확장 함수를 정의했다고 가정했을 때 아래 코드처럼 첫 번째 인자에 수신 객체를 넘겨주면 된다.

`"Kotlin".lastChar() -> StringUtilKt.lastChar("Java")`

```java
char c = StringUtilKt.lastChar("Java");
```

### 확장 함수는 오버라이드할 수 없다.

확장 함수는 정적 메서드와 같은 특징을 가지므로, 하위 클래스에서 오버라이드할 수는 없다.

```kotlin
open class View {
    open fun click() = println("View clicked")
}

class Button: View() {
    override fun click() = println("Button clicked")
}

val view: View = Button()
println(view.click()) // Button Clicked
```

확장 함수는 클래스의 일부가 아닌 클래스 밖에 선언되었기 때문에 위와 같이 오버라이드할 수 없다.

확장 함수를 호출할 때 수신 객체로 지정한 정적인 타입에 의해 확장 함수가 결정되며, 변수가 가지고 있는 객체의 타입에 의해 확장 함수가 결정되지 않는다.

```kotlin
fun View.showOff() = println("I'm a view!")
fun Button.showOff() = println("I'm a button!")

val view: View = Button()
println(view.showOff()) // I'm a view! (이전에 생성했던 정적인 함수로 결정된다.) 
```

`view`는 `Button` 객체를 받아왔지만, 타입은 `View`이기 때문에 `View`의 정적 함수가 실행된 것이다.

**확장 함수, 멤버 함수의 이름과 시그니처가 같으면 멤버 함수의 우선순위가 더 높다.**

### 확장 프로퍼티

```kotlin
val String.lastChar: Char
    get() = get(length - 1)
```

함수로 만들었던 `lastChar`를 프로퍼티로 만들면 위와 같다. 프로퍼티는 별도의 필드 변수가 존재하지 않기 때문에 getter 구현을 해야하며, 계산한 값을 담을 장소가 없기 때문에 초기화 코드도 사용할 수 없다.

```kotlin
var StringBuilder.lastChar: Char
    get() = get(length - 1)
    set(value: Char) {
        this.setCharAt(length - 1, value)
    }

println("Kotlin".lastChar) // n
val sb = StringBuilder("Kotlin?")
sb.lastChar = '!'
println(sb) // Kotlin!
```

위 코드는 `StringBuilder`의 확장 프로퍼티로 `lastChar`를 만들었으며, `setCharAt`을 사용하여 마지막 값을 수정하기 때문에 `var`을 사용했다.

## 컬렉션 처리

### 가변 인자 함수

```kotlin
public fun <T> listOf(vararg elements: T): List<T> = if (elements.size > 0) elements.asList() else emptyList()
```

자바에서는 `...`을 사용하지만 코틀린에서는 `vararg` 변경자를 파라미터 앞에 붙이면 된다.

또한 자바에서는 배열로 받은 args 값을 넘겨주면 되지만 코틀린에서는 스프레드(`*`) 연산을 사용하여 배열을 명시적으로 풀어서 각각의 원소를 인자로 전달되게 해야한다. (파이썬에서 볼 수 있는 형태이다.)

```kotlin
fun main(args: Array<String>) {
    val list = listOf("args: ", *args)
    println(list)
}
```

### 중위 호출

```kotlin
val map = mapOf(1 to "one", 7 to "seven", 53 to "fifty-three")
```

위 코드에서 to는 코틀린 키워드가 아닌 중위 호출이라는 방법으로 메서드를 호출한 것이다.

```kotlin
// Tuples.kt
public infix fun <A, B> A.to(that: B): Pair<A, B> = Pair(this, that)
```

`Tuples`에 정의된 코드를 보면 위와 같이 `infix` 변경자를 사용하여 `to` 함수를 정의하였다.

### 구조 분해

```kotlin
val (number, name) = 1 to "one"
```

또한 `Pair`를 통해 key, value로 나눠진 값을 number와 name에 분해되어 변수에 저장할 수 있다.

`withIndex()`도 구조 분해를 통해 `index`와 `element`에 데이터가 들어갈 수 있었다.

## 문자열과 정규식

```java
// String.java
public String[] split(String regex) {
    return split(regex, 0);
}

public String[] split(String regex, int limit) { ... }
```

```java
// String[] strList = "12.345-6.A".split("."); // X (.을 정규식으로 처리한다.)
String[] strList = "12.345-6.A".split("\\.|-"); // O
```

자바에서는 정규식으로 `split`을 사용해야 하며, `split(".")`는 빈 배열을 반환한다. (.은 모든 문자를 나타내는 정규식이기 때문에 모든 문자를 기준으로 split 되어 빈 배열을 반환한다.)

하지만 코틀린은 `split`을 자바 표준 API보다 좀 더 다양한 함수들을(split 확장 함수) 제공한다.

```kotlin
// Strings.kt
public fun CharSequence.split(vararg delimiters: String, ignoreCase: Boolean = false, limit: Int = 0): List<String> {
    if (delimiters.size == 1) {
        val delimiter = delimiters[0]
        if (!delimiter.isEmpty()) {
            return split(delimiter, ignoreCase, limit)
        }
    }

    return rangesDelimitedBy(delimiters, ignoreCase = ignoreCase, limit = limit).asIterable().map { substring(it) }
}

@kotlin.internal.InlineOnly
public inline fun CharSequence.split(regex: Regex, limit: Int = 0): List<String> = regex.split(this, limit)
```

```kotlin
println("12.345-6.A".split("\\.|-".toRegex())) // [12, 345, 6, A]
println("12.345-6.A".split(".", "-")) // [12, 345, 6, A]
```

자바와 다르게 `split`에 정규식이 아닌 문자를 사용할 수 있으며, `vararg`(가변인자)를 사용하기 때문에 여러 개의 delimiter를 받을 수 있다.

위 함수 외에도 편의를 위한 오버로딩 함수들이 구현되어 있다. (`Strings.kt` 참고)

또한 **3중 따옴표**를 사용하면 문자를 깔끔하게 표현할 수 있다.

```kotlin
println("12.345-6.A".split("""\.|-""".toRegex()))
println("""C:\Users\yole\kotlin-book""")
println("C:\\Users\\yole\\kotlin-book")

var regex = """(.+)/(.+)\.(.+)""".toRegex()

val kotlinKogo = """| //
                   .| //
                   .|/ \"""
println(kotlinKogo.trimMargin("."))
```
