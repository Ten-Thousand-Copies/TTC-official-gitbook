## 구현할 것
1. Generator - 프롭 랜덤 생성기
2. 카메라 추적
3. 게임 메니저 생성
## Generator
### Game Object
- 우선 empty object를 생성한다.
- Box collider 컴포넌트를 추가한다.
	- 이는 충돌 처리를 위함이 아니라 프롭이 생성되는 범위를 표시하기 위함
	- 크기는 x, z로 각각 50
### Script
- 루틴은 다음과 같다.
	- 게임 오브젝트 위치를 중심으로, 위 아래 50씩 공간을 잡는다.
	- 그 공간에 프롭 100개를 랜덤하게 Spawn한다.
		- 작은 것과 큰 것중에 랜덤으로 하나를 고른다.
		- 선택된 프롭에 대한 위치를 설정한다.
		- `instantiate`를 통해 생성한다.
	- 생성된 프롭들은 포탄에 의해 파괴 되었을 때, enable이 false가 된다.
	- 때문에 다음 라운드에서는 true로 변경하기만 하면 된다.

## Camera
### Object
- Main camera 오브젝트에 보면, Camera 컴포넌트가 있다.
	- Camara 컴포넌트에 Projection 설정이 있는데, 이는 두가지 옵션으로 나뉜다.
		- Perspective - 소실점이 있는 카메라
		- Orthographic - 소실점이 없는 카메라
	- Orthographic으로 설정
- 카메라가 포탄과의 일정한 거리를 두면서 추적해야 한다.
	- 때문에, Rig이라는 empty object를 생성한다.
	- 그리고 Rig의 자식으로 Main camera를 둔다.
### Script
- 카메라의 상태는 3가지로 나뉜다.
	- 라운드가 시작될 때
	- 포탄이 발사되기 전
	- 포탄을 추적할 때
- 카메라에 대한 스크립트는 움직임(Move)과 주밍(Zoom)만 설정하고, 실제 조작은 게임메니저가 진행
	- 타겟이 사라질 때까지 Move, Zoom한다.
	- Move, Zoom 모두 `SmoothDump`를 이용해서 부드러운 움직임이 되도록 한다.
## UI
### Object
- Canvas를 생성
	- Canvas에는 Canvas Scaler라는 컴포넌트가 있다.
	- 여기에 UI scale mode라는 설정이 있다.
		- Constant pixel size는 해상도와 관계없이 UI의 크기가 고정된다.
		- Scale with screen size는 해상도에 따라 UI의 크기가 변경된다.
- Canvas의 자식으로 Panel을 생성한다.
- 그리고 Panel에 두 개의 텍스트를 생성한다.
	- 두 개의 텍스트를 정렬하기 위해 Horizontal Layout Group 컴포넌트를 추가한다.
- Canvas의 또 다른 자식으로 Panel을 생성한다.
	- Ready 라는 텍스트를 출력하기 위한 Panel
## Game Manager
### Round
- 라운드(페이지)에는 3가지 상황이 있다.
	1. Ready
		- 카메라, 포신, 점수, UI 등을 리셋한다.
		- 대기시간을 가진다.
	2. Play
		- 포탄을 발사한다.
		- 포탄이 폭발될 때까지 무한 루프
	3. End
		- 포신을 비롯한 조작이 가능한 오브젝트에 대해서 조작이 불가능하게 한다.
		- 리셋한 뒤에 Ready로 넘어간다.
		- 대기시간을 가진다.
### Script
- 게임 메니저는 단 하나여야 하기 때문에 싱글톤 패턴을 사용한다.
- 라운드에 대한 조작은 코루틴을 사용한다.

## Singleton pattern
- 객체에 대한 인스턴스가 하나만 생성하도록 하는 디자인 패턴
- 인스턴스가 필요할 때, 생성하지 않고 기존의 인스턴스를 가져와 활용하는 것
	- 유사한 예시로는 전역 변수
- 특징
	- 객체에 대한 인스턴스가 단 하나
	- 외부에서 생성하지 못하도록 하기 위해, 생성자가 `private`이다.
	- 전역적으로 진입할 수 있는 `static` 인스턴스를 하나 가진다.
```C++
class Singleton
{
private:
	static Singleton *instance;
	...

	Singleton();
public:
	...
}
```
## Co-routine
- 실행을 일시 중지하고 재개할 수 있는 함수
	- 그렇다고 메인 게임 루프를 차단하지 않는다.
	- 즉, 특정 타이밍이 될 때까지 코루틴 함수가 기다리는 것이지, 게임 루프가 `wait`되는 것은 아니다.
- `IEnumerator`를 반환한다.
	- 반환할 때에는 `yield`키워드를 사용한다.
		- `yield return null` : 다음 프레임까지 대기
		- `yield return new WaitForSeconds()` : 지정된 시간만큼 대기
		- `yield return new WaitForFixedUpdate()` : 다음 물리 업데이트까지 대기
		- `yield return new WaitUntil()` : 특정 조건이 충족될 때까지 대기