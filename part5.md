# 람다로 프로그래밍
- 람달들 사용하면 쉽게 공통 구조를 라이브러리 함수로 뽑아낼 수 있다.

## 5.1 람다 식과 멤버 참조

### 5.1.1 람다 소개: 코드 블록을 함수 인자로 넘기기
- 무명 내부 클래스를 넘기는 대신, 람다
  - 람다를 사용하면 간결하고 읽기 쉽다. 

### 5.1.2 람다와 컬렉션
```kotlin
data class Person(val name: String, val age: Int) {
}

class Main {
    fun main(args: Array<String>) {
        val people = listOf(Person("Alice", 29), Person("Bob", 31))
        println(people.maxBy { it.age }) // 람다를 사용해 컬렉션 검색
    }
}
```
- 람다를 사용하면 코드가 짧아지고 실수 소지가 적어짐
- 자바 컬렉션에 대해 수행하던 대부분의 작업은 람다나 멤버 참조를 인자로 취하는 라이브러리 함수를 통해 개선

### 5.1.3 람다 식의 문법

![image](https://user-images.githubusercontent.com/7076334/230751180-c4604ed5-5b8e-4d4f-9306-5d9aec0ea88a.png)


```kotlin
fun main(args: Array<String>) {
    val people = listOf(Person("Alice", 29), Person("Bob", 31))
    println(people.maxBy { it.age })
    println(people.maxBy { p: Person -> p.age }) // 정식 람다버전
}
```
- 정식 람다버전을 사용하면 구분자가 많아 가독성이 떨어진다.
- 컴파일러가 문맥으로 유추할 수 있는 인자 타입을 굳이 적을 필요 없다.
- 인자가 단 하나뿐인 경우 굳이 인자에 이름을 붙이지 않아도 된다.

```kotlin
fun main(args: Array<String>) {
    val people = listOf(Person("Alice", 29), Person("Bob", 31))
    
    people.joinToString("") { p: Person -> p.name } // 람다를 괄호 밖에 전달
 
    people.maxBy { p: Person -> p.age } // 파라미터 타입을 명시
    people.maxBy { p -> p.age }         // 파라미터 타입을 생략(컴파일러가 추론)
    people.maxBy { it.age }             // 'it'은 자동 생성된 파라미터 이름 (디폴트 파라미터)
}
```
- 로컬 변수처럼 컴파일러는 람다 파라미터의 타입도 추록할 수 있음 (파타미터 타입을 명시할 필요 없음)
- 람다 파라미터 이름을 따로 지정하지 않은 경우에만 `it` 이라는 이름이 자동으로 생성


### 5.1.4 현재 영역에 있는 변수에 접근
- 람다를 함수 안에서 정의하면 함수의 파라미터뿐 아니라 람다 정의의 앞에 선언된 로컬 변수까지 람다에서 모두 사용 가능
  - 자바와 다른 점은 코틀린 람다 안에서는 파이널 변수가 아닌 변수에 접근할 수 있다는 점
- 기본적으로 함수 안에 정의된 로컬 변수의 생명주기는 함수가 반환되면 끝남 
  - 로컬 변수를 포획한 람다를 반환하거나 다른 변ㅂ수에 저장하면 로컬 변수의 생명주기와 함수의 생명주기가 달라질 수 있음
- 람다 안에서 사용하는 외부 변수를 '람다가 포획(capture) 한 변수` 라고 부른다.

### 5.1.5 멤버 참조
![image](https://user-images.githubusercontent.com/7076334/230752426-35c4542b-1fb1-4bc7-ba9b-121f1105003e.png)

- :: 를 사용하는 식을 멤버 참조라고 부른다.
- 최상위에 선언된(다른 클래스의 멤버가 아닌) 함수나 프로퍼티를 참조할 수도 있다.
- 생성자 참조를 사용하면 클래스 생성 작업을 연기하거나 저장해둘 수 있다.
```kotlin
fun main(args: Array<String>) {
    val createPerson = ::Person
    val p = createPerson("Alice", 29)
}
```


## 5.2 컬렉션 함수형 API

### 5.2.1 필수적인 함수: filter와 map

### 5.2.2 all, any, count, find: 컬렉션에 술어 적용

### 5.2.3 groupBy: 리스트를 여러 그룹으로 이뤄진 맵으로 변경

### 5.2.4 flatMap과 flatten : 중첩된 컬렉션 안의 원소 처리


## 5.3 지연 계산(lazy) 컬렉션 연산

### 5.3.1 시퀀스 연산 실행: 중간 연산과 최종 연산

### 5.3.2 시퀀스 만들기


## 5.4 자바 함수형 인터페이스 사용

### 5.4.1 자바 메소드에 람다를 인자로 전달

### 5.4.2 SAM 생성자: 람다를 함수형 인터페이스로 명시적으로 변경


## 5.5 수신 객체 지정 람다: with와 apply

### 5.5.1 with 함수

### 5.5.2 apply 함수



