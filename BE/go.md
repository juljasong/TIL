# Go
- 2009년 발표된 오픈 소스 프로그래밍 언어
- [공식 홈 인터렉티브 튜토리얼](https://go.dev/tour/welcome/1)
- [MOOC](https://www.udemy.com/)

## 목표
1. 다른 프로그래밍 언어 단점 해결
   - 복잡성 감소, 코드 가독성 향상
2. 기존 언어의 강점 개선
   - 컴파일 속도 개선, 정적 타입 시스템 적용
3. 주요 특징 : 단순, 효율적, 동시성
   - 고루틴(Goroutines)을 통한 동시성 지원
   - 강력한 표준 라이브러리 제공

## 성능 이점
- 동시성 : 경량 고루틴 및 채널 지원
- 시작 시간 : 빠른 시작, 정적 컴파일
- 메모리 사용량 : 낮은 메모리 소비
- 마이크로 서비스 : 간결함, 빠른 빌드, 작은 바이너리
- 정적 링크 : 바이너리 내 종속성 포함, 간단한 배포

## 이점
- like 파이썬, 빠름
  - 단순한 언어 -> 러닝커브 낮고 좋은 생산성
  - 프로그램이 작고 빠름
- 빌드된 Binary 하나만 있으면 됨(10-30mb 정도)
- 테스트 코드 짜기 쉬움
- 프로파일링 도구가 잘 되어 있음(pprof)
- 간결한 문법
- 구글의 지원
- 업으로 하는 사람이 드물다
  - 스페셜리스트가 될 수 있음!
- 재밌음
- Go가 어울리는 업무
  - 암호화, 저수준 네트워크, 동시성 지원이 필요한 일
- 모던 OS라면 어디나 쓸만함
- 크로스 컴파일러 내장
- 컨테이너 이미지
  - 스태틱 싱글 바이너리로 빌드
  - scratch 베이스로 이미지 사이즈를 5-10mb 사이로 빌드 가능
- 라즈베리 파이와 같은 SBC(Single Board Computer)에서 하드웨어 제어 가능
  -  periph: https://periph.io/

# 개발 환경 구축
## 1. Go 설치 
- [설치](https://go.dev/)
- 확인
  ```shell
    $ go version
  ```

## 2. VS Code 설치
- VS Code 설치
- Go Extention 설치
  - [View] - [Extentions] - go 설치

## 예제 실행
```shell
  $ go mod init project/folder  # go 모듈 생성
  $ go build                    # build
  $ ./test.exe                  # 실행
```

# 변수
- 값을 저장하는 메모리 공간

## 변수 선언
```go
  var a int = 10
  var msg string = "hello"
```

## 변수의 4가지 속성
- 이름
- 값
- 주소
- 타입
  - 숫자
    - uint : 부호 없는 정수 숫자
    - int : 부호 있는 정수 숫자
    - float : 실수
  - 불리언 : bool - true/false
  - 문자열 : string
  - 배열 : 고정 길이 배열
  - 슬라이스 : 가변 길이 배열
  - 구조체 : 필드(변수)의 집합 자료구조
  - 포인터 : 메모리 주소를 값으로 갖는 타입. 같은 메모리 공간을 가리키는 여러 변수 생성 가능
  - 함수 타입 : 함수 포인터. 사용할 함수를 동적으로 바꿀 때 유용
  - 인터페이스 : 메서드 정의의 집합
  - 맵 : key-value.
  - 채널 : 멀티스레드 환경에 특화된 큐 형태 자료구조

### 변수명
- 규칙
  - 문자, _, 숫자를 이용해 지을 수 있지만, 첫 글자는 반드시 문자나 _로 시작
  - _를 제외한 다른 특수문자 사용 불가
  - 영어 사용 권장
  - camelCase

### 변수 선언
```go
  var a int = 3   // 기본
  var b int       // 초기값 생략. 기본값
  var c = 4       // 타입 생략. 변수 타입은 우변 값의 타입
  d := 5          // var 키워드와 타입 생략
```

### 타입
- 타입 검사 유무
  - **O : 강 타입 언어** -> 자동 변환 X
  - X : 약 타입 언어
- 타입 변환
  - type(var) 

# 표준 입출력 (fmt)
## 서식 문자
- ```Printf(서식 문자열, 인수1, 인수2, ...)```
- ```%v``` : 데이터 타입에 맞춰 기본 형태로
- ```%T``` : 데이터 타입
- ```%t``` : 불리언 -> true/false
- ```%d``` : 10진수 정수값
- ```%b``` : 2진수
- ```%o``` : 8진수
- ```%x``` : 16진수
- ```%e %E``` : 지수 형태로 실수값 출력
- ```%f %F``` : 지수 형태가 아닌 실수값 그대로 출력
- ```%g %G``` : 값이 큰 실수는 지수 형태로, 작은 실수값은 그대로 출력
- ```%s``` : 문자열 출력
- ```%q``` : 특수 문자 기능 동작하지 않고 문자열 그대로 출력
- ```%p``` : 메모리 주소값 출력

# 함수
- 반복 사용되는 코드를 묶어서 재사용하기 위함

## 정의
```go
  func Add(a int, b int) int {  // func 함수명(매개변수, ..) 반환타입
    return a + b
  }
```
- 함수명
  - 첫 글자가 대문자 : public
  - 첫 글자가 소문자 : private
- 매개변수
  - 없으면 빈칸
- 반환타입
  - 없으면 빈칸

## 멀티 반환 함수
```go
  func Divide(a int, b int) (int, bool) {

    if b == 0 {
      return 0, false
    } 

    return a / b, true

  }
```

## 변수명 지정 반환
```go
  func Divide(a int, b int) (result int, success bool) {
    
    if b == 0 {
      result = 0
      success = false
      return
    }

    result = a / b
    success = true
    return
    
  }
```

# 상수
- 변하지 않는 값
- 정수, 실수, 문자열 등 기본 타입값만 상수로 선언 가능
- ```const```
- 함수 외부 + 첫 글자 대문자 -> public

## iota
```go
  const (
    Red   int = iota // 0
    Blue  int = iota // 1
    Green int = iota // 2
  )
```

# if
```go
  if ok && age < 20 {
    fmt.Println("under 20")
  } else if ok && age < 30 {
    fmt.Println("under 30")
  } else if ok {
    fmt.Println("hi")
  } else {
    fmt.Println("Error")
  }
```

- if 초기문; 조건문
```go
  if age, ok := getAge(); ok && age < 20 {
    fmt.Println("under 20")
  } else if ok && age < 30 {
    fmt.Println("under 30")
  } else if ok {
    fmt.Println("hi")
  } else {
    fmt.Println("Error")
  }
```

# switch
- ```break``` 없어도 case 실행 후 switch 빠져나감
- 이후 case 실행하고 싶을 땐 ```fallthrough```
```go
  switch 비굣값 {
    case 값1:
      문장
    case 값2, 값3:
      문장
    case 값4 > 값5:
      문장
    default:
      문장
  }
```
- switch 초기문
```go
  switch age := getAge(); age {
    case 10:
      fmt.Println("Teens")
    case 33:
      fmt.Println("Pair 3")
    default:
      fmt.Println("My age is %v", age)
  }
```

# for
```go
  for 초기문; 조건문; 후처리 {
    코드 블록
  }

  for true {
    코드 블록
  }

  for {
    코드 블록
  }

  // G0 1.22~
  for i := range 10 {
    코드 블록
  }
```
```go
  for i:= 0; i < 10; i++ {
    fmt.Print(i, " ")
  }
```
## continue
- 이후 코드 블록 수행하지 않고 앞으로 돌아감
## break
- for 문에서 빠져나옴

# 배열
```go
  // var 변수명 [요소개수]타입
  var t [5]float64 = [5]float64{24.0, 25.9, 27.8, 26.9, 26.2} 
```

## 선언 및 초기화
```go
  var nums [5]int

  var s = [5]int{1:10, 3:30}

  days := [3]string{"mon", "tue", "wed"}
  days := [...]string{"mon", "tue", "wed"}
```

## range 순회
```go
  var t [5]float64 = [5]float64{24.0, 25.9, 27.8, 26.9, 26.2}

  for i, v := range t {
    fmt.Println(i, v)
  }

  for _, v := range t { // 인덱스 사용 안하면 _
		fmt.Println(v)
	}

```

# 구조체
- 여러 필드를 묶어서 사용하는 타입
- 구조체를 통해 연관된 여러 데이터를 하나의 이름으로 묶을 수 있음
- 여러 값을 손쉽게 다른 함수로 전달 가능
- 

## 선언 및 기본 사용
```go
  type 타입명 struct {
    필드명 타입
  }

  타입명.필드명 = 값
```
- 타입명
  - 대문자 시작 : public
  - 소문자 시작 : private

## 변수 초기화
- 초기값 생략
  - 초기값 생략하면 모든 필드가 기본값으로 초기화
- 모든 필드 초기화
  - ```var 타입명 타입 = 타입 {값1, 값2, 값3, 값4, ..}```
- 일부 필드 초기화
  - ```var 타입명 타입 = 타입 {필드1: 값1, 필드2: 값2}```

## 구조체를 포함하는 구조체
### like 내장 타입
```go
  type User struct {
    Name string
    Id string
    Age int
  }

  type VIPUser struct {
    UserInfo User
    VIPLevel int
  }

  func main() {
    user := User {"송하나", "hana", 23}
    vip := VIPUser {
      user,
      3,
    }

    fmt.Println(vip.UserInfo.Name)
  }
```

### 포함된 필드 방식
- 구조체에서 다른 구조체를 필드로 포함할 때 필드명 생략하면 포함된 구조체 필드에 . 한 번으로 접근 가능
```go
  type User struct {
    Name string
    Id string
    Age int
  }

  type VIPUser struct {
    User
    VIPLevel int
  }

  func main() {
    user := User {"송하나", "hana", 23}
    vip := VIPUser {
      user,
      3,
    }

    fmt.Println(vip.Name)
  }
```
- 필드명이 중복되는 경우에는 vip.User.Name 과 같이 접근

## 구조체 크기 
- 패딩 : 메모리 정렬을 위해 필드 간 간격을 띄우는 것
- 8바이트 보다 작은 필드는 몰아서 배치

## 결합도 (Coupling)
- 의존성
- 모듈 간 상호 의존 관계를 형성해 서로 강하게 결합되어 있는 정도를 나타내는 용어

## 응집도 (Cohesion)
- 모듈의 완성도
- 모듈 내부의 모든 기능이 단일 목적에 충실하게 모여 있는지