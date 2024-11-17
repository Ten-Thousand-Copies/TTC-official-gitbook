# This Page is 
해당 페이지는 팀 온보딩을 위한 페이지 입니다.

## Organization 소개 

Ten-Thousand-Copies 조직의 GitHub 리포지토리 목록은 다음과 같고 다음의 역할들을 합니다. 

1. **.github**: 조직의 GitHub 설정 파일을 포함하는 리포지토리입니다.
2. **TTC-official-gitbook**: 프로젝트의 공식 문서를 관리하는 GitBook 리포지토리입니다. 학습한 내용들, 문제 해결했던 내용 등을 담아서 우리의 기술력을 보여주는 레포지터리입니다. 
3. **TTC-client**: 프로젝트의 클라이언트 측 소스 코드 레포지터리 입니다.

4. **TTC-proto-demo**: 프로젝트의 프로토타입이나 데모 버전을 위한 리포지토리입니다. 학습기간 동안 만들었던 내용들이 담길 기본 레포지터리입니다. 
5. **TTC-backend**: 프로젝트의 백엔드 소스 코드를 관리하는 리포지토리입니다. 


## GitBook 사용 가이드

GitBook을 효과적으로 사용하기 위해 아래 절차를 따라 작업하세요.

---

### 1. 기본 작업 흐름

GitBook은 다음과 같은 작업 흐름으로 사용합니다:

1. **GitBook 레포지토리 클론**  
   ```bash
   git clone <레포지토리 URL>
   cd <프로젝트 폴더>
   ```
   로컬 환경에 프로젝트를 클론합니다.

2. **새로운 브랜치 생성**  
   ```bash
   git checkout -b <브랜치 이름>
   ```
   변경 사항을 작업할 새로운 브랜치를 생성합니다. 브랜치 이름은 기능이나 작업 내용을 반영하도록 설정합니다. (예: `feature/update-intro`)

3. **내용 작업 및 수정**  
   프로젝트 폴더 내 파일을 편집합니다. 특히 `SUMMARY.md`를 수정하여 새로운 페이지를 추가하세요.

   ```markdown
   # SUMMARY.md
   * [Introduction](README.md)
   * [New Page](new-page.md)
   ```

   필요한 페이지는 `.md` 파일로 작성합니다.

4. **변경 사항 커밋**  
   변경 사항을 확인하고 커밋합니다.
   ```bash
   git add .
   git commit -m "작업 내용 설명"
   ```

5. **Pull Request 생성**  
   변경 사항을 원격 저장소로 푸시한 뒤, GitHub에서 Pull Request(PR)를 생성합니다.
   ```bash
   git push origin <브랜치 이름>
   ```

   PR에서 코드 리뷰 및 승인 과정을 거칩니다.

6. **브랜치 병합 및 삭제**  
   PR이 승인되면 브랜치를 병합(Merge)합니다. 필요 시 로컬 및 원격 브랜치를 삭제합니다.
   ```bash
   git branch -d <브랜치 이름>
   git push origin --delete <브랜치 이름>
   ```

---

### 2. 프로젝트 폴더 구조

GitBook은 프로젝트 내에서 특정 폴더 구조를 권장합니다. 아래는 예시입니다:

```
project/
├── docs/
│   ├── README.md       # GitBook 첫 화면
│   ├── SUMMARY.md      # 목차 파일
│   ├── chapter1/
│   │   ├── intro.md
│   │   └── details.md
│   └── assets/
│       ├── images/
│       │   ├── image1.png
│       │   └── image2.png
├── src/                # 이미지, 리소스 파일을 저장하는 폴더
│   ├── images/
│   │   ├── example.png
│   │   └── diagram.png
└── package.json        # npm 설정 파일 (선택 사항)
```

---

### 3. 이미지 추가 방법

이미지는 각 장의 `src/images/` 폴더에 저장하고, `.md` 파일에서 상대 경로를 사용하여 추가합니다.

#### 예시

1. **이미지 파일 저장**  
   `src/images/diagram.png`에 이미지를 저장합니다.

2. **이미지 삽입**  
   `.md` 파일에서 다음과 같이 작성합니다:
   ```markdown
   ![Diagram 설명](../src/images/diagram.png)
   ```

---

### 4. 로컬에서 GitBook 실행

GitBook을 로컬에서 확인하려면 아래 명령어를 사용하세요:

1. **GitBook CLI 설치 (최초 1회)**  
   ```bash
   npm install -g gitbook-cli
   ```

2. **로컬 서버 실행**  
   ```bash
   gitbook serve
   ```

   브라우저에서 [http://localhost:4000](http://localhost:4000)을 열어 결과를 확인합니다.

---

### 5. 주의 사항

- 브랜치 명과 커밋 메시지를 명확히 작성하여 작업 내용을 이해하기 쉽게 관리합니다.
- `SUMMARY.md`에서 추가한 페이지가 누락되지 않았는지 항상 확인하세요.
- 작업 후 Pull Request를 통해 코드 리뷰 과정을 거치는 것을 권장합니다.

---

위 가이드를 따라 GitBook 프로젝트를 효과적으로 관리할 수 있습니다.
