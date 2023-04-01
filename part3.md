# 3. 함수 정의와 호출

## 3.1 코틀린에서 컬렉션 만들기
```kotlin
fun main(argss: Array<String>) {
    val set = hashSetOf(1, 7, 53)
    val list = arrayListOf(1, 7, 53)
    val map = hashMapOf(1 to "one", 7 to "seven", 53 to "fifty-three")

    println(set.javaClass)  // class java.util.HashSet
    println(list.javaClass) // class java.util.ArrayList
    println(map.javaClass)  // class java.util.HashMap
    
    // 코틀린에서 제공하는 함수
    println(list.last())
    println(set.max())
}
```
- to는 키워드가 아니라 일반 함수
- 이는 코틀린이 자신만의 컬렉션 기능을 제공하지 않는다는 뜻임
    - 표준 자바 컬렉션을 활용하면 자바 코드와 상호작용하기가 훨씬 더 쉽다. (변환 필요 없음)
- 하지만 코틀린은 자바보다 더 많은 기능 제공
    

## 3.2 함수를 호출하기 쉽게 만들기

### joinToString() 초기 구현
```kotlin
fun <T> joinToString(
    collection: Collection<T>,
    separator: String,
    prefix: String,
    postfix: String
): String {
    val result = StringBuilder(prefix)
    for ((index, element) in collection.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(element)
    }

    result.append(postfix)
    return result.toString()
}

val list = listOf(1, 2, 3)
println(joinToString(list, "; ", "(", ")")) // (1; 2; 3)
```
- 처음에는 코틀린이 지원하는 여러 기능을 사용하지 않고 직접 함수 구현

### 3.2.1 이름 붙인 인자
- 위 예제는 함수 시그니처를 보지 않는 이상 문자열 값의 역할을 알기 어렵다.

```kotlin
println(joinToString(collection, separator = "; ", prefix = "(", postfix = ")"))
```
- 다음과 같이 인자 중 일부의 이름을 명시할 수 있다.
- 애석하게 자바로 작성한 코드를 호출할 때는 이름 붙인 인자를 사용할 수 없다. (JDK 6와 호환을 위해)

### 3.2.2 디폴트 파라미터 값
- 코틀린에서는 함수 선언에서 파라미터의 디폴트 값을 지정할 수 있으며 오버로드 중 상당수를 피할 수 있다.
- 이름 붙은 인자를 사용하면 인자 목록의 중간에 있는 인자 생략 가능
- 디폴트 값을 변경하면, 그 함수를 호출하는 코드 중 값을 지정하지 않은 모든 인자 자동으로 변경

```kotlin
fun <T> joinToString(
    collection: Collection<T>,
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""
): String {
    val result = StringBuilder(prefix)
    for ((index, element) in collection.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(element)
    }

    result.append(postfix)
    return result.toString()
}

joinToString(list, ", ", "","") // 1, 2, 3
joinToString(list)              // 1, 2, 3
joinToString(list, "; ")        // 1; 2; 3
```

- java에서는 디폴트 파라미터 개념이 없어서, `@JvmOverloads` 를 붙이면 코틀린에서 자동으로 오버로딩 함수를 만들어 준다.


### 3.2.3 정적인 유틸리티 클래스 없애기: 최상위 함수와 프로퍼티
- 실전에는 어느 한 클래스에 포함시키기 어려운 코드가 많이 생김
    - 유틸성 클래스 생김 ex) JDK의 Collections 클래스
- 코틀린에서는 클래스 없이 함수를 직접 소스 파일의 최상위 수준으로 위치시키면 된다.

```kotlin
package part3

fun <T> joinToString(...): String {...}
```

- 자바 변환 시
```java
package part3

public class JoinKt {
    public static String joinToString(...) {...}
}
```

- 코틀린 최상위 함수가 포함되는 클래스 이름 변경 시, `@JvmName` 애노테이션 사용 ( 패키지 이름 선언 전에 위치)
- 최상위 프로퍼티도 사용 가능

