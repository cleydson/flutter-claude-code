# Component Specifications

IMPORTANT: This file uses indexed references for token efficiency. For full visual specs, use MCP tools with node IDs from [FIGMA_REGISTRY.md](../FIGMA_REGISTRY.md).

## Quick Reference
Format: `Component|Widget|Key Props|Node ID|Frequency`

### Buttons 🔴
```
Primary|ElevatedButton|bg:Primary500, fg:White, pad:24h×16v, radius:8|362-6|🔴
Secondary|OutlinedButton|border:1px Primary500, fg:Primary500, pad:24h×16v, radius:8|362-6|🔴
Text|TextButton|fg:Primary500, pad:16h×8v, no bg/border|362-6|
Icon|IconButton|24×24 icon, pad:8, color:Primary500|362-6|🔴
Segmented|SegmentedButton|selected:Primary500/White, unselected:Transparent/Primary500|362-6|
```

### Inputs 🔴
```
Text|TextField|bg:Basic200, border:1px Basic400, focus:2px Primary500, radius:8|613-112|🔴
Password|TextField|obscureText:true, suffix:visibility toggle, bg:Basic200|613-112|🔴
Price|TextField|keyboardType:number, prefix:currency icon, bg:Basic200|613-112|
TextArea|TextField|minLines:3, maxLines:6, bg:Basic200|613-112|
Phone|TextField|keyboardType:phone, prefix:country code dropdown|613-112|
PIN/OTP|Row+TextField|4 boxes, maxLength:1, textAlign:center, width:48×48|613-112|
```

### Bottom Sheets 🔴
```
Action|showModalBottomSheet|actions list, handle, radius:16 top corners|906-2533|🔴
Selection|showModalBottomSheet|radio list, confirm/cancel buttons, radius:16|906-2533|🔴
Calendar|showModalBottomSheet|date picker + time periods + reminder toggle|1056-121|
Text Note|showModalBottomSheet|view/edit modes, action icons (edit/share/delete)|352-247|
```

### View Headers 🔴
```
Default|AppBar|leading:back, title:centered, actions:[], bg:Primary700|7398-31867|🔴
Project|AppBar|leading:back, title:centered, actions:[share, more]|7398-31867|
Calendar|AppBar|leading:menu, title:centered, actions:[help]|7398-31867|
Search|AppBar|search field, filter icon|7398-31867|
Tabs|AppBar|title + TabBar below|7398-31867|
```

### Navigation 🔴
```
Bottom Nav|BottomNavigationBar|4 items, icon-only, active:Primary500|369-373|🔴
Sidebar|Drawer|profile header, menu items, logout button, app version|1380-0|
```

### Cards 🔴
```
Standard|Card|bg:Basic100, elevation:2, radius:8, pad:16|—|🔴
Measurement|Card|front/back body views + measurement points|7781-34293|
Project List|Card|horizontal, thumbnail + name + price + time|7895-12007|🔴
Calendar Item|ListTile|thumbnail + name + price + status icon|7895-12263|🔴
Contact Item|ListTile|avatar + name + phone, trailing:chevron|7895-11357|🔴
Audio|Card|waveform visualization + timestamp + states|343-10|
Text|Card|text preview + states|7573-39191|
Document|Card|file icon + name + date + states|7765-24658|
Photo|Card|image preview + states|7771-21640|🔴
```

### Media & Feedback 🔴
```
Audio Player|Container|play/pause + progress slider + time + close|581-2579|
Snackbar|SnackBar|3 variants: success(Purple), error(Pink), neutral(Gray)|3164-18368|🔴
Carousel|ListView.builder|horizontal scroll, 8 items, blue border on active|414-1750|
Carousel Files|ListView.builder|8 file cards (PDF/PPT/DOC), icon + name + date|4621-34132|
More Menu|showModalBottomSheet|6 actions: Cover, Ungroup, Price, Share, Add, Delete|33736-41988🔴|
```
🔴 = Screen Designs file

### Lists & Items 🔴
```
List Item|ListTile|leading:icon, title:text, trailing:chevron, pad:16|—|🔴
Avatar Item|ListTile|leading:CircleAvatar(32×32), title:name, subtitle:info|—|🔴
Divider|Divider|height:1, color:Basic300, indent:16|—|
```

