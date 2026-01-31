<p align="center">
  <img  src="https://dasa7d6hxd0bp.cloudfront.net/images/mirrorfly.webp" data-canonical-src="https://dasa7d6hxd0bp.cloudfront.net/images/mirrorfly.webp" width="400"  alt=""/>
</p>

# **MirrorFly Flutter SDK For Video Chat & Calls**

[![Flutter Video SDK](https://img.shields.io/badge/Flutter%20Video%20SDK-blue)](https://www.mirrorfly.com/flutter-chat-sdk.php)
[![Docs](https://img.shields.io/badge/Docs-grey)](https://www.mirrorfly.com/docs/chat/flutter-plugin/v1/quick-start/)

# MirrorFly Flutter Plugin

MirrorFly Flutter Plugin is a powerful real-time communication SDK that enables chat, voice, video calling, live streaming, and AI-powered communication features in Flutter applications.

This repository helps you integrate MirrorFly’s Flutter SDK into Android and iOS apps with minimal development effort. Using a customizable white-label solution, you can build native messaging and calling experiences quickly while maintaining full data ownership and flexible deployment options.

This documentation covers all essential libraries, dependencies, supplementary features, and step-by-step installation instructions to make your Flutter app communication-ready.

---

## 🤹 Key Product Offerings

MirrorFly helps build omni-channel communication apps for any kind of business.

- 💬 **In-app Messaging** – One-to-one and group messaging in real time
- 🎯 **HD Video Calling** – Face-to-face communication from anywhere
- 🦾 **HQ Voice Calling** – Crystal-clear audio with latency as low as 3ms
- 🤖 **AI Voice Agent** – Intelligent AI voicebots for automated conversations
- 🤖 **AI Chatbot** – White-label AI chatbots for autonomous engagement
- 🦾 **Live Streaming** – Broadcast video content to millions of users

---

## ⚒️ MirrorFly Flutter Chat & Video Calls Plugin

### Requirements

#### Android

- Android Lollipop 5.0 (API Level 21) or above
- Java 8 or higher
- Gradle 4.1.0 or higher
- compileSdk 34
- targetSdkVersion 34 or above

#### iOS

- iOS 13.0 or above

---

## 🔑 Get MirrorFly License Key

To obtain your license key:

1. Sign up for a free MirrorFly account in the MirrorFly Console.
2. Navigate to the **Overview** page.
3. Copy the license key from the **Application Info** section.

---

## 📦 Create Android Dependency

Add the following to your root `build.gradle` file:

```gradle
allprojects {
  repositories {
    jcenter()
    maven {
      url "https://repo.mirrorfly.com/release"
    }
  }
}

android {
  compileSdk 34
  defaultConfig {
    minSdkVersion 21
    targetSdkVersion 34
  }
}
```

---

## 📦 Create iOS Dependency

Add the following at the end of `ios/Podfile`:

```ruby
post_install do |installer|
  installer.aggregate_targets.each do |target|
    target.xcconfigs.each do |variant, xcconfig|
      xcconfig_path = target.client_root + target.xcconfig_relative_path(variant)
      IO.write(
        xcconfig_path,
        IO.read(xcconfig_path).gsub("DT_TOOLCHAIN_DIR", "TOOLCHAIN_DIR")
      )
    end
  end

  installer.pods_project.targets.each do |target|
    flutter_additional_ios_build_settings(target)
    target.build_configurations.each do |config|
      config.build_settings['IPHONEOS_DEPLOYMENT_TARGET'] = '12.1'
      config.build_settings['ENABLE_BITCODE'] = 'NO'
      config.build_settings['APPLICATION_EXTENSION_API_ONLY'] = 'No'
      config.build_settings['BUILD_LIBRARY_FOR_DISTRIBUTION'] = 'YES'
      config.build_settings["EXCLUDED_ARCHS[sdk=iphonesimulator*]"] = 'arm64'
    end
  end
end
```

Enable required capabilities:

```
Project → Target → Signing & Capabilities → Click “+” → Add required capabilities
```

---

## 📦 Create Flutter Dependency

Add the dependency to `pubspec.yaml`:

```yaml
dependencies:
  mirrorfly_plugin: ^1.5.0
```

Run:

```bash
flutter pub get
```

Import the SDK:

```dart
import 'package:mirrorfly_plugin/mirrorfly.dart';
```

---

## 🚀 Initialize MirrorFly Plugin

Add this code in `main.dart` before calling `runApp()`:

```dart
void main() {
  WidgetsFlutterBinding.ensureInitialized();
  Mirrorfly.initializeSDK(
    licenseKey: LICENSE_KEY,
    iOSContainerID: iOS_APP_GROUP_ID,
    chatHistoryEnable: ENABLE_CHAT_HISTORY,
    enableDebugLog: ENABLE_DEBUG_LOG,
    flyCallback: (FlyResponse response) {
      runApp(const MyApp());
    },
  );
}
```

**Note:** Chat History securely syncs conversations across devices.

---

## 🔐 Login

```dart
Mirrorfly.login(
  userIdentifier: userIdentifier,
  fcmToken: token,
  isForceRegister: isForceRegister,
  identifierMetaData: identifierMetaData,
  flyCallback: (FlyResponse response) {
    if (response.isSuccess && response.hasData) {
      var userData = registerModelFromJson(value);
    }
  },
);
```

**Important:** Do not call login more than once unless the session is logged out.

---

## 💬 Send a One-to-One Message

```dart
var textMessage = MessageParams.text(
  toJid: toJid,
  replyMessageId: replyMessageId,
  topicId: topicId,
  textMessageParams: TextMessageParams(
    messageText: messageText,
  ),
);

Mirrorfly.sendMessage(
  messageParams: textMessage,
  flyCallback: (response) {
    if (response.isSuccess) {}
  },
);
```

---

## 📩 Receive a One-to-One Message

```dart
Mirrorfly.onMessageReceived.listen((result) {
  var chatMessage = sendMessageModelFromJson(result);
});
```

---

## 📞 Voice & Video Call Integration

### Required Permissions

- Audio Call: Microphone
- Video Call: Microphone, Camera

---

### Make a Voice Call

```dart
Mirrorfly.makeVoiceCall(
  toUserJid: USER_JID,
  flyCallBack: (FlyResponse response) {
    if (response.isSuccess) {}
  },
);
```

### Make a Video Call

```dart
Mirrorfly.makeVideoCall(
  toUserJid: USER_JID,
  flyCallBack: (FlyResponse response) {
    if (response.isSuccess) {}
  },
);
```

### Group Voice Call

```dart
Mirrorfly.makeGroupVoiceCall(
  groupJid: GROUP_JID,
  toUserJidList: USER_LIST,
  flyCallBack: (FlyResponse response) {
    if (response.isSuccess) {}
  },
);
```

### Group Video Call

```dart
Mirrorfly.makeGroupVideoCall(
  groupJid: GROUP_JID,
  toUserJidList: USER_LIST,
  flyCallBack: (FlyResponse response) {
    if (response.isSuccess) {}
  },
);
```

### Add Participants to Ongoing Call

```dart
Mirrorfly.inviteUsersToOngoingCall(
  jidList: USER_LIST,
  flyCallback: (FlyResponse response) {
    if (response.isSuccess) {}
  },
);
```

### Answer Incoming Call

```dart
Mirrorfly.onCallStatusUpdated.listen((event) {
  var statusUpdateReceived = jsonDecode(event);
});
```

### Disconnect Call

```dart
Mirrorfly.disconnectCall(
  flyCallBack: (FlyResponse response) {
    if (response.isSuccess) {}
  },
);
```

---

## ☁️ Deployment Models – Self-hosted & Cloud

- **Self-hosted:** Deploy on private servers or data centers
- **Cloud:** Host on MirrorFly’s multi-tenant cloud infrastructure

---

## 📚 Learn More

- Developer Documentation
- Product Tutorials
- MirrorFly Flutter Solution
- Dart & Pub.dev Documentation
- On-premise Deployment
- See who’s using MirrorFly

---

## 🧑‍💻 Hire Experts

Build your enterprise app with a full team of experts—from idea to launch.

---

## ⏱️ Round-the-clock Support

Contact our experts anytime for assistance during integration or deployment.

---

## 💼 Become a Part of Our Team

We’re hiring developers, support specialists, and product managers. Visit our careers page to learn more.

## **🗞️ Get the Latest Updates**

- [Blog](https://www.mirrorfly.com/blog/)
- [Facebook](https://www.facebook.com/MirrorFlyofficial/)
- [Twitter](https://twitter.com/mirrorflyteam)
- [LinkedIn](https://www.linkedin.com/showcase/mirrorfly-official/)
- [Youtube](https://www.youtube.com/@mirrorflyofficial)
- [Instagram](https://www.instagram.com/mirrorflyofficial/)