## 3.3 메소드를 다른 클래스에 추가: 확장 함수와 확장 프로퍼티
- 기존 코드와 코틀린 코드를 자연스럽게 통합하는 것이 코틀린의 핵심 목표중 하나
- 확장 함수는 어떤 클래스의 멤버 메소드인 것처럼 호출할 수 있지만 그 클래스의 밖에 선언된 함수

```kotlin
fun String.lastChar(): Char = this.get(this.length -1)

println("Kotlin".lastChar()) // n
```
- 확장 함수를 만들려면 추가하려는 함수 이름 앞에 그 함수가 확장할 클래스의 이름을 덧붙이기만 하면 된다.
- 클래스 이름을 수신 `객체 타입` 이라 부르며, 확장 함수가 호출되는 대상이 되는 값을 `수신 객체` 라고 부른다.
- 클래스 안에서 정의한 안에서 정의한 메소드와 달리 확장 함수 안에서는 클래스 내부에서만 사용되는 비공개 멤버나 보호된 멤버를 사용할 수 없다. (캡슐화 유지)

### 3.3.1 임포트와 확장 함수
- 확장 함수를 사용하기 위해서는 임포트해야만 한다. (충돌 발생할 수 있으므로)

```kotlin
// case1
import strings.lastChar

val c = "kotlin".lastChar()

// case2
import strings.*

val c = "kotlin".lastChar()

// case3
import strings.lastChar as last

val c = "kotlin".last()     // 임포트한 클래스나 함수를 다른 이름으로 부를 수 있다.
```
- 다른 여러 패키지에 속해있는 이름이 같은 함수를 사용할 경우 이름을 변경해서(as) 임포트하면 이름 충돌을 막을 수 있다.

### 3.3.2 자바에서 확장 함수 호출
- 내부적으로 확장 함수는 수신 객체를 첫 번째 인자로 받는 정적 메소드다. (실행 시점 비용이 들지 않음)

### 3.3.3 확장 함수로 유틸리티 함수 정의
```kotlin
fun <T> Collection<T>.joinToString( // Collection<T> 에 대한 확장 함수를 선언
    separator: String = ", ",       // 디폴트 파라미터
    prefix: String = "",
    postfix: String = ""
): String {
    val result = StringBuilder(prefix)
    for ((index, element) in this.withIndex()) {    // 기존에는 Collection을 파라미터 받아서 사용했지만, 수식 객체를 사용하기 때문에 this로 호출
        if (index > 0) result.append(separator)
        result.append(element)
    }

    result.append(postfix)
    return result.toString()
}
```

### 3.3.4 확장 함수는 오버라이드할 수 없다
- 확장 함수를 오버라이드할 수 없다.
- 확장 함수는 클래스의 일부가 아니다. 클래스 밖에 선언된다.
- 확장 함수 호출 시, 수신 객체로 지정한 변수의 정적 타입에 의해 어떤 확장 함수가 호출될지 결정되기 때문에

```kotlin
fun View.showOff() = println("I'm a view!")
fun Button.showOff() = println("I'm a button!")

val view : View = Button()
view.showOff()              // I'm a view!  (확장 함수는 정적으로 결정된다.)
```

```java
View view = new Button();
ExtensionsKt.showOff(view);
```

- 클래스를 확장한 삼수와 그 클래스의 멤버 함수의 이름과 시그니처가 같다면 멤버함수가 호출된다. (운선순위가 멤버 함수가 더 높음)

### 3.3.5 확장 프로퍼티
- 확장 프로퍼티를 사용하면 기존 클래스 객체에 대한 프로퍼티 형식의 구문으로 사용할 수 있는 API를 추가할 수 있다. (상태 없음)


