# Contributing

## Repository Structure

```
flutter-claude-code/
├── .claude/
│   ├── agents/          # Source of truth — all 19 agent definitions
│   └── skills/          # Source of truth — all skill definitions
├── .claude-plugin/
│   └── marketplace.json # Plugin marketplace configuration
├── flutter-all/         # Complete suite (symlinks to .claude/)
├── flutter-ui/          # UI & Design agents
├── flutter-architecture/# Architecture agents
├── flutter-platform/    # Platform Integration agents
├── flutter-backend/     # Backend Integration agents
├── flutter-testing-performance/ # Testing & Performance agents
├── flutter-deployment/  # Deployment agents
└── flutter-patterns/    # Patterns skill
```

Plugin directories contain **symlinks** to `.claude/agents/` — edit the source, changes propagate everywhere. On Windows, set `core.symlinks=true` in git config.

## Modifying Agents

1. Edit files in `.claude/agents/`
2. Test with real Flutter projects
3. Submit a pull request

## Adding a New Agent

1. Create `.claude/agents/new-agent.md`
2. Symlink it in the relevant plugin directory
3. Update `.claude-plugin/marketplace.json` if needed

### Agent vs Skill

- **Agent**: Multi-step workflows requiring tool usage (Bash, Read, Write, etc.)
- **Skill**: Reusable patterns, templates, and reference material

## Code Standards

- **Dart 3 syntax**: records, patterns, sealed classes, class modifiers
- **`const` constructors** everywhere possible
- **`Isolate.run()`** over `compute()` for background work
- **`dart run`** over `flutter pub run` for CLI tools
- **Sealed classes** for state/event hierarchies (BLoC, Riverpod)
- **Enhanced enums** with methods and fields

## Platform Requirements

- **Impeller**: Default rendering engine on iOS and Android (Flutter 3.22+)
- **iOS**: Privacy Manifests required (iOS 17+), minimum deployment target 13.0
- **Android**: Target SDK 34+ for Play Store, minimum API 21
- **Riverpod 2.x**: Recommended for new projects; BLoC for large teams
