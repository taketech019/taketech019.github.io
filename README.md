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

## 디자인 시스템 — "Sand Desk"

모래빛 책상(Sand) 위에 종이 한 장(Paper)을 펼쳐 놓은 구성입니다. 글자는 채도를 낮춘 Obsidian 보라 — 거의 먹색에 가까운 잉크 — 로 쓰고, 초록은 포인트로만 씁니다. 화면에서 초록이 보이면 "누를 수 있거나, 지금 여기"라는 뜻입니다.

### 컬러 (라이트 단일 테마)

세 원색의 **색상(Hue)은 고정**하고 채도·명도만 조절합니다.

| 원색            | 원본      | Hue  | 역할                                                          |
| --------------- | --------- | ---- | ------------------------------------------------------------- |
| Sand Beige      | `#EBDCC4` | 37°  | 바탕 — 페이지 프레임(원색 그대로), 본문 시트(Paper), 코드·표  |
| Obsidian Purple | `#5B4965` | 279° | 잉크 — 채도를 16% → 6–11%로 낮춰 제목·본문·보조 텍스트에 사용 |
| Forest Green    | `#1F3D2B` | 144° | 포인트 — 링크, 태그, 탐색기 현재 항목, 팁 콜아웃              |

톤 사다리:

| 토큰             | Hex       | 역할                                                | 대비 (Paper / Sand) |
| ---------------- | --------- | --------------------------------------------------- | ------------------- |
| S200 Sand        | `#EBDCC4` | 페이지 바탕 (사이드바·푸터)                         | —                   |
| S300 Sand Raised | `#DDCEB6` | 프레임 위 검색창                                    | —                   |
| S100 Code        | `#F3EDE2` | 코드 블록, 표 머리                                  | —                   |
| S50 Paper        | `#FAF6EF` | 본문 시트, 그래프 박스, 검색 결과                   | —                   |
| Line             | `#E6DED1` | 시트 안 경계선                                      | —                   |
| S800 Earth       | `#6F552A` | 경고·위험 콜아웃                                    | 6.5:1 / 5.2:1       |
| Ink 900          | `#27222A` | 제목, 사이트 이름                                   | 14.5:1 / 11.5:1     |
| Ink 700          | `#3F3842` | 본문, 사이드바 링크                                 | 10.5:1 / 8.4:1      |
| Ink 500          | `#635B67` | 보조 텍스트(날짜·메타)                              | 6.0:1 / 4.8:1       |
| Ink Note         | `#6F5F77` | 노트·정보 콜아웃                                    | 5.4:1 / —           |
| G600 Green       | `#28623F` | 본문 링크·태그, 현재 항목 바탕(글자는 Paper), hover | 6.7:1 / 5.4:1       |
| G800             | `#19482C` | 본문 링크 hover                                     | 9.7:1 / 7.8:1       |

규칙:

- 텍스트 토큰은 Sand와 Paper 양쪽에서 4.5:1 이상이므로, 면이 바뀌어도 글자색은 그대로입니다.
- 초록은 포인트에만: 사이드바·푸터 링크는 잉크, 초록은 hover와 현재 항목에만 나타납니다. 본문(시트) 안의 링크와 태그는 초록입니다.
- 콜아웃: 노트·정보 = Ink, 팁·성공·질문 = Green, 경고·위험·버그 = Earth.
- 다크 모드는 없습니다. 그림자·그라데이션도 쓰지 않고, 면의 구분은 명도 차이로만 합니다.

### 타이포그래피

| 역할      | 서체                | 비고                                           |
| --------- | ------------------- | ---------------------------------------------- |
| 제목·본문 | Pretendard Variable | 한글 장문 가독성. 제목은 700–800, 자간 -0.01em |
| 코드      | IBM Plex Mono       | 400 / 500                                      |

- 본문 행간 1.75, 시트의 본문 폭은 한 줄 약 40자(한글) 이내로 유지합니다.
- 대문자 라벨, 제목 속 한 단어만 색칠하기 같은 장식은 쓰지 않습니다.

### 레이아웃

```
┌────────────────────────── Sand (S200) ────────────────────────┐
│ 🛠️택택의 기술도입   ╭──────── Paper 시트 ───────╮  ╭─ Paper╮ │
│ [검색        ]      │ 경로 > 경로               │  │ 그래프 │ │
│ 🔎Explorer          │ 제목                      │  ╰────────╯ │
│  Notes              │ 날짜 · 읽는 시간           │  목차       │
│ [Growth Hacking]◀초록│ 본문 …                    │  백링크     │
│  DA Projects        │                           │             │
│                     ╰───────────────────────────╯             │
│                       GitHub   My Portfolio (푸터)             │
└───────────────────────────────────────────────────────────────┘
```

- 데스크톱: Sand 바탕 위에 Paper 시트 하나. 시트만 둥근 모서리(14px)를 가지고, 그 밖의 요소는 모서리를 거의 두지 않습니다.
- 모바일: 상단 Sand 바(제목·검색·탐색기) → Paper 시트 → Sand 하단(그래프·백링크·푸터). 시트 모서리는 12px.
- 정렬은 모두 왼쪽 정렬입니다.

### 구현 위치

- `quartz.config.yaml`의 `theme.colors` — 텍스트·링크 토큰과 Paper 시트(`:root`). `darkMode`도 같은 값이라 저장된 다크 설정이 있어도 바뀌지 않습니다. `darkmode` 플러그인은 비활성화.
- `quartz/styles/custom.scss` — Sand 프레임, Paper 시트, 사이드바 링크의 잉크/초록 전환, 콜아웃·코드·표의 면 색. 톤 사다리의 SCSS 변수가 파일 상단에 있습니다.

## 이 저장소만의 커스터마이징

Quartz를 업그레이드할 때 아래 항목들이 덮어써지지 않도록 주의해야 합니다.

- `quartz/components/Head.tsx` — 네이버 서치어드바이저 메타태그, Pretendard/IBM Plex Mono 폰트 `<link>` 태그.
- `quartz/styles/custom.scss` — Obsidian Dataview 코드블록은 Quartz 5에서 지원하지 않으므로 원본 쿼리가 보이지 않도록 숨깁니다.
- `quartz.config.yaml`의 `theme`/`quartz-fonts` 및 `quartz/styles/custom.scss` — 위 [디자인 시스템](#디자인-시스템--sand-desk). `fontOrigin`이 `local`이라 Pretendard/IBM Plex Mono는 `quartz/components/Head.tsx`의 `<link>`로 로드합니다(검색엔진 인증 메타태그도 여기에 있음). 테마 색을 덮어쓰지 않도록 `@quartz-themes/core`는 비활성화했습니다.
- `og-image` 플러그인 비활성화 — Google Fonts에서 폰트를 가져오는데 Pretendard는 Google Fonts에 없어 빌드가 실패합니다. 대신 `quartz/static/og-image.png` 기본 이미지를 사용합니다.
- `quartz.config.yaml`의 giscus 댓글 설정 — `categoryId`는 저장소 소유자가 [giscus.app](https://giscus.app)에서 Discussion 카테고리를 선택해 발급받아야 합니다.
- `quartz.config.yaml`의 `ignorePatterns` — `content/Utillities/templates`(Obsidian 템플릿)는 빌드에서 제외하되, `content/Utillities/attatchments`(글에서 참조하는 이미지)는 포함되도록 주의해서 구성되어 있습니다.
