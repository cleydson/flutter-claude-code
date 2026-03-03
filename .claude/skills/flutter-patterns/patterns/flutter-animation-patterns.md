---
name: flutter-animation-patterns
description: Reusable Flutter animation patterns including fade, slide, scale, rotation, and complex transitions. Use this skill for quick animation implementation reference.
---

# Flutter Animation Patterns - Quick Reference

Battle-tested animation patterns for common transitions and effects.

## Implicit Animations (Easiest)

### Animated Container

```dart
class AnimatedBox extends StatefulWidget {
  @override
  _AnimatedBoxState createState() => _AnimatedBoxState();
}

class _AnimatedBoxState extends State<AnimatedBox> {
  bool _expanded = false;

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: () => setState(() => _expanded = !_expanded),
      child: AnimatedContainer(
        duration: Duration(milliseconds: 300),
        curve: Curves.easeInOut,
        width: _expanded ? 200 : 100,
        height: _expanded ? 200 : 100,
        decoration: BoxDecoration(
          color: _expanded ? Colors.blue : Colors.red,
          borderRadius: BorderRadius.circular(_expanded ? 50 : 10),
        ),
        child: Center(child: Text('Tap me')),
      ),
    );
  }
}
```

### Animated Opacity (Fade)

```dart
AnimatedOpacity(
  opacity: _visible ? 1.0 : 0.0,
  duration: Duration(milliseconds: 500),
  child: YourWidget(),
)
```

### Animated Positioned (Slide)

```dart
Stack(
  children: [
    AnimatedPositioned(
      duration: Duration(milliseconds: 300),
      left: _active ? 0 : 100,
      top: _active ? 0 : 50,
      child: YourWidget(),
    ),
  ],
)
```

## Explicit Animations (More Control)

### Fade In Animation

```dart
class FadeIn extends StatefulWidget {
  final Widget child;
  final Duration duration;

  const FadeIn({
    required this.child,
    this.duration = const Duration(milliseconds: 500),
  });

  @override
  _FadeInState createState() => _FadeInState();
}

class _FadeInState extends State<FadeIn> with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _animation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(duration: widget.duration, vsync: this);
    _animation = Tween<double>(begin: 0.0, end: 1.0).animate(
      CurvedAnimation(parent: _controller, curve: Curves.easeIn),
    );
    _controller.forward();
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return FadeTransition(
      opacity: _animation,
      child: widget.child,
    );
  }
}
```

### Slide In Animation

```dart
class SlideIn extends StatefulWidget {
  final Widget child;
  final Offset begin;
  final Duration duration;

  const SlideIn({
    required this.child,
    this.begin = const Offset(1, 0), // Slide from right
    this.duration = const Duration(milliseconds: 300),
  });

  @override
  _SlideInState createState() => _SlideInState();
}

class _SlideInState extends State<SlideIn> with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<Offset> _animation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(duration: widget.duration, vsync: this);
    _animation = Tween<Offset>(
      begin: widget.begin,
      end: Offset.zero,
    ).animate(CurvedAnimation(parent: _controller, curve: Curves.easeOut));
    _controller.forward();
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return SlideTransition(
      position: _animation,
      child: widget.child,
    );
  }
}
```

### Scale Animation

```dart
class ScaleIn extends StatefulWidget {
  final Widget child;
  final Duration duration;

  const ScaleIn({
    required this.child,
    this.duration = const Duration(milliseconds: 200),
  });

  @override
  _ScaleInState createState() => _ScaleInState();
}

class _ScaleInState extends State<ScaleIn> with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _animation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(duration: widget.duration, vsync: this);
    _animation = Tween<double>(begin: 0.0, end: 1.0).animate(
      CurvedAnimation(parent: _controller, curve: Curves.elasticOut),
    );
    _controller.forward();
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return ScaleTransition(
      scale: _animation,
      child: widget.child,
    );
  }
}
```

## Page Transitions

### Slide Route Transition

```dart
class SlideRoute extends PageRouteBuilder {
  final Widget page;

  SlideRoute({required this.page})
      : super(
          pageBuilder: (context, animation, secondaryAnimation) => page,
          transitionsBuilder: (context, animation, secondaryAnimation, child) {
            const begin = Offset(1.0, 0.0);
            const end = Offset.zero;
            const curve = Curves.easeInOut;

            var tween = Tween(begin: begin, end: end).chain(
              CurveTween(curve: curve),
            );

            return SlideTransition(
              position: animation.drive(tween),
              child: child,
            );
          },
        );
}

// Usage
Navigator.push(context, SlideRoute(page: DetailsPage()));
```

### Fade Route Transition

