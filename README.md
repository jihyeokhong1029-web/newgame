# 코리아 트래블러 — 안드로이드 앱 프로젝트

대한민국 17개 시도를 실제로 여행하며 잠금 해제하고, 커뮤니티 포스트를 남기고,
가상 건물로 꾸미는 여행 게임의 Capacitor 기반 안드로이드 프로젝트입니다.

게임 자체(지도, 위치 확인, 미션, 꾸미기 로직)는 `www/index.html` 안에
순수 HTML/CSS/JS로 구현되어 있으며, 이 폴더 전체가 그대로 안드로이드 앱으로
패키징됩니다.

## ⚠️ 먼저 알아두세요: 이 파일들이 어떻게 만들어졌나

지금 이 대화(클라우드 작업 환경)에서는 안드로이드 APK를 직접 빌드할 수
없었습니다. APK를 만들려면 Android Gradle Plugin, AndroidX 등을
`dl.google.com` / `maven.google.com`에서 내려받아야 하는데, 이 환경의
네트워크 정책상 해당 주소가 차단되어 있어 다음 두 가지 방법 중 하나로
직접 빌드해야 합니다. (실제로 두 방법 모두 무료이고, 특별한 개발 지식 없이도
따라 하실 수 있어요.)

## 방법 A. GitHub Actions로 빌드하기 (추천, 컴퓨터에 아무것도 설치 안 해도 됨)

### A-1. git 명령어로 올리기 (가장 안전함, 강력 추천)

웹 드래그앤드롭 업로드는 `.github`처럼 점(.)으로 시작하는 폴더가 누락되는
경우가 있어서, 아래처럼 `git`으로 올리는 걸 강력히 추천해요. (컴퓨터에
[git](https://git-scm.com/downloads)만 설치되어 있으면 됩니다.)

1. GitHub에서 새 빈 저장소를 만듭니다 (README 등 아무것도 체크하지 않고 생성).
2. 이 zip 압축을 풀고, 터미널(명령 프롬프트)에서 그 폴더로 이동한 뒤
   아래 명령을 순서대로 실행합니다 (`<저장소주소>`는 저장소 생성 후
   GitHub가 알려주는 `https://github.com/아이디/저장소이름.git` 형태의 주소):

   ```
   git init
   git add .
   git commit -m "initial commit"
   git branch -M main
   git remote add origin <저장소주소>
   git push -u origin main
   ```
3. push가 끝나면 자동으로 Actions 탭에서 빌드가 시작됩니다.

### A-2. 웹사이트에서 드래그앤드롭으로 올리기 (git 설치가 번거로울 때)

1. 저장소 코드 탭 → **Add file → Upload files**.
2. 압축을 푼 폴더 **안의 내용물**(파일/폴더들)을 통째로 선택해 드래그합니다.
   이때 파일 탐색기에서 **숨김 파일 표시**를 켜서 `.github` 폴더가 실제로
   선택되는지 꼭 확인하세요 (윈도우: 탐색기 "보기 → 표시 → 숨긴 항목",
   맥: `Cmd+Shift+.`).
3. 업로드 후 저장소 최상위에 `.github` 폴더가 보이는지 꼭 확인하세요.
   안 보이면 지난번처럼 `.github/workflows/build-apk.yml`을 웹에서 직접
   새로 만들어야 합니다 (저장소 URL 뒤에
   `/new/main?filename=.github/workflows/build-apk.yml` 를 붙인 주소로
   들어가면 경로가 자동으로 채워져요).

### 빌드 결과 받기

- Actions 탭 → 실행된 워크플로 클릭 → 화면 아래 **Artifacts** 항목에서
  `korea-traveler-debug-apk`를 내려받습니다. (약 3~5분 소요)
- 압축을 풀면 `app-debug.apk` 파일이 나옵니다. 안드로이드 기기로 옮겨서
  설치하면 바로 실행됩니다. (기기 설정에서 "출처를 알 수 없는 앱 설치
  허용"이 필요할 수 있어요.)

## 방법 B. 내 컴퓨터에서 Android Studio로 빌드하기

1. [Android Studio](https://developer.android.com/studio)를 설치합니다.
2. `Open` → 이 프로젝트의 `android` 폴더를 엽니다.
3. Android Studio가 자동으로 Gradle 동기화를 진행합니다 (인터넷 필요).
4. 상단 메뉴 `Build` → `Build Bundle(s) / APK(s)` → `Build APK(s)`.
5. 완성된 APK는 `android/app/build/outputs/apk/debug/app-debug.apk` 에
   생성됩니다.

내용을 수정한 뒤 다시 빌드하려면 `www/index.html`을 수정한 후
`npx cap sync android`를 실행해서 변경 사항을 안드로이드 프로젝트에
반영해주세요.

## 프로젝트 구조

```
apk-project/
├── www/index.html          게임 전체 (HTML+CSS+JS 단일 파일)
├── android/                 Capacitor가 생성한 네이티브 안드로이드 프로젝트
├── capacitor.config.ts      앱 이름 / 패키지명 / webDir 설정
├── .github/workflows/       GitHub Actions 자동 빌드 워크플로
└── package.json
```

- 앱 이름: 코리아 트래블러
- 패키지 ID: `com.jihyeok.koreatraveler`
- 위치 권한: `ACCESS_FINE_LOCATION`, `ACCESS_COARSE_LOCATION`
  (`@capacitor/geolocation` 플러그인 사용, 앱 실행 중 OS 권한 팝업이 뜹니다)

## 게임 로직 요약

- **잠금 해제**: 기기의 실제 GPS 위치가 해당 시/도 경계 안에 들어오면
  미션(커뮤니티 포스트 작성)을 완료해 잠금 해제됩니다.
- **테스트 모드**: 실제로 모든 지역을 여행하지 않아도 화면 상단의
  "🧪 테스트 모드" 토글로 원하는 지역을 가상 위치로 지정해 테스트할 수
  있습니다. (출시용 앱에서는 이 기능을 숨기거나 제거하는 걸 권장합니다.)
- **꾸미기**: 잠금 해제된 지역마다 4×4 칸에 건물 아이콘을 배치할 수 있습니다.
- **데이터 저장**: 현재는 기기의 `localStorage`에만 저장됩니다 (다른 기기와
  동기화되지 않음, 앱 삭제 시 초기화됨). 여러 기기 동기화나 실제 소셜 피드가
  필요하다면 별도 백엔드(파이어베이스, 자체 서버 등) 연동이 필요합니다.
- **지도 데이터**: 17개 시도 경계는 southkorea/southkorea-maps
  (KOSTAT 2013 간소화본, CC BY-SA 계열 오픈 데이터)에서 가져와 SVG로
  변환했습니다.

## 다음 단계로 고려해볼 것들

- 시군구 단위(약 250개)로 지도 세분화
- 앱 아이콘 / 스플래시 이미지 커스터마이징 (`android/app/src/main/res` 내
  `mipmap-*` 교체)
- 실제 커뮤니티 포스트를 다른 사용자와 공유하려면 백엔드(파이어베이스 등)
  연동 및 사진 업로드 서버 저장 필요
- 정식 출시를 위한 서명 키 생성 및 `assembleRelease` 빌드, Play 스토어 등록
