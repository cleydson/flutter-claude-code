---
name: flutter-security-patterns
description: Security best practices for Flutter apps. Covers secure storage, API security, authentication, data protection, and common vulnerability prevention.
---

# Flutter Security Patterns

Production-ready security patterns to protect user data and prevent vulnerabilities.

## Secure Storage

### ✅ Never Store Sensitive Data in Plain Text

```dart
// ❌ NEVER DO THIS
await prefs.setString('password', 'user_password');
await prefs.setString('api_token', 'secret_token');

// ✅ Use flutter_secure_storage
import 'package:flutter_secure_storage/flutter_secure_storage.dart';

final storage = FlutterSecureStorage();

// Store
await storage.write(key: 'auth_token', value: token);

// Read
final token = await storage.read(key: 'auth_token');

// Delete
await storage.delete(key: 'auth_token');

// Delete all
await storage.deleteAll();
```

### ✅ Secure Storage Configuration

```dart
// Android: Use EncryptedSharedPreferences (default in v9+)
// iOS: Uses Keychain by default

const secureStorage = FlutterSecureStorage(
  aOptions: AndroidOptions(
    encryptedSharedPreferences: true,
    // Require device authentication to access
    // sharedPreferencesName: 'secure_prefs',
    // preferencesKeyPrefix: 'app_',
  ),
  iOptions: IOSOptions(
    accessibility: KeychainAccessibility.first_unlock_this_device,
    // Use first_unlock_this_device for data that shouldn't transfer to new devices
    // Use first_unlock for data that can migrate via backup
  ),
);

// Recommended: Use --dart-define for compile-time environment config
// flutter build --dart-define=ENV=production
```

## API Security

### ✅ HTTPS Only

```dart
// ❌ NEVER use HTTP in production
final response = await http.get(Uri.parse('http://api.example.com/data'));

// ✅ Always use HTTPS
final response = await http.get(Uri.parse('https://api.example.com/data'));

// ✅ Enforce HTTPS in dio
final dio = Dio(BaseOptions(
  baseUrl: 'https://api.example.com',
  validateStatus: (status) {
    return status! < 500;
  },
));
```

### ✅ Certificate Pinning

```dart
import 'package:dio/dio.dart';
import 'package:dio/io.dart';
import 'dart:io';

class SecureApiClient {
  final Dio dio;

  SecureApiClient() : dio = Dio() {
    // Dio 5.x certificate pinning pattern
    dio.httpClientAdapter = IOHttpClientAdapter(
      createHttpClient: () {
        final client = HttpClient();
        client.badCertificateCallback = (cert, host, port) {
          // Certificate pinning - compare SHA-256 fingerprint
          final certSha256 = sha256.convert(cert.der).toString();
          const expectedHash = 'YOUR_CERTIFICATE_SHA256_HASH';
          return certSha256 == expectedHash;
        };
        return client;
      },
    );
  }
}

// Alternative: Use http_certificate_pinning package for easier management
// import 'package:http_certificate_pinning/http_certificate_pinning.dart';
```

## Authentication Security

### ✅ Secure Token Management

```dart
class AuthService {
  final FlutterSecureStorage _storage;
  final Dio _dio;

  AuthService(this._storage, this._dio) {
    _setupInterceptors();
  }

  void _setupInterceptors() {
    // Add token to requests
    _dio.interceptors.add(InterceptorsWrapper(
      onRequest: (options, handler) async {
        final token = await _storage.read(key: 'auth_token');
        if (token != null) {
          options.headers['Authorization'] = 'Bearer $token';
        }
        handler.next(options);
      },
      onError: (error, handler) async {
        if (error.response?.statusCode == 401) {
          // Token expired - attempt refresh
          final refreshed = await _refreshToken();
          if (refreshed) {
            // Retry original request
            final options = error.requestOptions;
            final token = await _storage.read(key: 'auth_token');
            options.headers['Authorization'] = 'Bearer $token';
            final response = await _dio.fetch(options);
            return handler.resolve(response);
          }
        }
        handler.next(error);
      },
    ));
  }

  Future<bool> _refreshToken() async {
    try {
      final refreshToken = await _storage.read(key: 'refresh_token');
      if (refreshToken == null) return false;

      final response = await _dio.post('/auth/refresh', data: {
        'refresh_token': refreshToken,
      });

      final newToken = response.data['access_token'];
      final newRefreshToken = response.data['refresh_token'];

      await _storage.write(key: 'auth_token', value: newToken);
      await _storage.write(key: 'refresh_token', value: newRefreshToken);

      return true;
    } catch (e) {
      await logout();
      return false;
    }
  }

  Future<void> logout() async {
    await _storage.deleteAll();
    // Navigate to login screen
  }
}
```

