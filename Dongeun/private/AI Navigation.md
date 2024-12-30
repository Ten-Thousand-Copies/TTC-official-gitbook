2022 LTS 버전 이후부터 기존의 Built-in 방식에서 패키지 형태로 변경됨. -> AI Navigation 패키지를 설치해야 사용가능
![[Pasted image 20241230204417.png]]

## NavMesh Surface
- 오브젝트에서 Add component를 통해서 추가 (Bake 기능도 컴포넌트로 변경됨.)
- NavMesh의 생성의 기준
	(컴포넌트 단위)
  1. Render Meshed : 
     게임 오브젝트의 실제 보이는 3D 모델을 기준으로 NavMesh를 생성
     Mesh Renderer 컴포넌트가 있는 오브젝트의 형태를 따라 네비게이션 영역이 만들어진다
     시각적으로 보이는 형태와 정확히 일치하는 네비게이션 영역이 필요할 때 사용
  2.  3D Physics Colliders :
     게임 오브젝트의 Collider 컴포넌트를 기준으로 NavMesh를 생성
     Collider는 보통 단순화된 충돌 영역을 가지므로, 더 간단하고 최적화된 NavMesh가 생성
     실제 물리적 상호작용이 일어나는 영역을 기준으로 네비게이션이 이루어짐
	
	(오브젝트 단위 -> NavMesh 포함 여부)
1. All GameObject
    - 씬의 모든 게임 오브젝트를 대상으로 NavMesh를 생성
    - 선택한 기준(Render Meshes/Physics Colliders)을 가진 모든 오브젝트가 포함
2. Volume
    - NavMeshComponents가 있는 특정 영역(육면체) 내부의 오브젝트만 대상
    - Volume 안에 있는 오브젝트들 중 선택한 기준을 가진 것들만 포함
3. Current Object Hierarchy
    - 현재 선택된 오브젝트와 그 자식 오브젝트들만 대상
    - 선택된 계층구조 내에서 기준을 만족하는 오브젝트들만 포함
4. NavMeshModifier Component Only
    - NavMeshModifier 컴포넌트가 붙어있는 오브젝트만 대상
    - 이 컴포넌트가 있는 오브젝트들 중 선택한 기준을 만족하는 것만 포함


## NavMesh Agent 
### 설정
- NavMesh Agent는 NavMesh 위에서만 작동하므로, 항상 NavMesh 위에 배치해야 함.
- 오브젝트에 NavMesh Agent 컴포넌트를 추가한 뒤, 반지름과 높이를 오브젝트에 맞게 조절해야 함.
- 속도, 회전속도, 그리고 속력 등의 속성은 사용자가 원하는대로 설정할 수 있음.


### 목적지 지정 방식
- 에이전트는 설정한 NavMesh 영역 안에서만 안전하게 움직일 수 있으며, 목표에 도달했을 때는 조건문을 사용해 이벤트를 처리할 수 있다.
1. 오브젝트로 목적지를 지정하는 일반적인 방식으로, SetDestination 함수로 목표를 설정하면 AI가 자동으로 경로를 찾아감.
   ```c#
using UnityEngine;

public class SimpleStaticAgent : BaseAgent
{
	[SerializeField]
    Transform target;

	void Start()
	{
		agent.SetDestination(target.position);
	}
}
```
2. 마우스 클릭 위치를 통해 목표를 지정하는 것으로, RayCasting과 RayHit를 사용하여 정확한 위치에 SetDestination을 전송.
   ```c#
using UnityEngine;

public class PointerAgent : BaseAgent
{
	RaycastHit rayHit = new RaycastHit();
	
	void Update()
	{
		if (Input.GetMouseButtonDown(0))
		{
			Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);
			if (Physics.Raycast(ray.origin, ray.direction, out rayHit))
				agent.destination = rayHit.point;
		}
	}
}
```
3. 컨트롤러 방향키를 입력 받아 이동하는 방식으로, 이때 Stopping Distance를 0으로 설정하여 정확한 이동을 보장하는 것이 좋다.
      ```c#
using UnityEngine;
using UnityEngine.InputSystem;

public class InputAgent : BaseAgent
{
	Transform cam;
	Vector2 inputVec;
	
	void Start()
	{
		cam = Camera.main.transform;
	}
	
	void Update()
	{
		Vector3 dirVec = cam.right * inputVec.X + cam.forward * inputVec.y;
		agent.SetDestination(transform.position + dirVec.nomalized);
	}
	
	void OnMove(InputValue value)
	{
		inputVec = value.Get<Vector2>();
	}
}
```
- 목적지 도달 후 액션 설정하는 법
  유니티 내에 해당 이벤트는 존재 하지 않음. 따라서 직접 만들어야 함.
  Update에서 if문을 통해 PathComplete라는 상태와 목적지까지 남은 거리인 remainingDistance를 통해 목적지에 도달했다는 조건을 줄 수 있음.
  ```c#

	void Update()
	{
		if (agent.pathStatus == NavMeshPathStatus.PathComplete
		&& agent.remainingDistance - agent.stoppingDistance < 0.1f)
		{ onComplete.Invoke(); }
	}

```


## NavMesh Link
- 에이전트가 끊어진 구간을 연결하여 빠르게 이동할 수 있도록 돕는 시스템
- NavMesh Surface에서 Generate Links를 설정하고, Bake를 진행하면 자동으로 NavMesh Link가 생성됨.
  ![[Pasted image 20241230213015.png]]
- NavMesh Link컴포넌트를 통해 수동 생성 가능, 시작 점과 끝 점에 생성된 원을 기준으로 연결됨.
  (기본 값은 선으로 다수의 에이전트가 사용시 대기 필요. width 속성으로 넓이를 주고 해결.)
  ![[Pasted image 20241230213242.png]]
- Agent Link Mover 컴포넌트를 활용으로 해당 포인트에서 점프 애니메이션과 함께 자연스러운 이동이 가능하게 함.


## Area Type
- 모든 NavMesh는 AreaType을 지정해야 한다. 
  ![[Pasted image 20241230213803.png]]
- AreaType에는 cost가 존재하며, 목적지까지 가는 경로 중 더 적은 비용의 경로를 선택하게 한다.
  (Chick: 작은 캐릭터만 지나갈 수 있는 특수한 영역)


## NavMesh Obstacle
- 장애물을 추가할 수 있음.
- Shape은 Box와 Capsule 두 가지가 존재한다.
- 이 컴포넌트를 만나면 에이전트는 순회해서 지나감.
- Carve 속성:  장애물이 있는 영역 인식 방법
  - ture: 
    해당 장애물이 위치한 NavMesh를 제거, 동적으로 제거되므로 성능에 영향을 준다.
    영구적인 장애물, 장애물이 너무 길거나 여러 오브젝트가 일렬로 나열되어 장애물로 존재하는 경우
  - false: 
    NavMesh를 수정하지 않고 에이전트의 경로에만 영향을 준다. 일시적인 장애물일 때 사용.


## NavMesh Modifier
- NavMesh를 더 구체적이고 세밀하게 조정할 수 있음.(NavMesh Surface의 NavMesh 생성 기준에 NavMesh Modifier가 포함되어 있어, 기준에 따라 NavMesh를 형성할 수 있음)
- Remove 속성을 사용하면 생성 기준에서 제외할 수 있으며, Override Area 속성을 활성화하여 다른 Area Type으로 변경할 수 있다.(예: Walkable -> Jump)