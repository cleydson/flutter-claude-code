# Flutter Development Agents for Claude Code

19 specialized agents + 1 patterns skill for end-to-end Flutter development — from Figma design to App Store deployment.

## Installation

```bash
# Add marketplace
/plugin marketplace add https://github.com/cleydson/flutter-claude-code

# Install everything (recommended)
/plugin install flutter-all@flutter-claude-code
```

### Individual Categories

| Category | Install Command | Includes |
|----------|----------------|----------|
| UI & Design | `/plugin install flutter-ui@flutter-claude-code` | ui-designer, ui-implementer, ui-comparison, design-iteration-coordinator |
| Architecture | `/plugin install flutter-architecture@flutter-claude-code` | architect, state-management |
| Platform | `/plugin install flutter-platform@flutter-claude-code` | ios-integration, android-integration, platform-channel-architect, device-orchestrator |
| Backend | `/plugin install flutter-backend@flutter-claude-code` | rest-api, firebase, aws, graphql |
| Testing & Perf | `/plugin install flutter-testing-performance@flutter-claude-code` | testing, performance-analyzer, performance-optimizer |
| Deployment | `/plugin install flutter-deployment@flutter-claude-code` | ios-deployment, android-deployment |
| Patterns Skill | `/plugin install flutter-patterns@flutter-claude-code` | Widget, testing, performance, security, animation patterns |

## Agents

### Design Pipeline
- **flutter-ui-designer** — Analyze designs, map to Flutter widget hierarchies
- **flutter-ui-implementer** — Generate production-ready Flutter UI code
- **flutter-device-orchestrator** — Manage iOS simulators and Android emulators
- **flutter-ui-comparison** — Compare implementation vs original design
- **flutter-design-iteration-coordinator** — Orchestrate full design-to-code loop

### Architecture
- **flutter-architect** — Clean Architecture, project structure, DI, navigation
- **flutter-state-management** — Provider, Riverpod, BLoC, Redux, GetX

### Platform Integration
- **flutter-ios-integration** — iOS features, Swift/ObjC, permissions, frameworks
- **flutter-android-integration** — Android features, Kotlin/Java, Gradle config
- **flutter-platform-channel-architect** — Cross-platform channel design

### Backend
- **flutter-rest-api** — HTTP clients (Dio), JSON serialization, auth
- **flutter-firebase** — Auth, Firestore, Storage, FCM, Analytics
- **flutter-aws** — Amplify, Cognito, S3, AppSync, Lambda
- **flutter-graphql** — graphql_flutter, queries, mutations, subscriptions

### Quality & Deployment
- **flutter-testing** — Unit, widget, integration, and golden tests
- **flutter-performance-analyzer** — DevTools profiling, bottleneck detection
- **flutter-performance-optimizer** — Widget, image, list, animation optimization
- **flutter-ios-deployment** — App Store, TestFlight, code signing, provisioning
- **flutter-android-deployment** — Play Store, app signing, staged rollout

## Patterns Skill

Use `/flutter-patterns` for quick reference on widget, testing, performance, security, and animation best practices.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for architecture details and contribution guidelines.
