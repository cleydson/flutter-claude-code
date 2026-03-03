# Flutter Agent Usage Scenarios - Quick Reference

This document provides practical examples of how to use the Flutter agent ecosystem for common development scenarios.

---

## Scenario 1: Converting a Figma Design to Flutter (Complete Workflow)

**Goal**: Take a Figma design and create a pixel-perfect Flutter implementation.

### Step-by-Step Workflow

**Step 1: Export Design**
- Export screens from Figma as PNG (2x or 3x resolution)
- Save to project directory: `/designs/screen_name.png`

**Step 2: Invoke Design Iteration Coordinator**
```
User: "I have a login screen design at /designs/login_screen.png.
Please create a pixel-perfect Flutter implementation of this design."

flutter-design-iteration-coordinator will:
1. Invoke flutter-ui-designer to analyze the design
2. Invoke flutter-ui-implementer to generate code
3. Invoke flutter-device-orchestrator to run on simulator
4. Invoke flutter-ui-comparison to validate accuracy
5. Iterate until >95% fidelity achieved
```

**Expected Output:**
- `lib/screens/login_screen.dart` - Complete implementation
- `designs/login_screen_comparison.png` - Comparison report
- Iteration log showing fidelity improvements

**Time Estimate**: 5-10 minutes for simple screen, 15-30 minutes for complex screen

---

## Scenario 2: Setting Up a New Flutter Project with Clean Architecture

**Goal**: Initialize a new Flutter project with proper architecture and folder structure.

### Workflow

**User Request:**
```
"Create a new Flutter e-commerce app with Clean Architecture,
featuring authentication, product catalog, shopping cart, and checkout."
```

**flutter-architect will:**

1. Create folder structure:
```
lib/
  core/
    constants/
    utils/
    errors/
    network/
  features/
    authentication/
      data/
        models/
        repositories/
        data_sources/
      domain/
        entities/
        repositories/
        use_cases/
      presentation/
        pages/
        widgets/
        bloc/
    products/
      ... (same structure)
    cart/
      ... (same structure)
    checkout/
      ... (same structure)
  shared/
    widgets/
    themes/
```

2. Set up dependency injection
3. Configure routing
4. Create base classes and interfaces
5. Set up state management structure

**flutter-state-management will:**
1. Recommend BLoC for this medium-large app
2. Set up flutter_bloc package
3. Create base BLoC classes
4. Implement authentication BLoC as example
5. Configure BLoC providers

**Expected Output:**
- Complete project structure
- Base classes and interfaces
- Dependency injection setup
- Routing configuration
- Example BLoC implementation
- README with architecture documentation

---

## Scenario 3: Integrating Native iOS Camera Features

**Goal**: Access native iOS camera features not available in Flutter plugins.

### Workflow

**User Request:**
```
"I need to access iOS native camera features for custom image processing.
Create a platform channel that allows me to capture photos with manual
exposure and focus control."
```

**flutter-platform-channel-architect will:**

1. Design platform channel architecture:
```dart
// lib/services/native_camera_service.dart
class NativeCameraService {
  static const platform = MethodChannel('com.myapp/camera');

  Future<String?> captureWithManualControls({
    required double exposure,
    required double focus,
  }) async {
    try {
      final String? imagePath = await platform.invokeMethod(
        'capturePhoto',
        {'exposure': exposure, 'focus': focus},
      );
      return imagePath;
    } on PlatformException catch (e) {
      throw CameraException(e.message);
    }
  }
}
```

**flutter-ios-integration will:**

2. Create iOS implementation:
```swift
// ios/Runner/CameraChannel.swift
import AVFoundation

class CameraChannel {
  func register(with registrar: FlutterPluginRegistrar) {
    let channel = FlutterMethodChannel(
      name: "com.myapp/camera",
      binaryMessenger: registrar.messenger()
    )

    channel.setMethodCallHandler { call, result in
      if call.method == "capturePhoto" {
        guard let args = call.arguments as? [String: Any],
              let exposure = args["exposure"] as? Double,
              let focus = args["focus"] as? Double else {
          result(FlutterError(code: "INVALID_ARGS"))
          return
        }

        self.capturePhoto(exposure: exposure, focus: focus) { imagePath in
          result(imagePath)
        }
      }
    }
  }

  private func capturePhoto(exposure: Double, focus: Double,
                           completion: @escaping (String?) -> Void) {
    // AVFoundation camera implementation
    // ...
  }
}
```

