4\. 클래스, 객체,인터페이스
======================

## 4.1 클래스 계층 정의
-------------------

### 4.1.1 코틀린 인터페이스

```kotlin
interface Clickable {
    // 일반 메소드 선언
    fun click()
    // 디폴트 구현이 있는 메소드
    fun showOff() = println("I'm clickable")
}

interface Focusable {
    fun setFocus(b: Boolean) =
        println("I ${if (b) "got" else "lost"} focus.")
    fun showOff() = println("I'm fucusable!")
}

class Button : Clickable, Focusable {
    override fun click() = println("I was clicked")

    // showOff 는 Clickable 과 Focusable 양쪽에
    // 구현을 가지므로 반드시 override 해서 구현해야 함!
    override fun showOff() {
        super<Clickable>.showOff()
        super<Focusable>.showOff()
    }
}

fun main(args: Array<String>) {
    val button = Button()
    button.showOff()
    button.setFocus(true)
    button.click()
}

/*
I'm clickable!
I'm focusable!
I got focus.
I was clicked.
*/
```

```java
public final class Button implements Clickable {
    public void showOff() {
        Clickable.DefaultImpls.showOff(this);
    }
}

public interface Clickable {
    void showOff();

    public static final class DefaultImpls {
        public static void showOff(Clickable $this) {
            String var1 = "I'm clickable";
            System.out.println(var1);
        }
    }
}
```


- 코틀린의 인터페이스는 자바 8과 마찬가지로 구현 메소드(디폴트 메소드)를 정의할 수 있다.
- 코틀린은 자바 6와 호환되도록 설계 되었으므로 자바에서 디폴트 메소드가 있는 코틀린 인터페이스를 사용할 수 없음

### 4.1.2 open, final abstract 변경자: 기본적으로 final

