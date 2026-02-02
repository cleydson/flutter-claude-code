# Design Tokens

IMPORTANT: Use semantic token names in code, never hardcoded hex values. 🔴 marks high-frequency tokens (90% usage).

## Quick Lookup (Most-Used Tokens)
```
Colors:    primary|#3D2664  accent|#FFAA2E  surface|#FFFFFF  onPrimary|#FFFFFF  error|#FF7070
Spacing:   8|16|24|32  (8pt grid)
Typography: titleLarge|24sp|bold  bodyMedium|16sp|regular  labelSmall|12sp|regular
Radius:    8|12  (buttons/cards)
```

## Colors
Format: `Token|Hex|Usage|Frequency`

### Primary (Purple) 🔴
```
Primary 100|#C5BED1|Lightest tint|
Primary 200|#9E93B2|Light|
Primary 300|#776793|Medium-light|
Primary 400|#5A477B|Medium|
Primary 500|#3D2664|Base primary|🔴
Primary 600|#37225C|Medium-dark|
Primary 700|#2F1C52|Dark|🔴
Primary 800|#271748|Darker|
Primary 900|#1A0D36|Darkest|
```

### Basic (Neutrals) 🔴
```
Basic 100|#FFFFFF|White, primary bg|🔴
Basic 200|#F7F9FC|Off-white bg|🔴
Basic 300|#EDF1F7|Light gray bg|🔴
Basic 400|#E4E9F2|Medium-light gray|
Basic 500|#C5CEE0|Medium gray|🔴
Basic 600|#8F9BB3|Medium-dark gray|🔴
Basic 700|#2E3A59|Dark gray|🔴
Basic 800|#222B45|Darker gray|🔴
Basic 900|#192038|Very dark gray|
Basic 1000|#151A30|Near black|
Basic 1100|#101426|Darkest gray|
```

### Success (Green) 🔴
```
Success 100|#EDFFF3|Lightest|
Success 300|#8CFAC7|Medium-light|
Success 500|#00E096|Base success|🔴
Success 700|#008F72|Dark|
```

### Info (Blue) 🔴
```
Info 100|#F2F8FF|Lightest|
Info 300|#94CBFF|Medium-light|
Info 500|#0095FF|Base info|🔴
Info 700|#0057C2|Dark|
```

### Warning (Orange) 🔴
```
Warning 100|#FFF4E5|Lightest|
Warning 300|#FFD28A|Medium-light|
Warning 500|#FFAA2E|Base warning|🔴
Warning 700|#E68900|Dark|
```

### Danger (Red) 🔴
```
Danger 100|#FFEAEA|Lightest|🔴
Danger 300|#FFADAD|Medium-light|
Danger 500|#FF7070|Base danger|🔴
Danger 700|#E63E3E|Dark|
```

### Semantic Surface
```
surface-card|Basic 100|Card backgrounds|🔴
surface-error|Danger 100|Error backgrounds|🔴
surface-input|Basic 200|Input fields|🔴
surface-modal|Basic 100|Modals|🔴
surface-disabled|Basic 300|Disabled states|🔴
surface-notification-success|Primary 500|Success notifications|
surface-notification-error|Danger 100|Error notifications|
```

## Typography
Font: **Open Sans** (all text)

### Weights
```
Light|300  Regular|400  Italic|400i  SemiBold|600  Bold|700  BoldItalic|700i
```

### Sizes 🔴
```
h1|60sp  h2|48sp  h3|40sp  h4|32sp  h5|24sp🔴  h6|20sp🔴
caption|10sp  small|12sp🔴  medium|16sp🔴  large|20sp
```

## Spacing (8pt Grid) 🔴
Format: `Token|px|Flutter|Multiplier|Frequency`
```
none|0|0.0|0×|
half|4|4.0|0.5×|🔴
one|8|8.0|1×|🔴
two|16|16.0|2×|🔴
three|24|24.0|3×|🔴
four|32|32.0|4×|🔴
six|48|48.0|6×|
eight|64|64.0|8×|
twelve|96|96.0|12×|
```

### Semantic Spacing 🔴
```
xs|4  sm|8🔴  med|16🔴  lrg|24🔴  xl|32🔴  2xl|64
```

## Border Radius 🔴
```
none|0  xs|2  sm|4  med|8🔴  lg|12🔴  xl|16  xxl|20  3xl|24  4xl|32  full|9999🔴
```

### Semantic Radius 🔴
```
minimal|4  rounded|8🔴  full|12+🔴
```

## Border Width
```
thin|0.5  normal|1🔴  thick|2  thicker|3  thickest|4
```

## Flutter Usage

### Theme Access Pattern
```dart
// Use semantic names via theme
final colors = Theme.of(context).colorScheme;
final text = Theme.of(context).textTheme;

// Example: Primary button
Container(
  padding: EdgeInsets.all(16.0), // spacing-med
  decoration: BoxDecoration(
    color: colors.primary, // Primary 500
    borderRadius: BorderRadius.circular(8.0), // radius-rounded
  ),
  child: Text('Submit', style: text.labelLarge),
)
```

### CORRECT vs INCORRECT
```dart
// ❌ INCORRECT: Hardcoded values
Container(
  padding: EdgeInsets.all(15.0),
  decoration: BoxDecoration(
    color: Color(0xFF3D2664),
    borderRadius: BorderRadius.circular(7.0),
  ),
)

// ✅ CORRECT: Semantic tokens via theme
Container(
  padding: EdgeInsets.all(16.0), // Spacing/two
  decoration: BoxDecoration(
    color: Theme.of(context).colorScheme.primary,
    borderRadius: BorderRadius.circular(8.0), // Radius/med
  ),
)
```

## Design Rules
1. **8pt Grid**: All spacing = multiples of 8px (or 4px for half)
2. **Semantic > Primitive**: Use `spacing-med` not `16`
3. **Theme Access**: Via `Theme.of(context)` not hardcoded
4. **No Magic Numbers**: Every value must map to a token

**See:** [DESIGN_RULES.md](DESIGN_RULES.md) for validation checklist