**Expected Output:**
- Dart service class with type-safe methods
- iOS Swift implementation
- Error handling on both sides
- Example usage code
- Documentation

---

## Scenario 4: Integrating Firebase Authentication and Firestore

**Goal**: Set up Firebase with email authentication and Firestore database.

### Workflow

**User Request:**
```
"Set up Firebase Authentication with email/password and Google Sign-In,
plus Firestore for storing user profiles and posts."
```

**flutter-firebase will:**

1. Configure Firebase project:
```bash
# Install FlutterFire CLI
dart pub global activate flutterfire_cli

# Configure Firebase (generates firebase_options.dart)
flutterfire configure
```

2. Add dependencies:
```yaml
dependencies:
  firebase_core: ^3.8.0
  firebase_auth: ^5.4.0
  google_sign_in: ^6.2.2
  cloud_firestore: ^5.6.0
```

3. Create authentication service:
```dart
// lib/services/auth_service.dart
class AuthService {
  final FirebaseAuth _auth = FirebaseAuth.instance;
  final GoogleSignIn _googleSignIn = GoogleSignIn();

  Stream<User?> get authStateChanges => _auth.authStateChanges();

  Future<UserCredential> signInWithEmail(String email, String password) async {
    return await _auth.signInWithEmailAndPassword(
      email: email,
      password: password,
    );
  }

  Future<UserCredential> signInWithGoogle() async {
    final GoogleSignInAccount? googleUser = await _googleSignIn.signIn();
    final GoogleSignInAuthentication? googleAuth =
        await googleUser?.authentication;

    final credential = GoogleAuthProvider.credential(
      accessToken: googleAuth?.accessToken,
      idToken: googleAuth?.idToken,
    );

    return await _auth.signInWithCredential(credential);
  }

  Future<void> signOut() async {
    await Future.wait([
      _auth.signOut(),
      _googleSignIn.signOut(),
    ]);
  }
}
```

4. Create Firestore service:
```dart
// lib/services/firestore_service.dart
class FirestoreService {
  final FirebaseFirestore _db = FirebaseFirestore.instance;

  // User profiles
  Future<void> createUserProfile(String uid, Map<String, dynamic> data) async {
    await _db.collection('users').doc(uid).set(data);
  }

  Stream<UserProfile> getUserProfile(String uid) {
    return _db.collection('users').doc(uid).snapshots()
      .map((doc) => UserProfile.fromJson(doc.data()!));
  }

  // Posts
  Future<void> createPost(Post post) async {
    await _db.collection('posts').add(post.toJson());
  }

  Stream<List<Post>> getPosts({int limit = 20}) {
    return _db.collection('posts')
      .orderBy('createdAt', descending: true)
      .limit(limit)
      .snapshots()
      .map((snapshot) => snapshot.docs
        .map((doc) => Post.fromJson(doc.data()))
        .toList());
  }
}
```

**flutter-ios-integration & flutter-android-integration will:**

5. Configure platform-specific settings:

iOS (`ios/Runner/Info.plist`):
```xml
<key>CFBundleURLTypes</key>
<array>
  <dict>
    <key>CFBundleTypeRole</key>
    <string>Editor</string>
    <key>CFBundleURLSchemes</key>
    <array>
      <string>com.googleusercontent.apps.YOUR_IOS_CLIENT_ID</string>
    </array>
  </dict>
</array>
```

Android (`android/app/build.gradle`):
```gradle
dependencies {
    implementation 'com.google.firebase:firebase-bom:32.7.0'
    implementation 'com.google.android.gms:play-services-auth:20.7.0'
}
```

**Expected Output:**
- Configured Firebase project
- Authentication service with email and Google sign-in
- Firestore service with CRUD operations
- Platform configurations
- Example usage in UI
- Security rules for Firestore

