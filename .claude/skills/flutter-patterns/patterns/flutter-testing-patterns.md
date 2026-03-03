---
name: flutter-testing-patterns
description: Flutter testing templates and best practices. Quick reference for unit tests, widget tests, integration tests, BLoC testing, and mocking patterns.
---

# Flutter Testing Patterns - Quick Reference

Production-ready test templates for comprehensive test coverage.

## Unit Test Templates

### Basic Unit Test

```dart
import 'package:flutter_test/flutter_test.dart';

void main() {
  group('ClassName', () {
    test('description of what is being tested', () {
      // Arrange
      final input = 'test input';
      final expected = 'expected output';

      // Act
      final result = functionToTest(input);

      // Assert
      expect(result, expected);
    });
  });
}
```

### Test with Setup and Teardown

```dart
void main() {
  late MyClass instance;

  setUp(() {
    // Runs before each test
    instance = MyClass();
  });

  tearDown(() {
    // Runs after each test
    instance.dispose();
  });

  test('test description', () {
    expect(instance.value, isNotNull);
  });
}
```

### Testing Future/Async

```dart
test('async function returns expected value', () async {
  // Arrange
  final repository = MyRepository();

  // Act
  final result = await repository.fetchData();

  // Assert
  expect(result, isA<List<Data>>());
  expect(result.length, greaterThan(0));
});
```

### Testing Streams

```dart
test('stream emits correct values', () {
  // Arrange
  final controller = StreamController<int>();

  // Act & Assert
  expect(
    controller.stream,
    emitsInOrder([1, 2, 3, emitsDone]),
  );

  controller.add(1);
  controller.add(2);
  controller.add(3);
  controller.close();
});
```

## Widget Test Templates

### Basic Widget Test

```dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';

void main() {
  testWidgets('widget description', (WidgetTester tester) async {
    // Arrange
    await tester.pumpWidget(
      MaterialApp(
        home: MyWidget(),
      ),
    );

    // Act (if needed)
    // await tester.tap(find.byIcon(Icons.add));
    // await tester.pump();

    // Assert
    expect(find.text('Expected Text'), findsOneWidget);
  });
}
```

### Test with Theme

```dart
testWidgets('widget with theme', (tester) async {
  await tester.pumpWidget(
    MaterialApp(
      theme: ThemeData.light(),
      home: MyWidget(),
    ),
  );

  expect(find.byType(MyWidget), findsOneWidget);
});
```

### Test with Provider/BLoC

```dart
testWidgets('widget with provider', (tester) async {
  final mockBloc = MockMyBloc();

  when(() => mockBloc.state).thenReturn(InitialState());

  await tester.pumpWidget(
    MaterialApp(
      home: BlocProvider<MyBloc>.value(
        value: mockBloc,
        child: MyWidget(),
      ),
    ),
  );

  expect(find.byType(MyWidget), findsOneWidget);
});
```

### Test User Interaction

```dart
testWidgets('button tap triggers callback', (tester) async {
  bool tapped = false;

  await tester.pumpWidget(
    MaterialApp(
      home: Scaffold(
        body: ElevatedButton(
          onPressed: () => tapped = true,
          child: Text('Tap me'),
        ),
      ),
    ),
  );

  // Find and tap button
  await tester.tap(find.text('Tap me'));
  await tester.pump();

  expect(tapped, true);
});
```

### Test Text Input

```dart
testWidgets('text field accepts input', (tester) async {
  final controller = TextEditingController();

  await tester.pumpWidget(
    MaterialApp(
      home: Scaffold(
        body: TextField(
          controller: controller,
          key: Key('email_field'),
        ),
      ),
    ),
  );

  // Enter text
  await tester.enterText(find.byKey(Key('email_field')), 'test@example.com');
  await tester.pump();

  expect(controller.text, 'test@example.com');
  expect(find.text('test@example.com'), findsOneWidget);
});
```

### Test Navigation

```dart
testWidgets('navigation to detail page', (tester) async {
  await tester.pumpWidget(
    MaterialApp(
      home: HomePage(),
      routes: {
        '/details': (context) => DetailsPage(),
      },
    ),
  );

  // Tap navigation element
  await tester.tap(find.text('View Details'));
  await tester.pumpAndSettle(); // Wait for navigation animation

  expect(find.byType(DetailsPage), findsOneWidget);
});
```

