# Claude Code Plugins Marketplace

Claude Code를 위한 커스텀 스킬 마켓플레이스입니다. 개발 생산성을 높이기 위한 다양한 자동화 스킬들을 제공합니다.

## 빠른 시작

### 1. 마켓플레이스 추가

```bash
/plugin marketplace add bear2u/claude-plugins
```

### 2. 사용 가능한 플러그인 확인

```bash
/plugin marketplace list
```

### 3. 플러그인 설치

```bash
# Flutter 프로젝트 초기화
/plugin marketplace install suji-father-marketplace@flutter-init

# Next.js 15 프로젝트 초기화
/plugin marketplace install suji-father-marketplace@nextjs15-init

# 코드 변경사항 자동 문서화
/plugin marketplace install suji-father-marketplace@code-changelog

# Codex CLI 통합
/plugin marketplace install suji-father-marketplace@codex

# Codex-Claude 이중 AI 루프
/plugin marketplace install suji-father-marketplace@codex-claude-loop

# 메타 프롬프트 생성기
/plugin marketplace install suji-father-marketplace@meta-prompt-generator

# 프롬프트 향상기
/plugin marketplace install suji-father-marketplace@prompt-enhancer

# 웹페이지 마크다운 변환
/plugin marketplace install suji-father-marketplace@web-to-markdown

# 카드 뉴스 생성기
/plugin marketplace install suji-father-marketplace@card-news-generator

# 카드 뉴스 생성기 V2 (배경 이미지 지원)
/plugin marketplace install suji-father-marketplace@card-news-generator-v2

# 랜딩페이지 가이드
/plugin marketplace install suji-father-marketplace@landing-page-guide
```

### 4. 설치된 플러그인 확인

```bash
/plugin list
```

## 플러그인 관리

### 플러그인 업데이트

```bash
# 특정 플러그인 업데이트
/plugin update suji-father-marketplace@flutter-init

# 모든 플러그인 업데이트
/plugin update
```

### 플러그인 삭제

```bash
# 특정 플러그인 삭제
/plugin remove flutter-init

# 마켓플레이스 전체 삭제
/plugin marketplace remove suji-father-marketplace
```

## 사용 가능한 플러그인

### 1. Flutter Init
Use when user wants to create a new Flutter project (Todo/Habit/Note/Expense/Custom domain) with Clean Architecture, Riverpod 3.0, Drift, and modern Flutter stack

**주요 기능:**
- 도메인 기반 프로젝트 생성
- Clean Architecture 자동 구성
- Riverpod 3.0, Drift, Freezed 통합
- 다국어 지원

### 2. Next.js 15 Init
Use when user wants to create a new Next.js 15 project (Todo/Blog/Dashboard/E-commerce/Custom domain) with App Router, ShadCN, Zustand, Tanstack Query, and modern Next.js stack

**주요 기능:**
- App Router 기반 구조
- ShadCN UI 컴포넌트
- TypeScript Strict Mode
- Drizzle ORM, Better Auth

### 3. Code Changelog
AI가 만든 모든 코드 변경사항을 reviews 폴더에 기록하고 간단한 HTML 뷰어로 웹 브라우저에서 실시간 확인할 수 있습니다

**주요 기능:**
- 자동 문서 생성 (Markdown)
- HTML 뷰어 (Python 서버)
- 다크 모드 UI
- 실시간 서버 (http://localhost:4000)

### 4. Codex
Use when the user asks to run Codex CLI (codex exec, codex resume) or references OpenAI Codex for code analysis, refactoring, or automated editing

**주요 기능:**
- 대화형 모델 선택 (gpt-5, gpt-5-codex)
- 샌드박스 모드
- 세션 재개 기능
- 자동화된 코드 편집

### 5. Codex-Claude Loop
Orchestrates a dual-AI engineering loop where Claude Code plans and implements, while Codex validates and reviews, with continuous feedback for optimal code quality

**주요 기능:**
- Claude (계획 + 구현) → Codex (검증)
- 자체 수정 시스템
- 고품질 코드 보장

### 6. Meta Prompt Generator
간단한 설명을 받아 단계별 병렬 처리가 가능한 구조화된 커스텀 슬래시 커맨드를 자동으로 생성합니다

**주요 기능:**
- 지능형 지식 수집
- 단계 기반 워크플로우
- 포괄적인 테스트 생성

### 7. Prompt Enhancer
Enhance user prompts by analyzing project context (code structure, dependencies, conventions, existing patterns)

**주요 기능:**
- 프로젝트 구조 자동 분석
- 기존 패턴 인식
- 구조화된 요구사항 생성

### 8. Web to Markdown
웹페이지 URL을 입력받아 마크다운 형태로 변환하여 저장합니다

**주요 기능:**
- 3가지 변환 모드 (일반/AI 최적화/듀얼)
- 여러 URL 일괄 변환
- YAML 프론트매터
- WebFetch 자동 캐싱

### 9. Card News Generator
Create 600x600 Instagram-style card news series automatically with optional background images

**주요 기능:**
- 자동 카드 시리즈 생성
- 자동 텍스트 래핑
- 다양한 색상 프리셋

### 10. Card News Generator V2
Enhanced card news generator with background image support and advanced features

**주요 기능:**
- 배경 이미지 지원
- Cafe24Ssurround 폰트 번들
- 반투명 박스 + 테두리
- 오버레이 조절

### 11. Landing Page Guide
Comprehensive guide for creating high-conversion landing pages with Next.js and React, following DESIGNNAS 11 essential elements framework

**주요 기능:**
- 11가지 필수 요소
- ShadCN UI 통합
- SEO 최적화
- 반응형 디자인

## 문제 해결

### 마켓플레이스 추가 오류

```bash
# 기존 마켓플레이스 삭제 후 재추가
/plugin marketplace remove suji-father-marketplace
/plugin marketplace add bear2u/claude-plugins
```

### 플러그인이 보이지 않을 때

```bash
# Claude Code 재시작 또는
/plugin marketplace list
```

## 기여

새로운 플러그인을 추가하거나 기존 플러그인을 개선하고 싶으시다면:

1. 이 저장소를 Fork
2. `skills/` 폴더에 새로운 플러그인 추가
3. Pull Request 제출

## 라이선스

MIT License

## 참고

- [Claude Code Documentation](https://docs.claude.com/en/docs/claude-code)
- [Plugin Marketplace Guide](https://code.claude.com/docs/en/plugin-marketplaces.md)
