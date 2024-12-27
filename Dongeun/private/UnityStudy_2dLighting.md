# Unity Study \_ 2D 라이팅 시스템
## Render Pipeline 비교

### Built-in vs Universal Render Pipeline (URP)

#### Built-in Render Pipeline

- Unity의 전통적인 기본 렌더링 시스템
- 간단한 프로젝트에 적합
- 그래픽 설정 변경이 제한적
- 별도의 설정 없이 바로 사용 가능

#### Universal Render Pipeline (URP)

- 현대적이고 최적화된 렌더링 시스템
- 다양한 플랫폼 지원 (모바일, PC, 콘솔 등)
- 그래픽 설정의 세밀한 제어 가능
- 초기 설정이 필요하지만 더 나은 성능 제공

### URP의 주요 특징

#### 그래픽스 설정

- **위치**: Edit > Project Settings > Graphics
- **Default Render Pipeline Asset**: URP 설정의 중심
- **Renderer2D**: 2D 그래픽스 전용 설정 제공

#### 주요 기능

1. **씬 템플릿**

   - File > New Scene에서 URP 전용 템플릿 제공
   - 2D 카메라 기본 설정 포함
   - 기초 조명 시스템 내장

2. **Global Light 2D**

   - URP 2D 프로젝트의 특징적 요소
   - 전체 장면의 조명 제어
   - 세밀한 라이팅 설정 가능

3. **성능 최적화**
   - 다양한 플랫폼에 최적화된 그래픽 설정
   - 더 효율적인 렌더링 파이프라인
   - 배치 처리 개선

### 선택 가이드

#### Built-in 선택 시기

- 간단한 프로젝트 개발 시
- 빠른 프로토타입 필요 시
- 그래픽 성능이 중요하지 않을 때

#### URP 선택 시기

- 크로스 플랫폼 개발 시
- 고품질 그래픽 필요 시
- 성능 최적화가 중요할 때
- 2D 게임 개발 시 (특히 라이팅 중요)

### 전환 시 주의사항

- 프로젝트 중간에 파이프라인 변경은 권장하지 않음
- 에셋 호환성 확인 필요
- 쉐이더 변환 작업 필요
- 라이팅 시스템 재설정 필요

## 2D 라이팅 시스템

### Global Light 2D 이해하기

#### 기본 개념

- Global Light 2D는 Unity 2D 프로젝트의 기본 조명 시스템
- 전체 게임 장면의 기본 밝기와 분위기를 결정

#### Light Type 종류

1. **Global**:

   - 전체 장면에 영향을 주는 기본 조명
   - 게임의 전반적인 분위기 설정에 사용

2. **Freedom**:

   - 자유롭게 정점을 편집하여 원하는 형태의 조명 생성
   - 불규칙한 형태의 조명 효과에 적합

3. **Sprite**:

   - 스프라이트 이미지 형태를 그대로 조명으로 사용
   - 특정 이미지 모양의 조명 효과 필요할 때 사용

4. **Spot**:
   - 원형이나 호 형태의 조명 생성
   - 가로등, 손전등 같은 효과에 적합

#### 주요 설정

- **Color**: 조명의 색상 설정 (전체적인 분위기 결정)
- **Intensity**: 조명의 밝기 조절 (0~1 사이 값, 1이 가장 밝음)

### 성능 최적화

#### Light Pass 이해

- Render Graph Viewer를 통해 확인 가능 (Windows > Analysis > Render Graph Viewer)
- 불필요한 Light Pass는 성능에 부담

#### 최적화 방법

1. 기본 Global Light 2D (Color: White, Intensity: 1)가 필요 없는 경우 제거
2. Light Pass 제거로 렌더링 단계 감소
3. 저사양 디바이스에서 특히 효과적

### 2D 그래픽 정렬 시스템

#### 정렬 기준 (우선순위 순)

1. **Transform-Z**

   - 카메라와의 거리 기준
   - 카메라에 가까울수록 나중에 그려짐

2. **Sorting Layer**

   - 전용 레이어 시스템
   - Project Settings > Tags and Layers에서 관리
   - 순위가 높을수록 나중에 그려짐

