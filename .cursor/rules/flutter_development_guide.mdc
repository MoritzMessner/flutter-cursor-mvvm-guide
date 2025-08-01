---
description: "Comprehensive Flutter development guide using MVVM architecture"
globs: 
alwaysApply: true
---

# 📱 Flutter Development Guide - MVVM Architecture

This document provides comprehensive guidance for developing Flutter applications following MVVM architecture patterns and best practices. This guide is project-agnostic and can be applied to any Flutter project.

---

## 📱 Project Overview

This guide covers cross-platform mobile app development (iOS, iPad, Android) built with Flutter, using a clean MVVM architecture with Repository pattern. The patterns and practices outlined here are applicable to any Flutter project regardless of domain or complexity.

### Key Technologies
- **Framework**: Flutter
- **State Management**: Provider pattern with ChangeNotifier ViewModels
- **Backend**: API services (REST/GraphQL/Firebase)
- **Architecture**: MVVM with Repository pattern
- **CI/CD**: Standard Flutter CI/CD pipeline
- **Version Control**: Git with feature branches

---

## 🏗️ Strict Project Architecture Rules

### 1. Directory Structure (MUST FOLLOW)

```
lib/
├── ui/                                 # UI Layer
│   ├── core/
│   │   ├── ui/                        # Shared widgets
│   │   ├── themes/                    # Theme definitions
│   │   └── view_model/                # Core view models
│   └── <feature>/                     # Feature-specific UI
│       ├── view_model/                # ViewModels for feature
│       │   └── <feature>_view_model.dart
│       └── widgets/                   # Feature screens & widgets
│           └── <feature>_screen.dart  # Main screen widget
├── domain/
│   └── models/                        # Pure business models
│       └── <model_name>.dart
├── data/
│   ├── models/                        # Firestore data models
│   │   └── <record_name>_record.dart
│   ├── repositories/                  # Data access layer
│   │   └── <feature>_repository.dart
│   └── services/                      # Application services
│       └── <service_name>_service.dart
├── config/                            # App configuration
├── utils/                             # Utilities
├── routing/                           # Route handling
└── main.dart                          # App entry point
```

### 2. MVVM Implementation Rules

#### ViewModels MUST:
- Extend `ChangeNotifier`
- Be injected with repositories via constructor
- Handle all business logic and state management
- Never import UI widgets
- Use private fields with public getters
- Call `notifyListeners()` after state changes

**Naming**: `<Feature>ViewModel`
**Location**: `lib/ui/<feature>/view_model/<feature>_view_model.dart`

```dart
class HomeViewModel extends ChangeNotifier {
  HomeViewModel({
    required DataRepository dataRepository,
    required UserRepository userRepository,
  }) : _dataRepository = dataRepository,
       _userRepository = userRepository;
       
  final DataRepository _dataRepository;
  final UserRepository _userRepository;
  
  bool _isLoading = false;
  bool get isLoading => _isLoading;
  
  void setLoading(bool value) {
    _isLoading = value;
    notifyListeners();
  }
}
```

#### Screens (Views) MUST:
- Be StatelessWidget when possible
- Have minimal logic, delegate everything to ViewModel
- Use Provider to inject and access ViewModel
- Have one-to-one relationship with ViewModel

**Naming**: `<Feature>Screen`
**Location**: `lib/ui/<feature>/widgets/<feature>_screen.dart`

```dart
class HomeScreen extends StatelessWidget {
  const HomeScreen({super.key, required this.viewModel});
  final HomeViewModel viewModel;
  
  @override
  Widget build(BuildContext context) {
    return ChangeNotifierProvider.value(
      value: viewModel,
      child: const _HomeScreenContent(),
    );
  }
}
```

### 3. Repository Pattern Rules

#### Repositories MUST:
- Extend `BaseRepository`
- Handle all data access logic
- Be injected into ViewModels
- Return domain models or data models (not UI widgets)
- Use the provided query utilities from BaseRepository

**Naming**: `<Feature>Repository`
**Location**: `lib/data/repositories/<feature>_repository.dart`

#### Services MUST:
- Be stateless classes
- Handle cross-cutting concerns (audio, analytics, notifications)
- Be used only by Repositories or AppState
- NOT handle data access directly

**Naming**: `<Feature>Service`
**Location**: `lib/data/services/<feature>_service.dart`

### 4. Model Organization Rules

