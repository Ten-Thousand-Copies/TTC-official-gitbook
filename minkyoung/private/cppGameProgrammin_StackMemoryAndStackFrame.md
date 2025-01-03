# 스택메모리와 스택프레임

## Stack
**스택 영역**
- 함수의 호출과 관계있는 변수가 저장되는 영역  
- 스택 프레임에서 높은 주소에서 낮은 주소로 스택이 저장됨  
- [매개변수 -> 반환주소값 -> 지역변수] 순으로 함수에 대한 변수를 저장  

**32bit와 64bit의 차이**
- 레지스터 크기 차이  
- CPU 안에 있는 레지스터  
- 레지스터 사이즈가 클수록 가져올 수 있는 주소 사이즈가 큼  

**메모리 최적화**
- 힙에서 저장하는 영역에 대하여 최적화함을 의미  

**스택 오버플로우란**
- 함수에서 사용하는 변수들을 저장하는 스택 영역에서 지정한 사이즈의 영역보다 더 크게 메모리를 사용할 경우 발생하는 에러  

## 메모리 구조

<img src="/minkyoung/src/memory01.png">

- stack : 함수 호출시 변수를 저장영역 (매개변수, 반환주소, 지역변수)
- heap : 동적 메모리 할당 영역
- bss segment : 초기화되지않은 전역변수, 정적변수 저장영역
- data segment : 초기화된 전역변수, 정적변수 저장영역
- text(code) segment : 코드와 상수 데이터 저장영역

## 공부하다 궁금해진 것
**Q1**. 선언되지 않은 상수(문자열 리터럴)는 어디에 저장될까

``` C#
int main() {
	cout << "Hello World!";
}
```

**A1**. 
위 함수가 작동되는 방식을 설명해보자  
 int main함수가 실행되면 "Hello World!"  라는 문자리터럴이 data segment 영역에 로드가 되고,  
비트 이동 연산자 << 의해  cout 이라는 출력 스트림으로 전달이 되며 출력이 됩니다.

---
**Q2**. 함수의 매개변수로 string이있으면 string이라는 변수타입은 힙에 저장되어야하고 함수의 매개변수는 stack에 저장되어야하는데 이 경우에는 어느 저장 영역이 맞는가?

``` C#
void PrintMessage(string message) {
    cout << message;
}

string myString = "Hello, World!";
PrintMessage(myString);
```
**A2**. 매개변수와 데이터가 구분되어서 저장됩니다!
매개변수 (데이터를 가리키는 참조) message 는 stack에 저장되고
 myString 변수는 stack에 저장되고 "Hello, World!" 라는 문자열은 힙에 저장되고 해당 주소를 myString이 참조합니다.