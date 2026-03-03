# Contributing to Flutter Claude Code

This document provides comprehensive information about the architecture, implementation, and workflows of the Flutter Claude Code agent ecosystem.

**Table of Contents:**
- [Architecture Overview](#architecture-overview)
- [Agent Categories](#agent-categories)
- [Implementation Guide](#implementation-guide)
- [Workflow Diagrams](#workflow-diagrams)
- [Contributing](#contributing)

---

## Architecture Overview

This Flutter development ecosystem consists of **19 specialized agents** and **1 comprehensive skill** organized into **6 major categories** that work together to enable seamless Flutter development from design to production deployment on iOS and Android.

### Agent Categories

1. **Design-to-Implementation Pipeline** (5 agents)
   - Flutter UI Designer
   - Flutter UI Implementer
   - Flutter Device Orchestrator
   - UI Comparison & Validation Specialist
   - Design Iteration Coordinator

2. **Architecture & Code Organization** (2 agents)
   - Flutter Architect
   - Flutter State Management Specialist

3. **Platform-Specific Development** (3 agents)
   - iOS Integration Specialist
   - Android Integration Specialist
   - Platform Channel Architect

4. **API Integration & Backend Connectivity** (4 agents)
   - Flutter REST API Specialist
   - Flutter Firebase Integration Expert
   - Flutter AWS Integration Expert
   - Flutter GraphQL Integration Expert

5. **Performance Optimization** (2 agents)
   - Flutter Performance Analyzer
   - Flutter Performance Optimizer

6. **Quality Assurance & Deployment** (3 agents)
   - Flutter Testing Expert
   - Flutter Deployment Specialist (iOS)
   - Flutter Deployment Specialist (Android)

### Skills

- **flutter-patterns**: Comprehensive patterns for widgets, testing, performance, security, and animations

---

## Quick Start for Contributors

### Setting Up the Development Environment

1. Clone the repository
2. Ensure you have access to the `.claude/agents/` and `.claude/skills/` directories
3. The plugin directories use symlinks to avoid duplication

### File Structure

```
flutter-claude-code/
├── .claude/                          # Source of truth
│   ├── agents/                       # All 19 agent definitions
│   └── skills/                       # All skills
├── .claude-plugin/
│   └── marketplace.json              # Plugin marketplace config
├── flutter-all/                      # Complete suite
│   ├── agents -> ../../.claude/agents  (symlink)
│   └── skills/
│       └── flutter-patterns -> ...    (symlink)
├── flutter-ui/                       # UI category
│   └── agents/                       # Symlinks to specific agents
├── flutter-testing-performance/
├── flutter-architecture/
├── flutter-platform/
├── flutter-backend/
├── flutter-deployment/
└── flutter-patterns/
```

**Important**: Agent files in `.claude/agents/` are the source of truth. Plugin directories contain symlinks to these files.

---

## Additional Documentation

For more details, see:
- `WORKFLOW_DIAGRAMS.md` - Visual workflow diagrams
- `ARCHITECTURE_NOTES.md` - Technical architecture notes
- `AGENT_USAGE_SCENARIOS.md` - Practical usage examples

---

## Implementation Guide

### Creating or Modifying Agents

1. **Edit source files** in `.claude/agents/`
2. **Test thoroughly** with real Flutter projects
3. **Update documentation** if changing agent behavior
4. **Submit pull request** with description of changes

### Agent Structure

Each agent should follow this structure:

```markdown
# Agent Name

## Purpose
Brief description of what this agent does

## Responsibilities
- Specific task 1
- Specific task 2
- etc.

## Expertise Areas
- Knowledge domain 1
- Knowledge domain 2
- etc.

## Usage Examples
Examples of when to use this agent

## Tools Required
- Tool 1
- Tool 2
- etc.
```

### Testing Agents

Before submitting changes:
1. Test with multiple Flutter projects
2. Verify agent works in isolation
3. Test agent coordination with related agents
4. Check documentation accuracy

---

## Workflow Patterns

### Design-to-Implementation Workflow

```
Design Input → UI Designer → UI Implementer → Device Orchestrator →
UI Comparison → Design Iteration Coordinator → Pixel-Perfect Implementation
```

**Success Criteria**: Fidelity score >95%

### Complete Development Lifecycle

```
Design → Architecture → Implementation → Backend Integration →
Performance Optimization → Testing → Deployment
```

### Platform Integration Workflow

```
Native Feature Needed → Platform Channel Architect →
iOS/Android Integration Specialists → Native Code Implementation
```

For detailed workflow diagrams, see `WORKFLOW_DIAGRAMS.md`.

---

## Adding New Agents

To add a new agent:

1. **Identify the need**: What gap does this agent fill?
2. **Define responsibilities**: What will this agent do?
3. **Create agent file** in `.claude/agents/new-agent.md`
4. **Add to appropriate plugin**: Create symlink in relevant plugin directory
5. **Update marketplace**: Add to `.claude-plugin/marketplace.json` if needed
6. **Document usage**: Add examples to `AGENT_USAGE_SCENARIOS.md`
7. **Test thoroughly**: Verify agent works as expected

### Agent vs Skill Decision

**Create as Agent when:**
- Requires multi-step workflows
- Needs tool usage (Bash, Read, Write, etc.)
- Requires orchestration between operations
- Maintains context across interactions

**Create as Skill when:**
- Provides reusable patterns/templates
- Represents best practices reference
- Offers on-demand knowledge
- Augments other agents

---

## Plugin Development

### Creating a New Plugin Category

1. Create plugin directory: `mkdir flutter-new-category`
2. Create `.claude-plugin/` subdirectory
3. Add `plugin.json` with metadata
4. Create `agents/` directory
5. Add symlinks to relevant agents from `.claude/agents/`
6. Add plugin to marketplace.json
7. Create README.md for the plugin

### Plugin Metadata (plugin.json)

```json
{
  "name": "plugin-name",
  "description": "Brief description of what this plugin provides",
  "version": "1.0.0",
  "author": {
    "name": "Flutter Claude Code Contributors"
  }
}
```

---

## Documentation Guidelines

### When to Update Documentation

- Adding new agents → Update `AGENT_USAGE_SCENARIOS.md`
- Changing architecture → Update this file
- Modifying workflows → Update `WORKFLOW_DIAGRAMS.md`
- Plugin changes → Update `PLUGIN_INSTALLATION.md`

### Documentation Style

- Use clear, concise language
- Provide practical examples
- Include code samples when relevant
- Keep consistent formatting

---

## Code Quality Standards

### Agent Definition Quality

- Clear, focused purpose
- Well-defined responsibilities
- Comprehensive expertise areas
- Practical usage examples
- Accurate tool requirements

### Dart 3 Coding Standards

When writing or updating code examples in agents and skills:

- **Use Dart 3 syntax**: Records, patterns, sealed classes, class modifiers
- **Prefer `switch` expressions** over `switch` statements where appropriate
- **Use pattern matching** for type checks and destructuring
- **Use `final` by default** for local variables; `var` only when mutation is needed
- **Use `Isolate.run()`** instead of `compute()` for background work
- **Use `dart run`** instead of `flutter pub run` for build_runner and other tools
- **Prefer `const` constructors** everywhere possible
- **Use `sealed` classes** for state/event hierarchies (BLoC, Riverpod)
- **Use enhanced enums** with methods and fields
- **Use records** for returning multiple values instead of tuples or custom classes

```dart
// Dart 3 patterns example
sealed class Result<T> {
  const Result();
}
final class Success<T> extends Result<T> {
  final T value;
  const Success(this.value);
}
final class Failure<T> extends Result<T> {
  final String message;
  const Failure(this.message);
}

// Pattern matching
final message = switch (result) {
  Success(value: final v) => 'Got: $v',
  Failure(message: final m) => 'Error: $m',
};
```

### Testing Standards

- Test with multiple Flutter versions
- Verify cross-platform behavior
- Check edge cases
- Validate error handling
- Use `dart run` instead of `flutter pub run` for code generation

---

## Release Process

1. Update version in relevant `plugin.json` files
2. Update CHANGELOG (if exists)
3. Test all plugins
4. Tag release in git
5. Update documentation

---

## Getting Help

- Review `AGENT_USAGE_SCENARIOS.md` for practical examples
- Check `WORKFLOW_DIAGRAMS.md` for visual workflow guides
- See `ARCHITECTURE_NOTES.md` for technical architecture details
- Read the agent definitions in `.claude/agents/` for specific capabilities

---

## License

This project is open source.

---

## Acknowledgments

Built on the official Flutter documentation at https://docs.flutter.dev

See `ARCHITECTURE_NOTES.md` for modern Flutter architecture recommendations including Impeller, Dart 3, and platform requirements.

---

**Note**: This is a consolidated guide. For detailed architecture specifications, implementation guides, and workflow diagrams, refer to the individual documentation files listed above.
