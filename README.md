# MirrorFly Flutter SDK

## Introduction

This repository guides you through integrating MirrorFly's Flutter SDK into your cross-platform application. Build native messaging and calling experiences for both Android and iOS with minimal development effort using our customizable white-label solution. This documentation covers all essential libraries, dependencies, supplementary features, and step-by-step installation instructions to get your Flutter app communication-ready.

MirrorFly Flutter Plugin is a powerful real-time communication SDK that enables chat, voice, video calling, live streaming, and AI-powered communication features in Flutter applications.

This repository helps you integrate MirrorFly’s Flutter SDK into Android and iOS apps with minimal development effort. Using a customizable white-label solution, you can build native messaging and calling experiences quickly while maintaining full data ownership and flexible deployment options.

This documentation covers all essential libraries, dependencies, supplementary features, and step-by-step installation instructions to make your Flutter app communication-ready.

---

## 🤹 Key Product Offerings

MirrorFly helps build omni-channel communication apps for any kind of business.

- **In-app Messaging** – Connect users individually or as groups via instant messaging features.
- **HD Video Calling** – Engage users over face-to-face conversations anytime, and from anywhere.
- **HQ Voice Calling** – Deliver crystal clear audio calling experiences with latency as low as 3ms.
- **AI Voice Agent** – Build custom AI voicebots that can understand, act and respond to user questions.
- **AI Chatbot** – Deploy white-label AI chatbots that drive autonomous conversations across any web or mobile app.
- **Live Streaming** – Broadcast video content to millions of viewers around the world, within your own enterprise app.

## MirrorFly Flutter Chat & Video Calls Plugin

### Requirements

#### Android Requirements

- Android Lollipop 5.0 (API Level 21) or above
- Java 8 or higher
- Gradle 4.1.0 or higher
- `targetSdkVersion` 34 or above
- `compileSdk` 34

#### iOS Requirements

- iOS 13.0 or above

---

## Get MirrorFly License Key

To obtain your license key, follow these stepss:

1. Sign up for a free MirrorFly account in the [MirrorFly Console](https://console.mirrorfly.com). If you already have an account, sign in.
2. Navigate to the **Overview** page in your account to locate your license key.
3. Copy the license key from the **Application Info** section and use it during the integration process.

![MirrorFly License Key](https://www.mirrorfly.com/docs/assets/images/license-key-a1173e922ebff14b6ae1a2428f822eec.png)

<img src="https://www.mirrorfly.com/docs/assets/images/license-key-a1173e922ebff14b6ae1a2428f822eec.png" width="100%">

## Create Android Dependency

Add the following to your **root `build.gradle`** file inside the Android folder:

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
        targetSdkVersion 34 // or higher
    }
}
```

---

## Create iOS Dependency

Check and add the following code at the end of your **`ios/Podfile`**:

```ruby
post_install do |installer|
  installer.aggregate_targets.each do |target|
    target.xcconfigs.each do |variant, xcconfig|
      xcconfig_path = target.client_root + target.xcconfig_relative_path(variant)
      IO.write(xcconfig_path, IO.read(xcconfig_path).gsub("DT_TOOLCHAIN_DIR", "TOOLCHAIN_DIR"))
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

      shell_script_path = "Pods/Target Support Files/#{target.name}/#{target.name}-frameworks.sh"
      if File.exist?(shell_script_path)
        shell_script_input_lines = File.readlines(shell_script_path)
        shell_script_output_lines = shell_script_input_lines.map { |line| line.sub("source=\"$(readlink \"${source}\")\"", "source=\"$(readlink -f \"${source}\")\"") }
        File.open(shell_script_path, 'w') do |f|
          shell_script_output_lines.each { |line| f.write line }
        end
      end
    end
  end
end
```

Now, enable all the below mentioned capabilities into your project:

**Go to:** Project → Target → Signing & Capabilities → Click **+** → Search and enable the required capabilities.

---

<img  src="https://www.mirrorfly.com/assets/images/tutorials/build-chat-app-using-flutter/app-group.webp" width="100%"/>

## 📦 Create Flutter Dependency

Add the following dependency to your **`pubspec.yaml`** file:

```yaml
dependencies:
  mirrorfly_plugin: ^1.5.0
