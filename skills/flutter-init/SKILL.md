---
name: flutter-init
description: Use when user wants to create a new Flutter project (Todo/Habit/Note/Expense/Custom domain) with Feature-First Architecture, Riverpod 3 Code Gen, Drift, and modern Flutter stack
---

# Flutter Init Skill

도메인 기반 Flutter 프로젝트를 생성하고 **Feature-First 아키텍처**로 자동 설정합니다.
Todo, Habit, Note, Expense 또는 Custom 도메인을 선택하여 오프라인 우선(Offline-first) CRUD 앱을 즉시 생성할 수 있습니다.

## Tech Stack

| 영역 | 기술 | 비고 |
|------|------|------|
| **State** | Riverpod 3 | Code Gen 필수 (`@riverpod`) |
| **Data** | Freezed 3 | Immutable Models |
| **DB** | Drift | SQLite, Offline-first |
| **Network** | Dio | HTTP Client |
| **Routing** | GoRouter | Typed Routes 필수 |

## Quick Start

스킬 실행 시 다음 정보를 입력받습니다:
- 폴더명 (예: my_habit_app)
- 프로젝트명/패키지명 (예: habit_app)
- 도메인 선택 (Todo/Habit/Note/Expense/Custom)
- 스택 프리셋 (Minimal/Essential/Full Stack/Custom)

그 후 자동으로 다음 단계가 실행됩니다:

```bash
# 1. 프로젝트 생성 (Android/Kotlin, iOS/Swift)
flutter create --platforms android,ios --android-language kotlin --org com.example [폴더명]

# 2. 패키지 설치
flutter pub get

# 3. Feature-First 구조로 코드 자동 생성
# - lib/src/features/{domain}/presentation/ (UI + Notifier)
# - lib/src/features/{domain}/domain/ (Freezed 모델)
# - lib/src/features/{domain}/data/ (Repository, DAO)
# - lib/src/database/ (Drift DB 정의)
# - lib/src/routing/ (GoRouter Typed Routes)

# 4. 코드 생성 (Riverpod, Freezed, Drift)
dart run build_runner build --delete-conflicting-outputs

# 5. 코드 검증 및 오류 자동 수정 (필수)
flutter analyze

# 6. 앱 실행
flutter run
```

## Task Instructions

**IMPORTANT**: 이 스킬은 대화형으로 진행됩니다.

### Step 1: 도메인 및 프로젝트 설정 질문

**먼저 사용자에게 이렇게 물어보세요:**

"Flutter 앱을 생성합니다. 다음 정보를 알려주세요:

**1. 도메인(엔티티) 선택**

어떤 도메인의 앱을 만드시겠습니까?

**A) Todo (할 일 관리)**
- 필드: title, description, isCompleted, createdAt, completedAt
- 기능: CRUD, 필터링(전체/진행중/완료), 체크박스 토글

**B) Habit (습관 트래커)**
- 필드: name, description, frequency(daily/weekly/monthly), streak, lastCompletedAt, goal, isActive
- 기능: CRUD, 연속 기록 추적, 목표 달성률, 완료 체크

**C) Note (메모)**
- 필드: title, content, tags, isPinned, createdAt, updatedAt
- 기능: CRUD, 태그 필터링, 고정 메모, 검색

**D) Expense (지출 관리)**
- 필드: amount, category, description, date, paymentMethod
- 기능: CRUD, 카테고리별 집계, 월별 통계, 필터링

**E) Custom (직접 정의)**
- 엔티티명과 필드를 직접 입력

**2. 프로젝트 정보**
- **폴더명**: 프로젝트를 생성할 폴더 이름 (기본값: [도메인]_app, 예: habit_app)
  - 이 폴더에 Flutter 프로젝트가 생성됩니다
- **프로젝트명 (패키지명)**: Flutter 패키지 이름 (기본값: 폴더명과 동일)
  - pubspec.yaml의 name 필드에 사용됩니다
  - import 문에 사용됩니다 (예: package:habit_app/...)
