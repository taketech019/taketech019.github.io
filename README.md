# 택택의 기술도입 (taketech019.github.io)

Obsidian으로 작성한 노트를 [Quartz 5](https://quartz.jzhao.xyz/)로 빌드해 GitHub Pages에 배포하는 개인 기술 블로그입니다.

## 구조

- `content/` — Obsidian vault. 실제 글이 여기 있습니다. Obsidian 앱에서 직접 열어 작성/편집합니다.
- `quartz/` — 로컬 커스터마이징이 포함된 Quartz 빌드 엔진 (업스트림 포크).
- `quartz.config.yaml` — 사이트 설정, 플러그인, 레이아웃(플러그인별 `layout`)을 모두 담습니다. 플러그인은 `@quartz-community/*` npm 패키지입니다.
- `content/robots.txt` — Assets 단계에서 사이트 루트(`/robots.txt`)로 그대로 복사됩니다.
- `.github/workflows/deploy.yml` — `v5` 브랜치에 push되면 빌드 후 GitHub Pages에 배포합니다.

## 글쓰기 & 발행 워크플로우

1. Obsidian으로 `content/` 안에서 글을 작성합니다. 프론트매터에 `publish: true`가 있어야 사이트에 노출됩니다.
2. obsidian-git 플러그인 등으로 `v5` 브랜치에 커밋 & push합니다.
3. GitHub Actions가 자동으로 빌드해 Pages에 배포합니다.

## 로컬 개발

```bash
npm ci
npx quartz build --serve   # 로컬 미리보기 (파일 변경 감지)
npm run check               # 타입체크 + prettier 검사
npm test                    # quartz/**/*.test.ts 단위 테스트
```

## 디자인 시스템 — "Garden Plot"

이 블로그는 "Data Garden"입니다. 숲(Forest Green)이 페이지 전체를 감싸고, 그 한가운데에 글을 읽는 모래빛 밭(Sand Beige) 한 뙈기가 놓여 있습니다. 보라(Obsidian Purple)는 Obsidian에서 온 연결 — 링크·태그·현재 위치 — 에만 씁니다.

### 컬러 (60 · 30 · 10, 라이트 모드 단일 테마)

| 토큰            | Hex       | 비율 | 쓰임                                                           |
| --------------- | --------- | ---- | -------------------------------------------------------------- |
| Forest Green    | `#1F3D2B` | 60%  | 페이지 바탕, 좌·우 사이드바, 푸터 / 시트 위 제목 텍스트        |
| Sand Beige      | `#EBDCC4` | 30%  | 본문 시트(`.center`), 그래프 박스, 검색창 / 사이드바 위 텍스트 |
| Obsidian Purple | `#5B4965` | 10%  | 본문 링크, 태그, 탐색기 현재 항목, 텍스트 선택·하이라이트      |

파생 톤 (세 기본색의 명암만 조절, 새 색상은 들이지 않음):

| 표면         | 역할                               | Hex                   | 대비  |
| ------------ | ---------------------------------- | --------------------- | ----- |
| Sand 시트    | 본문 텍스트                        | `#2A4534`             | 7.8:1 |
|              | 보조 텍스트(날짜·메타)             | `#4F5A48`             | 5.4:1 |
|              | 경계·코드 배경 (Sand Deep)         | `#DCCBAE`             | —     |
|              | 링크 hover (Purple Deep)           | `#45364E`             | 8.2:1 |
| Green 프레임 | 텍스트·링크                        | `#EBDCC4`             | 8.8:1 |
|              | 보조 텍스트                        | `#B4BBA2`             | 6.0:1 |
|              | 입력창·hover 바탕 (Green Raised)   | `#2E5039`             | —     |
|              | 현재 항목: Sand 글자 + Purple 바탕 | `#EBDCC4` / `#5B4965` | 6.0:1 |

규칙:

- **보라는 Sand 위에서만 글자색으로 씁니다.** Green 위 보라 글자는 1.5:1이라 읽을 수 없으므로, Green 프레임에서는 보라를 바탕색(현재 항목 표시)으로만 씁니다.
- 다크 모드는 없습니다. 색 대비는 위 표 하나로 관리합니다.
- 그림자·그라데이션은 쓰지 않습니다. 면의 구분은 색 자체로 합니다.

### 타이포그래피

| 역할      | 서체                | 비고                                           |
| --------- | ------------------- | ---------------------------------------------- |
| 제목·본문 | Pretendard Variable | 한글 장문 가독성. 제목은 700–800, 자간 -0.01em |
| 코드      | IBM Plex Mono       | 400 / 500                                      |

- 본문 행간 1.75, 시트의 본문 폭은 한 줄 약 40자(한글) 이내로 유지합니다.
- 대문자 라벨, 제목 속 한 단어만 색칠하기 같은 장식은 쓰지 않습니다.

### 레이아웃

```
┌──────────────────────── Forest Green ─────────────────────────┐
│ 🛠️택택의 기술도입   ╭──────── Sand 시트 ────────╮  ╭─ Sand ─╮ │
│ [검색        ]      │ 경로 > 경로               │  │ 그래프 │ │
│ 🔎Explorer          │ 제목                      │  ╰────────╯ │
│  Notes              │ 날짜 · 읽는 시간           │  목차       │
│ ▌Growth Hacking ◀보라│ 본문 …                    │  백링크     │
│  DA Projects        │                           │             │
│                     ╰───────────────────────────╯             │
│                       GitHub   My Portfolio (푸터)             │
└───────────────────────────────────────────────────────────────┘
```

- 데스크톱: Green 바탕 위에 시트 하나. 시트만 둥근 모서리(16px)를 가지고, 그 밖의 요소는 모서리를 거의 두지 않습니다.
- 모바일: 상단 Green 바(제목·검색·탐색기) → Sand 시트 → Green 하단(그래프·백링크·푸터). 시트 모서리는 12px.
- 정렬은 모두 왼쪽 정렬입니다.

### 구현 위치

- `quartz.config.yaml`의 `theme.colors` — Sand 시트 토큰(`:root`). `darkMode`도 같은 값이라 저장된 다크 설정이 있어도 바뀌지 않습니다. `darkmode` 플러그인은 비활성화.
- `quartz/styles/custom.scss` — Green 프레임 토큰을 사이드바·푸터에 덮어씁니다(`frame-tokens` 믹스인). Quartz의 파생 변수(`--text-normal` 등)는 `:root`에서 계산되므로 믹스인에서 함께 다시 선언합니다.

## 이 저장소만의 커스터마이징

Quartz를 업그레이드할 때 아래 항목들이 덮어써지지 않도록 주의해야 합니다.

- `quartz/components/Head.tsx` — 네이버 서치어드바이저 메타태그, Pretendard/IBM Plex Mono 폰트 `<link>` 태그.
- `quartz/styles/custom.scss` — Obsidian Dataview 코드블록은 Quartz 5에서 지원하지 않으므로 원본 쿼리가 보이지 않도록 숨깁니다.
- `quartz.config.yaml`의 `theme`/`quartz-fonts` 및 `quartz/styles/custom.scss` — 위 [디자인 시스템](#디자인-시스템--garden-plot). `fontOrigin`이 `local`이라 Pretendard/IBM Plex Mono는 `quartz/components/Head.tsx`의 `<link>`로 로드합니다(검색엔진 인증 메타태그도 여기에 있음). 테마 색을 덮어쓰지 않도록 `@quartz-themes/core`는 비활성화했습니다.
- `og-image` 플러그인 비활성화 — Google Fonts에서 폰트를 가져오는데 Pretendard는 Google Fonts에 없어 빌드가 실패합니다. 대신 `quartz/static/og-image.png` 기본 이미지를 사용합니다.
- `quartz.config.yaml`의 giscus 댓글 설정 — `categoryId`는 저장소 소유자가 [giscus.app](https://giscus.app)에서 Discussion 카테고리를 선택해 발급받아야 합니다.
- `quartz.config.yaml`의 `ignorePatterns` — `content/Utillities/templates`(Obsidian 템플릿)는 빌드에서 제외하되, `content/Utillities/attatchments`(글에서 참조하는 이미지)는 포함되도록 주의해서 구성되어 있습니다.