3. **Order in Layer**
   - 같은 Sorting Layer 내에서의 순서
   - 높은 숫자가 나중에 그려짐

#### 레이어 관리 팁

- 배경, 캐릭터, UI 등 용도별 레이어 구분 권장
- 새 레이어 추가 후 에디터 재시작 필요

### 고급 라이팅 테크닉

#### 부분 라이팅

- Target Sorting Layer로 특정 레이어만 조명 적용 가능
- Everything: 모든 레이어에 적용
- Nothing: 모든 조명 효과 제거

#### 다중 Global Light 2D 활용

- 레이어별로 다른 조명 효과 적용 가능
- 예: 배경은 어둡게, 캐릭터는 밝게

#### 주의사항

- 동일한 Target Sorting Layer에 여러 Global Light 2D 설정 시 에러 발생
- 레이어별로 하나의 Global Light 2D만 사용해야 함

## 픽셀 조명 제작

### Spot Light 2D 기본 설정

- Hierarchy > Light > Spot Light 2D로 추가
- **주요 속성**:
  - Color: 조명 색상
  - Intensity: 밝기
  - Radius Outer: 조명 영역 크기
  - Inner/Outer Spot Angle: 조명 경계 선명도
  - Falloff Strength: 조명 감쇠 강도
- Scene 뷰에서 직관적인 모양 편집 가능
- 노란색 커서와 삼각형으로 스포트라이트 형태 조절 가능

### 픽셀 라이트 구현 방법

1. **Pixel Perfect Camera 활용**

   - Main Camera에 Pixel Perfect Camera 컴포넌트 추가
   - Pixels Per Unit: 스프라이트 에셋과 동일한 값 설정
   - Reference Resolution: 적절한 화면 크기 지정
   - Grid Snapping + Upscale Render Texture: 완벽한 픽셀화
   - **주의사항**:
     - Game 창 해상도는 짝수로 설정
     - 고정 카메라 사용 권장 (이동 시 어지러움 유발 가능)

2. **Sprite Light 2D 활용**
   - Hierarchy > Light > Sprite Light 2D로 추가
   - 미리 제작된 스프라이트 에셋 활용
   - **장점**: 구현이 간단
   - **단점**: 다양한 형태 필요 시 많은 에셋 필요
   - **추가 기능**:
     - Visual Scripting으로 동적 효과 추가 가능
     - Script Machine 컴포넌트 추가
     - Scale Wave 그래프로 부드러운 스케일링 효과
     - Variables 컴포넌트로 Speed 조절 가능

## 2D 그림자 시스템

### Shadow Caster 2D 이해하기

- 2D 공간에서 사실적인 그림자 표현을 위한 컴포넌트
- **Casting Source 종류**:

  1. Sprite Renderer: 스프라이트의 Custom Outline 기반
  2. Collider 2D: 물리 컴포넌트 형태 기반
  3. Shape Editor: 직접 편집 가능한 커스텀 형태
  4. None: 그림자 비활성화

- **Casting Options**:
  - Self Shadow: 자신의 영역 내부에 그림자 생성
  - Cast Shadow: 조명 방향 기준 방사형 그림자
  - Cast and Self Shadow: 두 옵션 혼합

### 그림자 구현 방법

1. **Sprite Renderer 방식**

   - Custom Outline 설정 필요
   - Sprite Editor에서 Outline 직접 편집
   - 세밀한 그림자 표현 가능
   - 픽셀 아트의 경우 정점 수동 조정 필요

2. **Collider 2D 방식**
   - 물리 컴포넌트 형태 그대로 활용
   - Trim Edge로 그림자 경계 조절
   - 간단한 구현에 적합
   - 플랫폼이나 벽 등에 효과적

### 성능 최적화

- **Light Render Scale 설정**
  - 기본값 0.5에서 1로 증가 권장
  - 픽셀 아트에서 더 선명한 결과물
  - Frame Debugger로 텍스처 품질 확인 가능
  - 참조 해상도가 작아 성능 영향 미미

### 주의사항

- Target Sorting Layer로 부분 그림자 설정 가능
- Custom Outline 설정 시 Apply 필수
- 높은 Light Render Scale은 메모리 사용량 증가
