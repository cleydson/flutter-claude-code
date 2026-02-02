# Figma Component Registry

IMPORTANT: This registry provides quick MCP access to African Puzzle Works components. All entries use pipe-delimited format for token efficiency.

## Files
Design System: `8S2Jt5xKHfTmlI8rSR6AcX`
Screen Designs: `zTFAYthaXpFu8nr6nFPDCH`

## Quick MCP Commands
```
Screenshot: mcp__figma__get_screenshot(fileKey="[FILE]", nodeId="[NODE]")
Context: mcp__figma__get_design_context(fileKey="[FILE]", nodeId="[NODE]")
Variables: mcp__figma__get_variable_defs(fileKey="[FILE]", nodeId="[NODE]")
```

## Design System Components
Format: `Component|NodeID|Description|Verified`

### Navigation & Layout
```
View Headers|7398-31867|7 variants (Default, Project, Calendar, Search, Tabs, Album, Contact)|2026-01-28
Bottom Nav|369-373|4-item nav (Album, Rendez-vous, Programmes, Contacts)|2026-01-29
Sidebar Drawer|1380-0|Main menu + secondary info menu, profile header, logout|2026-01-29
```

### Buttons & Inputs
```
Buttons|362-6|Primary, Secondary, Text, Icon, Segmented + all states|2026-01-28
Inputs|613-112|Text, password, price, textarea, phone, PIN/OTP + error states|2026-01-29
Input States|7298-18046|Default/disabled/focus/active/error states|2026-01-29
```

### Bottom Sheets & Modals
```
Bottom Sheets|906-2533|Action, Selection, Calendar, Price sheet variants|2026-01-28
Calendar Picker|1056-121|Date picker + month/year selector + time periods + reminder|2026-01-29
Text Note Sheet|352-247|View/edit modes, empty state, action icons (edit/share/delete)|2026-01-29
More Menu|33736-41988 🔴|Context menu: Cover, Ungroup, Price, Share, Add, Delete|2026-01-29
```
🔴 = Uses Screen Designs file (zTFAYthaXpFu8nr6nFPDCH)

### Cards & Lists
```
Measurement Card|7781-34293|Body measurement: front/back views with points|2026-01-29
Project List Card|7895-12007|Horizontal cards: empty, with image, new draft + name/price/time|2026-01-29
Calendar Item|7895-12263|Thumbnail + name + price + status indicator|2026-01-29
Contact Item|7895-11357|Header + avatar/name/phone + project views with pricing|2026-01-29
Audio Card|343-10|Waveform visualization + timestamp + multiple states|2026-01-29
Text Card|7573-39191|Text preview + multiple states|2026-01-29
Document Card|7765-24658|File icon + name + date + multiple states|2026-01-29
Photo Card|7771-21640|Image preview + multiple states|2026-01-29
```

### Media & Feedback
```
Audio Player|581-2579|Play/pause + progress slider + time + close button|2026-01-29
Snackbar|3164-18368|3 variants: success (purple), error (pink), neutral (gray)|2026-01-29
Carousel|414-1750|8 image thumbnails, blue border on active, horizontal scroll|2026-01-29
Carousel (Files)|4621-34132|8 file cards (PDF, PPT, DOC, XLS, etc) with icons + dates|2026-01-29
```

### Primitives
```
Colors|7353-10757|Complete palette: Primary, Basic, Success, Info, Warning, Danger + all shades|2026-01-28
Icons & Flags|341-157|White, Dark, Other icon sets|2026-01-28
```

## Screen Examples 🔴
Format: `Screen|NodeID|Key Features|Verified`
All use Screen Designs file (zTFAYthaXpFu8nr6nFPDCH)

```
Album Empty|33677-28088|Profile header, empty state message, content icons, orange FAB, bottom nav|2026-01-29
Album Content|33679-57077|Profile header, 2x2 photo grid, item count badge, orange FAB, bottom nav|2026-01-29
Calendar Empty|34171-248257|Calendar widget (FEVRIER 2024), empty message, orange FAB, bottom nav|2026-01-29
Calendar Content|34180-348591|Calendar widget, day "2" selected with badge, 2 appointment cards with prices/icons|2026-01-29
Photo Detail|33681-47508|Full-screen photo, back arrow, 3-dot menu, bottom thumbnail strip (4 images)|2026-01-29
```

## Usage Pattern

**For implementation:**
1. Look up component in table above
2. Run MCP command with node ID
3. Reference [references/components.md](references/components.md) for specs
4. Use [assets/theme/app_theme.dart](assets/theme/app_theme.dart) for styling

**Component count:** 24 components | 5 screens | v1.7.0

## Node ID Format
Both formats work: `362-6` (URL) or `362:6` (API)

## Version History
- **v1.7.0** (2026-01-29): Added Screens category + Carousels + More Menu
- **v1.6.0** (2026-01-29): Added Forms + Calendar Picker + Snackbar + Text Note + Sidebar
- **v1.5.1** (2026-01-29): Added Project/Calendar/Contact list items
- **v1.5.0** (2026-01-29): Added Media (Audio Player)
- **v1.4.0** (2026-01-29): Added Project Media Cards (4 types)
- **v1.3.0** (2026-01-29): Added Measurement Card
- **v1.2.0** (2026-01-29): Added Bottom Nav
- **v1.1.0** (2026-01-28): Added View Headers, Bottom Sheets
- **v1.0.0** (2026-01-15): Initial (Buttons, Icons, Colors)

## Troubleshooting
- "Component not found" → Check node ID is current, verify file access, try both formats
- Node ID changed → Get new ID from Figma URL, update registry, notify team

**Last Updated:** 2026-01-29
