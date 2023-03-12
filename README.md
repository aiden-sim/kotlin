# 2. 코틀린 기초

## 2.1 함수와 변수

### 간단한 예제
```kotlin
fun main(argss: Array<String>) {
    println("Hello, world!")
}
```

- 함수 선언 시, fun 키워드 사용
- 파라미터 이름 뒤에 파라미터의 타입을 사용 (자바랑 반대)
- 함수를 최상위 수준으로 정의 (클래스 안에 선언할 필요 없음)
- 배열도 일반적인 클래스와 동일 (배열을 위한 문법이 존재하지 않음)
- 줄 끝에 세미콜론(;) 생략 가능


```kotlin
@kotlin.internal.InlineOnly
public actual inline fun println(message: Any?) {
    System.out.println(message)
}
```
- 코틀린 표준 라이브러리는 자바 라이브러리를 감싼 래퍼 클래스 제공

### 2.1.2 함수
#### 결과값을 반환하는 함수

```kotlin
// 블록이 본문인 함수
fun max(a: Int, b: Int): Int {
    return if (a > b) a else b
}

// 식이 본문인 함수 (자주 사용)
fun max(a: Int, b: Int): Int = if (a > b) a else b

// 반환 타입 생략 (식이 본문인 함수의 경우(중요) 반환타입이 없어도 컴파일러가 타입 추론)
fun max(a: Int, b: Int) = if (a > b) a else b
```
- 함수 선언은 fun 키워드로 시작
- fun 다음에 함수 이름
- 함수 이름 뒤에는 괄호 안에 파라미터 목록
- 함수 반환 타입은 파라미터 다음에 오는데, 콜론(:) 으로 구분

#### 문(statement)과 식(expression)의 구분
- 식은 값을 만들어 내며 다른 식의 하위 요소로 계산에 참여할 수 있다.
- 문은 자신이 둘러싸고 있는 가장 안쪽 블록의 최상위 요소로 존재하며 아무런 값을 만들어내지 않는다.
  - 식은 값을 반환하는 하나의 코드, 문은 여러 개의 코드를 포함하여 작업을 수행하는 코드 (행위)

- 자바에서는 모든 제어 구조가 문인 반면 코틀린에서는 루프를 제외한 대부분의 제어 구조가 식이다.


### 2.1.3 변수
```kotlin
// 불가
String question = "나이"
Int answer = 20

// 명시적 타입
val question: String = "나이"
val answer: Int = 20

// 타입 생략
val question = "나이"
val answer = 20

// 타입 추론 불가
val question
val answer
```

- 식과 변수 선언을 구별하기 위해, 변수 이름 뒤에 타입을 명시하거나 생략을 허용
- 식이 본문인 함수와 동일하게 타입이 생략되면 컴파일러가 초기 식을 분석해서 타입 추론 해줌 
- 초기화 식 사용 안한다면 타입을 반드시 명시 (타입 추론 불가)

#### 변경 가능한 변수, 변경 불가능한 변수
- val(value) - 변경 불가능한 참조를 저장하는 변수 (immutable)
- var(variable) - 변경 가능한 참조 (mutable)
  - 권장은 val을 기본으로 사용하고 필요할 때만 var로 변경


```kotlin
val message: String

if(condition()) {
    message = "Success"
} else {
    message = "Failed"
}
```
- val 변수는 블록을 실행할 때 한번만 초기화 되야 하지만, 한 초기화 문장만 실행됨을 컴파일러가 확인할 수 있다면 조건에 따른 여러 값으로 초기화 가능
- val 참조는 불변이라고 그 참조가 가르키는 내부 값은 변경될 수 있음 (자바랑 동일)


```kotlin
var question = "나이"
question = 20 // 컴파일 오류
```
- var는 변수의 값을 변경할 수 있지만 타입은 변경 못함 (초기화식으로 타입을 추론하며, 재대입 시 추론한 타입으로 검사)

### 2.1.4 문자열 템플릿

- java
```java
String name = "aiden";
System.out.println(String.format("Hello, %s!", name));
System.out.println("Hello, " + name + "!");

result : Hello, aiden!
```

- kotlin
```kotlin
val name = "aiden"
println("Hello, $name!") // 변수가 존재 하지 않으면 컴파일 오류

result : Hello, aiden!
```
- 문자열 템플릿 기능 사용 (변수 앞에 $ 추가)
- 복잡한 식도 중괄호({})로 둘러싸서 문자열 템플릿 안에 넣을 수 있음
  - 가독성도 더 좋고, 컴파일러가 한글을 식별자로 인식할 수 있으므로 이 방법 권장

