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

또한 디폴트 파라미터 값을 정의할 수도 있다.

```kotlin
fun <T> joinToString(
    collection: Collection<T>,
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""
): String 
```

### 디폴트 파라미터

디폴트 파라미터는 함수를 호출하는 쪽이 아니라 함수 선언 쪽에서 컴파일할 때 지정하지 않은 인자들이 디폴트 값을 적용 받는다.

코틀린 함수를 자바에서 호출하고 싶을 때 `@JvmOverlaods`를 사용하면 된다.

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

JVM에서 동작하기 위해서는 **클래스 안에 있는 코드**만 실행할 수 있기 때문에 컴파일할 때 새로운 클래스를 정의해준다. 이때 파일명을 기준으로 클래스가 만들어진다. (ex: `join.kt` -> `public class JoinKt {}`)

이를 따로 지정하고 싶다면 `@JvmName`을 사용하면 된다. (파일 맨 앞, 패키지 이름 선언 이전에 위치해야 한다.)

## 확장 함수

클래스 밖에 선언된 함수지만 클래스의 멤버 메서드인 것처럼 호출할 수 있다.

단지 추가하려는 함수 이름 앞에 확장할 클래스의 이름을 적으면 된다. 

```kotlin
package strings 
fun String.lastChar(): Char = this.get(this.length - 1)
```

확장 함수가 캡슐화를 깨지는 않는다.

확장 함수 안에서는 private, protected 멤버를 사용할 수 없다.

### 확장 함수 임포트 방법

```kotlin
import strings.lastChar
val c = "Kotlin".lastChar()

import strings.lastChar as last
val c = "Kotlin".last()
```

확장 함수를 정의했더라도 임포트하지 않으면 사용할 수 없다. as 키워드를 사용하면 다른 이름으로 사용할 수 있으며, 파이썬에서 자주 사용되는 키워드이다.

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

확장 함수를 호출할 때 정적인 타입에 의해 어떤 확장 함수가 호출될지 결정하지 동적인 타입에 의해 확장 함수가 결정되지 않는다.

```kotlin
fun View.showOff() = println("I'm a view!")
fun Button.showOff() = println("I'm a button!")

val view: View = Button()
println(view.showOff()) // I'm a view! (이전에 생성했던 정적 함수로 결정된다.) 
```

`view`는 `Button` 객체를 받아왔지만, 타입은 `View`이기 때문에 `View`의 정적 함수가 실행된 것이다.

**확장 함수, 멤버 함수의 이름과 시그니처가 같으면 멤버 함수의 우선순위가 더 높다.**

## 확장 프로퍼티