```dart
class FadeRoute extends PageRouteBuilder {
  final Widget page;

  FadeRoute({required this.page})
      : super(
          pageBuilder: (context, animation, secondaryAnimation) => page,
          transitionsBuilder: (context, animation, secondaryAnimation, child) {
            return FadeTransition(
              opacity: animation,
              child: child,
            );
          },
        );
}
```

### Scale Route Transition

```dart
class ScaleRoute extends PageRouteBuilder {
  final Widget page;

  ScaleRoute({required this.page})
      : super(
          pageBuilder: (context, animation, secondaryAnimation) => page,
          transitionsBuilder: (context, animation, secondaryAnimation, child) {
            const curve = Curves.easeInOut;

            var scaleAnimation = Tween(begin: 0.0, end: 1.0).animate(
              CurvedAnimation(parent: animation, curve: curve),
            );

            var fadeAnimation = Tween(begin: 0.0, end: 1.0).animate(
              CurvedAnimation(parent: animation, curve: curve),
            );

            return ScaleTransition(
              scale: scaleAnimation,
              child: FadeTransition(
                opacity: fadeAnimation,
                child: child,
              ),
            );
          },
        );
}
```

## List Animations

### Staggered List Animation

```dart
class StaggeredList extends StatelessWidget {
  final List<Widget> children;

  const StaggeredList({required this.children});

  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      itemCount: children.length,
      itemBuilder: (context, index) {
        return AnimatedBuilder(
          animation: AlwaysStoppedAnimation(1.0),
          builder: (context, child) {
            return FadeIn(
              duration: Duration(milliseconds: 300 + (index * 100)),
              child: SlideIn(
                duration: Duration(milliseconds: 300 + (index * 100)),
                child: children[index],
              ),
            );
          },
        );
      },
    );
  }
}
```

### Animated List Item

```dart
class AnimatedListItem extends StatefulWidget {
  final Widget child;
  final int index;

  const AnimatedListItem({
    required this.child,
    required this.index,
  });

  @override
  _AnimatedListItemState createState() => _AnimatedListItemState();
}

class _AnimatedListItemState extends State<AnimatedListItem>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<Offset> _slideAnimation;
  late Animation<double> _fadeAnimation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(milliseconds: 500),
      vsync: this,
    );

    final delay = widget.index * 100;

    Future.delayed(Duration(milliseconds: delay), () {
      if (mounted) _controller.forward();
    });

    _slideAnimation = Tween<Offset>(
      begin: Offset(0, 0.5),
      end: Offset.zero,
    ).animate(CurvedAnimation(parent: _controller, curve: Curves.easeOut));

    _fadeAnimation = Tween<double>(begin: 0.0, end: 1.0).animate(
      CurvedAnimation(parent: _controller, curve: Curves.easeIn),
    );
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return SlideTransition(
      position: _slideAnimation,
      child: FadeTransition(
        opacity: _fadeAnimation,
        child: widget.child,
      ),
    );
  }
}
```

## Button Animations

### Animated Button Press

```dart
class AnimatedButton extends StatefulWidget {
  final VoidCallback onPressed;
  final Widget child;

  const AnimatedButton({
    required this.onPressed,
    required this.child,
  });

  @override
  _AnimatedButtonState createState() => _AnimatedButtonState();
}

class _AnimatedButtonState extends State<AnimatedButton>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _scaleAnimation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(milliseconds: 100),
      vsync: this,
    );
    _scaleAnimation = Tween<double>(begin: 1.0, end: 0.95).animate(
      CurvedAnimation(parent: _controller, curve: Curves.easeInOut),
    );
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  void _handleTapDown(TapDownDetails details) {
    _controller.forward();
  }

  void _handleTapUp(TapUpDetails details) {
    _controller.reverse();
    widget.onPressed();
  }

  void _handleTapCancel() {
    _controller.reverse();
  }

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTapDown: _handleTapDown,
      onTapUp: _handleTapUp,
      onTapCancel: _handleTapCancel,
      child: ScaleTransition(
        scale: _scaleAnimation,
        child: widget.child,
      ),
    );
  }
}
```

## Loading Animations

### Pulsing Dot

```dart
class PulsingDot extends StatefulWidget {
  final Color color;
  final double size;

  const PulsingDot({
    this.color = Colors.blue,
    this.size = 50,
  });

  @override
  _PulsingDotState createState() => _PulsingDotState();
}

class _PulsingDotState extends State<PulsingDot>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _animation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(milliseconds: 1000),
      vsync: this,
    )..repeat(reverse: true);

    _animation = Tween<double>(begin: 0.8, end: 1.2).animate(
      CurvedAnimation(parent: _controller, curve: Curves.easeInOut),
    );
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return ScaleTransition(
      scale: _animation,
      child: Container(
        width: widget.size,
        height: widget.size,
        decoration: BoxDecoration(
          color: widget.color,
          shape: BoxShape.circle,
        ),
      ),
    );
  }
}
```

