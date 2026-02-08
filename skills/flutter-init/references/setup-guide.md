# Flutter Init 상세 가이드

## 전체 Setup 프로세스

### 1. 프로젝트 생성

**중요**: Android(Kotlin), iOS(Swift)만 포함하고 웹, 윈도우, 리눅스는 제외합니다.

```bash
flutter create --platforms android,ios --android-language kotlin --org com.example my_app
cd my_app
```

### 2. pubspec.yaml 설정

**Riverpod 3 + Freezed 3 + Code Gen 필수**

```yaml
name: my_app
description: A Flutter app with Feature-First architecture
publish_to: 'none'
version: 1.0.0+1

environment:
  sdk: '>=3.0.0 <4.0.0'

dependencies:
  flutter:
    sdk: flutter

  # State Management (Riverpod 3 - Code Gen 필수)
  flutter_riverpod: ^2.5.1
  riverpod_annotation: ^2.3.5

  # Data Serialization (Freezed 3)
  freezed_annotation: ^2.4.4
  json_annotation: ^4.9.0

  # Local DB (Drift)
  drift: ^2.18.0
  drift_flutter: ^0.1.0
  sqlite3_flutter_libs: ^0.5.24

  # Network
  dio: ^5.5.0

  # Navigation (GoRouter - Typed Routes)
  go_router: ^14.2.3

  # Localization
  easy_localization: ^3.0.7

  # UI
  fluentui_system_icons: ^1.1.252

  # Utils
  path_provider: ^2.1.3
  shared_preferences: ^2.2.3

dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_lints: ^4.0.0

  # Code Generators
  build_runner: ^2.4.11
  riverpod_generator: ^2.4.0
  freezed: ^2.5.2
  json_serializable: ^6.8.0
  drift_dev: ^2.18.0
  go_router_builder: ^2.7.0
```

### 3. Feature-First 폴더 구조

```bash
mkdir -p lib/src/features
mkdir -p lib/src/database
mkdir -p lib/src/routing
mkdir -p lib/src/shared/widgets
mkdir -p lib/src/shared/utils
mkdir -p assets/translations
```

**전체 구조:**
```
lib/
├── main.dart
└── src/
    ├── features/
    │   └── {domain}/
    │       ├── presentation/
    │       │   ├── {domain}_screen.dart
    │       │   ├── {domain}_notifier.dart
    │       │   └── widgets/
    │       ├── domain/
    │       │   └── {domain}.dart (Freezed 모델)
    │       └── data/
    │           ├── {domain}_repository.dart
    │           └── {domain}_dao.dart
    ├── database/
    │   └── app_database.dart (Drift - 전역)
    ├── routing/
    │   └── app_router.dart (GoRouter Typed Routes)
    └── shared/
        ├── widgets/
        └── utils/
```

---

## 코드 템플릿

### main.dart

```dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:easy_localization/easy_localization.dart';

import 'src/routing/app_router.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await EasyLocalization.ensureInitialized();

  runApp(
    EasyLocalization(
      supportedLocales: const [Locale('en'), Locale('ko')],
      path: 'assets/translations',
      fallbackLocale: const Locale('en'),
      child: const ProviderScope(child: MyApp()),
    ),
  );
}

class MyApp extends ConsumerWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final router = ref.watch(goRouterProvider);

    return MaterialApp.router(
      title: 'My App',
      theme: ThemeData(
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.blue),
        useMaterial3: true,
      ),
      routerConfig: router,
      localizationsDelegates: context.localizationDelegates,
      supportedLocales: context.supportedLocales,
      locale: context.locale,
    );
  }
}
```

### Drift Database (lib/src/database/app_database.dart)

```dart
import 'package:drift/drift.dart';
import 'package:drift_flutter/drift_flutter.dart';

part 'app_database.g.dart';

// Todo 테이블 예시
class Todos extends Table {
  IntColumn get id => integer().autoIncrement()();
  TextColumn get title => text().withLength(min: 1, max: 200)();
  TextColumn get description => text().nullable()();
  BoolColumn get isCompleted => boolean().withDefault(const Constant(false))();
  DateTimeColumn get createdAt => dateTime().withDefault(currentDateAndTime)();
  DateTimeColumn get completedAt => dateTime().nullable()();
}

@DriftDatabase(tables: [Todos])
class AppDatabase extends _$AppDatabase {
  AppDatabase() : super(_openConnection());

  @override
  int get schemaVersion => 1;

  static QueryExecutor _openConnection() {
    return driftDatabase(name: 'app_database');
  }
}
```