### Test Scrolling

```dart
testWidgets('list scrolls to item', (tester) async {
  await tester.pumpWidget(
    MaterialApp(
      home: Scaffold(
        body: ListView.builder(
          itemCount: 100,
          itemBuilder: (context, index) => ListTile(
            title: Text('Item $index'),
          ),
        ),
      ),
    ),
  );

  // Scroll to bottom
  await tester.scrollUntilVisible(
    find.text('Item 99'),
    500.0,
  );

  expect(find.text('Item 99'), findsOneWidget);
});
```

## BLoC Testing Templates

### Basic BLoC Test

```dart
import 'package:bloc_test/bloc_test.dart';
import 'package:flutter_test/flutter_test.dart';

void main() {
  group('MyBloc', () {
    late MyBloc bloc;

    setUp(() {
      bloc = MyBloc();
    });

    tearDown(() {
      bloc.close();
    });

    test('initial state is correct', () {
      expect(bloc.state, isA<InitialState>());
    });

    blocTest<MyBloc, MyState>(
      'emits [LoadingState, LoadedState] when LoadEvent is added',
      build: () => bloc,
      act: (bloc) => bloc.add(LoadEvent()),
      expect: () => [
        LoadingState(),
        LoadedState(data: testData),
      ],
    );
  });
}
```

### BLoC Test with Mock Dependencies

```dart
blocTest<ProductsBloc, ProductsState>(
  'emits [Loading, Loaded] when LoadProducts succeeds',
  build: () {
    final mockRepository = MockProductsRepository();
    when(() => mockRepository.getProducts())
        .thenAnswer((_) async => Right(testProducts));
    return ProductsBloc(repository: mockRepository);
  },
  act: (bloc) => bloc.add(LoadProducts()),
  expect: () => [
    ProductsLoading(),
    ProductsLoaded(testProducts),
  ],
  verify: (bloc) {
    verify(() => mockRepository.getProducts()).called(1);
  },
);
```

### BLoC Test with Error Handling

```dart
blocTest<ProductsBloc, ProductsState>(
  'emits [Loading, Error] when LoadProducts fails',
  build: () {
    final mockRepository = MockProductsRepository();
    when(() => mockRepository.getProducts())
        .thenAnswer((_) async => Left(ServerFailure('Error')));
    return ProductsBloc(repository: mockRepository);
  },
  act: (bloc) => bloc.add(LoadProducts()),
  expect: () => [
    ProductsLoading(),
    ProductsError('Error'),
  ],
);
```

## Mock Patterns

### Mockito Setup

```dart
import 'package:mockito/annotations.dart';
import 'package:mockito/mockito.dart';

// Generate mocks
@GenerateMocks([UserRepository, AuthService])
void main() {}

// Run: dart run build_runner build
```

### Mock with Return Value

```dart
test('mock returns value', () async {
  final mockRepo = MockUserRepository();

  when(mockRepo.getUser('123'))
      .thenAnswer((_) async => User(id: '123', name: 'Test'));

  final user = await mockRepo.getUser('123');

  expect(user.name, 'Test');
  verify(mockRepo.getUser('123')).called(1);
});
```

### Mock with Exception

```dart
test('mock throws exception', () async {
  final mockRepo = MockUserRepository();

  when(mockRepo.getUser('123'))
      .thenThrow(Exception('User not found'));

  expect(
    () => mockRepo.getUser('123'),
    throwsException,
  );
});
```

### Mocktail Pattern

```dart
import 'package:mocktail/mocktail.dart';

class MockUserRepository extends Mock implements UserRepository {}

void main() {
  late MockUserRepository mockRepo;

  setUp(() {
    mockRepo = MockUserRepository();
  });

  test('using mocktail', () async {
    when(() => mockRepo.getUser(any()))
        .thenAnswer((_) async => User(id: '1', name: 'Test'));

    final user = await mockRepo.getUser('1');

    expect(user.name, 'Test');
    verify(() => mockRepo.getUser(any())).called(1);
  });
}
```

## Integration Test Templates

### Basic Integration Test

