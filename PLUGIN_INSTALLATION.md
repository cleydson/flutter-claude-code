# Flutter Claude Code Plugin Installation Guide

This repository provides specialized Flutter development agents and skills for Claude Code, organized as modular plugins that you can install individually or as a complete suite.

## Quick Start

### Install Everything (Recommended for Most Users)

```bash
/plugin marketplace add /path/to/flutter-claude-code
/plugin install flutter-all@flutter-claude-code
```

This installs all 19 agents and the comprehensive patterns skill.

### Install Specific Categories

If you only need specific functionality, install individual plugin categories:

#### UI & Design
```bash
/plugin install flutter-ui@flutter-claude-code
```
**Includes:**
- `flutter-ui-designer` - Analyze designs and recommend Flutter widgets
- `flutter-ui-implementer` - Generate production-ready Flutter UI code
- `flutter-ui-comparison` - Compare implementation with designs for pixel-perfect accuracy
- `flutter-design-iteration-coordinator` - Complete design-to-implementation workflow

#### Testing & Performance
```bash
/plugin install flutter-testing-performance@flutter-claude-code
```
**Includes:**
- `flutter-testing` - Write unit, widget, and integration tests
- `flutter-performance-analyzer` - Profile and identify performance bottlenecks
- `flutter-performance-optimizer` - Apply performance optimization patterns

#### Architecture & State Management
```bash
/plugin install flutter-architecture@flutter-claude-code
```
**Includes:**
- `flutter-state-management` - Provider, Riverpod, BLoC, Redux, GetX
- `flutter-architect` - Clean Architecture, project structure, dependency injection

#### Platform Integration
```bash
/plugin install flutter-platform@flutter-claude-code
```
**Includes:**
- `flutter-ios-integration` - iOS-specific features and Swift/Objective-C integration
- `flutter-android-integration` - Android-specific features and Kotlin/Java integration
- `flutter-platform-channel-architect` - Cross-platform communication architecture
- `flutter-device-orchestrator` - iOS simulator and Android emulator management

#### Backend Integration
```bash
/plugin install flutter-backend@flutter-claude-code
```
**Includes:**
- `flutter-rest-api` - REST API integration with Dio
- `flutter-graphql` - GraphQL with graphql_flutter
- `flutter-firebase` - Firebase Auth, Firestore, Storage, FCM, Analytics
- `flutter-aws` - AWS Amplify, Cognito, S3, AppSync

#### Deployment
```bash
/plugin install flutter-deployment@flutter-claude-code
```
**Includes:**
- `flutter-ios-deployment` - App Store deployment, TestFlight, code signing
- `flutter-android-deployment` - Google Play deployment, app signing

#### Patterns & Best Practices
```bash
/plugin install flutter-patterns@flutter-claude-code
```
**Includes:**
- `flutter-patterns` skill - Comprehensive patterns for widgets, testing, performance, security, and animations

## Using the Agents

After installation, agents are available via the Agent tool. Example:

```
I need to implement a login screen from this Figma design
```

Claude Code will automatically use the `flutter-ui-designer` agent to analyze the design, then `flutter-ui-implementer` to generate code.

You can also explicitly request specific agents:

```
Use the flutter-state-management agent to help me implement BLoC for authentication
```

## Using the Skill

The `flutter-patterns` skill provides quick reference for Flutter best practices:

```
/flutter-patterns
```

This loads comprehensive patterns covering:
- Widget patterns (StatefulWidget, StatelessWidget, InheritedWidget, etc.)
- Testing patterns (unit, widget, integration, BLoC testing, mocking)
- Performance patterns (const constructors, keys, builders, RepaintBoundary)
- Security patterns (secure storage, API keys, SSL pinning, authentication)
- Animation patterns (AnimationController, Tween, Hero, implicit animations)

## Plugin Structure

```
flutter-claude-code/
├── .claude-plugin/
│   └── marketplace.json          # Marketplace configuration
├── flutter-all/                   # Complete suite (all agents + skill)
├── flutter-ui/                    # UI & Design agents
├── flutter-testing-performance/   # Testing & Performance agents
├── flutter-architecture/          # Architecture & State agents
├── flutter-platform/              # Platform Integration agents
├── flutter-backend/               # Backend Integration agents
├── flutter-deployment/            # Deployment agents
└── flutter-patterns/              # Patterns skill
```

## Updating Plugins

To update a plugin to the latest version:

```bash
/plugin update flutter-all@flutter-claude-code
```

Or update a specific category:

```bash
/plugin update flutter-ui@flutter-claude-code
```

## Uninstalling Plugins

To remove a plugin:

```bash
/plugin uninstall flutter-all@flutter-claude-code
```

## Available Agents Reference

### UI & Design (flutter-ui)
- **flutter-ui-designer** - Design analysis and widget hierarchy planning
- **flutter-ui-implementer** - Production-ready Flutter UI code generation
- **flutter-ui-comparison** - Visual comparison and design fidelity validation
- **flutter-design-iteration-coordinator** - Complete design-to-implementation workflow

### Testing & Performance (flutter-testing-performance)
- **flutter-testing** - Unit tests, widget tests, integration tests, BLoC testing
- **flutter-performance-analyzer** - DevTools profiling, jank detection, memory leak analysis
- **flutter-performance-optimizer** - Performance optimization implementation

### Architecture & State (flutter-architecture)
- **flutter-state-management** - Provider, Riverpod, BLoC, Redux, GetX implementation
- **flutter-architect** - Clean Architecture, project structure, DI patterns

### Platform Integration (flutter-platform)
- **flutter-ios-integration** - HealthKit, ARKit, platform channels, iOS permissions
- **flutter-android-integration** - WorkManager, sensors, platform channels, Android permissions
- **flutter-platform-channel-architect** - Cross-platform communication design
- **flutter-device-orchestrator** - Simulator/emulator launch, screenshot capture

### Backend Integration (flutter-backend)
- **flutter-rest-api** - Dio, HTTP clients, JSON serialization, error handling
- **flutter-graphql** - graphql_flutter, queries, mutations, subscriptions
- **flutter-firebase** - Authentication, Firestore, Storage, FCM, Analytics
- **flutter-aws** - Amplify, Cognito, S3, AppSync, Lambda

### Deployment (flutter-deployment)
- **flutter-ios-deployment** - App Store Connect, TestFlight, provisioning profiles
- **flutter-android-deployment** - Play Console, app signing, staged rollout

## Troubleshooting

### Plugin Not Found
Ensure you've added the marketplace first:
```bash
/plugin marketplace add /path/to/flutter-claude-code
```

### Agent Not Available
Verify the plugin is installed:
```bash
/plugin list
```

### Need Help?
Check the main README.md for detailed agent documentation and usage scenarios.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for information about the agent architecture and how to contribute new agents or improvements.

## License

This project is open source.