- **조직명**: (기본값: com.example)
  - Android/iOS 패키지 식별자에 사용됩니다 (예: com.example.habit_app)

**3. 스택 프리셋 선택**

다음 중 하나를 선택해주세요:

**A) Essential (권장)**
- ✅ GoRouter (타입 안전한 라우팅)
- ✅ SharedPreferences (로컬 설정 저장)
- ✅ FPDart (함수형 에러 핸들링)
- ✅ Google Fonts
- ✅ FluentUI Icons
- ❌ Auth 시스템 제외
- ❌ Responsive Utils 제외

**B) Minimal (가장 단순)**
- ❌ GoRouter (기본 Navigator 사용)
- ✅ SharedPreferences
- ❌ FPDart 제외
- ❌ Google Fonts 제외
- ✅ 기본 FluentUI Icons
- ❌ Auth 시스템 제외
- ❌ Responsive Utils 제외

**C) Full Stack (모든 기능)**
- ✅ GoRouter
- ✅ SharedPreferences
- ✅ FPDart (함수형 에러 핸들링)
- ✅ Google Fonts
- ✅ Responsive Utils
- ✅ FluentUI Icons
- ✅ Auth 시스템 (Login/Register) - 선택 도메인에 따라

**D) Custom (직접 선택)**
- 각 기능을 개별적으로 선택

어떤 도메인과 프리셋을 선택하시겠습니까? (도메인: A/B/C/D/E, 프리셋: A/B/C/D)"

### Step 2: Custom 선택 시 추가 질문

#### 2-1. Custom 도메인 (E) 선택 시:

1. **엔티티명**: 엔티티 이름을 입력하세요 (예: Task, Event, Book)
2. **필드 정의**: 각 필드를 입력하세요 (형식: 필드명:타입, 예: title:String, amount:double, isActive:bool)
   - 지원 타입: String, int, double, bool, DateTime
   - createdAt, updatedAt은 자동 추가됨
3. **주요 기능**: 필터링/정렬 기준이 될 필드를 선택하세요

#### 2-2. Custom 스택 프리셋 (D) 선택 시:

다음 질문들을 순차적으로 하세요:

1. **네비게이션**: GoRouter를 사용하시겠습니까? (예/아니오)
2. **에러 핸들링**: FPDart를 사용하시겠습니까? (예/아니오)
3. **UI**: Google Fonts를 사용하시겠습니까? (예/아니오)
4. **반응형**: Responsive Utils를 포함하시겠습니까? (예/아니오)
5. **인증 시스템**: Auth 시스템을 포함하시겠습니까? (예/아니오)

### Step 3: 선택된 도메인과 스택에 따라 프로젝트 생성

1. **Flutter 프로젝트 생성**:
   - 사용자가 지정한 **폴더명**으로 프로젝트 생성
   - 명령어: `flutter create --platforms android,ios --android-language kotlin --org [조직명] [폴더명]`
   - 예: `flutter create --platforms android,ios --android-language kotlin --org com.example my_habit_app`
   - 폴더명과 프로젝트명(패키지명)이 다른 경우, 생성 후 pubspec.yaml의 `name` 필드를 수정

2. **Kotlin DSL 확인** (최신 Flutter는 자동으로 Kotlin DSL 사용)

3. **선택된 패키지 설치**: `pubspec.yaml` 업데이트 후 `flutter pub get`

4. **Feature-First 폴더 구조 생성**:
   ```
   lib/
   └── src/
       ├── features/
       │   └── {domain}/
       │       ├── presentation/   # UI 위젯 + Notifier (@riverpod)
       │       ├── domain/         # Freezed 모델/엔티티
       │       └── data/           # Repository 구현, DAO
       ├── database/               # Drift DB 정의 (전역)
       ├── routing/                # GoRouter Typed Routes
       └── shared/                 # 공통 위젯, 유틸리티
   ```