## 2.2 클래스와 프로퍼티
- 코틀린을 활용하면 더 적은 양의 코드로 클래스와 관련 있는 대부분 작업 수행 가능

- 간단한 자바빈 클래스 Person

- java
```java

@Getter
@RequiredArgsConstructor // 롬복 사용
public class Person {
    private final String name;

    public Person(String name) {
        this.name = name; // 필드가 늘어나면 대입문 늘어남
    }

    public String getName() { // 필드가 늘어나면 getter에 대한 부분도 늘어남
        return name;
    }
}
```

- kotlin
```kotlin
class Person(val name: String)
```
- 이런 유형의 클래스를 값 객체라 부름
- `public` 접근 제어자가 사라졌는데 코틀린은 기본 접근 제한자가 `public` 이라 생략 가능

### 2.2.1 프로퍼티
- 클래스 목적은 데이터를 캡슐화하고 데이터를 다루는 코드를 한 주체 아래 가두는 것
- 자바빈즈
    - 자바에서는 데이터를 필드에 저장, 보통 비공개
    - 클래스는 클라이언트가 데이터를 접근할 수 있게 **접근자 메소드** 를 제공
        - 필드를 읽기 위한 게터(getter)
        - 필드를 변경하게 위한 세터(setter)
    - 자바에서는 필드와 접근자를 한데 묶어 **프로퍼티** 라 부른다.
- 코틀린은 프로퍼티를 언어 기본 기능으로 제공하며, 자바의 필드와 접근자 메소드를 완전히 대신한다.

- java
```java
public class Person {
    private final String name;

    private boolean isMarried;

    public Person(String name, boolean isMarried) {
        this.name = name;
        this.isMarried = isMarried;
    }

    public String getName() {
        return name;
    }

    public boolean isMarried() {
        return isMarried;
    }

    public void setMarried(boolean married) {
        isMarried = married;
    }
}
```

- kotlin으로 변환
```kotlin
class Person(
    val name: String,       // private field, public getter
    var isMarried: Boolean  // private field, public getter, public setter
)

// 코틀린에서 호출
val person = Person("aiden", false) // new 키워드 없음
println(person.name)                // 프로퍼티 직접 사용해도 자동으로 게터 호출
println(person.isMarried)
person.isMarried = true

// 자바에서 호출
Person person = new Person("aiden", false);
System.out.println(person.getName());
System.out.println(person.isMarried());
person.setMarried(true) // is를 set으로 변경
```

- 자바와 코틀린에서 정의한 Person 클래스 중, 어느쪽을 사용해도 client 코드를 바꿀 필요 없다.
- 게터/세터의 이름을 정하는데 예외가 있는데 is로 시작하는 프로퍼티의 게터는 get이 붙지 않고 원래 이름 사용, 세터는 is를 set으로 바꿔서 사용

### 2.2.2 커스텀 접근자 
- 프로퍼티의 접근자를 직접 작성하는 방법에 대해 설명

``` kotlin
class Rectangle(val height: Int, val width: Int) {
    // 커스텀 게터 
    val isSquare: Boolean
        get() {
            return height == width;
        }
        
    // 함수로 정의
    fun isSquare2(): Boolean {
        return height == width;
    }
}

// 정사각형 체크
val rectangle = Rectangle(41, 43)
println(rectangle.isSquare)    // false
println(rectangle.isSquare2()) // false
```
- 직사각형 클래스가 자신이 정사각형인지 확인하는 기능 (isSquare)
- 클라이언트가 프로퍼티에 접근할 때마다 게터가 프로퍼티 값을 매번 다시 계산
- 함수와 커스텀 게터 정의 차이?
    - 구현이나 성능상 차이 없음
    - 일반적으로 클래스의 특성을 정의하고 싶다면 프로퍼티로 그 특성을 정의 (가독성)
- 커스텀 게터는 일반적인 속성 값의 가공, 함수는 코드 블록 작업을 수행하고 결과 반환 으로 이해하면 될까?!

### 2.2.3 코틀린 소스코드 구조: 디렉터리와 패키지
- 코틀린 파일 맨 앞에 package를 넣으면 파일 안에 있는 모든 선언(클래스, 함수, 프로퍼티 등)이 해당 패키지에 속한다.
- 같은 패키지면 다른 파일이에서 정의한 선언이라도 직접 사용가능
- 다른 패키지면 임포트를 통해 선언 후 사용