#### Data Models (`lib/data/models/`)
- API-specific models with network logic
- Named with `_dto.dart` suffix for DTOs or `_model.dart` for data models
- Include serialization/deserialization for JSON

#### Domain Models (`lib/domain/models/`)
- Pure business objects without external dependencies
- Framework-agnostic
- Contain business logic specific to your application domain

#### Response Models (`lib/data/models/responses/`)
- API response wrapper models
- Handle pagination, error states, metadata

---

## 🔧 Development Guidelines

### 1. Branch Strategy

- **main**: Production-ready code
- **feature/[description]**: Feature branches from main

### 2. Pull Request Format

**Branch**: `feature/brief-description`
**Title**: `Brief description of changes`

**Required checklist:**
- [ ] Follows MVVM architecture
- [ ] Uses correct repository pattern
- [ ] Proper dependency injection
- [ ] Follows naming conventions
- [ ] Uses shared UI components where appropriate

### 3. Code Changes Only

**Focus on code implementation only:**
- Make the necessary code changes
- No need to run commands or generate assets
- Implementation is sufficient

### 4. Dependencies and State Management

- Use **Provider** for dependency injection
- Services and Repositories at app level
- ViewModels injected with required repositories
- NO direct API calls in ViewModels (use repositories)
- All network logic should be in repositories

---

## 🎯 Core Design Patterns

Following Flutter's official architecture design patterns, implement these key patterns to improve code maintainability and error handling in any Flutter application.

### Command Pattern

The Command pattern wraps ViewModel actions and handles different states (running, complete, error) automatically. This prevents UI rendering errors and standardizes user interaction handling.

#### Command Implementation

```dart
abstract class Command<T> extends ChangeNotifier {
  bool _running = false;
  bool get running => _running;
  
  Result<T>? _result;
  bool get error => _result is Error;
  bool get completed => _result is Ok;
  Result<T>? get result => _result;
  
  void clearResult() {
    _result = null;
    notifyListeners();
  }
}

class Command0<T> extends Command<T> {
  Command0(this._action);
  final Future<Result<T>> Function() _action;
  
  Future<void> execute() async {
    if (_running) return; // Prevent multiple executions
    
    _running = true;
    _result = null;
    notifyListeners();
    
    try {
      _result = await _action();
    } finally {
      _running = false;
      notifyListeners();
    }
  }
}
```

#### Using Commands in ViewModels

```dart
class DataListViewModel extends ChangeNotifier {
  DataListViewModel({
    required DataRepository dataRepository,
  }) : _dataRepository = dataRepository {
    loadData = Command0(_loadData)..execute();
    refreshData = Command0(_refreshData);
  }
  
  final DataRepository _dataRepository;
  late final Command0<List<DataModel>> loadData;
  late final Command0<List<DataModel>> refreshData;
  
  Future<Result<List<DataModel>>> _loadData() async {
    return await _dataRepository.getData();
  }
  
  Future<Result<List<DataModel>>> _refreshData() async {
    return await _dataRepository.refreshData();
  }
}
```

#### Command Usage in UI

```dart
class DataListScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: ListenableBuilder(
        listenable: widget.viewModel.loadData,
        builder: (context, child) {
          if (widget.viewModel.loadData.running) {
            return const Center(child: CircularProgressIndicator());
          }
          
          if (widget.viewModel.loadData.error) {
            return Center(
              child: Text('Error: ${widget.viewModel.loadData.result?.error}'),
            );
          }
          
          return child!;
        },
        child: RefreshIndicator(
          onRefresh: () => widget.viewModel.refreshData.execute(),
          child: DataListView(items: widget.viewModel.dataItems),
        ),
      ),
    );
  }
}
```

### Result Pattern

The Result pattern provides explicit error handling by wrapping function returns in `Result<T>` objects instead of throwing exceptions.

#### Result Class Implementation

```dart
sealed class Result<T> {
  const Result();
  
  factory Result.ok(T value) = Ok._;
  factory Result.error(Exception error) = Error._;
}

final class Ok<T> extends Result<T> {
  const Ok._(this.value);
  final T value;
  
  @override
  String toString() => 'Result<$T>.ok($value)';
}

final class Error<T> extends Result<T> {
  const Error._(this.error);
  final Exception error;
  
  @override
  String toString() => 'Result<$T>.error($error)';
}
```

#### Repository Implementation with Result