---

## Scenario 5: Building and Deploying to App Store and Play Store

**Goal**: Deploy Flutter app to both iOS App Store and Google Play Store.

### iOS Deployment Workflow

**User Request:**
```
"Prepare my Flutter app for iOS App Store submission.
App version is 1.2.0, build 5."
```

**flutter-ios-deployment will:**

1. Update version in `pubspec.yaml`:
```yaml
version: 1.2.0+5
```

2. Configure Xcode project:
- Bundle ID: com.company.appname
- Deployment target: iOS 13.0+
- Display name configured
- App icons added (all sizes)
- Launch screen configured

3. Configure code signing:
```bash
# Ensure signing is configured in Xcode
# Automatic signing enabled for development
# Manual signing for distribution
```

4. Create release build:
```bash
# Build with obfuscation
flutter build ipa \
  --release \
  --obfuscate \
  --split-debug-info=build/ios/debug-info

# Output: build/ios/ipa/app_name.ipa
```

5. Upload to App Store:
```bash
# Using Transporter app (GUI)
# OR using command line:
xcrun altool --upload-app \
  --type ios \
  --file build/ios/ipa/app_name.ipa \
  --username your@email.com \
  --password @keychain:AC_PASSWORD
```

6. Provide checklist:
- [ ] App Store Connect app created
- [ ] Privacy policy URL added
- [ ] App description and screenshots prepared
- [ ] TestFlight testing completed
- [ ] Age rating configured
- [ ] App category selected
- [ ] Ready for submission

---

### Android Deployment Workflow

**User Request:**
```
"Prepare my Flutter app for Google Play Store submission.
App version is 1.2.0, build 5."
```

**flutter-android-deployment will:**

1. Update version in `pubspec.yaml`:
```yaml
version: 1.2.0+5
```

2. Configure Gradle:
```kotlin
// android/app/build.gradle.kts
android {
    defaultConfig {
        applicationId = "com.company.appname"
        minSdk = 21
        targetSdk = 34
        versionCode = 5
        versionName = "1.2.0"
    }

    signingConfigs {
        create("release") {
            storeFile = file("../keystore.jks")
            storePassword = System.getenv("KEYSTORE_PASSWORD")
            keyAlias = System.getenv("KEY_ALIAS")
            keyPassword = System.getenv("KEY_PASSWORD")
        }
    }

    buildTypes {
        release {
            signingConfig = signingConfigs.getByName("release")
            isMinifyEnabled = true
            proguardFiles(
                getDefaultProguardFile("proguard-android-optimize.txt"),
                "proguard-rules.pro"
            )
        }
    }
}
```

3. Create signing key (if needed):
```bash
keytool -genkey -v \
  -keystore android/keystore.jks \
  -keyalg RSA \
  -keysize 2048 \
  -validity 10000 \
  -alias upload
```

4. Create release build:
```bash
# App Bundle (recommended)
flutter build appbundle --release

# OR architecture-specific APKs
flutter build apk --split-per-abi --release

# Outputs:
# build/app/outputs/bundle/release/app-release.aab
# OR
# build/app/outputs/flutter-apk/app-armeabi-v7a-release.apk
# build/app/outputs/flutter-apk/app-arm64-v8a-release.apk
# build/app/outputs/flutter-apk/app-x86_64-release.apk
```

5. Upload to Play Console:
- Manual upload through Play Console
- OR use Fastlane for automation

6. Provide checklist:
- [ ] Play Console app created
- [ ] App signing by Google Play enabled
- [ ] Privacy policy URL added
- [ ] App description and screenshots prepared
- [ ] Content rating completed
- [ ] Target audience selected
- [ ] Internal testing track uploaded
- [ ] Ready for production release

---

## Scenario 6: Optimizing App Performance

**Goal**: Identify and fix performance issues causing janky scrolling.

### Workflow

**User Request:**
```
"My product list screen is janky when scrolling.
Help me identify and fix the performance issues."
```

**flutter-performance-analyzer will:**

