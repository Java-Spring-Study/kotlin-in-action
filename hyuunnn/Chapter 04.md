## 인터페이스

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

## 코틀린은 기본적으로 final을 사용하고 있다.

자바는 취약한 기반 클래스(fragile base class) 라는 문제가 있는데 메서드를 override하여 의도와 다른 방향으로 동작할 수 있다는 것이다. 

즉 자바에서는 클래스, 메소드의 상속에 대해 열려있다. 하지만 코틀린은 기본적으로 `final`을 사용하며, 상속을 허용해야 한다면 `open`을 사용한다.

```kotlin
open class RichButton : Clickable { // 다른 클래스가 이 클래스를 상속할 수 있다.
    fun disable() {} // final 메서드
    open fun animate() {} // override를 허용하는 메서드
    final override fun click() {} // override는 기본적으로 열려있기 때문에 하위 클래스에서 override 하지 못하게 하려면 final을 사용해야 한다. (타입이 변경되지 않기 때문에 스마트 캐스트를 사용할 수 있다는 장점도 있다.)
}
```