```dart
class DataRepository extends BaseRepository {
  DataRepository(this._apiService);
  
  final ApiService _apiService;
  
  Future<Result<List<DataModel>>> getData() async {
    try {
      final data = await _apiService.fetchData();
      return Result.ok(data);
    } on NetworkException catch (e) {
      return Result.error(e);
    } on Exception catch (e) {
      return Result.error(e);
    }
  }
  
  Future<Result<DetailModel>> getDetailInfo(String itemId) async {
    try {
      final detail = await _apiService.fetchDetailInfo(itemId);
      return Result.ok(detail);
    } on Exception catch (e) {
      return Result.error(e);
    }
  }
}
```

#### Handling Results in ViewModels

```dart
class DetailViewModel extends ChangeNotifier {
  Future<void> loadDetailInfo(String itemId) async {
    final result = await _dataRepository.getDetailInfo(itemId);
    
    switch (result) {
      case Ok<DetailModel>():
        _detailInfo = result.value;
        _error = null;
      case Error<DetailModel>():
        _error = result.error;
        _detailInfo = null;
    }
    notifyListeners();
  }
}
```

### Key-Value Data Storage Pattern

For storing user preferences, app settings, and simple data persistence using SharedPreferences.

#### Storage Service Implementation

```dart
class SharedPreferencesService {
  static const String _kFavoriteItems = 'favoriteItems';
  static const String _kNotificationSettings = 'notificationSettings';
  static const String _kThemeMode = 'themeMode';
  
  Future<void> setFavoriteItems(List<String> items) async {
    final prefs = await SharedPreferences.getInstance();
    await prefs.setStringList(_kFavoriteItems, items);
  }
  
  Future<List<String>> getFavoriteItems() async {
    final prefs = await SharedPreferences.getInstance();
    return prefs.getStringList(_kFavoriteItems) ?? [];
  }
  
  Future<void> setNotificationsEnabled(bool enabled) async {
    final prefs = await SharedPreferences.getInstance();
    await prefs.setBool(_kNotificationSettings, enabled);
  }
  
  Future<bool> getNotificationsEnabled() async {
    final prefs = await SharedPreferences.getInstance();
    return prefs.getBool(_kNotificationSettings) ?? true;
  }
}
```

#### Settings Repository

```dart
class SettingsRepository {
  SettingsRepository(this._preferencesService);
  
  final SharedPreferencesService _preferencesService;
  final _settingsController = StreamController<UserSettings>.broadcast();
  
  Future<Result<UserSettings>> getSettings() async {
    try {
      final favoriteItems = await _preferencesService.getFavoriteItems();
      final notificationsEnabled = await _preferencesService.getNotificationsEnabled();
      
      final settings = UserSettings(
        favoriteItems: favoriteItems,
        notificationsEnabled: notificationsEnabled,
      );
      
      return Result.ok(settings);
    } on Exception catch (e) {
      return Result.error(e);
    }
  }
  
  Future<Result<void>> updateNotificationSettings(bool enabled) async {
    try {
      await _preferencesService.setNotificationsEnabled(enabled);
      _notifySettingsChanged();
      return Result.ok(null);
    } on Exception catch (e) {
      return Result.error(e);
    }
  }
  
  Stream<UserSettings> observeSettings() => _settingsController.stream;
  
  void _notifySettingsChanged() async {
    final result = await getSettings();
    if (result is Ok<UserSettings>) {
      _settingsController.add(result.value);
    }
  }
}
```

#### Settings ViewModel with Commands

```dart
class SettingsViewModel extends ChangeNotifier {
  SettingsViewModel(this._settingsRepository) {
    loadSettings = Command0(_loadSettings)..execute();
    toggleNotifications = Command0(_toggleNotifications);
  }
  
  final SettingsRepository _settingsRepository;
  
  late final Command0<UserSettings> loadSettings;
  late final Command0<void> toggleNotifications;
  
  UserSettings? _settings;
  UserSettings? get settings => _settings;
  
  Future<Result<UserSettings>> _loadSettings() async {
    final result = await _settingsRepository.getSettings();
    if (result is Ok<UserSettings>) {
      _settings = result.value;
    }
    return result;
  }
  
  Future<Result<void>> _toggleNotifications() async {
    if (_settings == null) return Result.error(Exception('Settings not loaded'));
    
    final newValue = !_settings!.notificationsEnabled;
    final result = await _settingsRepository.updateNotificationSettings(newValue);
    
    if (result is Ok<void>) {
      _settings = _settings!.copyWith(notificationsEnabled: newValue);
    }
    
    return result;
  }
}
```