1. Profile the app with DevTools:
```bash
# Run app in profile mode
flutter run --profile

# Open DevTools
dart devtools
```

2. Analyze performance issues:
- Identify frames taking >16ms (causing jank)
- Check for expensive build operations
- Identify unnecessary rebuilds
- Analyze memory usage
- Check for large images

3. Generate report:
```
Performance Issues Detected:

1. ProductListItem widget rebuilds on every scroll (HIGH PRIORITY)
   - Cause: State management triggering rebuilds
   - Fix: Use const constructor and key

2. Product images not optimized (HIGH PRIORITY)
   - Cause: Full resolution images loaded (4000x3000)
   - Fix: Use cacheHeight/cacheWidth, or resize on server

3. Expensive build method in ProductCard (MEDIUM PRIORITY)
   - Cause: Complex calculations in build method
   - Fix: Move calculations to computed properties

4. No RepaintBoundary around list items (LOW PRIORITY)
   - Cause: Entire list repaints on any change
   - Fix: Add RepaintBoundary wrapper
```

**flutter-performance-optimizer will:**

4. Implement fixes:

**Fix 1: Use const and keys**
```dart
// Before
class ProductList extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      itemCount: products.length,
      itemBuilder: (context, index) {
        return ProductListItem(product: products[index]);
      },
    );
  }
}

// After
class ProductList extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      itemCount: products.length,
      itemBuilder: (context, index) {
        return ProductListItem(
          key: ValueKey(products[index].id),
          product: products[index],
        );
      },
    );
  }
}

class ProductListItem extends StatelessWidget {
  const ProductListItem({Key? key, required this.product}) : super(key: key);
  final Product product;

  // ... rest of implementation
}
```

**Fix 2: Optimize images**
```dart
// Before
Image.network(product.imageUrl)

// After
Image.network(
  product.imageUrl,
  cacheHeight: 300,
  cacheWidth: 300,
  errorBuilder: (context, error, stackTrace) {
    return const Icon(Icons.error);
  },
  loadingBuilder: (context, child, loadingProgress) {
    if (loadingProgress == null) return child;
    return Center(
      child: CircularProgressIndicator(
        value: loadingProgress.expectedTotalBytes != null
          ? loadingProgress.cumulativeBytesLoaded /
            loadingProgress.expectedTotalBytes!
          : null,
      ),
    );
  },
)
```

**Fix 3: Move calculations out of build**
```dart
// Before
class ProductCard extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final discountedPrice = product.price * (1 - product.discount);
    final savings = product.price - discountedPrice;
    final rating = calculateAverageRating(product.reviews);

    return Card(...);
  }
}

// After
class ProductCard extends StatelessWidget {
  ProductCard({required this.product}) {
    _discountedPrice = product.price * (1 - product.discount);
    _savings = product.price - _discountedPrice;
    _rating = product.averageRating; // Pre-calculated in model
  }

  late final double _discountedPrice;
  late final double _savings;
  late final double _rating;

  @override
  Widget build(BuildContext context) {
    return Card(...);
  }
}
```

**Fix 4: Add RepaintBoundary**
```dart
ListView.builder(
  itemCount: products.length,
  itemBuilder: (context, index) {
    return RepaintBoundary(
      child: ProductListItem(
        key: ValueKey(products[index].id),
        product: products[index],
      ),
    );
  },
)
```

5. Re-profile and verify improvements:
```
Performance Improvements:

Frame rendering: 42ms → 8ms (81% improvement)
Memory usage: 156MB → 98MB (37% reduction)
Jank frames: 47% → 2% (96% reduction)

Status: OPTIMIZED ✓
```

---

## Scenario 7: Adding Platform-Specific Features (iOS & Android Parity)

**Goal**: Implement biometric authentication that works on both iOS and Android.

### Workflow

**User Request:**
```
"Add biometric authentication (Face ID/Touch ID on iOS,
fingerprint/face unlock on Android) to the login screen."
```

**flutter-ios-integration + flutter-android-integration will:**

1. Add dependencies:
```yaml
dependencies:
  local_auth: ^2.3.0
```