```kotlin
package part2           // 패키지 선언

import java.util.Random // 클래스 임포트

class Rectangle(val height: Int, val width: Int) {
    val isSquare: Boolean
        get() {
            return height == width;
        }
}

fun createRandomRectangle(): Rectangle {
    val random = Random()
    return Rectangle(random.nextInt(), random.nextInt())
}
```

- 패키지 이름 뒤에 `.*`를 추가하면 패키지 안의 모든 선언을 임포트할 수 있다. (모든 클래스뿐 아니라 최상위 정의된 함수나 프로퍼티까지 불러옴)

#### 자바의 디렉터리 방식 (디렉터리 구조가 패키지 구조와 같아야함)

- ![image](https://user-images.githubusercontent.com/7076334/224544653-8b33b0f4-af64-4b40-b17f-d4330c9a74ff.png)



#### 코틀린의 디렉터리 방식 (패키지 구조와 디렉터리 구조가 달라도 된다.)

- ![image](https://user-images.githubusercontent.com/7076334/224544753-0fb763e3-d743-4ab0-91d7-e345c31090f5.png)

```kotlin
package geometry.example

class example {
}

package geometry.shapes

class shapes {
}
```

- 코틀린은 어느 디렉터리에 소스코드 파일을 위치시키든 상관 없다.
- 하지만 자바 규칙을 지키는 편이 낫다 (마이그레이션 시, 문제 발생)


## 2.3 enum과 when (선택 표현과 처리)
- when은 자바의 switch 랑 비슷하지만 더 강력하다.
- 스마트 캐스트에 대해서도 같이 확인

### 2.3.1 enum 클래스 정의

#### 심플 enum class
```kotlin
enum class Color {
    RED, ORANGE, YELLOW, GREEN, BLUE, INDIGO, VIOLET
}
```
- 자바는 `enum` 만 사용하지만, 코틀린은 `enum class` 를 사용한다.
- 코틀린에서 `enum`은 **소프트 키워드** 라 부르며 필드, 변수등에서도 사용 가능 (class는 불가)

#### 프로퍼티와 메서드가 있는 enum
```kotlin
enum class Color(val r: Int, val g: Int, val b: Int) {
    RED(255, 0, 0), ORANGE(255, 165, 0),
    YELLOW(255, 255, 0), GREEN(0, 255, 0), BLUE(0, 0, 255),
    INDIGO(75, 0, 130), VIOLET(238, 130, 238); // 유일하게 세미콜론 생략 불가

    fun rgb() = (r * 256 + g) * 256 + b
}

// 사용
println(Color.BLUE.rgb())
```
- enum에서도 일반 클래스와 마찬가지로 생성자와 프로퍼티 선언 가능
- enum은 클래스 안에 메소드 정의 시, 코틀린에서 유일하게 세미콜론(;) 필수

### 2.3.2 when으로 enum 클래스 다루기

#### when 기본 사용
```kotlin
fun getMnemonic(color: Color) = // 니모닉(어떤 것을 기억하는 데 쉽게 하도록 도움을 주는 것, 또는 쉽게 기억되는 성질)
    when(color) {
        Color.RED -> "Richard"
        Color.ORANGE -> "Of"
        Color.YELLOW -> "York"
        Color.GREEN -> "Gave"
        Color.BLUE -> "Battle"
        Color.INDIGO -> "In"
        Color.VIOLET -> "Vain"
}
```
- 자바의 차이점은 분이 끝에 break를 넣이 않아도 됨
- if 문과 비슷

#### 한 when 분기 안에 여러 값 사용
```kotlin
fun getWarmth(color: Color) =
    when(color) {
        Color.RED, Color.ORANGE, Color.YELLOW -> "warm"
        Color.GREEN -> "neutral"
        Color.BLUE, Color.INDIGO, Color.VIOLET -> "cold"
    }
```

### 2.3.3 when과 임의의 객체를 함께 사용
- 분기 조건에 상수만을 사용할 수 있는 자바 switch 와 달리 코틀린의 when의 분기 조건은 임의의 객체를 허용

```kotlin
fun mix(c1: Color, c2: Color) =
    when(setOf(c1, c2)) {   // when 식의 인자로 아무 객체나 사용 가능. 인자로 받은 객체가 분기 조건에 있는 객체와 같은지 테스트
        setOf(RED, YELLOW) -> ORANGE
        setOf(YELLOW, BLUE) -> GREEN
        setOf(BLUE, VIOLET) -> INDIGO
        else -> throw Exception("Dirty color")
}
```
- when 식은 인자 값과 매치하는 조건 값을 찾을 때까지 각 분기를 검사
- 분기 조건 매치할 때 **동등성** 을 사용 (set 형태이기 때문에 원소의 순서는 중요하지 않음)

### 2.3.4 인자 없는 when 사용
- 앞의 코드는 호출될 때마다 Set 인스턴스를 생성하기 때문에 비효율적
- 인자가 없는 when 식을 사용하면 불필요한 객체 생성을 막을 수 있다. (트레이드오프 성능은 좋아지지만 코드가 읽기 어려워짐)

```kotlin
fun mixOptimized(c1: Color, c2: Color) =
    when {
        (c1 == RED && c2 == YELLOW) || (c1 == YELLOW && c2 == RED) -> ORANGE
        (c1 == YELLOW && c2 == BLUE) || (c1 == BLUE && c2 == YELLOW) -> GREEN
        (c1 == BLUE && c2 == VIOLET) || (c1 == VIOLET && c2 == BLUE) -> INDIGO
        else -> throw Exception("Dirty color")
}
```
- when에 아무 인자도 없으려면 각 분기의 조건이 불리언 결과를 계산하는 식어야 한다.

### 2.3.5 스매트 캐스트 : 타입 검사와 타입 캐스트 조합

```kotlin
interface Expr

class Num(val value: Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr
```

- Expr 인터페이스에는 두 가지 구현 클래스 존재
    - 어떤 식이 수라면 그 값을 반환
    - 어떤 식이 합계라면 좌항과 우항의 합한 값을 반환 

#### 자바 스타일 작성 (If문 사용)
```kotlin
fun eval(e: Expr): Int {
    if (e is Num) {
        val n = e as Num // 불필요 하다.
        return n.value
    }

    if (e is Sum) {
        return eval(e.right) + eval(e.left)
    }
    throw IllegalArgumentException("Unknown expression")
}

// (1 + 2) + 4 = Sum(Sum(Num(1), Num(2)), Num(4))
```

- 자바에서는 `instanceof` 를 통해 타입을 확인하고, 타입을 캐스팅해서 사용해야 된다.
- 코틀린에서는 컴파일러가 대신 캐스팅 해준다. `is` 로 검사하고 나면 굳이 캐스팅이 필요 없다. (스마트 캐스트)
- 원하는 타입으로 명시적으로 타입 캐스팅 하려면 `as` 키워드 사용


### 2.3.6 리팩토링 : if를 when으로 변경
- 코틀리에서는 if가(식) 값을 만들어 내기 때문에 삼항 연산자가 따로 없다.
- 이런 특성을 이용해서 `eval` 함수에서 `return` 문과 중괄호를 없애고 `if` 식을 사용해 간단하게 리팩토링

#### 코틀린 스타일 작성 (If문 사용)
```kotlin
fun eval(e: Expr): Int =
    if (e is Num) {
        e.value
    } else if (e is Sum) {
        eval(e.right) + eval(e.left)
    } else {
        throw IllegalArgumentException("Unknown expression")
    }
```

#### 코틀린 스타일 작성 (If문 대신 when 사용)
```kotlin
fun eval(e: Expr): Int =
    when (e) {
        is Num -> e.value
        is Sum -> eval(e.right) + eval(e.left)
        else -> throw IllegalArgumentException("Unknown expression")
    }
```

- 받은 값의 타입을 검사하는 when 분기문 형태로 사용 (스마트 캐스트)

### 2.3.7 if와 when의 분기에서 블록 사용
```kotlin
fun evalWithLoggin(e: Expr): Int =
    when (e) {
        is Num -> {
            println("num: $(e.value}")
            e.value
        }

        is Sum -> {
            val left = evalWithLoggin(e.left)
            val right = evalWithLoggin(e.right)
            println("sum: $left + $right")
            left + right
        }

        else -> throw IllegalArgumentException("Unknown expression")
    }

```
- 블록의 마지막 식이 블록의 결과여야 한다.



## 2.4 while과 for 루프 (대상을 이터레이션)
- 2장의 특성 중, 자바와 가장 비슷한 것이 인터레이션

### 2.4.1 while 루프
- while과 do-while은 자바 문법과 동일

### 2.4.2 수에 대한 이터레이션: 범위와 수열
- 자바의 for 루프 형태가 아닌 범위(range)를 사용한다.
- 어떤 범위에 속한 값을 일정한 순서로 이터레이션하는 경우를 **수열** 이라고 부른다.

```kotlin
fun fizzBuzz(i: Int) = when {
    i % 15 == 0 -> "FizzBuzz"
    i % 3 == 0 -> "Fizz"
    i % 5 == 0 -> "Buzz"
    else -> "$i "
}

// 일반 모드
for(i in 1..100) {
    print(fizzBuzz(i))
}

// 하드 모드 (거꾸로 세면서 짝수만)
for(i in 100 downTo 1 step 2) {
    print(fizzBuzz(i))
}
```

- `..`는 항상 범위의 끝 값(우항)을 포함한다.
- `until` 을 사용하면 끝 값(우항)을 포함하지 않는다.

### 2.4.3 맵에 대한 이터레이션

```kotlin
val binaryReps = TreeMap<Char, String>()

for(c in 'A'..'F') {    // A부터 F까지 문자의 범위를 이터레이션
    val binary = Integer.toBinaryString(c.toInt())
    binaryReps[c] = binary
}

for((letter, binary) in binaryReps) {   // 맵에 대한 이터레이션 (키와 값을 각 각 대입)
    println("$letter = $binary")
}
```
- `..` 연산자는 숫자 타입의 값 뿐만 아니라 문자 타입의 값에도 적용가능 (A ~ F)
- Map에 대한 이터레이션은 key, value 형태로 처리


### 2.4.4 in으로 컬렉션이나 범위의 원소 검사
- `in` 연산자를 사용해 어떤 값이 범위에 속하는지 검사할 수 있다.
- `!in` 연산자를 사용하면 어떤 값이 범위에 속하지 않는지 검사할 수 있다. 

#### in을 사용해 값이 범위에 속하는지 검사
```kotlin
fun isLetter(c: Char) = c in 'a'..'z' || c in 'A'..'Z'
fun isNotDigit(c: Char) = c !in '0'.. '9'
    
println(isLetter('q'))      // true
println(isNotDigit('x'))    // false
```

#### when에서 in 사용
```kotlin
fun recognize(c: Char) = when(c) {
    in '0'..'9' -> "It's a digit!"
    in 'a'..'z', in 'A'..'Z' -> "It's a letter"
    else -> "I don't know"
}

println(recognize('8'))   // It's a digit!
```

- `java.lang.Comparable` 인터페이스를 구현한 클래스라면 인스턴스 객체를 사용해 범위를 만들 수 있다.
    - ex) `println("Kotlin" in "Java".."Scala")`
- 컬렉션도 in 연산 사용 가능
    - ex) `println("Kotlin" in setOf("Java", "Scala"))`