### Pattern Benefits for Flutter Applications

1. **Command Pattern**:
   - Prevents multiple API calls when user taps refresh/submit buttons quickly
   - Automatically handles loading states for data operations
   - Standardizes error handling across different features
   - Simplifies UI state management
   - Provides consistent user feedback during async operations

2. **Result Pattern**:
   - Makes error handling explicit in data fetching operations
   - Prevents uncaught exceptions from network failures
   - Forces developers to handle both success and error cases
   - Improves debugging of API integration issues
   - Creates more robust and predictable code

3. **Key-Value Storage**:
   - Stores user preferences and favorite items
   - Persists notification and app settings
   - Caches user configuration across app restarts
   - Maintains theme preferences and UI state
   - Provides offline-first user experience

---

## 🎯 Current Implementation Status

### ✅ Completed Architecture
- Repository pattern implementation
- Model organization cleanup  
- MVVM structure established
- Backward compatibility maintained

### 🔄 Migration Guidelines

**For New Code (REQUIRED):**
```dart
// Use repositories directly
import 'package:your_app/data/repositories/data_repository.dart';

final repository = DataRepository();
final data = await repository.fetchData();
```

**Legacy Code (If Any):**
```dart
// Direct API calls should be replaced with repository pattern
// Move all API logic into repositories
```

### 📋 Repository Structure
Repositories should be created based on your application's domain needs. Common patterns include:
- `DataRepository`: Core application data management
- `UserRepository`: User-related operations and preferences
- `AuthRepository`: Authentication and authorization
- `SettingsRepository`: Application configuration and user preferences
- `NotificationRepository`: Push notifications and alerts
- `CacheRepository`: Local data caching and offline support

**Note**: Create repositories as needed for your specific project domain. The examples above are common patterns that can be adapted to any application.

---

## 🚀 Development Workflow

### 1. Adding New Features

1. **Create Directory Structure**:
   ```
   lib/ui/<feature>/
   ├── view_model/
   │   └── <feature>_view_model.dart
   └── widgets/
       └── <feature>_screen.dart
   ```

2. **Create ViewModel**: Extend ChangeNotifier, inject repositories
3. **Create Screen**: StatelessWidget, use Provider for ViewModel
4. **Add Repository if needed**: Extend BaseRepository
5. **Add Models**: Domain in `domain/models/`, Data in `data/models/`
6. **Register Dependencies**: Use Provider at app level

### 2. Code Implementation Focus

**Concentrate on code changes only:**
- Implement the requested features
- Follow MVVM architecture patterns
- Use correct repository patterns
- No need to run or test the code

---

## 🔍 Code Implementation Guidelines

### Focus Areas

1. **Architecture compliance**: Ensure MVVM pattern is followed
2. **Repository usage**: Use repositories instead of direct data access
3. **Provider hierarchy**: Check dependency injection structure
4. **BaseRepository extension**: Verify proper repository implementation
5. **Code quality**: Follow naming conventions and patterns

---

## 📚 Key Files to Reference

- `lib/data/ARCHITECTURE_SUMMARY.md`: Architecture refactoring summary (create as needed)
- `lib/data/repositories/README.md`: Repository usage guide (create as project grows)
- `README.md`: Project setup and configuration
- `.cursor/rules/flutter_development_guide.md`: This comprehensive guide

---

## 🎯 Development Priorities

1. **Always follow MVVM**: Use ViewModels for logic, Screens for UI only
2. **Use Repository pattern**: No direct API calls in ViewModels
3. **Implement proper data flow**: Repository → ViewModel → Screen
4. **Code implementation focus**: Concentrate on proper code structure
5. **Follow naming conventions**: Consistent file and class naming
6. **Use shared components**: Leverage existing UI components in core/ui/

---

## 🔐 Security & Best Practices

- Never commit sensitive data (API keys handled via environment variables)
- Use environment variables for configuration
- Implement proper API authentication and authorization
- Implement comprehensive error handling in repositories
- Use type-safe repository methods
- Handle network timeouts and offline scenarios
- Implement proper caching strategies for application data
- Follow principle of least privilege for data access

---

This guide ensures consistent development practices and helps navigate any Flutter application codebase effectively while maintaining architectural integrity.