```dart
import 'package:flutter_test/flutter_test.dart';
import 'package:integration_test/integration_test.dart';
import 'package:myapp/main.dart' as app;

void main() {
  IntegrationTestWidgetsFlutterBinding.ensureInitialized();

  testWidgets('full app test', (tester) async {
    app.main();
    await tester.pumpAndSettle();

    // Login flow
    await tester.enterText(find.byKey(Key('email')), 'test@example.com');
    await tester.enterText(find.byKey(Key('password')), 'password123');
    await tester.tap(find.byKey(Key('login_button')));
    await tester.pumpAndSettle();

    // Verify home page
    expect(find.text('Welcome'), findsOneWidget);
  });
}
```

### Integration Test with Network

```dart
testWidgets('complete purchase flow', (tester) async {
  app.main();
  await tester.pumpAndSettle();

  // Browse products
  await tester.tap(find.text('Products'));
  await tester.pumpAndSettle(Duration(seconds: 2)); // Wait for API

  // Add to cart
  await tester.tap(find.byKey(Key('add_to_cart_0')));
  await tester.pumpAndSettle();

  // Checkout
  await tester.tap(find.byIcon(Icons.shopping_cart));
  await tester.pumpAndSettle();

  await tester.tap(find.text('Checkout'));
  await tester.pumpAndSettle(Duration(seconds: 3)); // Wait for payment

  expect(find.text('Order Complete'), findsOneWidget);
});
```

## Golden Test Templates

### Basic Golden Test

```dart
import 'package:flutter_test/flutter_test.dart';
import 'package:golden_toolkit/golden_toolkit.dart';

void main() {
  testGoldens('widget golden test', (tester) async {
    await tester.pumpWidgetBuilder(
      MyWidget(),
      wrapper: materialAppWrapper(theme: ThemeData.light()),
      surfaceSize: Size(400, 600),
    );

    await screenMatchesGolden(tester, 'my_widget');
  });
}
```

### Multi-Device Golden Test

```dart
testGoldens('responsive layout golden test', (tester) async {
  await tester.pumpWidgetBuilder(
    MyResponsiveWidget(),
    wrapper: materialAppWrapper(),
  );

  await multiScreenGolden(
    tester,
    'responsive_widget',
    devices: [
      Device.phone,
      Device.iphone11,
      Device.tabletPortrait,
      Device.tabletLandscape,
    ],
  );
});
```

## Test Helper Patterns

### Pump Widget Helper

```dart
extension WidgetTesterX on WidgetTester {
  Future<void> pumpApp(Widget widget) async {
    await pumpWidget(
      MaterialApp(
        home: widget,
      ),
    );
  }

  Future<void> pumpWithProvider<T>(Widget widget, T value) async {
    await pumpWidget(
      MaterialApp(
        home: Provider<T>.value(
          value: value,
          child: widget,
        ),
      ),
    );
  }
}
```

### Test Data Fixtures

```dart
// test/fixtures/test_data.dart
class TestData {
  static final user = User(
    id: '1',
    name: 'Test User',
    email: 'test@example.com',
  );

  static final products = [
    Product(id: '1', name: 'Product 1', price: 9.99),
    Product(id: '2', name: 'Product 2', price: 19.99),
  ];

  static final order = Order(
    id: '1',
    userId: '1',
    items: [OrderItem(productId: '1', quantity: 2)],
    total: 19.98,
  );
}
```

## Common Test Matchers

```dart
// Equality
expect(value, equals(expected));
expect(value, isNot(equals(unexpected)));

// Types
expect(value, isA<MyClass>());
expect(value, isNull);
expect(value, isNotNull);

// Numbers
expect(value, greaterThan(5));
expect(value, lessThan(10));
expect(value, closeTo(5.0, 0.1));

// Strings
expect(value, contains('substring'));
expect(value, startsWith('prefix'));
expect(value, endsWith('suffix'));
expect(value, matches(RegExp(r'\d+')));

// Collections
expect(list, isEmpty);
expect(list, isNotEmpty);
expect(list, hasLength(3));
expect(list, contains(item));
expect(map, containsKey('key'));
expect(map, containsValue('value'));

// Widgets
expect(find.text('Hello'), findsOneWidget);
expect(find.byType(MyWidget), findsNWidgets(3));
expect(find.byKey(Key('my_key')), findsNothing);

// Exceptions
expect(() => throwingFunction(), throwsException);
expect(() => throwingFunction(), throwsA(isA<MyException>()));

// Async
await expectLater(future, completion(equals(expected)));
await expectLater(stream, emits(value));
await expectLater(stream, emitsInOrder([1, 2, 3]));
```