### ✅ Biometric Authentication

```dart
import 'package:local_auth/local_auth.dart';
import 'package:local_auth_android/local_auth_android.dart';
import 'package:local_auth_darwin/local_auth_darwin.dart';

class BiometricAuth {
  final LocalAuthentication _auth = LocalAuthentication();

  Future<bool> canCheckBiometrics() async {
    try {
      // Check both biometrics and device credentials
      return await _auth.canCheckBiometrics || await _auth.isDeviceSupported();
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

  Future<bool> authenticate({
    required String localizedReason,
    bool biometricOnly = false,
  }) async {
    try {
      return await _auth.authenticate(
        localizedReason: localizedReason,
        options: AuthenticationOptions(
          stickyAuth: true,
          biometricOnly: biometricOnly,
          sensitiveTransaction: true,
        ),
        authMessages: const <AuthMessages>[
          AndroidAuthMessages(
            signInTitle: 'Authentication Required',
            cancelButton: 'Cancel',
            biometricHint: 'Verify your identity',
          ),
          IOSAuthMessages(
            cancelButton: 'Cancel',
            lockOut: 'Please re-enable biometrics',
          ),
        ],
      );
    } catch (e) {
      return false;
    }
  }
}

// Usage
final biometricAuth = BiometricAuth();

if (await biometricAuth.canCheckBiometrics()) {
  final authenticated = await biometricAuth.authenticate(
    localizedReason: 'Authenticate to access your account',
  );

  if (authenticated) {
    // Proceed to app
  }
}
```

**iOS Configuration Required:**
```xml
<!-- ios/Runner/Info.plist -->
<key>NSFaceIDUsageDescription</key>
<string>We use Face ID to securely authenticate you</string>
```

**Android Configuration Required:**
```xml
<!-- android/app/src/main/AndroidManifest.xml -->
<uses-permission android:name="android.permission.USE_BIOMETRIC"/>
```

## Data Encryption

### ✅ Encrypt Sensitive Data

```dart
import 'package:encrypt/encrypt.dart';

class DataEncryption {
  // ⚠️ NEVER use static keys/IVs - generate and store securely
  final Key _key;
  final Encrypter _encrypter;

  DataEncryption._(this._key) : _encrypter = Encrypter(AES(_key));

  /// Create with a securely stored key
  static Future<DataEncryption> create() async {
    final keyString = await KeyManager.getOrCreateKey();
    final key = Key.fromBase64(keyString);
    return DataEncryption._(key);
  }

  String encrypt(String plainText) {
    // Generate a fresh IV for each encryption
    final iv = IV.fromSecureRandom(16);
    final encrypted = _encrypter.encrypt(plainText, iv: iv);
    // Prepend IV to ciphertext for decryption
    return '${iv.base64}:${encrypted.base64}';
  }

  String decrypt(String encryptedText) {
    final parts = encryptedText.split(':');
    final iv = IV.fromBase64(parts[0]);
    final encrypted = Encrypted.fromBase64(parts[1]);
    return _encrypter.decrypt(encrypted, iv: iv);
  }
}

// Usage
final encryption = await DataEncryption.create();
final encrypted = encryption.encrypt(sensitiveData);
await secureStorage.write(key: 'encrypted_data', value: encrypted);
```

### ✅ Secure Key Storage

```dart
// Generate and store encryption key securely
class KeyManager {
  static const _storage = FlutterSecureStorage();
  static const _keyName = 'encryption_key';

  static Future<String> getOrCreateKey() async {
    var key = await _storage.read(key: _keyName);

    if (key == null) {
      // Generate new key
      key = base64.encode(List<int>.generate(32, (_) => Random.secure().nextInt(256)));
      await _storage.write(key: _keyName, value: key);
    }

    return key;
  }

  static Future<void> deleteKey() async {
    await _storage.delete(key: _keyName);
  }
}
```