### GoRouter Typed Routes (lib/src/routing/app_router.dart)

```dart
import 'package:flutter/material.dart';
import 'package:go_router/go_router.dart';
import 'package:riverpod_annotation/riverpod_annotation.dart';

import '../features/todo/presentation/todo_list_screen.dart';
import '../features/todo/presentation/todo_detail_screen.dart';

part 'app_router.g.dart';

@riverpod
GoRouter goRouter(GoRouterRef ref) {
  return GoRouter(
    initialLocation: '/todos',
    routes: [
      GoRoute(
        path: '/todos',
        name: 'todos',
        builder: (context, state) => const TodoListScreen(),
        routes: [
          GoRoute(
            path: ':id',
            name: 'todo-detail',
            builder: (context, state) {
              final id = int.parse(state.pathParameters['id']!);
              return TodoDetailScreen(todoId: id);
            },
          ),
        ],
      ),
    ],
    errorBuilder: (context, state) => Scaffold(
      body: Center(child: Text('Page not found: ${state.uri}')),
    ),
  );
}
```

---

## Feature 템플릿 (Todo 예시)

### Domain Layer (lib/src/features/todo/domain/todo.dart)

```dart
import 'package:freezed_annotation/freezed_annotation.dart';

part 'todo.freezed.dart';
part 'todo.g.dart';

@freezed
class Todo with _$Todo {
  const factory Todo({
    required int id,
    required String title,
    String? description,
    @Default(false) bool isCompleted,
    required DateTime createdAt,
    DateTime? completedAt,
  }) = _Todo;

  factory Todo.fromJson(Map<String, dynamic> json) => _$TodoFromJson(json);
}
```

### Data Layer - Repository (lib/src/features/todo/data/todo_repository.dart)

```dart
import 'package:riverpod_annotation/riverpod_annotation.dart';
import 'package:drift/drift.dart';

import '../../../database/app_database.dart';
import '../domain/todo.dart';

part 'todo_repository.g.dart';

@riverpod
AppDatabase appDatabase(AppDatabaseRef ref) {
  return AppDatabase();
}

@riverpod
TodoRepository todoRepository(TodoRepositoryRef ref) {
  final db = ref.watch(appDatabaseProvider);
  return TodoRepository(db);
}

class TodoRepository {
  final AppDatabase _db;

  TodoRepository(this._db);

  Future<List<Todo>> getTodos() async {
    final rows = await _db.select(_db.todos).get();
    return rows.map(_rowToTodo).toList();
  }

  Future<Todo?> getTodoById(int id) async {
    final row = await (_db.select(_db.todos)
          ..where((t) => t.id.equals(id)))
        .getSingleOrNull();
    return row != null ? _rowToTodo(row) : null;
  }

  Future<int> createTodo(String title, String? description) async {
    return await _db.into(_db.todos).insert(
          TodosCompanion.insert(
            title: title,
            description: Value(description),
          ),
        );
  }

  Future<void> updateTodo(Todo todo) async {
    await (_db.update(_db.todos)..where((t) => t.id.equals(todo.id))).write(
      TodosCompanion(
        title: Value(todo.title),
        description: Value(todo.description),
        isCompleted: Value(todo.isCompleted),
        completedAt: Value(todo.completedAt),
      ),
    );
  }

  Future<void> toggleComplete(int id) async {
    final todo = await getTodoById(id);
    if (todo != null) {
      await (_db.update(_db.todos)..where((t) => t.id.equals(id))).write(
        TodosCompanion(
          isCompleted: Value(!todo.isCompleted),
          completedAt: Value(todo.isCompleted ? null : DateTime.now()),
        ),
      );
    }
  }

  Future<void> deleteTodo(int id) async {
    await (_db.delete(_db.todos)..where((t) => t.id.equals(id))).go();
  }

  Todo _rowToTodo(TodoData row) {
    return Todo(
      id: row.id,
      title: row.title,
      description: row.description,
      isCompleted: row.isCompleted,
      createdAt: row.createdAt,
      completedAt: row.completedAt,
    );
  }
}
```

