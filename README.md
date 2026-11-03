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

## 이 저장소만의 커스터마이징

Quartz를 업그레이드할 때 아래 항목들이 덮어써지지 않도록 주의해야 합니다.

- `quartz/components/Head.tsx` — 네이버 서치어드바이저 메타태그, Pretendard/IBM Plex Mono 폰트 `<link>` 태그.
- `quartz/styles/custom.scss` — Obsidian Dataview 코드블록은 Quartz 5에서 지원하지 않으므로 원본 쿼리가 보이지 않도록 숨깁니다.
- `quartz.config.yaml`의 `theme`/`quartz-fonts` 및 `quartz/styles/custom.scss` — 자체 디자인 시스템("미니멀 데이터 닥스": stone 배경 + teal 포인트, Pretendard 폰트). `fontOrigin`이 `local`이라 Pretendard/IBM Plex Mono는 `quartz/components/Head.tsx`의 `<link>`로 로드합니다(검색엔진 인증 메타태그도 여기에 있음). 테마 색을 덮어쓰지 않도록 `@quartz-themes/core`는 비활성화했습니다.
- `og-image` 플러그인 비활성화 — Google Fonts에서 폰트를 가져오는데 Pretendard는 Google Fonts에 없어 빌드가 실패합니다. 대신 `quartz/static/og-image.png` 기본 이미지를 사용합니다.
- `quartz.config.yaml`의 giscus 댓글 설정 — `categoryId`는 저장소 소유자가 [giscus.app](https://giscus.app)에서 Discussion 카테고리를 선택해 발급받아야 합니다.
- `quartz.config.yaml`의 `ignorePatterns` — `content/Utillities/templates`(Obsidian 템플릿)는 빌드에서 제외하되, `content/Utillities/attatchments`(글에서 참조하는 이미지)는 포함되도록 주의해서 구성되어 있습니다.