### Shimmer Loading

```dart
class ShimmerLoading extends StatefulWidget {
  final Widget child;

  const ShimmerLoading({required this.child});

  @override
  _ShimmerLoadingState createState() => _ShimmerLoadingState();
}

class _ShimmerLoadingState extends State<ShimmerLoading>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<double> _animation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(milliseconds: 1500),
      vsync: this,
    )..repeat();

    _animation = Tween<double>(begin: -2, end: 2).animate(_controller);
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return AnimatedBuilder(
      animation: _animation,
      builder: (context, child) {
        return ShaderMask(
          shaderCallback: (bounds) {
            return LinearGradient(
              begin: Alignment.topLeft,
              end: Alignment.bottomRight,
              stops: [
                _animation.value - 0.3,
                _animation.value,
                _animation.value + 0.3,
              ],
              colors: [
                Colors.grey[300]!,
                Colors.grey[100]!,
                Colors.grey[300]!,
              ],
            ).createShader(bounds);
          },
          child: widget.child,
        );
      },
    );
  }
}
```

## Hero Animation

```dart
// Source screen
GestureDetector(
  onTap: () {
    Navigator.push(
      context,
      MaterialPageRoute(builder: (context) => DetailScreen(product)),
    );
  },
  child: Hero(
    tag: 'product-${product.id}',
    child: Image.network(product.imageUrl),
  ),
)

// Destination screen
class DetailScreen extends StatelessWidget {
  final Product product;

  const DetailScreen(this.product);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Column(
        children: [
          Hero(
            tag: 'product-${product.id}',
            child: Image.network(
              product.imageUrl,
              width: double.infinity,
              height: 300,
              fit: BoxFit.cover,
            ),
          ),
          // Rest of detail content
        ],
      ),
    );
  }
}
```

## Combined Animations

### Slide and Fade

```dart
class SlideAndFade extends StatefulWidget {
  final Widget child;

  const SlideAndFade({required this.child});

  @override
  _SlideAndFadeState createState() => _SlideAndFadeState();
}

class _SlideAndFadeState extends State<SlideAndFade>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<Offset> _slideAnimation;
  late Animation<double> _fadeAnimation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: Duration(milliseconds: 600),
      vsync: this,
    );

    _slideAnimation = Tween<Offset>(
      begin: Offset(0, 0.3),
      end: Offset.zero,
    ).animate(CurvedAnimation(parent: _controller, curve: Curves.easeOut));

    _fadeAnimation = Tween<double>(begin: 0.0, end: 1.0).animate(
      CurvedAnimation(parent: _controller, curve: Curves.easeIn),
    );

    _controller.forward();
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return SlideTransition(
      position: _slideAnimation,
      child: FadeTransition(
        opacity: _fadeAnimation,
        child: widget.child,
      ),
    );
  }
}
```

## TweenAnimationBuilder (No Controller Needed)

```dart
// Simple declarative animation without AnimationController
TweenAnimationBuilder<double>(
  tween: Tween(begin: 0.0, end: _isExpanded ? 1.0 : 0.0),
  duration: const Duration(milliseconds: 300),
  builder: (context, value, child) {
    return Transform.scale(
      scale: 0.8 + (0.2 * value),
      child: Opacity(
        opacity: value,
        child: child,
      ),
    );
  },
  child: const Card(child: Text('Animated content')),
)
```

## AnimatedSwitcher

```dart
// Animate between different child widgets
AnimatedSwitcher(
  duration: const Duration(milliseconds: 300),
  transitionBuilder: (child, animation) {
    return FadeTransition(
      opacity: animation,
      child: ScaleTransition(
        scale: animation,
        child: child,
      ),
    );
  },
  child: _showFirst
      ? const Icon(Icons.check, key: ValueKey('check'))
      : const Icon(Icons.close, key: ValueKey('close')),
  // Key is essential for AnimatedSwitcher to detect changes
)
```

## AnimatedList & SliverAnimatedList

```dart
// Animated insertion/removal of list items
final _listKey = GlobalKey<AnimatedListState>();

AnimatedList(
  key: _listKey,
  initialItemCount: items.length,
  itemBuilder: (context, index, animation) {
    return SizeTransition(
      sizeFactor: animation,
      child: ListTile(title: Text(items[index])),
    );
  },
)

// Insert item with animation
void _addItem(String item) {
  items.insert(0, item);
  _listKey.currentState?.insertItem(0);
}

// Remove item with animation
void _removeItem(int index) {
  final removedItem = items.removeAt(index);
  _listKey.currentState?.removeItem(
    index,
    (context, animation) => SizeTransition(
      sizeFactor: animation,
      child: ListTile(title: Text(removedItem)),
    ),
  );
}
```

