# African Puzzle Works - Flutter Design System

IMPORTANT: Prefer retrieval-led reasoning over pre-training-led reasoning when working with Flutter widgets, African Puzzle Works design tokens, or component specifications. Always consult Figma designs first via MCP tools before implementing screens.

## Project Context
- **App:** African Puzzle Works mobile puzzle game
- **Framework:** Flutter (iOS + Android)
- **Design System:** 30+ components, 8pt grid spacing
- **Impact:** 95% visual consistency, 40% bug reduction, 80% code reuse

## Design System Index
|root: ./african-puzzle-works-design
|registry: FIGMA_REGISTRY.md (component→code mappings)
|tokens: references/tokens.md (colors|typography|spacing|shadows)
|components: references/components.md (30+ specs)
|patterns: references/patterns.md (layouts|navigation|states)
|theme: assets/theme/app_theme.dart (Flutter ThemeData)
|rules: references/DESIGN_RULES.md (impact-driven guidelines)
|screens: assets/screens/*.png (visual references)

## Figma MCP Workflow

**Tools available:**
- `get_variable_defs` → Extract color/spacing/typography tokens
- `get_design_context` → Get component structure + props
- `get_screenshot` → Capture visual reference
- `get_code_connect_map` → Find existing component mappings

**Standard workflow:**
1. Get screenshot of target screen
2. Get design context for main components
3. Check FIGMA_REGISTRY.md for existing mappings
4. Reference tokens.md for semantic names
5. Implement using app_theme.dart

## Quick Reference

**Most-used tokens (90% coverage):**
- Colors: `primary`, `accent`, `surface`, `onPrimary`
- Spacing: `8`, `16`, `24`, `32` (8pt grid)
- Typography: `titleLarge`, `bodyMedium`, `labelSmall`
- Radii: `8`, `16` (buttons/cards)

**Common patterns:**
- App bar: `24px` top padding, `8px` icon-title gap
- Cards: `16px` padding, `8px` border radius
- Lists: `16px` item padding, `8px` vertical spacing
- Navigation: 3-item bottom nav with icons

## Design Rules (High Impact)

See [references/DESIGN_RULES.md](references/DESIGN_RULES.md) for:
- ✅ CORRECT vs ❌ INCORRECT code patterns
- Impact metrics (e.g., "2-10× cold start improvement")
- Validation checklist (0 hardcoded values, 100% semantic tokens)

## Known Component Patterns

- **App Bar:** back arrow + centered title (ellipsis) + actions
- **Bottom Nav:** 3 items, icon-only, active state indicator
- **Cards:** image + price badge overlay, `8px` rounded corners
- **List Items:** leading icon + text + trailing chevron, `16px` padding
- **FAB:** green circular button with "+" icon
- **Modals:** radio selection with confirm/cancel actions

## Integration Notes

When flutter-ui-designer or flutter-ui-implementer work on screens:
1. Load design system context from indexed files (on-demand)
2. Use semantic token names (never hardcode values)
3. Follow 8pt grid spacing strictly
4. Match component specs from components.md
5. Apply AppTheme from assets/theme/app_theme.dart

## References
- [Flutter Claude Code Skills](https://github.com/cleydson/flutter-claude-code)
- [Figma MCP Server](https://github.com/figma/mcp-server-guide)