## Input Validation

### ✅ Sanitize User Input

```dart
class InputValidator {
  // Email validation
  static bool isValidEmail(String email) {
    final emailRegex = RegExp(
      r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$',
    );
    return emailRegex.hasMatch(email);
  }

  // Password strength
  static bool isStrongPassword(String password) {
    if (password.length < 8) return false;

    final hasUppercase = password.contains(RegExp(r'[A-Z]'));
    final hasLowercase = password.contains(RegExp(r'[a-z]'));
    final hasDigits = password.contains(RegExp(r'[0-9]'));
    final hasSpecialCharacters = password.contains(RegExp(r'[!@#$%^&*(),.?":{}|<>]'));

    return hasUppercase && hasLowercase && hasDigits && hasSpecialCharacters;
  }

  // SQL Injection prevention (use parameterized queries)
  static String sanitizeSql(String input) {
    return input.replaceAll(RegExp(r"[';\"\\]"), '');
  }

  // XSS prevention
  static String sanitizeHtml(String input) {
    return input
        .replaceAll('<', '&lt;')
        .replaceAll('>', '&gt;')
        .replaceAll('"', '&quot;')
        .replaceAll("'", '&#x27;')
        .replaceAll('/', '&#x2F;');
  }

  // Phone number validation
  static bool isValidPhone(String phone) {
    final phoneRegex = RegExp(r'^\+?[1-9]\d{1,14}$');
    return phoneRegex.hasMatch(phone);
  }

  // URL validation
  static bool isValidUrl(String url) {
    try {
      final uri = Uri.parse(url);
      return uri.hasScheme && (uri.scheme == 'http' || uri.scheme == 'https');
    } catch (e) {
      return false;
    }
  }
}
```

## Platform Security

### ✅ Android Security

```xml
<!-- android/app/src/main/AndroidManifest.xml -->

<!-- Prevent screenshots in sensitive screens -->
<application>
  <activity
    android:name=".MainActivity"
    android:windowSoftInputMode="adjustResize">

    <!-- Add this for secure screens -->
    <meta-data
      android:name="io.flutter.embedding.android.EnableScreenshotSecurity"
      android:value="true" />
  </activity>
</application>

<!-- Network security config -->
<application
  android:networkSecurityConfig="@xml/network_security_config">
</application>
```

```xml
<!-- android/app/src/main/res/xml/network_security_config.xml -->
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
  <domain-config cleartextTrafficPermitted="false">
    <domain includeSubdomains="true">yourdomain.com</domain>
  </domain-config>
</network-security-config>
```

### ✅ iOS Security

```xml
<!-- ios/Runner/Info.plist -->

<!-- App Transport Security -->
<key>NSAppTransportSecurity</key>
<dict>
  <key>NSAllowsArbitraryLoads</key>
  <false/>
  <key>NSAllowsLocalNetworking</key>
  <false/>
</dict>
```

### ✅ iOS Privacy Manifests (Required since iOS 17 / Xcode 15)

Apple requires Privacy Manifests (`PrivacyInfo.xcprivacy`) for apps and SDKs that use certain APIs.

```xml
<!-- ios/Runner/PrivacyInfo.xcprivacy -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
  "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>NSPrivacyTracking</key>
  <false/>
  <key>NSPrivacyTrackingDomains</key>
  <array/>
  <key>NSPrivacyCollectedDataTypes</key>
  <array>
    <!-- Declare data types your app collects -->
  </array>
  <key>NSPrivacyAccessedAPITypes</key>
  <array>
    <!-- Declare required reason APIs your app uses -->
    <dict>
      <key>NSPrivacyAccessedAPIType</key>
      <string>NSPrivacyAccessedAPICategoryUserDefaults</string>
      <key>NSPrivacyAccessedAPITypeReasons</key>
      <array>
        <string>CA92.1</string>
      </array>
    </dict>
  </array>
</dict>
</plist>
```