```kotlin
open class RichButton: Clickable {
    fun disable() { }
    open fun animate() { }
    // final 이 없는 override 매소드나 프로퍼티는 기본적으로 열려있다.
    final override fun click() { }
}
````

- 코틀린의 class 는 기본적으로 상속 금지인 자바의 final class
- 취약한 기반 클래스(fragile base class) 문제: 하위 클래스가 기반 클래스에 대해 가졌던 가정이 기반 클래스를 변경함으로써 깨져버리는 경우
- [GoF Design Pattern: "Program to an interface, not an implementation."](https://www.artima.com/lejava/articles/designprinciplesP.html)
- 에릭 감마: "클래스에 의존성을 추가하는 것은 너무나도 쉽지만, 흥미롭게도 역은 쉽지 않습니다. 인터페이스에만 의존하면 구현과 분리됩니다. 이는 구현이 다를 수 있음을 의미하며 이는 건전한 의존 관계입니다."
- 조슈아 블로크: "상속을 위한 설계와 문서를 갖추거나, 그럴 수 없다면 상속을 금지하라"

### 의문(P. 148)

    # 열린 클래스와 스마트 캐스트
    클래스의 기본적인 상속 가능 상태를 final로 함으로써 얻을 수 있는 큰 이익은 다양한 경우에 스마트 캐스트가 가능하다는 점이다. 2.3.5절에서 말한 것처럼 스마트 캐스트는 타입 검사 뒤에 변경될 수 없는 변수에만 적용 가능하다. 클래스 프로퍼티의 경우 이는 val이면서 커스텀 접근자가 없는 경우에만 스마트 캐스트를 쓸 수 있다는 의미이다. 이 요구 사항은 또한 프로터피가 final이어야만 한다는 뜻이기도 하다. 프로퍼티가 fianl이 아니라면 그 프로퍼티를 다른 클래스가 상속하면서 커스텀 접근자를 정의함으로써 스마트 캐스트의 요구사항을 깰 수 있다. 프로퍼티는 기본적으로 final이기 때문에 따로 고민할 필요 없이 대부분의 프로퍼티를 스마트 캐스트에 활용할 수 있다. 이는 코드를 더 이해하기 쉽게 만든다.

- 표 4.1 클래스 내에서 상속 제어 변경자의 의미

| 변경자       | 이 변경자가 붙은 맴버는...   | 설명                   |
| ---        | ---                     | ---                   |
| `fianl`    | 오버라이드 할 수 없음        | 클래스 맴버의 기본 변경자다. |
| `open`     | 오버리이드 할 수 있음        | 반드시 open을 명시해야 오버리이드 할 수 있다. |
| `abstract` | 반드시 오버리이드해야 함      | 추상 클래스의 맴버에만 이 변경자를 붙일 수 있다. |
| `override` | 상위 클래스나 상위 인스턴스의 맴버를 오버리이드하는 중 | 오버라이드하는 맴버는 기본적으로 열려있다. 하위 클래스의 오버라이드를 금지하려면 final을 명시해야 한다. |

### 4.1.3 가시성 변경자: 기본적으로 공개

| 변경자                | 클래스 맴버                 | 최상위 선언                |
| ---                 | ---                       | ---                     |
| `public`(기본 가시성임) | 모든 곳에서 볼 수 있다.       | 모든 곳에서 볼 수 있다.      |
| `internal`          | 같은 모듈 안에서만 볼 수 있다.   | 같은 모듈 안에서 볼 수 있다.  |
| `protected`         | 하위 클래스 안에서만 볼 수 있다. | (최상위 선언에 적용할 수 없음) |
| `private`           | 같은 클래스 안에서만 볼 수 있다. | 같은 파일 안에서만 볼 수 있다. |

- 코틀린의 `private` 클래스는 바이트코드 상 패키지-전용 클래스로 컴파일한다. 
- 코틀린의 `internal` 은 컴파일 될 때 바이트코드 상 `public` 이 된다. 그러므로 Java 코드에서 코틀린 `internal` 맴버를 접근 할 수 있지만 맹글(mangle)되므로 보기 불편하고 못생겨 보인다.

### 4.1.4 내부 클래스와 중첩된 클래스: 기본적으로 중첩 클래스

| 클래스 B 안에 정의된 클래스 A                   | 자바에서는          | 코틀린에서는       |
| ---                                       | ---              | ---             |
| 중첩 클래스(바깥쪽 클래스에 대한 참조를 저장하지 않음) | `static class A` | `class A`       |
| 내부 클래스(바깥쪽 클래스에 대한 참조를 저장함)      | `class A`        | `inner class A` |

```kotlin
class Outer {
    inner class Inner {
        fun getOuterReferences(): Outer = this@Outer
    }
}
```

### 4.1.5 봉인된 클래스: 클래스 계층 정의 시 계층 확장 제한

- Without sealed

```kotlin
interface Expr
class Num(val value: Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr

fun eval(e: Expr) :Int {
    return when(e) {
        is Num -> e.value
        is Sum -> eval(e.right) + eval(e.left)
        else -> // 반드시 else 분기가 있어야 한다.
            throw IllegalArgumentException("Unknown expression")
    }
}
```

- With sealed

```kotlin
sealed class Expr
class Num(val value: Int) : Expr()
class Sum(val left: Expr, val right: Expr) : Expr()

fun eval(e: Expr) :Int {
    return when(e) {
        is Expr.Num -> e.value
        is Sum -> eval(e.right) + eval(e.left)
    }
}
```

## 4.2 뻔하지 않은 생성자와 프로퍼티를 갖는 클래스 선언

```kotlin
class User(val nickname: String)

class User constructor(_nickanme String) {
    val nickname: String
    init {
        nickname = _nickname
    }
}

// val은 파라미터에 상응하는 프로퍼티가 생성된다는 뜻이다.
class User(val nickname: String)
// 생성자 파라미터에 대한 디폴트 값을 제공한다.
class User(val nickname: String,
    val isSubscribed: Boolean = true)
// isSubscribed 파라미터에는 디폴트 값이 쓰인다.
>> val hyun = User("현석")
>> println(hyun.isSubscribed) // true
>> val hyun = User("혜원", isSubscribed = false)
>> println(hyun.isSubscribed) // false
```
- 아래 클래스의 호출 순서는 어떨까?
```kotlin
class Class {
    private val value: String
    constructor(value: String) {
        this.value = value
        println("constructor")
    }

    init {
        println("init")
    }
}
```


```java
public final class Class {
   private final String value;

   public Class(@NotNull String value) {
      Intrinsics.checkParameterIsNotNull(value, "value");
      super();
      String var2 = "init";
      System.out.println(var2);
      this.value = value;
      var2 = "constructor";
      System.out.println(var2);
   }
}
```

- 실행 순서: 생성자 호출 -> init 블록 호출 -> 생성자 안쪽 블록 호출

```kotlin
class Secretive private constructor() {
    companian object {
        fun create(): Secretive = Secretive()
    }
}
```
- 위 처럼 팩토리 메소드 생성 방법을 통해서 객체를 생성하도록 생성자를 `private` 으로 선언할 수도 있음

```kotlin
open class View {
    // 주 생성자
    constructor(ctx: Context) {
        TODO()
    }

    // 부 생성자
    constructor(ctx: Context, attr: AttributeSet) {
        TODO()
    }
}

class MyButton : View {
    constructor(ctx: Context) : this(ctx, MY_STYLE) {
        TODO()
    }

    constructor(ctx: Context, attr: AttributeSet)
        : super(ctx, attr) {
            TODO()
    }
}
```

## 4.2.3 인터페이스에 선언된 프로퍼티 구현

```kotlin
// 인터페이스에 추상 프로퍼티 선언을 넣을 수 있음
interface User {
    val email: String
    // 프로퍼티에 뒷받침하는 필드가 없다.
    // 대신 매번 결과를 계산해 돌려준다.
    // 인터페이스는 상태를 저장할 수 없다.
    val nickname: String
        get() = email.substringBefore('@')
}

class PrivateUser(
    override val email: String,
    override val nickname: String
) : User

class SubscribingUser(
    override val email: String
): User {
    override val nickname: String
        get() = email.substringBefore('@')
}

class FacebookUser(
    val accountId: Int,
    override val email: String
) : User {
    override val nickname = getFacebookName(accountId)
}
```

## 4.2.4 게터와 세터에서 뒷받침하는 필드에 접근

```kotlin
class User(val name: String) {
    var address: String = "unspecified"
        set(value: String) {
            println("""
            Address was changed for $name:
            "$field" -> "$value".
            """.trimIndent()) // 뒷받침하는 필드 값 읽기
            field = value // 뒷받침하는 필드 값 변경하기
        }
}

>>> val user = User("Alice")
>>> user.address = "Elsenheimerstrasse 47, 80687 Muenchen"
Address was changed for Alice:
"unspecified" -> "Elsenheimerstrasse 47, 80687 Muenchen"
```

### 4.2.5 접근자의 가시성 변경

```kotlin
class LengthCounter {
    // 이 클래스 밖에서 이 프로퍼티의 값을 바꿀 수 없다.
    var counter: Int = 0
        private set
    
    fun addWord(word: String) {
        counter += word.length
    }
}

>>> val lengthCounter = LengthCounter()
>>> lengthCounter.addWord("Hi!")
>>> println(lengthCounter.counter)
// 3
```

## 4.3 컴파일러가 생성한 메소드: 데이터 클래스와 클래스 위임
-----------------------------------------------

자바 플랫폼에서는 `equals`, `hashCode`, `toString` 등의 메소드를 직접 구현해야 함. (Lombok 같은 프로젝트도 있지만 언어 표준이 아니기 때문에 고통스러움)

### 4.3.1 모든 클래스가 정의해야 하는 메소드

```kotlin
class Client(val name: String, val postalCode: Int) {
    override fun toString(): String =
        "Client(name=$name, postalCode=$postalCode)"

    override fun equals(other: Any?): Boolean {
        if (other == null || other !is Client)
            return false
        return name == other.name &&
            postalCode == other.postalCode
    }

    override fun hashCode(): Int =
        name.hashCode() * 31 + postalCode
}

>>> val client = Client("오현석", 4122)
>>> println(client)
// Client(name=오현석, postalCode=4122)

>>> val client1 = Client("오현석", 4122)
>>> val client2 = Client("오현석", 4122)
>>> println(client1 == client2)
// client1.equals(client2)
// true

>>> val processed = hashSetOf(Client("오현석", 4122))
>>> println(processed.contains(Client("오현석", 4122)))
// true
```

### 4.3.2 데이터 클래스: 모든 클래스가 정의해야 하는 메소드 자동 생성

```kotlin
data class Client(val name: String val postalCode: Int)
```

## 데이터 클래스와 불변성: copy() 메소드

HashMap 등의 컨테이너에 데이터 클래스를 담거나, 다중스레드 프로그램에서 불변 객체를 사용하면 프로그램의 오류를 줄일 수 있다. `data class`는 `copy` 메소드를 구현해 준다.

```kotlin
class Client(val name: String, val postalCode: Int) {
    fun copy(name: String = this.name,
        postalCode = this.postalCode) =
            Client(name, postalCode)
}

>>> val lee = Client("이계영", 4122)
>>> println(lee.copy(postalCode = 4000))
Client(name=이계영, postalCode=4000)
```

### 4.3.3 클래스 위임: by 키워드 사용

```kotlin
// 데코레이터를 만들 때 원래 작성해야하는 코드양
class DelegationCollection<T> : Collection<T> {
    private val innerList = arrayListOf<T>()
    override fun size: int get() = innserList.size
    override fun isEmpty(): Boolean = innerList.isEmpty()
    ...
    override fun ...
}

// 클래스 위임을 사용했을 때 코드양
class DelegationCollection<T>(
    innsrList: Collection<T> = ArrayList<T>()
) : Collection<T> by innerList()
```

## 4.4 object 키워드: 클래스 선언과 인스턴스 생성
----------------------------------------

- 객체 선언(object declaration)은 싱글턴을 정의하는 방법 중 하나
- 동반 객체(companion object)는 자바에서 static method
- 객체 식은 자바의 무명 내부 클래스(anonymous inner class) 대신 쓰임

## 4.4.1 객체 선언: 싱글턴을 쉽게 만들기

```kotlin
object Payroll {
    val allEmployess = arrayListOf<Person>()
    fun calculateSalary() {
        TODO()
    }
}

Payroll.allEmployess.add(Person(...))
Payroll.calculateSalary()

object CaseInsensitiveFileComparator : Comparator<File> {
    override fun compare(f1:File, f2:File): Int {
        ...
    }
}
>>> println(CaseInsensitiveFileComparator.compare(f1, f2))

// 자바에서
>>> CaseInsensitiveFileComparator.INSTANCE.compare(f1, f2);
```

- 대규모 소프트웨어 개발에서는 싱글턴이 적합하지 않을 수도 있다: 생성을 제어할 수 없고 생성자 파라미터를 지정할 수 없으므로 단위 테스트를 하거나 의존 관계를 바꿀때 곤란하다.

## 4.4.2 동반 객체: 팩토리 메소드와 정적 맴버가 들어갈 장소

```kotlin
class A {
    companion object {
        fun bar() {
            TODD()
        }
    }
}
>>> A.bar()
// 자바에서
>>> A.Companion.bar()
```

## 4.4.3 동반 객체를 일반 객체처럼 사용

```kotlin
class Person(val name: String) {
    companion object Loader {
        fun fromJSON(jsonText: String) : Person = { TODO() }
    }
}
>>> person = Person.Loader.fromJSON("{name: 'Dmitry'}")

interface JSONFactory<T> {
    fun fromJSON(jsonText: String): T
}

class Person(val name: String) {
    companion object : JSONFactory<Person> {
        override fun fromJSON(jsonText: String): Person = ...
    }
}
```

## 4.4.4 객체 식: 무명 내부 클래스를 다른 방식으로 작성

```kotlin
var clickCount = 0
window.addMouseListener {
    object : MouseAdapter() {
        override fun mouseClicked(e: MouseEvent) {
            clickCount++ // 로컬 변수의 값을 접근 할 수 있음
        }
        override fun mouseEntered(e: MouseEvent) = ...
    }
}
```
