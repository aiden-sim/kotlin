# 연산자 오버로딩과 기타 관례
- 자바에서는 기능이 클래스와 연관 (AutoCloseable 등)
- 코틀린은 특정 함수와 연관
  - 어떤 기능과 미리 정해진 이름의 함수를 연결해주는 기법을 관례(convention) 라고 부름 

## 7.1 산술 연산자 오버로딩
- 자바에서는 원시 타입에 대해서만 산술 연산자 사용 가능
- BigInteger 에서 add가 아닌 + 연산을 사용하고 싶다면?! (코틀린은 가능)

### 7.1.1 이항 산술 연산 오버로딩
```kotlin
data class Point(val x: Int, val y: Int) {
    operator fun plus(other: Point): Point {
        return Point(x + other.x, y + other.y)
    }
}

// 확장 함수 형태도 가능 (외부 라이브러리 등)
operator fun Point.plus(other: Point): Point {
    return Point(x + other.x, y + other.y)
}

>>> val p1 = Point(10, 20)
>>> val p2 = Point(30, 40)
>>> println(p1 + p2) // + 계산시 'plus' 함수가 호출됨
Point(x=40, y=60)
```
- 연산자를 오버로딩하는 함수 앞에는 꼭 `operator` 가 있어야 함 (관례를 따르는 함수임을 명확히 알 수 있다.)
  - `operator` 없으면 컴파일 시점에 에러남

