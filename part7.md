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

### 7.1.3 단항 연산자 오버로딩


## 7.2 비교 연산자 오버로딩

### 7.2.1 동등성 연산자: equals

### 7.2.2 순서 연산자: compareTo


## 7.3 컬렉션과 범위에 대해 쓸 수 있는 관례

### 7.3.1 인덱스로 원소에 접근: get과 set

### 7.3.2 in 관례

### 7.3.3 rangeTo 관례

### 7.3.4 for 루프를 위한 iterator 관례


## 7.4 구조 분해 선언과 component 함수

### 7.4.1 구조 분해 선언과 루프


## 7.5 프로퍼티 접근자 로직 재활용: 위임 프로퍼티

### 7.5.1 위임 프로퍼티 소개

### 7.5.2 위임 프로퍼티 사용: by lazy()를 사용한 프로퍼티 초기화 지연

### 7.5.3 위임 프로퍼티 구현

### 7.5.4 위임 프로퍼티 컴파일 규칙

### 7.5.5 프로퍼티 값을 맵에 저장

### 7.5.6 프레임워크에서 위임 프로퍼티 활용