## Testing Best Practices

1. **AAA Pattern**: Arrange, Act, Assert
2. **One assertion per test**: Focus on single behavior
3. **Descriptive names**: Test names should describe what they test
4. **Mock external dependencies**: Isolate unit under test
5. **Don't test implementation details**: Test behavior, not internals
6. **Use factories for test data**: Reusable, consistent test objects
7. **Clean up resources**: Dispose controllers, close streams
8. **Test edge cases**: Empty lists, null values, errors
9. **Golden tests for UI**: Catch visual regressions
10. **Integration tests for flows**: Test complete user journeys

## Riverpod Testing Patterns

### Testing a Riverpod Provider

```dart
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:flutter_test/flutter_test.dart';

void main() {
  test('counter provider increments', () {
    final container = ProviderContainer();
    addTearDown(container.dispose);

    expect(container.read(counterProvider), 0);

    container.read(counterProvider.notifier).increment();

    expect(container.read(counterProvider), 1);
  });
}
```

### Testing Riverpod with Overrides

```dart
test('user repository returns mock data', () async {
  final container = ProviderContainer(
    overrides: [
      userRepositoryProvider.overrideWithValue(MockUserRepository()),
    ],
  );
  addTearDown(container.dispose);

  final users = await container.read(usersProvider.future);
  expect(users, hasLength(2));
});
```

### Widget Test with Riverpod

```dart
testWidgets('displays user name from provider', (tester) async {
  await tester.pumpWidget(
    ProviderScope(
      overrides: [
        userProvider.overrideWith((ref) => User(name: 'Test User')),
      ],
      child: const MaterialApp(home: UserProfilePage()),
    ),
  );

  expect(find.text('Test User'), findsOneWidget);
});
```

### Testing AsyncNotifier

```dart
test('async notifier loads and updates data', () async {
  final container = ProviderContainer(
    overrides: [
      apiClientProvider.overrideWithValue(MockApiClient()),
    ],
  );
  addTearDown(container.dispose);

  // Initial state is loading
  expect(
    container.read(productsProvider),
    const AsyncValue<List<Product>>.loading(),
  );

  // Wait for data to load
  await container.read(productsProvider.future);

  // Verify loaded state
  final state = container.read(productsProvider);
  expect(state.value, isNotNull);
  expect(state.value!, hasLength(greaterThan(0)));
});
```

## Patrol Testing Patterns

### Basic Patrol Test

```dart
import 'package:patrol/patrol.dart';

void main() {
  patrolTest('login flow works end-to-end', ($) async {
    await $.pumpWidgetAndSettle(const MyApp());

    // Patrol provides concise finders
    await $(#emailField).enterText('test@example.com');
    await $(#passwordField).enterText('password123');
    await $('Login').tap();

    // Wait for navigation
    await $.pumpAndSettle();

    expect($('Welcome'), findsOneWidget);
  });
}
```

### Patrol Native Interaction Test

```dart
patrolTest('handles native permission dialog', ($) async {
  await $.pumpWidgetAndSettle(const MyApp());

  await $('Enable Location').tap();

  // Interact with native iOS/Android permission dialog
  await $.native.grantPermissionWhenInUse();

  await $.pumpAndSettle();
  expect($('Location enabled'), findsOneWidget);
});
```

### Patrol with Custom Finders

```dart
patrolTest('product list scrolls and loads more', ($) async {
  await $.pumpWidgetAndSettle(const MyApp());

  // Navigate to products
  await $(Icons.shopping_bag).tap();
  await $.pumpAndSettle();

  // Scroll down to trigger pagination
  await $(#productList).scrollTo(find.text('Product 20'));

  expect($('Product 20'), findsOneWidget);
});
```

These patterns provide comprehensive test coverage for Flutter applications.