2. Configure iOS (`ios/Runner/Info.plist`):
```xml
<key>NSFaceIDUsageDescription</key>
<string>We use Face ID to securely authenticate you</string>
```

3. Configure Android (`android/app/src/main/AndroidManifest.xml`):
```xml
<manifest>
  <uses-permission android:name="android.permission.USE_BIOMETRIC"/>
</manifest>
```

4. Create biometric service:
```dart
// lib/services/biometric_service.dart
import 'package:local_auth/local_auth.dart';
import 'package:local_auth_android/local_auth_android.dart';
import 'package:local_auth_ios/local_auth_ios.dart';

class BiometricService {
  final LocalAuthentication _auth = LocalAuthentication();

  Future<bool> isBiometricAvailable() async {
    try {
      return await _auth.canCheckBiometrics;
    } catch (e) {
      return false;
    }
  }

  Future<List<BiometricType>> getAvailableBiometrics() async {
    try {
      return await _auth.getAvailableBiometrics();
    } catch (e) {
      return [];
    }
  }

  Future<bool> authenticate() async {
    try {
      return await _auth.authenticate(
        localizedReason: 'Please authenticate to continue',
        options: const AuthenticationOptions(
          stickyAuth: true,
          biometricOnly: true,
        ),
        authMessages: const <AuthMessages>[
          AndroidAuthMessages(
            signInTitle: 'Biometric Authentication',
            cancelButton: 'Cancel',
          ),
          IOSAuthMessages(
            cancelButton: 'Cancel',
          ),
        ],
      );
    } catch (e) {
      return false;
    }
  }
}
```

5. Integrate into login flow:
```dart
// lib/screens/login_screen.dart
class LoginScreen extends StatelessWidget {
  final BiometricService _biometricService = BiometricService();

  Future<void> _handleBiometricLogin() async {
    // Check if biometric is available
    final isAvailable = await _biometricService.isBiometricAvailable();
    if (!isAvailable) {
      _showError('Biometric authentication not available');
      return;
    }

    // Get available biometric types
    final types = await _biometricService.getAvailableBiometrics();
    final biometricType = types.contains(BiometricType.face)
      ? 'Face ID'
      : 'Fingerprint';

    // Authenticate
    final authenticated = await _biometricService.authenticate();
    if (authenticated) {
      // Proceed with login
      _navigateToHome();
    } else {
      _showError('Authentication failed');
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Column(
        children: [
          // Email/password fields...

          ElevatedButton(
            onPressed: _handleBiometricLogin,
            child: const Text('Login with Biometrics'),
          ),
        ],
      ),
    );
  }
}
```

**Expected Output:**
- Biometric service working on both platforms
- Platform-specific UI messages
- Graceful fallback if biometrics unavailable
- Consistent user experience on iOS and Android

---

## Scenario 8: Setting Up CI/CD for Automated Testing and Deployment

**Goal**: Set up GitHub Actions for automated testing and deployment.

### Workflow

**User Request:**
```
"Set up CI/CD with GitHub Actions to:
1. Run tests on every PR
2. Build and deploy to TestFlight on merge to main
3. Build and deploy to Play Store internal testing on merge to main"
```

**flutter-ios-deployment + flutter-android-deployment will:**

1. Create `.github/workflows/test.yml`:
```yaml
name: Test

on:
  pull_request:
    branches: [ main, develop ]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: subosito/flutter-action@v2
        with:
          flutter-version: '3.29.x'

      - name: Install dependencies
        run: flutter pub get

      - name: Run analyzer
        run: flutter analyze

      - name: Run tests
        run: flutter test --coverage

      - name: Upload coverage
        uses: codecov/codecov-action@v4
        with:
          files: ./coverage/lcov.info
```