## Third-Party Animation Libraries

```dart
// flutter_animate - Declarative animation composition
// dependencies: flutter_animate: ^4.5.0
import 'package:flutter_animate/flutter_animate.dart';

Text('Hello')
    .animate()
    .fadeIn(duration: 600.ms)
    .slideY(begin: 0.3, end: 0)
    .then(delay: 200.ms) // Chain with delay
    .shake();

// Rive - Interactive vector animations
// dependencies: rive: ^0.13.0
// Great for complex interactive animations designed in the Rive editor

// Lottie - After Effects animations
// dependencies: lottie: ^3.0.0
// Import animations from LottieFiles
```

## Material 3 Motion Patterns

### Emphasis Transition (Material 3)

```dart
// Material 3 uses easing and duration tokens
// Standard: Curves.easeInOutCubicEmphasized, 500ms
// Entering: Curves.easeOutCubic, 400ms
// Exiting: Curves.easeInCubic, 200ms

class M3PageTransition extends PageRouteBuilder {
  final Widget page;

  M3PageTransition({required this.page})
      : super(
          transitionDuration: const Duration(milliseconds: 500),
          reverseTransitionDuration: const Duration(milliseconds: 200),
          pageBuilder: (context, animation, secondaryAnimation) => page,
          transitionsBuilder: (context, animation, secondaryAnimation, child) {
            final fadeIn = Tween(begin: 0.0, end: 1.0).animate(
              CurvedAnimation(
                parent: animation,
                curve: Curves.easeOutCubic,
              ),
            );

            final slideIn = Tween(
              begin: const Offset(0.0, 0.05),
              end: Offset.zero,
            ).animate(
              CurvedAnimation(
                parent: animation,
                curve: Curves.easeInOutCubicEmphasized,
              ),
            );

            return FadeTransition(
              opacity: fadeIn,
              child: SlideTransition(
                position: slideIn,
                child: child,
              ),
            );
          },
        );
}
```

### Shared Axis Transition (Material 3)

```dart
// Using the animations package for Material 3 transitions
// dependencies: animations: ^2.0.0
import 'package:animations/animations.dart';

// Shared axis transition between pages
PageRouteBuilder sharedAxisRoute(Widget page) {
  return PageRouteBuilder(
    pageBuilder: (context, animation, secondaryAnimation) => page,
    transitionsBuilder: (context, animation, secondaryAnimation, child) {
      return SharedAxisTransition(
        animation: animation,
        secondaryAnimation: secondaryAnimation,
        transitionType: SharedAxisTransitionType.horizontal,
        child: child,
      );
    },
  );
}

// Container transform (expand from card to full page)
OpenContainer(
  closedBuilder: (context, openContainer) {
    return ListTile(
      title: Text(item.title),
      onTap: openContainer,
    );
  },
  openBuilder: (context, closeContainer) {
    return DetailPage(item: item);
  },
)
```

## Impeller Animation Performance Notes

**Impeller** (Flutter's modern rendering engine) provides significant animation improvements:

1. **No Shader Compilation Jank**: Impeller pre-compiles all shaders at build time, eliminating the first-run jank that was common with Skia
2. **Consistent Frame Times**: More predictable rendering performance across frames
3. **Better GPU Utilization**: Impeller is designed for modern GPU architectures (Metal on iOS, Vulkan on Android)
4. **Simplified Profiling**: No need to warm up shaders or use `--trace-skia`

**Animation Best Practices with Impeller:**
- RepaintBoundary is still valuable for isolating animated subtrees
- Transform operations remain more efficient than layout-based animations
- Complex custom painters and shader effects work differently - test thoroughly
- saveLayer operations are more performant with Impeller than Skia

## Usage Tips

1. **Performance**: Use `RepaintBoundary` for complex animations
2. **Dispose**: Always dispose controllers in `dispose()`
3. **vsync**: Use `SingleTickerProviderStateMixin` or `TickerProviderStateMixin`
4. **Curves**: Use Material 3 easing tokens:
   - `Curves.easeInOutCubicEmphasized` (standard M3 emphasis)
   - `Curves.easeOutCubic` (entering elements)
   - `Curves.easeInCubic` (exiting elements)
5. **Duration**: Material 3 duration recommendations:
   - Micro interactions: 100-200ms
   - Standard transitions: 300-500ms (M3 standard: 500ms)
   - Complex animations: 500-1000ms
6. **Impeller**: Shader jank is eliminated - animations are smooth from first frame

These patterns provide smooth, professional animations for Flutter apps.