```kotlin
// 확장 함수
fun String.lastChar(): Char = this.get(this.length - 1)

// 확장 프로퍼티
val String.lastChar: Char
    get() = get(length - 1)
```
- 뒷받침하는 필드가 없어서 기본 게터 구현을 제공할 수 없기 때문에 최소한 게터는 꼭 정의해야 한다.
- 확장 프로퍼티는 상태를 가질 수 없기 때문에 초기화 코드 사용 불가

## 3.4 컬렉션 처리: 가변 길이 인자, 중위 함수 호출, 라이브러리 지원
- 컬렉션을 처리할 때 쓰는 코틀린 표준 라이브러리 소개
    - vararg 키워드를 사용하면 호출 시 인자 개수가 달라질 수 있는 함수를 정의
    - 중위 함수 호출 구문을 사용하면 인자가 하나뿐인 메소드를 간편하게 호출할 수 있다.
    - 구조 분해 선언을 사용하면 복합적인 값을 분해해서 여러 변수에 나눠 담을 수 있다.

### 3.4.1 자바 컬렉션 API 확장

```kotlin
val strings: List<String> = listOf("first", "second", "fourteenth")
strings.last()  // fourteenth

val numbers: Collection<Int> = setOf(1, 14, 2)
numbers.max()   // 14


public fun <T> List<T>.last(): T {
    if (isEmpty())
        throw NoSuchElementException("List is empty.")
    return this[lastIndex]
}

public fun <T : Comparable<T>> Iterable<T>.max(): T? {
    return maxOrNull()
}
```
- last와 max는 코틀린에서 제공하는 확장 함수

### 3.4.2 가변 인자 함수: 인자의 개수가 달라질 수 있는 함수 정의
- 자바에서는 가변인자로 `...` 를 붙이지만 코틀리에서는 파라미터 앞에 `vararg` 변경자를 붙인다.
- 코틀리에서는 가변인자에 배열을 넘길때 명ㅇ시적으로 풀어서 원소가 인자로 전달되게 해야된다.
    - 이러한 역할을 `스프레드 연산자(*)` 가 해준다.

```kotlin
var list = listOf("args: ", *args) // 스프레드 연산자
println(list)
```

### 3.4.3 값의 쌍 다루기: 중위 호출과 구조 분해 선언
```kotlin
val map = mapOf(1 to "one", 7 to "seven", 53 to "fifty-three")
```
- to라는 단어는 코틀린 키워드가 아닌 `중위 호출` 이라는 특별한 방식으로 to라는 일반 메소드를 호출한 것
- `구조 분해 선언`


## 3.5 문자열과 정규식 다루기
- 코틀린 문자열은 자바 문자열과 같다.  (특별 변환도 필요 없고, 별도의 래퍼도 생기지 않는다.)
- 코틀린은 다양한 확장 함수를 제공한다.

### 3.5.1 문자열 나누기
- 자바에서 split은 정규식 사용하기 때문에 `.` 인식을 못함
- 코틀린에서는 혼돈을 줄이기 위해 정규식을 파라미터로 받는 함수는 String이 아닌 Regex 타입의 값을 받음

```kotlin
println("12.345-6.A".split("\\.|-".toRegex()))  // 정규식 방식
println("12.345-6.A".split(".", "-"))           // 문자열 방식
```

### 3.5.2 정규식과 3중 따옴표로 묶은 문자열
```kotlin
fun parsePath(path: String) {
    val regex = """(.+)/(.+)\.(.+)""".toRegex()
    val matchResult = regex.matchEntire(path)
    if(matchResult != null) {
        val (directory, fileName, extension) = matchResult.destructured
        println("Dir: $directory, name: $fileName, ext: $extension ")
    }
}
```
- 3중 따옴표 무낮열을 사용해 정규식을 사용
- 문자열 이스케이프 필요 없음 (\\)

### 3.5.3 여러 줄 3중 따옴표 문자열
- 3중 따옴표는 줄 바꿈을 표현하는 문자열도 그대로 들어간다.

## 3.6 코드 다듬기: 로컬 함수와 확장
