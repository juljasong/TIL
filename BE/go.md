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

# 포인터
```go
  p = &a // 포인터 변수 p가 변수 a를 가리킨다
```

- 메모리 주소를 값으로 갖는 타입
- 동일한 메모리 공간을 여러 변수가 가리킬 수 있음(동일 인스턴스)
- 메모리 복사를 줄일 수 있음
- 반환값 없이 변수값을 바꿀 수 있음
- 사용
  - 복사 X
  - arg로 받은 값의 변경이 필요할 때

## 포인터 변수 선언
```go
  var a int
  var p *int

  p = &a  // p에 a 메모리 주소 대입
  *p = 20 // a의 값도 바뀜
```

## 포인터 변수값 비교
- ```==``` : 같음 메모리 공간 가리키는 지 확인 가능

## 기본값 nil
- 포인터 변수값 초기화 x -> nil

## 구조체 생성해 포인터 변수 초기화하기
```go
  var data Data
  var p *Data = &data
  var p *Data = &Data{}

  p1 := &Data{}
  var p2 = new(Data)
```

## new() 내장함수
- 인수로 타입을 받음
- ```new(Type)```
- 내부 필드 값을 채울 수 없음

## Stack / Heap
- 탈출 검사를 해서 어느 메모리에 할당할 지 결정
  - 함수 외부로 공개 : Heap
  - 함수 내부에서만 사용 : Stack

# 문자열 string
- UTF-8
- ```"``` : 특수 문자 동작
- ```\``` : 특수 문자 동작 x. nl 포함한 입력한 그대로 

## rune 
- 문자 하나 표현 (char)
- 4바이트 정수 타입 int32의 별칭 타입
- ```'```
```go
  rune := []rune{ 72, 101, 108, 108, 111}
  str := string(rune)
```

## 문자열 순회
```go
  str := "hello world"
```

### 1. 인덱스를 사용한 바이트 단위 순회
```go
  for i := 0; i < len(str); i++ {
		fmt.Printf("%T %d %c \t", str[i], str[i], str[i])
	}
```

### 2. []rune으로 타입 변환 후 한 글자씩 순회
```go
  arr := []rune(str)
	for i := 0; i < len(arr); i++ {
		fmt.Printf("%T %d %c \t", str[i], str[i], str[i])
	}
```

### 3. range 키워드 이용한 한 글자씩 순회
```go
  for _, v := range str {
		fmt.Printf("%T %d %c \t", v, v, v)
	}
```

## 문자열 비교
- ```==``` : 같으면 true
- ```!=``` : 다르면 true

## 문자열 합산
- string을 합산하면 java와 같이 인스턴스가 왕왕 생겨남
- ```Builder``` 사용
  ```go
    var builder strings.Builder

    builder.WriteString("hello")
    builder.WriteString(" ")
    builder.WriteString("world")

    fmt.Println(builder.String())
  ```

# 패키지
- 코드를 묶는 가장 큰 단위
- 타입, 필드 함수에 대한 접근 제공
- 연관 함수, 타입 등의 코드를 패키지로 묶어 배포 가능
- 정리
  - 함수 : 코드 블록
  - 구조체 : 데이터
  - 패키지 : 함수, 구조체와 그 외 코드
- main 패키지(필수 요소)/외부 패키지(선택 요소)
- ```import``` : 패키지 초기화 시작 (전역 변수 초기화 -> init()함수 호출)

## main 패키지
- 필수
- 프로그램 시작점
- Load : 프로그램을 메모리를 올리는 것

## 그 외 패키지
- main 외 다른 패키지
- fmt(표준 입출력), crypto(암호화), net(네트워크), ..