## Flutter Theme Access

### CORRECT Pattern ✅
```dart
// Use theme tokens
Container(
  padding: EdgeInsets.all(16.0), // Spacing/two
  decoration: BoxDecoration(
    color: Theme.of(context).colorScheme.primary, // Primary 500
    borderRadius: BorderRadius.circular(8.0), // Radius/med
  ),
  child: Text('Label', style: Theme.of(context).textTheme.labelLarge),
)
```

### INCORRECT Pattern ❌
```dart
// Hardcoded values - NEVER do this
Container(
  padding: EdgeInsets.all(15.0), // Not 8pt grid
  decoration: BoxDecoration(
    color: Color(0xFF3D2664), // Hardcoded hex
    borderRadius: BorderRadius.circular(7.0), // Non-standard radius
  ),
  child: Text('Label', style: TextStyle(fontSize: 15)), // Hardcoded font
)
```

## Common Widget Patterns

### App Bar with Centered Title 🔴
```dart
AppBar(
  leading: IconButton(icon: Icon(Icons.arrow_back), onPressed: () {}),
  title: Text('Title', maxLines: 1, overflow: TextOverflow.ellipsis),
  centerTitle: true,
  actions: [IconButton(icon: Icon(Icons.more_vert), onPressed: () {})],
  backgroundColor: Theme.of(context).colorScheme.primary,
)
```

### List Item with Avatar 🔴
```dart
ListTile(
  leading: CircleAvatar(radius: 16, child: Icon(Icons.person)),
  title: Text('Name', style: Theme.of(context).textTheme.bodyLarge),
  subtitle: Text('Details', style: Theme.of(context).textTheme.bodySmall),
  trailing: Icon(Icons.chevron_right),
  contentPadding: EdgeInsets.all(16.0),
  onTap: () {},
)
```

### Bottom Sheet with Handle 🔴
```dart
showModalBottomSheet(
  context: context,
  shape: RoundedRectangleBorder(
    borderRadius: BorderRadius.vertical(top: Radius.circular(16.0)),
  ),
  builder: (context) => Column(
    mainAxisSize: MainAxisSize.min,
    children: [
      Container(
        width: 40, height: 4, margin: EdgeInsets.symmetric(vertical: 12),
        decoration: BoxDecoration(
          color: Colors.grey, borderRadius: BorderRadius.circular(2),
        ),
      ),
      // Sheet content
    ],
  ),
)
```

## Component States

### Button States
```
Default → Hover → Pressed → Disabled
Colors adjust via MaterialStateProperty
```

### Input States
```
Default → Focus (Primary500 border) → Active → Error (Danger500 border, Danger100 bg)
```

### Card States
```
Default → Selected (Primary500 border) → Disabled (Basic300 bg)
```

## Validation Checklist

Before implementing any component:
- [ ] Uses semantic tokens via `Theme.of(context)`
- [ ] Spacing follows 8pt grid (4, 8, 16, 24, 32...)
- [ ] Border radius uses standard values (8, 12, 16)
- [ ] No hardcoded colors (use ColorScheme)
- [ ] No hardcoded sizes (use token constants)
- [ ] Typography via TextTheme
- [ ] Handles all required states (default/hover/pressed/disabled)

**See:** [DESIGN_RULES.md](DESIGN_RULES.md) for impact metrics and detailed validation

## Implementation Priority

High-frequency components (implement first):
1. AppBar (Default variant)
2. Bottom Navigation
3. Primary/Secondary Buttons
4. Text Input
5. Cards (Standard, Photo, Project List)
6. List Items (Avatar variant)
7. Snackbar
8. Bottom Sheets (Action, Selection)

Low-frequency components (implement as needed):
- Segmented Button
- PIN/OTP Input
- Carousel (both variants)
- Measurement Card
- More Menu

## Full Code Examples

For complete Flutter implementation code with all states and edge cases, see:
- [assets/theme/app_theme.dart](../assets/theme/app_theme.dart) - Theme configuration
- [FIGMA_REGISTRY.md](../FIGMA_REGISTRY.md) - MCP access to live designs
- Use `get_design_context` MCP tool for current specs
