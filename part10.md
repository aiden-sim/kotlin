# 애노테이션과 리플렉션
- 애노테이션과 리플렉션을 사용하면 미리 알지 못하는 임의 클래스를 다를 수 있다.

## 10.1 애노테이션 선언과 적용
- 메타데이터를 선언에 추가하면 애노테이션을 처리하는 처리기가 컴파일 시점이나 실행 시점에 적절할 처리를 해준다.

### 10.1.1 애노테이션 적용
- 애노테이션을 적용하려면 적용하려는 대상 앞에 애노테이션을 붙이면 된다.
- 코틀린은 `@Deprecated` 되는 코드에 대해 경고를 해줄 뿐만 아니라 자동으로 그 코드를 새로운 API 버전에 맞는 코드로 바꿔주는 퀵 픽스도 제시

- 애노테이션 인자
  - 클래스는 ::class를 클래스 이름 뒤에 넣어야함 ex) @MyAnnotion(MyClass::class)
  - 다른 애노테이션을 넣을 경우 @를 뺴야 한다. ex) @Deprecated("message", ReplaceWith("removeAt(index)"))
  - 배열을 인자로 하려여면 arrayOf 함수를 사용한다. ex) @RequestMapping(path = arrayOf("/foo", "/bar"))

- 임의의 프로퍼티를 인자로 지정할 수 없다. (컴파일 시점에 알 수없다.) const (상수)를 이용해서 초기화할 수 있다.

### 10.1.2 애노테이션 대상
- 사용 지점 대상 선언으로 애노테이션을 붙일 요소를 정할 수 있다.

- 사용 지점 대상을 지정할 때 지원하는 대상 목록
  - property 프로퍼티 전체. 자바에서 선언된 애노테이션에는 사용 못함
  - field 프로퍼티에 의해 생성되는 필드
  - get 프로퍼티 게터
  - set 프로퍼티 세터
  - receiver 확장 함수나 프로퍼티의 수신 객체 파라미터
  - param 생성자 파라미터
  - setparam 세터 파라미터
  - delegate 위임 프로퍼티의 위임 인스턴스를 담아둔 필드
  - file 파일 안에 선언된 최상위 함수와 프로퍼티를 담아두는 클래스 


### 10.1.3 애노테이션을 활용한 JSON 직렬화 제어


### 10.1.4 애노테이션 선언


### 10.1.5 메타애노테이션: 애노테이션을 처리하는 방법 제어

### 10.1.6 애노테이션 파라미터로 클래스 사용


### 10.1.7 애노테이션 파라미트로 제네릭 클래스 받기


## 10.2 리플렉션: 실행 시점에 코틀린 객체 내부 관찰

### 10.2.1 코틀린 리플렉션 API: KClass, KCallable, KFunction, KProperty

### 10.2.2 리플렉션을 사용한 객체 직렬화 구현

### 10.2.3 애노테이션을 활용한 직렬화 제어

### 10.2.4 JSON 파싱과 객체 역직렬화

### 10.2.5 최종 역직렬화 단계: callBy(), 리플렉션을 사용해 객체 만들기