## 2.5 예외 처리
- 코틀린의 기본 예외 구문은 자바와 비슷하다. (new를 붙일 필요 없다는 차이점이 있다.)
- 코틀린의 throw는 식이므로 다른 식에 포함될 수 있다.

### 2.5.1 try, catch, finally

#### 자바스타일로 사용 (Statement)
```kotlin
fun readNumber(reader: BufferedReader): Int? {
    try {
        val line = reader.readLine()
        return Integer.parseInt(line)
    } catch (e: NumberFormatException) {
        return null
    } finally {
        reader.close();
    }
}
```

```java
public interface Readable {
    public int read(java.nio.CharBuffer cb) throws IOException;
}
```
- 자바 코드와 가장 큰 차이는 `throws` 절이 없다는 점 (IOException는 체크 예외기 때문에 catch를 잡거나 throws를 던지거나)
- 자바는 체크 예외 처리를 강제하지만, 코틀린은 체크 예외와 언체크 예외를 구별하지 않는다.
- 하지만 프로그래머들이 의미 없이 예외를 다시 던지거나, 예외를 잡되 처리하지 않고 무시하는 코드가 흔하다.
- 체크 예외의 의존성 문제도 있다.
    - ex) JDBC(SQLException) -> JPA(JPAException) 연관된 코드를 다 변경해야 된다.

### 2.5.2 try를 식으로 사용
```kotlin
fun readNumber2(reader: BufferedReader) {
    val number = try {
        Integer.parseInt(reader.readLine())
    } catch (e: NumberFormatException) {
        return
    }

    println(number)
}
```
- 코틀린의 try 키워드는 식이기 때문에 값을 변수에 대입할 수 있다.
- try의 본문을 반드시 중괄호 {}로 둘러 싸야되고, 마지막은 결과 값이어야 한다.