5. **도메인별 보일러플레이트 생성** (Feature-First + Riverpod Code Gen):

   **A) Todo**: title, description, isCompleted, createdAt, completedAt
   - `features/todo/domain/todo.dart` - Freezed 엔티티
   - `features/todo/data/todo_repository.dart` - Repository + DAO
   - `features/todo/presentation/todo_notifier.dart` - `@riverpod` Notifier
   - `features/todo/presentation/todo_list_screen.dart` - UI

   **B) Habit**: name, description, frequency, streak, lastCompletedAt, goal, isActive
   - `features/habit/domain/habit.dart` - Freezed 엔티티
   - `features/habit/data/habit_repository.dart` - Repository + DAO
   - `features/habit/presentation/habit_notifier.dart` - `@riverpod` Notifier
   - `features/habit/presentation/habit_list_screen.dart` - UI

   **C) Note**: title, content, tags, isPinned, createdAt, updatedAt
   - `features/note/domain/note.dart` - Freezed 엔티티
   - `features/note/data/note_repository.dart` - Repository + DAO
   - `features/note/presentation/note_notifier.dart` - `@riverpod` Notifier
   - `features/note/presentation/note_list_screen.dart` - UI

   **D) Expense**: amount, category, description, date, paymentMethod
   - `features/expense/domain/expense.dart` - Freezed 엔티티
   - `features/expense/data/expense_repository.dart` - Repository + DAO
   - `features/expense/presentation/expense_notifier.dart` - `@riverpod` Notifier
   - `features/expense/presentation/expense_list_screen.dart` - UI

   **E) Custom**: 사용자 정의 필드
   - 동일한 Feature-First 구조로 생성

6. **설정 파일 생성**:
   - `lib/src/database/app_database.dart` - Drift DB (전역)
   - `lib/src/routing/app_router.dart` - GoRouter Typed Routes
   - `assets/translations/` - Easy Localization

7. **코드 생성**: `dart run build_runner build --delete-conflicting-outputs`

8. **코드 검증 및 오류 수정**:

   a. `flutter analyze` 실행

   b. 발견된 오류 수정:
   - **import 경로**: `package:` 형식 사용
   - **Riverpod 3 Code Gen**: 모든 Provider는 `@riverpod` 어노테이션 사용
   - **Freezed 3**: `@freezed` 어노테이션 + `part` 파일 확인
   - **AsyncValue**: 로딩/에러 상태는 `.when()` 패턴 사용

   c. 재검증: 모든 error 레벨 오류가 없을 때까지 반복

   d. 목표: `flutter analyze` 결과가 "0 issues found"

   **✅ CRITICAL**: 이 단계는 필수입니다. 모든 error를 제거해야 다음 단계로 진행할 수 있습니다.

### Step 4: 최종 검증 및 안내

**✅ CRITICAL**: 이 단계는 프로젝트 완료의 필수 조건입니다.

1. **최종 분석 실행**:
   ```bash
   flutter analyze
   ```

2. **성공 기준**:
   - ✅ **성공 예시**:
     ```
     Analyzing todo_app...
     No issues found!
     ```
     또는
     ```
     Analyzing todo_app...
     1 issue found. (ran in 2.3s)
     info • Prefer using lowerCamelCase for constant names • lib/core/constants.dart:5:7 • constant_identifier_names
     ```

   - ❌ **실패 예시** (error가 있으면 반드시 수정):
     ```
     error • Target of URI doesn't exist: 'package:...' • lib/main.dart:5:8 • uri_does_not_exist
     error • The getter 'xyz' isn't defined for the type 'ABC' • lib/presentation/screens/home.dart:42:15
     ```

3. **검증 결과 요약** (성공 시):
   ```
   ✅ Flutter 프로젝트 생성 완료!
   ✅ 코드 생성 완료 (Freezed, Drift, JSON Serializable)
   ✅ Flutter analyze 통과 (0-1 issues found, info 레벨만)
   ✅ 모든 패키지 설치 완료
   ```

