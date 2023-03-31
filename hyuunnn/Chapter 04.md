## 클래스 계층

### 인터페이스

```kotlin
interface Clickable {
    fun click()
}

class Button : Clickable {
    override fun click() = println("I was clicked")
}
```

자바에서는 `extends`나 `implements`를 사용했지만 코틀린에서는 `:`을 사용하면 된다.

또한 자바에서는 `@Override`를 선택적으로 사용했지만 코틀린은 `override`라는 변경자를 꼭 사용해야 한다. (하위 클래스에서 메서드를 실수로 override하는 경우를 방지할 수 있다.)

### 디폴트 메서드

```kotlin
interface Clickable {
    fun click()
    fun showOff() = println("I'm clickable!")
}
```

자바에서는 `default` 키워드를 사용해야 했지만 코틀린은 일반 메서드처럼 만들면 된다.

```kotlin
interface Focusable {
    fun showOff() = println("I'm focusable!")
}

class Button : Clickable, Focusable {
    override fun click() = println("I was clicked")
    override fun showOff() {
        super<Clickable>.showOff()
        super<Focusable>.showOff()
    }
}
```

또한 인터페이스에 선언된 동일한 이름의 디폴트 메서드는 위와 같이 `super`를 사용하여 인터페이스를 명시해야 하며, 명시하지 않으면 컴파일러 오류가 발생한다.

### 코틀린은 기본적으로 final을 사용하고 있다. (`open`, `final`, `abstract`)

자바는 취약한 기반 클래스(fragile base class) 라는 문제가 있는데 메서드를 override하여 의도와 다른 방향으로 동작할 수 있다는 것이다. 

즉 자바에서는 클래스, 메소드의 상속에 대해 열려있다. 하지만 코틀린은 기본적으로 `final`을 사용하며, 상속을 허용할 경우 클래스 외에 override를 허용하고 싶은 메서드, 프로퍼티의 앞에도 `open`을 사용해야 한다.

```kotlin
open class RichButton : Clickable { // 다른 클래스가 이 클래스를 상속할 수 있다.
    fun disable() {} // final 메서드
    open fun animate() {} // override를 허용하는 메서드
    final override fun click() {} // override는 기본적으로 열려있기 때문에 하위 클래스에서 override 하지 못하게 하려면 final을 사용해야 한다. (타입이 변경되지 않기 때문에 스마트 캐스트를 사용할 수 있다는 장점도 있다.)
}
```

```kotlin
abstract class Animated {
    abstract fun animate() // 추상 메서드 (반드시 override해야 한다.)
    open fun stopAnimating() {} // 추상 클래스의 멤버에는 구현이 있을 수 있다.
    fun animateTwice() {} // final 메서드
}
```

### 클래스 내 상속 제어 변경자

final : 오버라이드할 수 없음, 클래스 멤버의 기본 변경자
open : 오버라이드할 수 있음, 반드리 open을 명시해야 오버라이드가 가능하다.
abstract : 반드시 오버라이드해야 함, 추상 클래스의 멤버에만 사용 가능 + 추상 멤버에 구현이 있으면 안된다.
override : 상위 클래스 or 상위 인스턴스의 멤버를 override, override는 기본적으로 열려있기 때문에 금지하려면 final을 명시해야 한다. (`final override fun click()`)

### 가시성 변경자

자바에서는 변경자를 명시하지 않았을 때 `package-private`으로 설정되지만, 코틀린은 `public`으로 설정된다.

```java
class Test { // package-private
    String name; // package-private

    public Test(String name) {
        this.name = name;
    }
}
```

자바에서는 위와 같이 `package-private`를 사용했을 때 클래스를 선언하면 외부에서도 `name`에 접근할 수 있다. (최상위 클래스의 변경자는 `package-private`, `public` 두 가지만 사용할 수 있다.)

하지만 코틀린은 최상위 선언에 `private`를 사용하여 외부에 노출되지 않게 할 수 있다. 

```kotlin
private var haha: String = ""
private fun testFunc() = println("test")

private open class Test {

    fun print() {
        testFunc()
        println(haha)
    }
}
```

자바 바이트코드로 컴파일할 때 자바에서는 `private` 클래스를 사용할 수 없으므로 `package-private`로 컴파일된다고 한다. (`internal`은 자바 바이트코드에서 `public`으로 된다고 한다.)

이러한 차이 때문에 코틀린에서 접근할 수 없지만 자바에서는 가능한 경우가 생긴다.

### 중첩 클래스

자바는 기본적으로 내부 클래스로 동작하기 때문에 바깥 클래스에 대한 참조를 가지고 있으며 `static`을 사용하여 참조를 가지지 않는 중첩 클래스를 만들 수 있다.

```java
public class Outer {
    private String name = "Outer";

    public class Inner {
        public void print() {
            System.out.println(name);
        }
    }
}
```

하지만 코틀린은 기본적으로 자바의 `static` 중첩 클래스와 동일하게 동작하며 `inner`를 사용하여 참조를 가질 수 있는 내부 클래스로 동작하게 만들 수 있다.

```kotlin
class Outer {
    private var name: String = "Outer"

    inner class Inner { // inner를 사용하지 않으면 바깥 클래스를 참조할 수 없기 때문에 name을 사용할 수 없다.
        fun print() {
            println(name)
        }
    }
}
```

### sealed 클래스

TODO..

## 클래스 선언

### 주 생성자 (primary constructor)

```kotlin
class User(val nickname: String, val isSubscribed: Boolean = true)
```

지금까지 사용해왔던 익숙한 형태의 클래스 선언이다. 중괄호 안에 넣으면 생성자 파라미터와 프로퍼티를 자동으로 생성해주며 디폴트 값을 지정할 수도 있다. 하지만 원하는 코드를 추가할 수 없기 때문에 제한적이라고 볼 수 있다.

이를 `constructor`와 `init`를 사용하여 표현하면 아래 코드와 같다.

```kotlin
class user constructor(_nickname: String, _isSubscribed: Boolean = true) {
    val nickname: String
    val isSubscribed: Boolean

    init {
        nickname = _nickname
        isSubscribed = _isSubscribed
    }
}
```

### 부 생성자 (secondary constructor)

TODO..

## 클래스 내부 정의와 클래스 위임

TODO..