### Presentation Layer - Notifier (lib/src/features/todo/presentation/todo_notifier.dart)

```dart
import 'package:riverpod_annotation/riverpod_annotation.dart';

import '../data/todo_repository.dart';
import '../domain/todo.dart';

part 'todo_notifier.g.dart';

enum TodoFilter { all, pending, completed }

@riverpod
class TodoFilterNotifier extends _$TodoFilterNotifier {
  @override
  TodoFilter build() => TodoFilter.all;

  void setFilter(TodoFilter filter) => state = filter;
}

@riverpod
class TodoListNotifier extends _$TodoListNotifier {
  @override
  Future<List<Todo>> build() async {
    return ref.watch(todoRepositoryProvider).getTodos();
  }

  Future<void> addTodo(String title, String? description) async {
    final repository = ref.read(todoRepositoryProvider);
    await repository.createTodo(title, description);
    ref.invalidateSelf();
  }

  Future<void> toggleComplete(int id) async {
    final repository = ref.read(todoRepositoryProvider);
    await repository.toggleComplete(id);
    ref.invalidateSelf();
  }

  Future<void> deleteTodo(int id) async {
    final repository = ref.read(todoRepositoryProvider);
    await repository.deleteTodo(id);
    ref.invalidateSelf();
  }
}

@riverpod
Future<List<Todo>> filteredTodos(FilteredTodosRef ref) async {
  final todos = await ref.watch(todoListNotifierProvider.future);
  final filter = ref.watch(todoFilterNotifierProvider);

  return switch (filter) {
    TodoFilter.all => todos,
    TodoFilter.pending => todos.where((t) => !t.isCompleted).toList(),
    TodoFilter.completed => todos.where((t) => t.isCompleted).toList(),
  };
}
```

### Presentation Layer - Screen (lib/src/features/todo/presentation/todo_list_screen.dart)

```dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:fluentui_system_icons/fluentui_system_icons.dart';
import 'package:go_router/go_router.dart';

import 'todo_notifier.dart';

class TodoListScreen extends ConsumerWidget {
  const TodoListScreen({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final todosAsync = ref.watch(filteredTodosProvider);
    final currentFilter = ref.watch(todoFilterNotifierProvider);

    return Scaffold(
      appBar: AppBar(
        title: const Text('Todos'),
        actions: [
          PopupMenuButton<TodoFilter>(
            icon: const Icon(FluentIcons.filter_24_regular),
            onSelected: (filter) {
              ref.read(todoFilterNotifierProvider.notifier).setFilter(filter);
            },
            itemBuilder: (context) => [
              PopupMenuItem(
                value: TodoFilter.all,
                child: Text('All ${currentFilter == TodoFilter.all ? '✓' : ''}'),
              ),
              PopupMenuItem(
                value: TodoFilter.pending,
                child: Text('Pending ${currentFilter == TodoFilter.pending ? '✓' : ''}'),
              ),
              PopupMenuItem(
                value: TodoFilter.completed,
                child: Text('Completed ${currentFilter == TodoFilter.completed ? '✓' : ''}'),
              ),
            ],
          ),
        ],
      ),
      body: todosAsync.when(
        data: (todos) {
          if (todos.isEmpty) {
            return const Center(
              child: Column(
                mainAxisAlignment: MainAxisAlignment.center,
                children: [
                  Icon(FluentIcons.checkbox_unchecked_24_regular, size: 64),
                  SizedBox(height: 16),
                  Text('No todos yet'),
                ],
              ),
            );
          }

          return ListView.builder(
            itemCount: todos.length,
            itemBuilder: (context, index) {
              final todo = todos[index];
              return ListTile(
                leading: IconButton(
                  icon: Icon(
                    todo.isCompleted
                        ? FluentIcons.checkbox_checked_24_filled
                        : FluentIcons.checkbox_unchecked_24_regular,
                  ),
                  onPressed: () {
                    ref.read(todoListNotifierProvider.notifier).toggleComplete(todo.id);
                  },
                ),
                title: Text(
                  todo.title,
                  style: TextStyle(
                    decoration: todo.isCompleted ? TextDecoration.lineThrough : null,
                  ),
                ),
                subtitle: todo.description != null ? Text(todo.description!) : null,
                trailing: const Icon(FluentIcons.chevron_right_24_regular),
                onTap: () => context.go('/todos/${todo.id}'),
              );
            },
          );
        },
        loading: () => const Center(child: CircularProgressIndicator()),
        error: (error, stack) => Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              const Icon(FluentIcons.error_circle_24_regular, size: 48, color: Colors.red),
              const SizedBox(height: 16),
              Text('Error: $error'),
              const SizedBox(height: 16),
              ElevatedButton(
                onPressed: () => ref.invalidate(todoListNotifierProvider),
                child: const Text('Retry'),
              ),
            ],
          ),
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () => _showAddTodoDialog(context, ref),
        child: const Icon(FluentIcons.add_24_regular),
      ),
    );
  }

  void _showAddTodoDialog(BuildContext context, WidgetRef ref) {
    final titleController = TextEditingController();
    final descriptionController = TextEditingController();

    showDialog(
      context: context,
      builder: (context) => AlertDialog(
        title: const Text('Add Todo'),
        content: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            TextField(
              controller: titleController,
              decoration: const InputDecoration(labelText: 'Title'),
              autofocus: true,
            ),
            const SizedBox(height: 8),
            TextField(
              controller: descriptionController,
              decoration: const InputDecoration(labelText: 'Description (optional)'),
            ),
          ],
        ),
        actions: [
          TextButton(
            onPressed: () => Navigator.pop(context),
            child: const Text('Cancel'),
          ),
          FilledButton(
            onPressed: () {
              if (titleController.text.isNotEmpty) {
                ref.read(todoListNotifierProvider.notifier).addTodo(
                      titleController.text,
                      descriptionController.text.isEmpty ? null : descriptionController.text,
                    );
                Navigator.pop(context);
              }
            },
            child: const Text('Add'),
          ),
        ],
      ),
    );
  }
}
```

### Detail Screen (lib/src/features/todo/presentation/todo_detail_screen.dart)

```dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:fluentui_system_icons/fluentui_system_icons.dart';
import 'package:go_router/go_router.dart';
import 'package:riverpod_annotation/riverpod_annotation.dart';

import '../data/todo_repository.dart';
import '../domain/todo.dart';
import 'todo_notifier.dart';

part 'todo_detail_screen.g.dart';

@riverpod
Future<Todo?> todoDetail(TodoDetailRef ref, int id) async {
  return ref.watch(todoRepositoryProvider).getTodoById(id);
}

class TodoDetailScreen extends ConsumerWidget {
  final int todoId;

  const TodoDetailScreen({super.key, required this.todoId});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final todoAsync = ref.watch(todoDetailProvider(todoId));

    return Scaffold(
      appBar: AppBar(
        title: const Text('Todo Detail'),
        actions: [
          IconButton(
            icon: const Icon(FluentIcons.delete_24_regular),
            onPressed: () async {
              final confirmed = await showDialog<bool>(
                context: context,
                builder: (context) => AlertDialog(
                  title: const Text('Delete Todo'),
                  content: const Text('Are you sure?'),
                  actions: [
                    TextButton(
                      onPressed: () => Navigator.pop(context, false),
                      child: const Text('Cancel'),
                    ),
                    TextButton(
                      onPressed: () => Navigator.pop(context, true),
                      child: const Text('Delete'),
                    ),
                  ],
                ),
              );

              if (confirmed == true && context.mounted) {
                await ref.read(todoListNotifierProvider.notifier).deleteTodo(todoId);
                if (context.mounted) context.go('/todos');
              }
            },
          ),
        ],
      ),
      body: todoAsync.when(
        data: (todo) {
          if (todo == null) {
            return const Center(child: Text('Todo not found'));
          }

          return ListView(
            padding: const EdgeInsets.all(16),
            children: [
              Card(
                child: ListTile(
                  leading: Icon(
                    todo.isCompleted
                        ? FluentIcons.checkbox_checked_24_filled
                        : FluentIcons.checkbox_unchecked_24_regular,
                  ),
                  title: const Text('Status'),
                  subtitle: Text(todo.isCompleted ? 'Completed' : 'Pending'),
                  onTap: () {
                    ref.read(todoListNotifierProvider.notifier).toggleComplete(todoId);
                    ref.invalidate(todoDetailProvider(todoId));
                  },
                ),
              ),
              const SizedBox(height: 8),
              Card(
                child: ListTile(
                  leading: const Icon(FluentIcons.text_24_regular),
                  title: const Text('Title'),
                  subtitle: Text(todo.title),
                ),
              ),
              if (todo.description != null) ...[
                const SizedBox(height: 8),
                Card(
                  child: ListTile(
                    leading: const Icon(FluentIcons.document_24_regular),
                    title: const Text('Description'),
                    subtitle: Text(todo.description!),
                  ),
                ),
              ],
              const SizedBox(height: 8),
              Card(
                child: ListTile(
                  leading: const Icon(FluentIcons.calendar_24_regular),
                  title: const Text('Created'),
                  subtitle: Text(todo.createdAt.toString()),
                ),
              ),
              if (todo.completedAt != null) ...[
                const SizedBox(height: 8),
                Card(
                  child: ListTile(
                    leading: const Icon(FluentIcons.checkmark_circle_24_regular),
                    title: const Text('Completed'),
                    subtitle: Text(todo.completedAt.toString()),
                  ),
                ),
              ],
            ],
          );
        },
        loading: () => const Center(child: CircularProgressIndicator()),
        error: (error, stack) => Center(child: Text('Error: $error')),
      ),
    );
  }
}
```