**Privacy Manifest Checklist:**
- [ ] `PrivacyInfo.xcprivacy` added to Xcode project
- [ ] Required Reason APIs declared (UserDefaults, file timestamp, disk space, etc.)
- [ ] Data collection types documented
- [ ] Third-party SDK privacy manifests included
- [ ] Tracking domains declared (if applicable)
- [ ] App Store submission includes privacy nutrition labels

## Code Obfuscation

### ✅ Enable Obfuscation

```bash
# Build with obfuscation
flutter build apk --obfuscate --split-debug-info=./debug-info

# iOS
flutter build ios --obfuscate --split-debug-info=./debug-info
```

### ✅ ProGuard Rules

```proguard
# android/app/proguard-rules.pro

# Keep Flutter classes
-keep class io.flutter.app.** { *; }
-keep class io.flutter.plugin.** { *; }
-keep class io.flutter.util.** { *; }
-keep class io.flutter.view.** { *; }
-keep class io.flutter.** { *; }
-keep class io.flutter.plugins.** { *; }

# Keep your model classes
-keep class com.yourapp.models.** { *; }

# Remove logging
-assumenosideeffects class android.util.Log {
    public static *** d(...);
    public static *** v(...);
    public static *** i(...);
}
```

## Secure Deep Links

### ✅ Validate Deep Link Parameters

```dart
class DeepLinkHandler {
  static Future<void> handleDeepLink(Uri uri) async {
    // Validate scheme
    if (uri.scheme != 'https' && uri.scheme != 'yourapp') {
      throw SecurityException('Invalid scheme');
    }

    // Validate host
    if (uri.host != 'yourdomain.com') {
      throw SecurityException('Invalid host');
    }

    // Sanitize parameters
    final params = uri.queryParameters.map(
      (key, value) => MapEntry(
        key,
        InputValidator.sanitizeHtml(value),
      ),
    );

    // Route based on path
    switch (uri.path) {
      case '/profile':
        final userId = params['id'];
        if (userId != null && isValidUserId(userId)) {
          navigateToProfile(userId);
        }
        break;
      default:
        throw SecurityException('Invalid path');
    }
  }

  static bool isValidUserId(String id) {
    return RegExp(r'^[a-zA-Z0-9]{10,}$').hasMatch(id);
  }
}
```

## Logging Security

### ✅ Never Log Sensitive Data

```dart
import 'package:logger/logger.dart';

class SecureLogger {
  static final _logger = Logger(
    filter: ProductionFilter(), // Only log in debug mode
    printer: PrettyPrinter(),
  );

  static void log(String message, {dynamic error, StackTrace? stackTrace}) {
    // Remove sensitive data before logging
    final sanitized = _sanitizeLog(message);

    if (error != null) {
      _logger.e(sanitized, error, stackTrace);
    } else {
      _logger.i(sanitized);
    }
  }

  static String _sanitizeLog(String message) {
    // Remove potential sensitive patterns
    var sanitized = message;

    // Remove email addresses
    sanitized = sanitized.replaceAll(
      RegExp(r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b'),
      '[EMAIL_REDACTED]',
    );

    // Remove credit card numbers
    sanitized = sanitized.replaceAll(
      RegExp(r'\b\d{4}[\s-]?\d{4}[\s-]?\d{4}[\s-]?\d{4}\b'),
      '[CC_REDACTED]',
    );

    // Remove phone numbers
    sanitized = sanitized.replaceAll(
      RegExp(r'\b\+?[1-9]\d{1,14}\b'),
      '[PHONE_REDACTED]',
    );

    // Remove tokens (Bearer pattern)
    sanitized = sanitized.replaceAll(
      RegExp(r'Bearer\s+[\w-]+\.[\w-]+\.[\w-]+'),
      'Bearer [TOKEN_REDACTED]',
    );

    return sanitized;
  }
}

// ❌ NEVER log sensitive data
logger.d('User password: $password'); // NEVER!
logger.d('API token: $token'); // NEVER!
logger.d('Credit card: $ccNumber'); // NEVER!

// ✅ Use secure logger
SecureLogger.log('User login attempt'); // Safe
```

## Permissions Security

### ✅ Request Minimum Permissions

```yaml
# Only request what you need
# android/app/src/main/AndroidManifest.xml
<uses-permission android:name="android.permission.CAMERA" />

# NOT this unless you need it:
# <uses-permission android:name="android.permission.READ_CONTACTS" />
```