## 유용한 패키지 찾기
- [Standard library](https://golang.org/pkg)
- [Awesome Go](https://github.com/avelino/awesome-go)

## 겹치는 패키지 문제
- 별칭 주기
```go
  import (
    ttemplate "text/template"
    htemplate "html/template"
  )
```

## 사용하지 않는 패키지 포함하기
```go
  import(
    "database/sql"
    _ "fmt"
  )
```
- init()이 필요한 경우

## Go 모듈
- Go 패키지들을 모아놓은 Go 프로젝트 단위
- Go 1.16 버전 부터 기본
- **```go build```를 하려면 반드시 Go 모듈 루트 폴더에 go.mod 파일 필요**
- ```go mod tidy``` : Go 모듈에 필요한 패키지 찾아 다운로드

## 패키지명과 패키지 외부 공개
- 패키지명은 쉽고 간단하게
- 모든 문자를 소문자로 할 것을 권장
- 패키지 전역으로 선언된 첫 글자가 대문자로 시작되는 모든 변수, 상수, 타입, 함수, 메서드는 패키지 외부로 공개

# 슬라이스
- 동적 배열 : 자동으로 배열 크기를 증가시키는 자료구조
- 장점
  - 길이가 요소 개수에 따라 자동으로 증가해 관리 편리
  - 슬라이싱 기능으로 배열의 일부를 나타내는 슬라이스를 만들 수 있어 유용

## 슬라이스 선언 및 초기화
```go
  var slice []int

  // 1. {} 로 초기화
  var slice1 = []int{1, 2, 3}
  var slice2 = []int{1, 5:2, 10:3}

  // 2. make()로 초기화
  var slice3 = make([]int, 3)
```
- 배열의 개수 적지 않고 선언
- 초기화 하지 않으면 길이가 0인 슬라이스 만들어짐

### make()를 이용한 선언
```go
  var slice1 = make([]int, 3)     // len : 3, cap : 3 (3개는 0으로 채워짐)
  var slice2 = make([]int, 3, 5)  // len : 3, cap : 5 (나머지 2개는 빈 배열)
```

## append() - 슬라이스 요소 추가
```go
  slice1 = append(slice1, 4, 5, 6, 7)        // 추가만
  appendSlice := append(slice1, 4, 5, 6, 7)  // 복사 + 추가
```

### 슬라이스와 배열 동작 차이
- 배열/슬라이스를 다른 값에 대입 시
  - 배열 : 값 복사
  - 슬라이스 : 포인터 복사(실제 배열 복사 X)

### append() 사용 시 발생하는 문제1
- append() 호출 시
  1. 슬라이스에 값 추가할 수 있는 빈 공간 있는지 확인(cap - len)
  2. (cap - len) > 추가하는 값 개수 : 배열 뒷 부분에 값 추가 후 len 값 증가

```go
  var slice1 = make([]int, 3, 5)
	slice1[1] = 10
	slice1[2] = 20

	slice2 := append(slice1, 30, 40)
	fmt.Println(slice2)

	slice1[1] = 100
	slice1 = append(slice1, 300)

	fmt.Println(slice1)
	fmt.Println(slice2)
```

## 슬라이싱
- 배열의 일부를 집어내는 기능
- ```array[startIdx:endIndex]```
- 시작 인데스 ~ 끝 인덱스 -1 까지의 배열 일부를 나타내는 슬라이스 반환
- 메모리 주소로 가지고 있음

## 슬라이스 복제
```go
  // 1. 복제
  slice1 := []int{1, 2, 3, 4, 5}
  slice2 := make([]int, len(slice1))
  
  for i, v := range slice1 {
    slice2[i] = v
  }

  // 2. append() 이용
  slice2 := append([]int{}, slice1...)

  // 3. copy() 이용
  cnt := copy(slice2, slice1) // slice1을 slice2에 복사, cnt엔 복사된 요소 개수 들어감
```

## 요소 삭제
```go
  slice = append(slice[:idx], slice[idx+1:]...)
```

## 요소 추가
```go
  slice = append(slice[:idx], append([]int{100}, slice[idx:]...)...)
  
  // 개선
  slice = append(slice, 0)
  copy(slice[idx+1:], slice[idx:]) // 복사하는 위치, 복사 대상
  slice[idx] = 100
```

## 슬라이스 정렬
### Int 정렬
```go
  s := []int{5, 2, 4, 7, 8}
  sort.Ints(s)
```

### 구조체 슬라이스 정렬
- ```Len()```, ```Less()```, ```Swap()```
```go
  
type Student struct {
	Name string
	Age  int
}

// []Student의 별칭 타입
type Students []Student

func (s Students) Len() int {
	return len(s)
}

// 나이 비교
func (s Students) Less(i, j int) bool {
	return s[i].Age < s[j].Age
}

func (s Students) Swap(i, j int) {
	s[i], s[j] = s[j], s[i]
}

func main() {
	s := []Student{
		{"xxx", 11}, {"bbb", 22}, {"ddd", 8}, {"ccc", 30},
	}

  // 정렬
	sort.Sort(Students(s))
	fmt.Println(s)
}

```

# 메서드
- 함수의 일종
- Go에는 클래스 x => 구조체 밖에 메서드 지정
- 구조체 밖에 메소드 정의 시, 리시버라는 특별한 기능 사용
- 효과
  - 메서드를 사용해 데이터와 기능을 묶어 응집도를 높임
  - 코드 재사용성을 높임
  - 모듈화로 코드의 가독성이 좋아짐

## 리시버
- 구조체 밖에 메서드가 있어 메서드가 어느 구조체에 속하는 지 표시할 방법 필요
- 메서드가 속하는 타입을 알려주는 기법

## 메서드 선언
```go
func (r Rabbit) info() int { // Rabbit 타입에 속하는 메서드
  return r.width * r.height
}
```
- 리시버가 있으면 메서드, 없으면 일반 함수

## 별칭 리시버 타입
- int와 같은 내장 타입들도 별칭 타입을 활용해 메서드를 가질 수 있음
```go
  // 사용자 정의 별칭 타입
  type customInt int

  // customInt 별칭 타입을 리시버로 갖는 메서드
  func (a customInt) add(b int) int {
    return int(a) + b
  }
```

## 포인터 메서드 vs 값 타입 메서드
- 포인터 메서드를 호출하면 포인터가 가리키고 있는 메모리의 주솟값이 복사됨 -> **인스턴스 중심 메서드**
- 값 타입 메서드를 호출하면 리시버 타입의 모든 값이 복사됨 -> **값 중심 메서드**

# 인터페이스
- 구현을 포함하지 않는 메서드 집합
- 구체화된 타입이 아닌 인터페이스만 가지고 메서드를 호출할 수 있음 => 추후 프로그램 요구사항 변경 시 유연하게 대처 가능
- 효과
  - 객체 간 상호작용 정의 가능
  - 덕 타이핑으로 서비스 사용자 중심의 코딩 가능
  - 인터페이스를 타입 변환해 구체화된 타입으로 변환 가능
- 사용 이유
  - 인터페이스만 가지고 메서드 호출 가능 -> 큰 코드 수정 없이 필요에 따라 구체화된 객체를 바꿔 사용할 수 있게 됨

## 선언
```go
  type InterfaceName interface {
    Fly()
    Walk(distance int) int
  }
```
- 타입명
- 인터페이스 이름
- interface : 인터페이스 선언 키워드
- 메서드 집합
  - 메서드는 반드시 메서드명이 있어야 함
  - **매개변수와 반환이 다르더라도 이름이 같은 메서드는 있을 수 없음**
  - 인터페이스에서는 메서드 구현을 포함하지 않음

## 추상화 계층
- 추상화(abstraction) : 내부 동작을 감춰서 서비스를 제공하는 쪽과 사용하는 쪽 모두에게 자유를 주는 방식
- 인터페이스는 추상화를 제공하는 추상화 계층(abstraction layer)

## 덕 타이핑
- 어떤 타입이 인터페이스를 포함하고 있는지 여부를 결정할 떄 덕 타이핑 방식 사용
- 덕 타이핑 방식 : 타입 선언 시 인터페이스 구현 여부를 명시적으로 나타낼 필요 없이 **인터페이스에 정의한 메서드 포함 여부 만으로 결정하는 방식**

## 서비스 사용자 중심 코딩
- 인터페이스를 패키지를 이용하는 쪽에서 만들게 됨
- 덕 타이핑 -> 서비스 제공자가 인터페이스를 정의할 필요 없이 구체화된 객체만 제공하고 서비스 이용자가 필요에 따라 인터페이스를 정의해서 사용할 수 있음

## 인터페이스의 기능
### 포함된 인터페이스
```go
  type Reader interface {
    Read() (n int, err error)
    Close() error
  }

  type Writer interface {
    Writer() (n int, err error)
    Close() error
  }

  type ReadWriter interface {
    Reader
    Writer
  }
```

### 빈 인터페이스
- ```interface{ }``` : 메서드를 가지고 있지 않은 빈 인터페이스
- 어떤 값이든 받을 수 있는 함수, 메서드, 변수값 만들 떄 사용

### 인터페이스 기본값 nil
- 유효하지 않은 메모리 주소를 나타내는 nil

## 인터페이스 변환하기
### 구체화된 다른 타입으로 타입 변환하기
- 인터페이스 변수를 다른 구체화된 타입으로 변환 가능
- ```newType := interfaceVar.(NewType)```
- 해당 타입이 인터페이스 메서드 집합을 포함하고 있어야 함 (x -> 컴파일ㅇ 에러)

### 다른 인터페이스로 타입 변환하기
- 변경되는 인터페이스가 변경 전 인터페이스를 포함하지 않아도 됨
- 다만, 인터페이스가 가리키고 있는 실제 인스턴스가 변환하고자 하는 다른 인터페이스를 포함해야 함

### 타입 변환 성공 여부 반환
- 타입 변환 반환값을 두 개의 변수로 받으면
- 타입 변환 가능 여부를 두 번째 반환값(bool)로 알려줌
- 타입 반환이 실패해도 false로 반환될 뿐, 런 타임 에러 발생 X
```go
  var a Interface
  t, ok := a.(ConcreteType)

  if c, ok := reader.(Closer); ok {
    ...
  }
```

# 함수 고급편
## 가변 인수 함수
- ```...``` 키워드 이용해 가변 인수 처리 가능
```go
  func sum(nums ...int) int {
    sum := 0

    for _, v := range nums {
      sum += v
    }
    return sum
  }
```

## defer 지연 실행
- 함수 종료 직전
- 파일이나 소켓 핸들 처럼 OS 내부 자원 사용하는 경우
- ```defer 명령문```
- stack. 호출 순 역순

## 함수 타입 변수
```go
  func getOperator(op string) func(int, int) int
```
- ```func(int, int) int```가 return 함수
- ```type opFunc func(int, int) int```처럼 타입을 사용하면 ```func getOperator(op string) opFunc```과 같이 줄일 수 있음

## 함수 리터럴
- 이름 없는 함수
- 함수명 적지 않고 함수 타입 변숫값으로 대입되는 함숫값
- 다른 언어에서는 익명 함수 or Lambda라고도 함
```go
  func getOperator(op string) func(int, int) int {

	if op == "+" {
		return func(a, b int) int {
			return a + b
		}
	} else if op == "*" {
		return func(a, b int) int {
			return a * b
		}
	} else {
		return nil
	}

}
```
```go
  mul := func(a, b int) int {
		return a * b
	}(1,5)      // 직접 호출

  mul(1, 5)   // 함수 호출
```

### 함수 리터럴 내부 상태
- 필요 변수를 내부 상태로 가질 수 있음

# 자료구조
- 여러 데이터를 저장하는 구조

## 리스트
```go
  import "container/list"

  v := list.New()       // 새로운 리스트 생성
  e4 := v.PushBack(4)   // 리스트 뒤에 요소 추가
  e1 := v.PushFront(1)  // 리스트 앞에 요소 추가
  v.InsertBefore(3, e4) // e4 요소 앞에 요소 삽입
  v.InsertAfter(2, e1)  // e1 요소 뒤에 요소 삽입

  for e := v.Front(); e != nil; e = e.Next() {
    ..  
  }

  for e := v.Back(); e != nil; e = e.Prev() {
    ..
  }
```
- 비연속 메모리 사용해 요소 저장
- 요소 삽입과 삭제가 배열 보다 빠름
- Double Linked List

### 포인터로 연결된 요소
```go
  type Element struct {     // 구조체
	Value interface{}       // 데이터 저장 필드
	Next  *Element          // 다음 요소 주소 저장하는 필드
	Prev  *Element          // 이전 요소 주소 저장하는 필드
}
```
- 데이터를 담고 있는 요소들을 포인터로 연결한 자료구조
- 서로 떨어진 Element 인스턴스들이 Next/Prev 포인터로 연결된 불연속 자료구조

### 데이터 지역성 Data Locality
- 데이터가 밀집한 정도
- 필요한 데이터가 인접해 있을 수록 캐시에 의해 처리 속도가 빨라짐
- 삽입 및 삭제가 빈번하면 리스트가 배열 보다 좋다고 함
- But, 요소 수가 적으면? 데이터 지역성 때문에 오히려 배열이 리스트 보다 효율적임

### Queue / Stack
- **Queue** : 보통 리스트로 구현(FIFO)
- **Stack** : 보통 배열로 구현 (LIFO)

## 링
```go
  import "container/ring"

  r := ring.New(5)    // 요소가 5개인 링 생성
  n := r.Len()        // 링 길이 반환

  for i := 0; i < n; i++ {
    r.Value = 'A' + i
    r = r.Next()
  }
```
- 환형 리스트
- 처음과 끝이 연결된 리스트
- 크기 고정

## 맵
- ```map[key]value```
- 키와 값 형태로 자료가 저장되는 자료구조
- 딕셔너리 dictionary, 해시테이블 hash table, 해시맵 hash map 이라고도 함
- container 패키지가 아닌 **Go 기본 내장** 타입(사용 빈도 높음)
```go
  m := make(map[string]string)
	m["key1"] = "value1"
	m["key2"] = "value2"
	m["key3"] = "value3"

	fmt.Printf("%s : %s", "key1", m["key1"])

  delete(m, "key1")
```

### 맵 순회
```go
  for k, v := range m {
		fmt.Println(k, " : ", v)
	}
```
- 입력한 순서 X
- 정렬 X

### 맵, 배열, 리스트 속도 비교
- 맵
  - 삭제, 추가, 읽기에서 요소 개수 상관 없이 속도 일정
  - 빠름
  - 입력한 순서 보장 x
  - 상대적으로 메모리 많이 차지
- 배열
  - 추가, 삭제에서 요소 개수가 많아질 수록 오래 걸림
- 리스트
  - 읽기에서 요소 개수 많아질 수록 오래 걸림

### 해시 함수
- hash : 잘게 부수다
- 해시 함수
  - 같은 입력이 들어오면 같은 결과가 나옴
  - 다른 입력이 들어오면 되도록 다른 결과가 나옴
  - 입력값의 범위는 무한대이고 결과는 특정 범위를 가짐

# 에러 핸들링
- 프로그래미어 실수 혹은 메모리 부족, 디스크 공간 부족, 네트워크 단절 등이 원인
- 효과
  - 함수에서 에러를 반환해 처리 가능
  - 오류를 사전에 빠르게 인지하여 수정
  - 복구 가능한 오류를 복구하여 안정적인 프로그램 만들 수 있음

## 에러 반환
- 에러를 처리하는 가장 기본적인 방식

## 사용자 에러 반환
```go
fmt.Errorf(
  "에러 메시지. 상세 : %s", details
)
```
```go
import "errors"

// func New(text string) error
errors.New("에러 메시지")
```
- 직접 에러를 만들어 반환

## 에러 타입
```go
type error interface {
  Error() string
}
```
- 어떤 타입이든 문자열을 반환하는 Error() 메서드를 포함하고 있으면 에러로 사용할 수 있음

```go
type PasswordError struct {
  Len         int
  RequireLen  int
}

func (err PasswordError) Error() string {
  return "암호 길이가 짧습니다."
}

if errInfo, ok := err.(PasswordError); ok {
  fmt.Printf("%v Len: %d RequireLen: %d\n, errInfo, errInfo.Len, errInfo.RequireLen")
}
```

## 에러 랩핑
- 에러를 감싸서 새로운 에러를 만드는 것
```go
fmt.Errorf("Error: %w", err)

var numError *strconv.NumError
errors.As(err, &numError)
```

## 패닉
```go
panic()
```
- 프로그램을 정상 진행시키기 어려운 상황일 때, 프로그램 흐름을 중지시키는 기능
- 문제 발생 시점에 프로그램을 바로 종료 시켜 빠르게 문제 발생 시점을 알 수 있음
- 버그 수정에 유용
- panic() 내장 함수 호출, 인수로 에러 메시지 입력 -> 프로그램 즉시 종료, 에러 메시지 출력, 함수 호출 순서 콜 스택 표시

### 패닉 생성
```go
// func panic(interface{})
panic(43)
panic("unreachable")
panic(fmt.Errorf("This is error num:%d", num))
```

### 패닉 전파 그리고 복구
- panic은 호출 순서를 거슬러 올라가며 전파됨
- ```recover()``` : 복구
  - 함수 호출 시점에 패닉이 전파 중이면 panic 객체 반환하고 그렇지 않으면 nil 반환

# 고루틴 goroutine
- Go 언어에서 관리하는 경량 스레드
- 함수나 명령을 동시에 수행 시 사용
- 동시성 프로그래밍(Concurrent Programming) : 여러 고루틴을 갖는 프로그램
- 효과
  - 여러 작업을 동시에 수행 가능
  - Go 언어에 내장된 기능으로 외부 라이브러리에 의존하지 않고 동시성 프로그램 구현 가능
  - 멀티코어 환경에서 CPU를 더 효율적으로 사용해 빠르게 작업을 완료할 수 있음
  - 기존 OS 스레드에서 발생되는 컨텍스트 스위칭에 따른 성능 손실을 최소화해서 효율적으로 사용
  - 고루틴 간 메모리 간섭으로 인해 발생하는 문제점에 주의

## 스레드
- 실행 흐름
- 싱글 태스킹 : 한 번에 한 프로세스만 동작시키는 것
- 멀티 태스킹 : 여러 프로세스를 동시에 동작시키는 것
- 하나의 CPU 코어는 하나의 명령만 수행 가능
- But, CPU 코어가 스레드를 빠르게 전환해가면서 수행하여 여러 스레드를 수행하는 것 처럼 보이게 하는 것

## 컨텍스트 스위칭
- 컨텍스트 스위칭 비용 : CPU 코어가 여러 스레드를 전환하며 수행하면서 발생하는 비용
- 스레드 컨텍스트 (thread context) : 스레드 전환 시 현재 상태 보관(명령 포인터, 스택 메모리 등)
- 적정 개수를 넘어 여러 스레드를 수행하면 성능이 저하됨(보통 코어 개수의 두 배 이상)
- But, __Go 언어에서는 CPU 코어 마다 OS 스레드 하나만 할당해서 사용하여 컨텍스트 스위칭 발생 X__

## 고루틴 사용
```go
go 함수1_호출
go 함수2_호출
```
- 모든 프로그램은 고루틴을 최소 하나 가지고 있음 (main 루틴)
- go 키워드와 함께 함수를 호출하면 해당 함수를 수행하는 새로운 고루틴 생성
- main 함수가 종료되면 다른 서브 로구린들을 모두 즉시 종료되고 프로그램이 종료됨

### 서브 고루틴이 종료될 때 까지 기다리기
```go
import "sync"

var wg sync.WaitGroup

wg.Add(3)   // 작업 개수 설정
wg.Done()   // 작업 완료될 때 마다 호출
wg.Wait()   // 모든 작업이 완료될 때 까지 대시
```

## 고루틴 동작 방법
- 고루틴은 명령을 수행하는 단일 흐름
- OS 스레드를 이용하는 경량 스레드(lightweight thread)

### 고루틴이 하나일 때
- [코어1] - [OS스레드1] - [고루틴1]

### 고루틴이 두 개일 때
- [코어1] - [OS스레드1] - [고루틴1]
- [코어2] - [OS스레드2] - [고루틴2]

### 고루틴이 세 개일 때
- [코어1] - [OS스레드1] - [고루틴1]
- [코어2] - [OS스레드2] - [고루틴2]
- 대기 중 : [고루틴3]

### 시스템 콜 호출 시
- 시스템 콜 : 운영체제가 지원하는 서비스 호출할 때(네트워크 기능 등)
- 시스템 콜 호출 : 운영체제에서 해당 서비스가 완료될 때 까지 대기해야 함
- 대기 상태인 고루틴에 CPU 코어와 OS 스레드를 할당하면 CPU 자원 낭비 발생
- Go 언어에서는 **이런 상태에 들어간 루틴을 대기 상태로 보내고, 실행을 기다리는 다른 루틴에 CPU 코어와 OS 스레드 할당해 실행함**
- 장점
  - 컨텍스트 스위칭 비용 발생 X(코어의 스레드 변경 X, 고루틴만 옮겨다님)

## 동시성 프로그래밍 주의점
- 동일한 메모리 자원에 여러 고루틴 접근 시
  - 뮤텍스**mutex**(상호배제)를 이용한 동시성 문제 해결
    - 고루틴에서 값을 변경할 때 다른 고루틴이 건들지 못하게 함
    - 뮤텍스의 ```Lock()``` 메서드를 호출해 뮤텍스 획득, ```Unlock()```으로 반납
    - Lock()을 가지지 못한 고루틴은 뮤텍스가 반납될 때 까지 대기
      ```go
        import "sync"

        var mutex sync.Mutex

        mutex.Lock()          // 뮤텍스 획득
        defer mutex.Unlock()  // defer를 사용한 Unlock
      ```
    - 문제점
      - 동시성 프로그래밍으로 얻는 성능 향상 이점 X
      - **데드락** 발생 가능

### 뮤텍스와 데드락
- 데드락 : 무한 대기로 인한 프로그램을 완전히 멈추게 만듦
- 뮤텍스를 서로 획득하지 못하는 상황에 발생
- 뮤텍스가 꼬이지 않도록 좁은 범위에서 데드락 걸리지 않는지 철저히 확인해서 사용하면 유용함
- Google Cloud Profiler 같은 프로파일링 툴을 이용해 뮤텍스 검사 가능

### 또 다른 자원 관리 방법
- 각 고루틴이 같은 자원에 접근하지 않으면 애당초 문제 발생 X
- **영역을 나누는 방법**
  - 각 고루틴이 할당된 작업만 하도록 함
  - ex. 100개의 파일을 읽어서 분석할 땐 파일 별로 고루틴을 할당해서 수행하기
- **역할을 나누는 방법**
  - 채널 이용

# 채널 & 컨텍스트
- Go 언어에서 동시성 프로그래밍을 도와주는 기능
- **채널** : 고루틴 간 메시지를 전달하는 메시지 큐
  - 뮤텍스 없이 동시성 프로그래밍 가능
- **컨텍스트** : 고루틴에 작업 요청 시 작업 취소나 작업 시간 등을 설정할 수 있는 작업 명세서 역할
  - 작업 요청 시 작업 시간, 취소, 추가 데이터 등을 지정할 수 있음

## 채널
- 메시지 큐의 메시지들은 차례대로 쌓이고 메시지를 읽을 땐 처음 들어온 메시지부터 차례대로 읽힘(FIFO)
- **크기**
  - default : 0
  - 채널에 들어온 데이터를 담아둘 곳이 없음
  - 채널에 데이터를 넣었지만 빼가지 않으면 데이터가 빠지기 까지 대기함
- **버퍼를 가진 채널**
    ```go
      var chan string message = make(chan string, 2)
    ```
    - 버퍼가 2인 채널 생성
    - 2개 까지의 데이터 보관 가능

### 채널 인스턴스 생성
```go
  var messages chan string = make(chan string)
```
- make() 함수로 생성
- 채널 타입 : chan 키워드 + 메시지 타입 

### 채널에 데이터 넣기
```go
   messages <- "This is a message"
```
- ```<-``` 연산자 이용

### 채널에서 데이터 빼기
```go
  var msg string = <- messages
```
- ```<-``` 연산자 이용

### 채널에서 데이터 대기
- 채널에 데이터가 없어서 무한 대기 중인 경우 데드록 발생
- 채널에 있는 데이터를 모두 사용한 다음에는 ```close(ch)```로 채널을 닫고 채널이 닫혔음을 알려주어야 함

### select문
```go
  select {
    case n := <- ch1 :    // ch1에서 데이터를 빼낼 수 있을 때 실행
      ...
    case n2 := <- ch2 :   // ch2에서 데이터를 빼낼 수 있을 때 실행
      ...
    case ...
  }
```
- 채널에 데이터가 없는 경우 다른 작업을 하거나 여러 채널을 동시에 대기하고 싶을 때 사용

### 일정 간격으로 실행
```go
func square(wg *sync.WaitGroup, ch chan int) {
  // 일정 시간 간격 주기로 신호를 보내주는 채널 생성하여 반환하는 함수
  tick = time.Tick(time.Second)
  // 현재 시간 이후 일정 시간 경과 후 신호를 보내주는 채널 생성해 반환하는 함수 
  terminate := time.After(10 * time.Second)

  for {
    select {
      case <- tick:
        fmt.Println("Tick")
      case <- terminate:
        fmt.Println("Terminated!")
        wg.Done()
        return
      case n := <- ch:
        fmt.Printf("Square: %d\n", n*n)
        time.Sleep(time.Second)
    }
  }
}

```

## 채널로 생성자 소비자 패턴(Producer consumer Pattern)구현
```go
import (
	"fmt"
	"sync"
	"time"
)

type Car struct {
	Body  string
	Tire  string
	Color string
}

var wg sync.WaitGroup
var startTime = time.Now()

func main() {
	tireCh := make(chan *Car)
	paintCh := make(chan *Car)

	fmt.Println("Start Factory")

	wg.Add(3)
	go MakeBody(tireCh)
	go InstallTire(tireCh, paintCh)
	go PaintCar(paintCh)

	wg.Wait()
	fmt.Println("Close the factory")

}

func MakeBody(tireCh chan *Car) {
	tick := time.Tick(time.Second)
	after := time.After(10 * time.Second)

	for {
		select {
		case <-tick:
			car := &Car{}
			car.Body = "Sports car"
			tireCh <- car
		case <-after:
			close(tireCh)
			wg.Done()
			return
		}

	}
}

func InstallTire(tireCh, paintCh chan *Car) {
	for car := range tireCh {
		time.Sleep(time.Second)
		car.Tire = "Winter tire"
		paintCh <- car
	}
	wg.Done()
	close(paintCh)
}

func PaintCar(paintCh chan *Car) {
	for car := range paintCh {
		time.Sleep(time.Second)
		car.Color = "Red"
		duration := time.Now().Sub(startTime)
		fmt.Printf("%.2f Complete Car: %s %s %s\n", duration.Seconds(), car.Body, car.Tire, car.Color)
	}
	wg.Done()
}
```
- 한 쪽에서 데이터를 생성해서 넣어주면 다른 쪽에서 생성된 데이터를 빼서 사용하는 방식

## 컨텍스트 
- context 패키지에서 제공하는 기능
- 작업을 지시할 때 작업 가능 시간, 작업 취소 등의 조건을 지시할 수 있는 작업 명세서 역할
- 새로운 고루틴으로 작업을 시작할 때 일정 시간 동안만 작업을 지시하거나 외부에서 작업을 취소할 때 사용
- 작업 설정에 관한 데이터 전달도 가능

### 작업 취소가 가능한 컨텍스트
```go
func main() {

	wg.Add(1)
	ctx, cancel := context.WithCancel(context.Background()) // 컨텍스트 생성
	go PrintEverySecond(ctx)
	time.Sleep(5 * time.Second)
	cancel() // 취소

	wg.Wait()
}

func PrintEverySecond(ctx context.Context) {
	tick := time.Tick(time.Second)
	for {
		select {
		case <-ctx.Done():
			wg.Done()
			return
		case <-tick:
			fmt.Println("Tick")
		}
	}
}
```

### 작업 시간을 설정한 컨텍스트
```go
  ctx, cancel := context.WithTimeout(context.Background(), 3 * time.Second)
```

### 특정 값을 설정한 컨텍스트
```go
  ctx, cancel := context.WithValue(context.Background(), "key", "value")

  ctx.Value("key")
```

### 컨텍스트 안에 컨텍스트
```go
  ctx, cancel := context.WithCancel(context.Background()) // 취소 기능이 있는 컨텍스트
  ctx = context.WithValue(ctx, "key", "value")  // 값을 설정한 컨텍스트로 기존 컨텍스트를 감싼다
```