---

## Localization 설정

### assets/translations/en.json

```json
{
  "app_title": "My App",
  "todos": "Todos",
  "add_todo": "Add Todo",
  "no_todos": "No todos yet",
  "all": "All",
  "pending": "Pending",
  "completed": "Completed",
  "delete": "Delete",
  "cancel": "Cancel",
  "title": "Title",
  "description": "Description"
}
```

### assets/translations/ko.json

```json
{
  "app_title": "내 앱",
  "todos": "할 일",
  "add_todo": "할 일 추가",
  "no_todos": "할 일이 없습니다",
  "all": "전체",
  "pending": "진행 중",
  "completed": "완료",
  "delete": "삭제",
  "cancel": "취소",
  "title": "제목",
  "description": "설명"
}
```

### pubspec.yaml에 assets 추가

```yaml
flutter:
  uses-material-design: true
  assets:
    - assets/translations/
```

---

## 코드 생성 및 검증

```bash
# 코드 생성
dart run build_runner build --delete-conflicting-outputs

# 또는 Watch 모드 (개발 시 권장)
dart run build_runner watch

# 린트 검사
flutter analyze

# 테스트
flutter test

# 앱 실행
flutter run
```

---

## analysis_options.yaml

```yaml
include: package:flutter_lints/flutter.yaml

analyzer:
  exclude:
    - "**/*.g.dart"
    - "**/*.freezed.dart"
  errors:
    invalid_annotation_target: ignore

linter:
  rules:
    - always_use_package_imports
    - avoid_print
    - prefer_const_constructors
    - prefer_const_literals_to_create_immutables
    - prefer_single_quotes
    - prefer_final_locals
    - use_super_parameters
```

---

## 선택적 옵션

### FPDart (함수형 에러 핸들링)

```yaml
dependencies:
  fpdart: ^1.1.0
```

```dart
import 'package:fpdart/fpdart.dart';

// Repository에서 Either 사용
Future<Either<Failure, List<Todo>>> getTodos() async {
  try {
    final todos = await _db.select(_db.todos).get();
    return Right(todos.map(_rowToTodo).toList());
  } catch (e) {
    return Left(DatabaseFailure(e.toString()));
  }
}
```

### Google Fonts

```yaml
dependencies:
  google_fonts: ^6.2.1
```

```dart
import 'package:google_fonts/google_fonts.dart';

theme: ThemeData(
  textTheme: GoogleFonts.notoSansTextTheme(),
  useMaterial3: true,
),
```
