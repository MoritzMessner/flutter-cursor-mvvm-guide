![Flutter](https://img.shields.io/badge/Flutter-%2302569B.svg?style=for-the-badge&logo=Flutter&logoColor=white)

**This guide is based on the official Flutter documentation:**

- [Architecture case study](https://docs.flutter.dev/app-architecture/case-study)
- [UI Layer](https://docs.flutter.dev/app-architecture/case-study/ui-layer)
- [Data Layer](https://docs.flutter.dev/app-architecture/case-study/data-layer)
- [Dependency Injection](https://docs.flutter.dev/app-architecture/case-study/dependency-injection)
- [Testing](https://docs.flutter.dev/app-architecture/case-study/testing)
- [Command Pattern](https://docs.flutter.dev/app-architecture/design-patterns/command)
- [Result Pattern](https://docs.flutter.dev/app-architecture/design-patterns/result)

---

# Flutter Cursor MVVM Guide

A Flutter development guide with MVVM architecture for better Cursor AI prompts and consistent development.

## What is this?

This guide contains **Cursor Rules** that are automatically applied during Flutter development to help you:

- Implement **MVVM Architecture** correctly
- Use **Repository Pattern**
- Follow **consistent code structure**
- Apply **best practices** automatically
- Get **better AI prompts** from Cursor

## Installation

### Step 1: Copy the file
Copy the file [`flutter_development_guide.mdc`](.cursor/rules/flutter_development_guide.mdc) from this repository.

### Step 2: Add to your project
Create the `.cursor/rules/` folder in your Flutter project root and place the file there:

```
your-flutter-project/
├── .cursor/
│   └── rules/
│       └── flutter_development_guide.mdc  ← Place here
├── lib/
├── pubspec.yaml
└── ...
```

### Step 3: Done!
The Cursor Rules are now active and will be automatically applied during development.

## What does the guide include?

### Architecture Rules
- **MVVM Pattern** with ChangeNotifier ViewModels
- **Repository Pattern** for data management
- **Provider** for dependency injection
- **Strict folder structure** guidelines

### Design Patterns
- **Command Pattern** for async operations
- **Result Pattern** for error handling
- **Key-Value Storage** for user preferences

### Flutter Best Practices
- Prefer **StatelessWidget** where possible
- **Proper State Management** with Provider
- **Clean Code** conventions
- **Security Guidelines**

## Contributing

Improvements and extensions are welcome! Feel free to create an issue or pull request.

## License

MIT License - see [LICENSE](LICENSE) for details.