### ✅ Runtime Permission Handling

```dart
import 'package:permission_handler/permission_handler.dart';

class PermissionManager {
  static Future<bool> requestCameraPermission() async {
    final status = await Permission.camera.status;

    if (status.isGranted) {
      return true;
    }

    if (status.isDenied) {
      final result = await Permission.camera.request();
      return result.isGranted;
    }

    if (status.isPermanentlyDenied) {
      // Show dialog to open settings
      await openAppSettings();
      return false;
    }

    return false;
  }
}
```

## Security Checklist

### ✅ Pre-Release Security Audit

- [ ] All secrets in secure storage (not SharedPreferences)
- [ ] HTTPS only for all network requests
- [ ] Certificate pinning implemented
- [ ] API tokens refreshed automatically
- [ ] Biometric auth for sensitive operations
- [ ] Data encrypted at rest
- [ ] Input validation on all user inputs
- [ ] SQL injection prevention (parameterized queries)
- [ ] XSS prevention (sanitize HTML)
- [ ] Code obfuscation enabled
- [ ] ProGuard/R8 configured
- [ ] Debug logs removed from production
- [ ] Sensitive data not logged
- [ ] Screenshots disabled for secure screens
- [ ] Deep links validated and sanitized
- [ ] Minimum permissions requested
- [ ] Runtime permissions handled gracefully
- [ ] No hardcoded API keys in code
- [ ] Environment variables for secrets
- [ ] Dependency vulnerabilities checked
- [ ] Security patches applied

### ✅ Environment Variables

```bash
# Preferred: Use --dart-define-from-file (compile-time, no runtime dependency)
# Create env.json (ADD TO .gitignore!)
# {
#   "API_KEY": "your_api_key_here",
#   "API_URL": "https://api.example.com"
# }

flutter run --dart-define-from-file=env.json
flutter build apk --dart-define-from-file=env.json
```

```dart
// Access compile-time environment variables in Dart
class EnvConfig {
  static const apiKey = String.fromEnvironment('API_KEY');
  static const apiUrl = String.fromEnvironment('API_URL',
    defaultValue: 'https://api.example.com');
}

// Usage
final response = await dio.get(
  '${EnvConfig.apiUrl}/data',
  options: Options(headers: {'X-API-Key': EnvConfig.apiKey}),
);

// ❌ NEVER commit env.json to git!
// Add to .gitignore:
// env.json
// env.*.json
```

### ✅ App Attestation & Integrity Verification

```dart
// iOS: App Attest (DeviceCheck framework)
// Verifies app authenticity to your backend
// Use package: app_attest or implement via platform channels

// Android: Play Integrity API (replaces SafetyNet)
// Verifies device and app integrity
// Use package: play_integrity

// Implementation pattern:
class IntegrityService {
  /// Request integrity token and send to backend for verification
  Future<bool> verifyAppIntegrity() async {
    // 1. Request a nonce from your backend
    // 2. Request integrity token from platform API
    // 3. Send token to your backend for verification
    // 4. Backend verifies with Google/Apple servers
    throw UnimplementedError('Implement per platform');
  }
}
```

**Checklist:**
- [ ] App Attest configured for iOS (DeviceCheck framework)
- [ ] Play Integrity API configured for Android
- [ ] Backend verification endpoint implemented
- [ ] Fallback handling for unsupported devices

## Common Vulnerabilities

### ❌ Avoid These

```dart
// 1. Hardcoded secrets
const apiKey = 'abc123'; // NEVER!

// 2. Plain text storage
prefs.setString('password', 'secret'); // NEVER!

// 3. HTTP in production
final url = 'http://api.example.com'; // NEVER!

// 4. Unvalidated input
final userId = request.params['id']; // Validate first!

// 5. SQL injection
db.rawQuery('SELECT * FROM users WHERE id = $id'); // Use parameterized!

// 6. Logging sensitive data
print('Password: $password'); // NEVER!

// 7. Weak encryption
final hash = md5.convert(password.codeUnits); // Use bcrypt!

// 8. No authentication
// Unprotected routes without auth checks
```

This security guide ensures your Flutter app follows industry best practices.