```

Run the following command:

```bash
flutter pub get
```

Import the SDK:

```dart
import 'package:mirrorfly_plugin/mirrorfly.dart';
```

---

## Initialize MirrorFly Plugin

Add the following code inside the `main()` function in **`main.dart`**, before calling `runApp()`:

```dart
void main() {
  WidgetsFlutterBinding.ensureInitialized();
  Mirrorfly.initializeSDK(
    licenseKey: LICENSE_KEY,
    iOSContainerID: IOS_APP_GROUP_ID,
    chatHistoryEnable: ENABLE_CHAT_HISTORY,
    enableDebugLog: ENABLE_DEBUG_LOG,
    flyCallback: (FlyResponse response) {
      runApp(const MyApp());
    },
  );
}
```

### Chat Init Function Description

**Note:** The Chat History feature allows you to retrieve past conversations whenever you log in from a new device. The system stores chat history securely, ensuring that users can access the same conversation threads across devices without any data loss.

---

## Login

Use the following method to log in a user in Sandbox or Live mode.

**Important:** Do not invoke the login method more than once unless the current session has been logged out.

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

---

## Send a One-to-One Message

```dart
var textMessage = MessageParams.text(
  toJid: toJid,
  replyMessageId: replyMessageId,
  topicId: topicId,
  textMessageParams: TextMessageParams(messageText: messageText),
);

Mirrorfly.sendMessage(messageParams: textMessage, flyCallback: (response) {
  if (response.isSuccess) {

  }
});
```

## Receive a One-to-One Message

```dart
Mirrorfly.onMessageReceived.listen((result) {
  var chatMessage = sendMessageModelFromJson(result);
});
```

---

## Voice & Video Call Integration

### Required Permissions

- **Audio Call:** Microphone
- **Video Call:** Microphone, Camera

### Make a Voice Call

```dart
Mirrorfly.makeVoiceCall(toUserJid: USER_JID, flyCallBack: (FlyResponse response) {
  if (response.isSuccess) {

  }
});
```

### Make a Video Call

```dart
Mirrorfly.makeVideoCall(toUserJid: USER_JID, flyCallBack: (FlyResponse response) {
  if (response.isSuccess) {

  }
});
```

### Make a Group Voice Call

```dart
Mirrorfly.makeGroupVoiceCall(
  groupJid: GROUP_JID,
  toUserJidList: USER_LIST,
  flyCallBack: (FlyResponse response) {
    if (response.isSuccess) {

    }
  },
);
```

### Make a Group Video Call

```dart
Mirrorfly.makeGroupVideoCall(
  groupJid: GROUP_JID,
  toUserJidList: USER_LIST,
  flyCallBack: (FlyResponse response) {
    if (response.isSuccess) {

    }
  },
);
```

### Add Participants to an Ongoing Call

```dart
Mirrorfly.inviteUsersToOngoingCall(jidList: USER_LIST, flyCallback: (FlyResponse response) {
  if (response.isSuccess) {

  }
});
```

### Receive Incoming Call Status

```dart
Mirrorfly.onCallStatusUpdated.listen((event) {
  var statusUpdateReceived = jsonDecode(event);
  var callMode = statusUpdateReceived["callMode"].toString();
  var userJid = statusUpdateReceived["userJid"].toString();
  var callType = statusUpdateReceived["callType"].toString();
  var callStatus = statusUpdateReceived["callStatus"].toString();
});
```

### Disconnect an Ongoing Call

```dart
Mirrorfly.disconnectCall(flyCallBack: (FlyResponse response) {
  if (response.isSuccess) {

  }
});
```

---

## Deployment Models

- **Self-hosted** – Deploy your client on your own data centers, private cloud or third-party servers.
- **Cloud** – Host your client on MirrorFly’s multi-tenant cloud servers.

Learn more: [https://www.mirrorfly.com](https://www.mirrorfly.com)

---

## Resources

- [Developer Documentation](https://www.mirrorfly.com/docs)
- [Product Tutorials](https://www.mirrorfly.com/tutorials)
- [MirrorFly Flutter Solution](https://www.mirrorfly.com/flutter)
- [Dart Documentation](https://dart.dev)
- [Pub.dev Documentation](https://pub.dev)
- [NPM Documentation](https://www.npmjs.com)

---

## Hire Experts

Need a tech team to build your enterprise app? Hire a full team of experts. From concept to launch, we handle every step of the development process.

---

## Round-the-clock Support

Our experts are available 24/7 to help you.

---

## Join Our Team

Visit our careers page: [https://www.mirrorfly.com/careers](https://www.mirrorfly.com/careers)

---

## Get the Latest Updates

- [Blog](https://www.mirrorfly.com/blog)
- [Facebook](https://www.facebook.com/mirrorfly)
- [Twitter](https://twitter.com/mirrorfly)
- [LinkedIn](https://www.linkedin.com/company/mirrorfly)
- [YouTube](https://www.youtube.com/@mirrorfly)
- [Instagram](https://www.instagram.com/mirrorfly)
