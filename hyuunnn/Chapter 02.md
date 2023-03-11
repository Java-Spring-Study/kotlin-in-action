## 함수

```kotlin
// 블록이 본문인 함수
fun max(a: Int, b: Int): Int {
    return if (a > b) a else b
}
```

코틀린에서는 함수를 만들 때 `fun` 키워드를 사용하고, 매개변수 이름 뒤에 타입이 온다. 반환 타입을 지정할 수도 있다.

그리고 자바는 모든 제어 구조가 문인 반면 코틀린에서는 루프를 제외한 대부분의 제어 구조가 식이다.

이러한 특성 때문에 위 코드처럼 `return`에 `if`를 사용할 수 있는데 값을 만들거나 계산에 참여할 수 있는 것이다.

반면 대입문은 자바에서 식이었으나 코틀린은 문이 됐다. 그로 인해 대입식(`=`)과 비교식(`==`)을 잘못 사용하는 실수가 없다.

```kotlin
// 식이 본문인 함수
// 산술식, 함수 호출식뿐 아니라 if, when, try 등의 복잡한 식에서도 자주 쓰인다.
fun max(a: Int, b: Int): Int = if (a > b) a else b

// 반환 타입 생략 가능 (컴파일러가 식을 분석해서 타입을 추론해준다.)
// 식이 본문인 함수의 반환 타입만 생략 가능하다.
fun max(a: Int, b: Int) = if (a > b) a else b
```

처음에 만든 `max` 함수를 식으로 만들면 코드를 더 간결하게 만들 수 있다. 또한 식은 `if` 분기의 마지막이 결과 값이기 때문에 `return`을 제거할 수 있다.

<!-- 자바와 다르게 무조건 클래스 안에 함수를 넣을 필요가 없다. -->

## 변수

코틀린에서는 타입 지정을 생략하는 경우가 흔하지만, 타입을 명시할 수 있다. (`var answer: Int = 42`)

식이 본문인 함수처럼 변수도 타입을 지정하지 않으면 컴파일러가 식을 분석해서 타입을 지정해준다.

### mutable, immutable 변수

* val(value): 변경 불가능(immutable)한 변수. 자바의 final에 해당한다.
* var(variable): 변경 가능한(mutable)한 변수. 자바의 일반 변수에 해당한다.

기본적으로 모든 변수에 `val` 키워드를 사용하여 불변 변수로 선언하고, 나중에 꼭 필요할 때만 `var`로 변경하라고 한다.

### 문자열 템플릿

```java
fun main(args: Array<String>) {
    val name = if (args.size > 0) args[0] else "Kotlin"
    println("Hello, $name!");
    println("Hello, ${if (args.size > 0) args[0] else "someone"}!")
}
```

문자열 템플릿을 사용하면 쌍따옴표 안에 `${variable}` 형식으로 넣어서 출력할 수 있으며, `if` 식을 사용하여 변수를 유동적으로 선언할 수 있다.

## 클래스

```java
public class Person {
    private final String name;
    private final Boolean isMarried;

    public Person(String name, Boolean isMarried) {
        this.name = name;
        this.isMarried = isMarried;
    }

    public String getName() {
        return name;
    }

    public Boolean isMarried() {
        return isMarried;
    }

    public Boolean setMarried(Boolean isMarried) {
        this.isMarried = isMarried;
    }
}
```

```kotlin
// 코드 없이 데이터만 저장하는 클래스를 값 객체라 부른다. (생성자, getter, setter가 생략되어 있다.)
class Person (
    val name: String,
    var isMarried: Boolean
)
```

자바의 `public` 접근 제한자가 코틀린 코드에는 보이지 않는다. 왜냐하면 코틀린은 기본 제한자가 `public`이기 때문에 생략할 수 있다.

### 프로퍼티

코틀린은 프로퍼티 개념을 기본으로 제공하기 때문에 자바에서 선언했던 getter, setter 메서드를 코틀린에서는 `val`와 `var`에 따라서 유동적으로 만들어주며, 생성자도 만들어준다.