4. **프로젝트 정보 제공**:
   - **폴더명**: [사용자 입력값] (예: my_habit_app)
   - **프로젝트명 (패키지명)**: [사용자 입력값] (예: habit_app)
   - **조직명**: [사용자 입력값] (예: com.example)
   - **도메인**: [선택된 도메인] (Todo/Habit/Note/Expense/Custom)
   - **선택된 스택**: [프리셋명] (GoRouter, Drift, FPDart 등)
   - **주요 기능**: [도메인] CRUD, 다국어 지원, 로컬 저장소 등
   - **생성된 파일**: XX개 Dart 파일 (core, data, domain, presentation)

5. **실행 방법 안내**:
   ```bash
   cd [폴더명]
   flutter run
   ```

6. **다음 단계 제안** (선택사항, 도메인별):
   - **Todo**: 항목 추가/수정/삭제, 필터링(전체/진행중/완료), 완료 토글
   - **Habit**: 습관 기록, 연속 기록 확인, 목표 달성률, 통계 확인
   - **Note**: 메모 작성/편집, 태그 추가, 고정 메모, 검색
   - **Expense**: 지출 기록, 카테고리별 통계, 월별 집계, 필터링
   - **공통**: 언어 전환 (영어 ↔ 한국어), 다크/라이트 테마 전환

## Core Principles

- **Feature-First**: 기능 단위 독립 구조 (`lib/src/features/{feature}/`)
- **Riverpod 3 Code Gen**: 모든 Provider는 `@riverpod` 어노테이션 필수
- **Freezed 3**: 불변 모델 + JSON 직렬화
- **Offline-First**: Drift SQLite로 로컬 우선 저장
- **Typed Routes**: GoRouter로 타입 안전한 네비게이션
- **AsyncValue**: 로딩/에러 상태는 `.when()` 패턴 사용

## Coding Conventions

### 1. Riverpod 3 (State Management)
```dart
// ✅ Code Gen 사용 (필수)
@riverpod
class TodoNotifier extends _$TodoNotifier {
  @override
  Future<List<Todo>> build() async {
    return ref.watch(todoRepositoryProvider).getTodos();
  }
}

// UI에서 사용
ref.watch(todoNotifierProvider).when(
  data: (todos) => ListView(...),
  error: (e, st) => ErrorWidget(e),
  loading: () => CircularProgressIndicator(),
);
```

### 2. Freezed 3 (Models)
```dart
@freezed
class Todo with _$Todo {
  const factory Todo({
    required int id,
    required String title,
    @Default(false) bool isCompleted,
  }) = _Todo;

  factory Todo.fromJson(Map<String, dynamic> json) => _$TodoFromJson(json);
}
```

### 3. 에러 핸들링
- Repository: `try-catch` 후 커스텀 에러 반환 또는 `AsyncValue`로 래핑
- UI: `.when(data, error, loading)` 패턴으로 에러 표시

### 4. Drift (Local DB)
- 테이블 정의: `lib/src/database/` (전역)
- DAO: `features/{feature}/data/` 내에 위치

## Reference Files

[references/setup-guide.md](references/setup-guide.md) - 완전한 가이드
- Feature-First 구조 상세
- Riverpod 3 Code Gen 패턴
- Drift DAO 패턴
- GoRouter Typed Routes

## Notes

- **Feature-First 구조**: 기능별 독립 폴더 (`lib/src/features/`)
- **Code Gen 필수**: Riverpod, Freezed, Drift 모두 코드 생성 사용
- **도메인 지원**: Todo, Habit, Note, Expense, Custom
- **프리셋 제공**: Full Stack, Essential, Minimal, Custom
- **플랫폼**: Android/Kotlin, iOS/Swift
- **주요 명령어**:
  - Build: `dart run build_runner build --delete-conflicting-outputs`
  - Watch: `dart run build_runner watch`
  - Lint: `flutter analyze`
  - Test: `flutter test`