- 오버로딩 가능한 이항 산술 연산자
  - ![image](https://github.com/simjunbo/kotlin/assets/7076334/2942c5c2-bde9-461c-a5fb-240fb4e3f8bf)


```kotlin
operator fun Point.times(scale: Double): Point {
    return Point((x * scale).toInt(), (y * scale).toInt())
}

>>> val p = Point(10, 20)
>>> println(p * 1.5)
Point(x=15, y=30)

>>> println(1.5 * p1) // 교환 법칙은 지원하지 않음. 별도 정의해야됨
```
- 두 피연산자의 타입이 다른 연산자 정의
- 연산자 함수의 반환 타입이 꼭 두 피연산자와 일치하지 않아도 됨


### 7.1.2 복합 대입 연산자 오버로딩
- 코틀린은 +=, -= 등과 같은 복합 대입 연산자도 지원

```kotlin
>>> var point = Point(1, 2)
>>> point += Point(3, 4) // point = point + Point(3,4)와 같다
>>> println(point)
Point(x=4, y=6)
```
- 변수가 변경 가능한 경우에만 복합 대입 연산자 사용 가능
- 경우에 따라 객체 대한 참조를 다른 참조로 바꾸기보다 원래 객체의 내부 상태를 변경하고 싶을 때 사용

```kotlin
data class Point(var x: Int, var y: Int) {

}

operator fun Point.plus(other: Point): Point {
    return Point(x + other.x, y + other.y)
}

operator fun Point.plusAssign(other: Point) {
    this.x += other.x
    this.y += other.y
}
```

- 복합 대입 연산자는 `minusAssign`, `timeAssign` 등의 이름 사용
- 이론적으로 +=를 plus와 plusAssign 둘다 컴파일 가능. 둘다 정의되어 있으면 아래와 같은 컴파일 에러 발생 (동시 정의 하지 마라)
  - ![image](https://github.com/simjunbo/kotlin/assets/7076334/4317ea59-3641-448b-bd5b-1f0985d3f9e5)

- 클래스가 불변이면 `plus`와 같이 새로운 값을 반환하는 연산만 사용. 빌더와 같이 변경 가능한 클래스라면 `plusAssign` 연산 제공


### 7.1.3 단항 연산자 오버로딩

```kotlin
operator fun Point.unaryMinus(): Point {
    return Point(-x, -y)
}

>>> val p = Point(10, 20)
>>> println(-p)
Point(x=-10, y=-20)
```
- 이항 연산자와 마찬가지로 미리 정해진 이름의 함수를 선언하면서 operator로 표시하면 됨
- 단항 연산자를 오버로딩하기 위해 사용하는 함수는 인자를 취하지 않는다.

- 오버로딩할 수 있는 단항 산술 연산자
  - ![image](https://github.com/simjunbo/kotlin/assets/7076334/523eb949-1c33-4245-88dc-4b7d64ed3236)


## 7.2 비교 연산자 오버로딩
- equals나 compareTo를 호출해야 하는 자바와 달리 코틀린에서는 == 비교 연산자를 직접 사용할 수 있어 비교 코드가 간결하며 이해하기 쉽다. (코틀린에서는 == 호출을 equals 메서드 호출로 컴파일함)

### 7.2.1 동등성 연산자: equals

```kotlin
// data class 사용 시, equals, hashCode, toString 제공
data class Point(var x: Int, var y: Int) {

}
```

- data class 사용 시 다음과 같이 자동 생성
  - 인스턴스 간 비교를 위한 equals
  - HashMap과 같은 해시 기반 컨테이너에서 키로 사용할 수 있는 hashCode
  - 클래스의 각 필드를 선언 순서대로 표시하는 문자열 표현을 만들어주는 toString

```kotlin
class Point(var x: Int, var y: Int) {
    override fun equals(obj: Any?): Boolean {
        if (obj === this) return true
        if (obj !is Point) return false
        return obj.x == x && obj.y == y
    }
}
```

- 다른 연산자 오버로딩 관례와 달리 equals는 Any에 정의된 메소드 이기 때문에 override 가 필요하다.
- Any의 equals에는 operator가 붙어있지만 그 메소드를 오버라이드하는 메소드 앞에는 operator 변경자를 붙이지 않아도 자동으로 적용됨
- 또한 Any에서 상속받은 equals가 확장 함수보다 우선순위가 높기 때문에 확장함수로 정의 불가

### 7.2.2 순서 연산자: compareTo
- 자바에서는 정렬등을 사용하기 위해 Comparable 인터페이스 구현해야됨
- 코틀린에서도 동일하게 Comparable 인터페이스 지원. 코틀린에서는 비교 연산자(<, >, <=, >=) 는 compareTo 호출로 컴파일됨
  - ex) p1 < p2 는 p1.compareTo(p2) < 0 과 같음

```kotlin
public interface Comparable<in T> {
    public operator fun compareTo(other: T): Int
}
```
- Comparable 의 compareTo도 operator 변경자가 붙어 있으므로 하위 클래스의 오버라이딩 함수에 operator를 붙일 필요 없음


## 7.3 컬렉션과 범위에 대해 쓸 수 있는 관례

### 7.3.1 인덱스로 원소에 접근: get과 set
- 인덱스 연산자를 사용해 원소를 읽는 연산은 get 연산자 메소드로 변환되고, 원소를 쓰는 연산은 set 연산자 메소드로 변환된다.

```kotlin
// get 관례
operator fun Point.get(index: Int): Int {
    return when (index) {
        0 -> x
        1 -> y
        else ->
            throw IndexOutOfBoundsException("Invalid coordinate $index")
    }
}

>>> val p = Point(10, 20)
>>> println(p1[1])
20
```
- get 이라는 메소드를 만들고 operator 변경자를 붙이기만 하면 됨
- get 메소드의 파라미터는 Int가 아닌 타입도 사용 가능(map key), 여러 파라미터도 사용 가능(2차원 배열)

```kotlin
// set 관례
data class MutablePoint(var x: Int, var y: Int)

operator fun MutablePoint.set(index: Int, value: Int) {
    when (index) {
        0 -> x = value
        1 -> y = value
        else ->
            throw IndexOutOfBoundsException("Invalid coordinate $index")
    }
}

>>> val p = MutablePoint(10, 20)
>>> p[1] = 42
>>> println(p)
MutablePoint(x=10, y=42)
```
- set 이라는 메소드를 만들고 operator 변경자를 붙이기만 하면 됨

### 7.3.2 in 관례
```kotlin
data class Rectangle(val upperLeft: Point, val lowerRight: Point)

// until 을 사용하면 끝 값(우항)을 포함하지 않는다.
operator fun Rectangle.contains(p: Point): Boolean {
    return p.x in upperLeft.x until lowerRight.x &&
            p.y in upperLeft.y until lowerRight.y
}

>>> val rect = Rectangle(Point(10, 20), Point(50, 50))
>>> println(Point(20, 30) in rect)
true
>>> println(Point(5, 5) in rect)
false
```
- in 연산자와 대응하는 함수는 contains
- ![image](https://github.com/simjunbo/kotlin/assets/7076334/e15cace4-c92f-4958-b717-ffe2dcdc3595)


### 7.3.3 rangeTo 관례

- .. 연산자는 rangeTo 함수를 간략하게 표현하는 방법
- ![image](https://github.com/simjunbo/kotlin/assets/7076334/74fb0803-67ef-47eb-b90e-6193db25c99d)

- rangeTo 연산자는 아무 클래스에 정의 가능. 하지만 Comparable 인터페이스 구현하면 rangeTo 함수가 들어 있다.

```kotlin
>>> val now = LocalDate.now()
>>> val vacation = now..now.plusDays(10) // now.rangeTo(now.plusDays(10)) 으로 변환
>>> println(now.plusWeeks(1) in vacation)
true
```

### 7.3.4 for 루프를 위한 iterator 관례
```kotlin
for(x in list) {
}
```
- for 루프에서 사용하는 in의 의미는 다르다.
  - list.iterator() 를 호출해서 이터레이터를 얻은 다음, hasNext와 next 호출을 반복하는 식으로 변환됨


- 하지만 코틀린에서는 이 또한 관례이므로 iterator 메소드를 확장 함수로 정의할 수 있다.

```kotlin
public operator fun CharSequence.iterator(): CharIterator = object : CharIterator() {
    private var index = 0

    public override fun nextChar(): Char = get(index++)

    public override fun hasNext(): Boolean = index < length
}

>>> for (c in "abc") {}
```
- 코틀린 표준 라이브러리는 String의 상위 클래스인 CharSequence에 대한 iterator 확장 함수를 제공
- 클래스 안에 직접 iterator 메소드 구현 가능 ex) p.325


## 7.4 구조 분해 선언과 component 함수
- 구조 분해를 사용하면 복합적인 값을 분해해서 여러 다른 변수를 한꺼번에 초기화할 수 있음
- 구조 분해 선언은 함수에서 여러 값을 반환할 때 유용

```kotlin
>>> val p = Point(10, 20)
>>> val (x, y) = p // x와 y 변수를 선언한 다음에 p의 여러 컴포넌트로 초기화
>>> println(x)
10
>>>println(y)
20
```
- 구조 분해 선언(관례 사용)의 각 변수를 초기화하기 위해 componentN 이라는 함수를 호출
  - N은 구조 분해 선언에 있는 변수 위치의 번호 (최대 다섯개 까지 제공)

- ![image](https://github.com/simjunbo/kotlin/assets/7076334/de5a245a-c25b-4c50-a0a1-8ed904f49631)

### 의문점) 그냥 getter 를 사용하면 안되나?
```kotlin
data class NameComponents(val name: String, val extension: String)

fun splitFilename(fullName: String): NameComponents {
    val result = fullName.split('.', limit = 2)
    return NameComponents(result[0], result[1])
}

// 구조분해
>>> val(name, ext) = splitFilename("example.kt")
>>> println(name)
>>> println(ext)

// getter
>>> val fileName = splitFilename("example.kt")
>>> println(fileName.name)
>>> println(fileName.extension)
```

- 일반적인 data class 반환 구조보다 크기가 정해진 컬렉션을 다루는 구조 분해가 유용
```kolint
fun splitFilename(fullName: String): NameComponents {
    val (name, extension) = fullName.split('.', limit = 2)
    return NameComponents(name, extension)
}
```

### 7.4.1 구조 분해 선언과 루프

```kotlin
fun printEntries(map: Map<String, String>) {
    for ((key, value) in map) {
        println("$key -> $value")
    }
}
```
- 루프안에서 구조 분해 선언은 이터레이션할 때 매우 유용
- 위 예제는 두 가지 코틀린 관례 활용
  - 이터레이션 관례 (Map에서 iterator)
  - Map.Entry 에 대한 구조 분해 


## 7.5 프로퍼티 접근자 로직 재활용: 위임 프로퍼티
- 예를 들어 프로퍼티 위임을 통해 자신의 값을 필드가 아닌 데이터베이스 테이블이나 브라우저 세션, 맵등에 저장
- 위임은 객체가 직접 작업을 수행하지 않고 다른 도우미 객체가 그 작업을 처리하도록 맡기는 디자인 패턴. 이때 작업을 처리하는 도우미 객체를 위임 객체라 부름

### 7.5.1 위임 프로퍼티 소개 (기본 형태)

```kotlin
class Foo {
    var p: Type by Delegate()
}

// 컴파일 시
class Foo {
    private val delegate = Delegate() // 도우미 프로퍼티. by 키워드를 통해 프로퍼티와 위임 객체를 연결
    var p: Type // p 프로퍼티는 내부에서 delegate.getValue(...) 호출
    set(value: Type) = delegate.setValue(..., value)
    get() = gelegate.getValue(...)
}

class Delegate {
    operator fun getValue(...) {...}
    operator fun setValue(..., value: Type) {...}
}
```
- 일반적인 문법 형태
- p 프로퍼티는 접근자 로직을 다른 객체에 위임. Delegate 클래스의 인스턴스를 위임 객체로 사용(도우미 객체)
- 위임 관례를 따르는 Delegate 클래스는 `getValue`와 `setValue` 메소드를 제공

- ![image](https://github.com/simjunbo/kotlin/assets/7076334/239bb608-150c-4a8d-8e03-c92410644442)
  - client 입장에서는 Delegate 존재를 모른다. (일반 프로퍼티 처럼 사용)
  - proxy 역할?


### 7.5.2 위임 프로퍼티 사용: by lazy()를 사용한 프로퍼티 초기화 지연
- 지연 초기화는 객체의 일부분을 초기화하지 않고 남겨뒀다가 실제로 필요 시 초기화해서 사용 (쓸대 없는 메모리 낭비 방지)

```kotlin
// 일반 방식
class Person(val name: String) {
    private var _emails: List<Email>? = null
    val emails: List<Email>
        get() {
            if (_emails == null) {  // 최초 접근 시 이메일 가져옴
                _emails = loadEmails(this)
            }
            return _emails!!
        }
}
```
- 기존 방식은 코드가 다소 복잡하고, 스레드 세이프하지 않다.

```kotlin
// 지연 초기화 방식
class Person(val name: String) {
      val emails by lazy { loadEmails(this) }
}
```
- lazy 함수는 코틀린 관례에 맞는 시그니처의 getValue 메소드가 들어있는 객체를 반환
  - lazy를 by 키워드로 함께 사용하면 위임 프로퍼티를 만들 수 있다. 
- lazy 함수는 기본저긍로 스레드에 안전

### 7.5.3 위임 프로퍼티 구현
- 통지 시스템을 위임 프로퍼티 없이 구현하고, 나중에 위임 프로퍼티를 사용하게 리팩토링 내용

- 1) 통지 시스템을 그냥 코드로 위임하게 구현
- 2) 통지 시스템의 위임 부분을 ObservableProperty 로 만들어서 위임 프로퍼티 형태로 사용
- 3) 코틀린에서 제공하는 라이브러리(Delegates.observable) 사용해서 통지 시스템 구현

### 7.5.4 위임 프로퍼티 컴파일 규칙

```kotlin
class C {
    var prop: Type By MyDeleage()
}

// 컴파일 시
class C {
    private var <delegate> = MyDeleage() // 감춰진 프로퍼티 (delegate)
    var prop: Type
    get() = <delegate>.getValue(this, <property>) // propertyName 을 의미 하는가?
    set(value: Type) = <delegate>.setValue(this, <property>, value)
}
```

- ![image](https://github.com/simjunbo/kotlin/assets/7076334/c734ef2a-f06a-43a6-8d86-79a4f902301d)
  - 컴파일러는 모든 프로퍼티 접근자 안에 getValue와 setValue 호출 코드를 생성해 준다.

- 프로퍼티 값이 저장될 장소를 바꿀 수 있다. (이 부분 좀 이해가 안됨. 뒤에 좀 더 보자)


### 7.5.5 프로퍼티 값을 맵에 저장
- 자신의 프로퍼티를 동적으로 정의할 수 있는 객체를 만들 때 위임 프로퍼티를 활용하는 경우가 많은데, 그런 객체를 확장 가능한 객체(expando object)라 부르기도 한다.

```kotlin
class Person {
    // 추가 정보 (회사 등)
    private val _attributes = hashMapOf<String, String>()

    fun setAttribute(attrName: String, value: String) {
        _attributes[attrName] = value
    }

    // 필수 정보
    val name: String
        get() = _attributes["name"]!!
}

>>> val prop = Person()
>>> val data = mapOf("name" to "Dmitty", "company" to "JetBrains")
>>> for((attrName, value) in data)
      prop.setAttribute(attrName, value)

>>> println(prop.name)
Dmitty
```

```kotlin
// 필수 정보
/*  
    val name: String
    get() = _attributes["name"]!!
*/
    val name: String by _attributes
```
- by _attributes 코드를 통해서 쉽게 사용할 수 있다.
- 이게 가능한 이유는 Map 인터페이스에 대해 getValue, setValue 확장 함수를 제공하고, getValue에서 맵 프로퍼티 값을 저장 시, 자동으로 프로퍼티 이름을 키로 활용

### 7.5.6 프레임워크에서 위임 프로퍼티 활용

```kotlin
object Users : IdTable() {  // 데이터베이스 테이블에 해당
    val name = varchar("name", lenght = 50).index()
    val age = integer("age")

}

class User(id: EntityID) : Entity(id) { // 테이블에 들어 있는 구체적인 엔티티에 해당
    var name: String by Users.name      // 테이블의 name 컬럼에 매치
    var age: Int by Users.age
}

>>> user.age += 1 // user에 해당하는 데이터베이스 엔티티가 자동으로 갱신
```


