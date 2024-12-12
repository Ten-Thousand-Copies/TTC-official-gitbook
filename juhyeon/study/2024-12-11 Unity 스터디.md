
## 개요
- 이전까지 "Amazing bowling"이라는 포트리스 형태의 게임을 제작 중이었다.
- 포신을 만들고, 프리팹을 통해 포탄과 몹(상자)을 제작했다.
- 이번에 만들 것은 포신 UI(파워 슬라이드)이다.
## 구현해야 할 것
- 포신이 "Fire"키를 입력받으면 다음과 같이 행동한다.
	1. 첫번째 Fire키는 y축을 기준으로 회전한다.
	2. 두번째 Fire키는 x축을 기준으로 회전한다.
	3. 세번째 Fire키는 포탄을 얼마나 멀리 쏠지 결정한다.
- 세번째 Fire키를 입력할 때, 파워 슬라이드가 표시되도록 구현한다.
	- 필요한 것은 Slider UI와 이를 조작하기 위한 코드들이다.
## Slider
- 생성할 수 있는 UI 요소 중 하나
- 구조는 "Canvas > Slider > Background / Handle slide area / Fill Area > Fill"
	- 구현에는 Background와 Handle slide area가 필요없으므로 삭제
- EventSystem도 형성되는데, EventSystem은 유저의 입력을 받기 위한 요소
	- 구현에는 필요 없기 때문에 삭제
### Canvas
- Render mode : canvas가 랜더링되는 방식을 세팅한다.
	- Default로 Screen space - Overlay로 되어 있어, 게임 세상(World space)와 상관없이 표시된다.
	- World space로 변경하면, 게임 세상의 좌표에 종속되어서 랜더링된다.
- Reference pixel per unit : Pixel per unit과 매칭되는 값
	- UI가 Scene에서 얼만큼의 크기로 표시될 지를 결정한다.
	- 기본값은 100이다.
### Slider
- Canvas의 자식으로 되어 있다.
- Trnasition : 유저의 입력에 따라 어떻게 반응하는지 결정한다.
	- Default로 Color tint라고 되어 있다.
	- 하지만, 필요없기 때문에 None으로 설정한다.
- Direction : 슬라이더의 방향을 결정한다.
- Min value / Max value : 슬라이더의 최소값과 최대값을 설정한다.
	- 여기에서는 강의에 따라 15, 30으로 설정했다.
- Value : 슬라이더가 표시하고 있는 실제 값
	- 이는 코드 상에서 조작할 수 있다.
### 구현 과정
- "Canvas > Slider > Fill area"에서 Anchor presets을 클릭한다.
- Alt키를 입력한채, stretch 옵션을 선택해서 Fill area가 Slider에 꽉 차도록 한다.
- Scene 창에서 Rect tool을 이용해 크기와 위치를 조절한다.
- Fill에서 색을 적당히 조절하면 파워 슬라이더에 대한 준비가 완료된다.
## 포탄 발사
- 포신 오브젝트의 구조는 다음과 같다.
	- Shooter pivot은 Empty object로, 포신의 중심점 역할을 한다.
	- Ball berral model은 실제 우리에게 보이는 포신 모델이다.
	- Fire position은 Empty object로, 포탄이 발사될 위치이다.
	- Canvas는 방금 제작했던 파워 슬라이더이다.
- Shooter pivot에 Audio source 컴포넌트를 추가한다.
	- 이는 파워 슬라이드의 소리와 포신의 소리를 재생하기 위함이다.
> Audio Cilp vs Audio Source
> - Audio Clip이 카세트 테이프라면, Audio Source는 워크맨이다.
- 객체는 포신의 회전을 위한 "ShooterRotater"와 포탄 발사를 위한 "BallShooter"
	- BallShooter
		- Fire 버튼을 입력했을 때, 입력 중일 때, 입력을 종료한 후로 나뉜다.
			- Fire 버튼을 입력했을 때, 최소값 `_minForce`부터 시작하도록 한다.
				- 오디오는 `_sliderSound`가 나도록 한다.
			- Fire 버튼을 입력 중일 때에는 `_currentForce`를 계속 업데이트 한다.
				- UI로도 계속 업데이트 해준다.
			- Fire 버튼 입력을 종료하면 발사한다.
		- 발사가 한 번만 되어야 하기 때문에, 발사가 되었는지에 대한 플래그 `_isFired`를 계속 확인한다.
		- 만일 발사가 되었다면, `return`을 통해 발사를 중지한다.
- [Github](https://github.com/juhyeonlee134/Unity-Study)