자바에서는 `setName()`, `getName()` 메서드를 호출했지만 코틀린에서는 `person.name`을 출력하면 `getName()`, `person.name = "qwe"`를 하면 `setName()`이 호출된다. 직관적인 코드 작성이 가능하다고 볼 수 있다. (C#에서 사용하는 형태이다.)

#### 커스텀 접근자

```kotlin
class Rectangle(val height: Int, val width: Int) {
//        val isSquare: Boolean
//        get() { // 프로퍼티 getter 선언
//            return height == width
//        }

    // 위와 같은 블록 표현을 식으로 변경하면 코드가 더욱 간결해진다.
    val isSquare: Boolean get() = height == width
}
```

자바에서는 보통 `isSquare`라는 메서드를 만들지만 코틀린은 함수(`fun`) 혹은 프로퍼티의 커스텀 접근자로 `isSquare`를 표현할 수 있다.

## when

```kotlin
fun getMnemonic(color: Color) =
    when (color) {
        Color.RED -> "Richard"
        Color.ORANGE -> "Of"
        Color.YELLOW -> "York"
        Color.GREEN -> "Gave"
    }

fun getMnemonic2(color: Color) =
    when (color) {
        Color.RED, Color.ORANGE -> "warm"
        Color.GREEN -> "neutral"
    }
```

자바에서는 `switch`가 있지만 코틀린은 `when`이라는 문법을 제공한다. 위 코드를 보면 `break`가 없으며, `:`가 아닌 `->`를 사용한다는 점이다. 그리고 `,`를 사용하여 한 분기 안에서 여러 값을 매칭할 수도 있다.

```kotlin
fun mix(c1: Color, c2: Color) =
    // 인자로 받은 객체가 각 분기 조건에 있는 객체와 같은지 테스트한다. - 동등성(equal) 검사
    when (setOf(c1, c2)) {
        setOf(Color.RED, Color.YELLOW) -> Color.ORANGE
        setOf(Color.YELLOW, Color.BLUE) -> Color.GREEN
        else -> throw Exception("Dirty color")
    }

fun mixOptimized(c1: Color, c2: Color) =
    // when 인자가 없으려면 각 분기의 조건이 불리언 계산식이어야 한다.
    // 이전 함수와 비교했을 때 추가 객체를 만들지 않는다는 장점이 있지만, 가독성은 더 떨어진다.
    when {
        (c1 == Color.RED && c2 == Color.YELLOW) ->
            Color.ORANGE
        (c1 == Color.YELLOW && c2 == Color.RED) ->
            Color.GREEN

        else -> throw Exception("Dirty Color")
    }
```

또한 `setOf`를 사용하여 인자로 받은 객체가 각 분기의 조건에 있는 객체와 같은지 확인할 수 있으며, `boolean` 계산식으로 표현할 수도 있다.

즉 임의의 객체를 분기의 조건으로 사용할 수 있다는 장점이 있다. 

## 스마트 캐스트

### if 식을 사용하는 예제

```kotlin
interface Expr
class Num(val value: Int) : Expr
class Sum(val left: Expr, var right: Expr) : Expr

fun eval(e: Expr) : Int {
    if (e is Num) {
        // 자바에서는 이렇게 타입을 검사하고 캐스팅을 해야하는데, 코틀린에서는 스마트 캐스트를 지원한다.
        // is로 검사하고 나면 원하는 타입으로 캐스팅하지 않아도 마치 처음부터 그 변수가 원하는 타입으로 선언된 것처럼 사용할 수 있다.
        // 컴파일러가 캐스팅을 수행한다.
        val n = e as Num
        return n.value
    }

    // 스마트 캐스트는 타입 검사 후 값이 바뀔 수 없는 경우에만 작동한다.
    // 또한 반드시 val이어야 하며, val을 사용했으나 커스텀 접근자를 사용하는 경우에는 작동하지 않는다. (항상 같은 값이 나온다고 확신할 수 없기 때문이다.)
    if (e is Sum) {
        return eval(e.right) + eval(e.left)
    }

    throw IllegalArgumentException()
}

// if 분기의 마지막이 결과 값이기 때문에 return 문을 지울 수 있다.
fun evalUsingIfExpression(e: Expr) : Int =
    if (e is Num) {
        e.value
    } else if (e is Sum) {
        eval(e.right) + eval(e.left)
    } else {
        throw IllegalArgumentException()
    }
```

### when을 사용하는 예제

```kotlin
fun eval(e: Expr) : Int =
    when (e) {
        is Num ->
            e.value
        is Sum ->
            eval(e.right) + eval(e.left)
        else ->
            throw IllegalArgumentException()
    }
```

`if`를 사용하여 만든 `eval` 함수와 비교하면 훨씬 직관적인 코드로 변경되었다. `when`에서 `boolean`을 사용할 수 있다는 장점을 활용하여 `is`를 사용했을 때 스마트 캐스팅이 동작하며 값이 `true`라면 캐스팅된 값으로 코드가 진행된다.

```kotlin
fun evalUsingWhen(e: Expr) : Int =
    when(e) {
        is Num -> {
            println("num: ${e.value}")
            e.value
        }
        is Sum -> {
            val left = evalUsingWhen(e.left)
            val right = evalUsingWhen(e.right)
            left + right
        }
        else ->
            throw IllegalArgumentException()
    }
```

## while, for loop

### 수에 대한 iteration

```kotlin
fun fizzBuzz(i: Int) = when {
    i % 15 == 0 -> "FizzBuzz "
    i % 3 == 0 -> "Fizz "
    i % 5 == 0 -> "Buzz "
    else -> "$i "
}

fun main(args: Array<String>) {
    // ..으로 범위 지정 가능
    for (i in 1..30) {
        print(fizzBuzz(i))
    }
    println()
    for (i in 0 until 30) {
        print(fizzBuzz(i))
    }
    println()
    for (i in 50 downTo 1 step 2) {
        print(fizzBuzz(i))
    }
}
```

`..`으로 범위 지정을 할 수 있으며, `downTo`를 사용하여 역방향 loop를 하거나 `step`을 사용하여 증가 값을 수정할 수도 있다. python의 `for i in range(start, stop, step)`와 매우 유사한 형태라고 생각하면 된다.

### Map에 대한 iteration

```kotlin
val binaryReps = TreeMap<Char, String>()
for (c in 'A'..'F') { // 아스키로 문자 범위 loop도 가능
    val binary = Integer.toBinaryString(c.code) // toInt()는 deprecated라서 c.code로 수정
    binaryReps[c] = binary // 자바의 binaryReps.put(c, binary)와 동일
}
```

자바의 프로퍼티 기능 덕분에 별도의 메서드를 호출할 필요 없이 값을 가져오거나 수정할 수 있다.

```kotlin
val list = arrayListOf("10", "11", "1001")
for ((index, element) in list.withIndex()) {
    println("$index: $element")
}
```

그리고 `withIndex`를 사용하면 python의 `enumerate` 처럼 사용할 수 있다.

### in으로 범위 또는 원소 검사

```kotlin
fun isLetter(c: Char) = c in 'a'..'z' || c in 'A'..'Z'
fun isNotDigit(c: Char) = c !in '0'..'9'
```

`..`을 사용하여 python의 `in` 처럼 범위에 값이 있는지 검사할 수 있다. 

또한 범위는 문자에 국한되지 않으며 비교가 가능한 클래스라면 (`Comparable` 인터페이스를 구현) 범위를 만들 수 있다. 아래 코드를 보면 쉽게 이해할 수 있다.

```kotlin
// "Java"와 "Kotlin" 사이의 모든 문자열을 이터레이션할 수 없다.
// in을 사용하여 값이 범위 안에 속하는지 알파벳 순서로 비교한다.
// "Java" <= "Kotlin" && "Kotlin" <= "Scala"
println("Kotlin" in "Java".."Scala") // true

// 컬렉션에서 in을 사용하여 집합에 해당 문자가 있는지 확인한다.
println("Kotlin" in setOf("Java", "Scala", "kotlin")) // false
```

## 예외처리

```kotlin
fun readNumber(reader: BufferedReader) : Int? {
    try {
        val line = reader.readLine()
        return Integer.parseInt(line)
    }
    catch (e: NumberFormatException) {
        return null
    }
    finally {
        reader.close()
    }
}

// try도 식으로 표현할 수 있다. (마지막 식이 결과 값이다.)
fun readNumberUsingExpression(reader: BufferedReader) {
    var number = try {
        Integer.parseInt(reader.readLine())
    } catch (e: NumberFormatException) {
        null
    }
}
```

자바에서는 예외처리를 할 때 발생할 수 있는 예외들을 `catch`와 `throws`를 사용해야 한다.

하지만 코틀린은 체크 예외(ex: `IOException`)와 언체크 예외를 구별하지 않으며 예외를 잡아도 되고 잡아내지 않아도 된다. 

이는 자바에서 의미 없이 예외를 잡거나, 예외를 잡아도 추가 동작을 하지 않는 코드들 때문에 위와 같이 설계했다고 한다.