2. Create `.github/workflows/deploy-ios.yml`:
```yaml
name: Deploy iOS

on:
  push:
    branches: [ main ]

jobs:
  deploy-ios:
    runs-on: macos-latest
    steps:
      - uses: actions/checkout@v4

      - uses: subosito/flutter-action@v2
        with:
          flutter-version: '3.29.x'

      - name: Install dependencies
        run: flutter pub get

      - name: Build iOS
        run: |
          flutter build ipa \
            --release \
            --obfuscate \
            --split-debug-info=build/ios/debug-info \
            --export-options-plist=ios/ExportOptions.plist

      - name: Upload to TestFlight
        env:
          APP_STORE_CONNECT_KEY_ID: ${{ secrets.APP_STORE_CONNECT_KEY_ID }}
          APP_STORE_CONNECT_ISSUER_ID: ${{ secrets.APP_STORE_CONNECT_ISSUER_ID }}
          APP_STORE_CONNECT_KEY: ${{ secrets.APP_STORE_CONNECT_KEY }}
        run: |
          xcrun altool --upload-app \
            --type ios \
            --file build/ios/ipa/*.ipa \
            --apiKey $APP_STORE_CONNECT_KEY_ID \
            --apiIssuer $APP_STORE_CONNECT_ISSUER_ID
```

3. Create `.github/workflows/deploy-android.yml`:
```yaml
name: Deploy Android

on:
  push:
    branches: [ main ]

jobs:
  deploy-android:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-java@v4
        with:
          distribution: 'zulu'
          java-version: '17'

      - uses: subosito/flutter-action@v2
        with:
          flutter-version: '3.29.x'

      - name: Decode keystore
        env:
          KEYSTORE_BASE64: ${{ secrets.KEYSTORE_BASE64 }}
        run: |
          echo $KEYSTORE_BASE64 | base64 -d > android/keystore.jks

      - name: Build Android
        env:
          KEYSTORE_PASSWORD: ${{ secrets.KEYSTORE_PASSWORD }}
          KEY_ALIAS: ${{ secrets.KEY_ALIAS }}
          KEY_PASSWORD: ${{ secrets.KEY_PASSWORD }}
        run: flutter build appbundle --release

      - name: Upload to Play Store
        uses: r0adkll/upload-google-play@v1
        with:
          serviceAccountJsonPlainText: ${{ secrets.PLAY_STORE_SERVICE_ACCOUNT }}
          packageName: com.company.appname
          releaseFiles: build/app/outputs/bundle/release/app-release.aab
          track: internal
```

**Expected Output:**
- Automated testing on every PR
- Automated iOS TestFlight deployment
- Automated Android internal testing deployment
- Setup instructions for required secrets
- Deployment status notifications

---

## Summary: When to Use Which Agent

| Scenario | Primary Agent(s) | Supporting Agent(s) |
|----------|-----------------|-------------------|
| Convert design to UI | flutter-design-iteration-coordinator | flutter-ui-designer, flutter-ui-implementer, flutter-device-orchestrator, flutter-ui-comparison |
| New project setup | flutter-architect | flutter-state-management |
| Native iOS feature | flutter-ios-integration | flutter-platform-channel-architect |
| Native Android feature | flutter-android-integration | flutter-platform-channel-architect |
| REST API integration | flutter-rest-api | flutter-state-management |
| Firebase integration | flutter-firebase | flutter-ios-integration, flutter-android-integration |
| AWS integration | flutter-aws | flutter-state-management |
| GraphQL integration | flutter-graphql | flutter-state-management |
| Performance issues | flutter-performance-analyzer | flutter-performance-optimizer |
| Add tests | flutter-testing | - |
| Deploy to App Store | flutter-ios-deployment | - |
| Deploy to Play Store | flutter-android-deployment | - |
| Both deployments | flutter-ios-deployment, flutter-android-deployment | - |

---

## Tips for Effective Agent Usage

1. **Be Specific**: Provide detailed requirements and constraints
2. **Provide Context**: Share relevant files, screenshots, or designs
3. **Iterate**: Use feedback loops for complex implementations
4. **Combine Agents**: Many scenarios benefit from multiple agents working together
5. **Review Output**: Always review generated code before deploying
6. **Test Thoroughly**: Run tests and manual QA after agent implementations
7. **Document Changes**: Keep track of what agents implemented for team knowledge

---

This quick reference should help you effectively utilize the Flutter agent ecosystem for common development scenarios.
