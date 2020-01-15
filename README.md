# React Native Fingerprint Scanner

[![React Native Version](https://img.shields.io/badge/react--native-latest-blue.svg?style=flat-square)](http://facebook.github.io/react-native/releases)
[![Version](https://img.shields.io/npm/v/react-native-fingerprint-scanner.svg)](https://www.npmjs.com/package/react-native-fingerprint-scanner)
[![NPM](https://img.shields.io/npm/dm/react-native-fingerprint-scanner.svg)](https://www.npmjs.com/package/react-native-fingerprint-scanner)

React Native Fingerprint Scanner is a [React Native](http://facebook.github.io/react-native/) library for authenticating users with Fingerprint (TouchID).

## Table of Contents

- [Installation](#installation)
- [Compatibility](#compatibility)
- [Example](#example)
- [API](#api)
- [License](#license)

### iOS Version
The usage of the TouchID is based on a framework, named **Local Authentication**.

It provides a **Default View** that prompts the user to place a finger to the iPhone’s button for scanning.

<div>
<img src="https://github.com/hieuvp/react-native-fingerprint-scanner/raw/master/screenshots/ios-availability.png" height="600">
<img src="https://github.com/hieuvp/react-native-fingerprint-scanner/raw/master/screenshots/ios-authentication.gif" height="600">
</div>

### Android Version
Using an expandable Android Fingerprint API library, which combines [Samsung](http://developer.samsung.com/galaxy/pass#) and [MeiZu](http://open-wiki.flyme.cn/index.php?title=%E6%8C%87%E7%BA%B9%E8%AF%86%E5%88%ABAPI)'s official Fingerprint API.

Samsung and MeiZu's Fingerprint SDK supports most devices which system versions less than Android 6.0.

<div>
<img src="https://github.com/hieuvp/react-native-fingerprint-scanner/raw/master/screenshots/android-availability.png" height="600">
<img src="https://github.com/hieuvp/react-native-fingerprint-scanner/raw/master/screenshots/android-authentication.gif" height="600">
</div>

## Installation

```sh
$ npm install react-native-fingerprint-scanner --save
```
or
```sh
$ yarn add react-native-fingerprint-scanner
```

### Automatic Configuration
For RN >= 0.60
```sh
$ cd ios && pod install
```

For RN < 0.60, use react-native link to add the library to your project:
```sh
$ react-native link react-native-fingerprint-scanner
```

### Manual Configuration

#### iOS

1. In XCode, in the project navigator, right click `Libraries` ➜ `Add Files to [your project's name]`
2. Go to `node_modules` ➜ `react-native-fingerprint-scanner` and add `ReactNativeFingerprintScanner.xcodeproj`
3. In XCode, in the project navigator, select your project. Add `libReactNativeFingerprintScanner.a` to your project's `Build Phases` ➜ `Link Binary With Libraries`
4. Run your project (`Cmd+R`)

#### Android

1. Open up `android/app/src/main/java/[...]/MainApplication.java`
  - Add `import com.hieuvp.fingerprint.ReactNativeFingerprintScannerPackage;` to the imports at the top of the file
  - Add `new ReactNativeFingerprintScannerPackage()` to the list returned by the `getPackages()` method
2. Append the following lines to `android/settings.gradle`:
  	```
  	include ':react-native-fingerprint-scanner'
  	project(':react-native-fingerprint-scanner').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-fingerprint-scanner/android')
  	```
3. Insert the following lines inside the dependencies block in `android/app/build.gradle`:
  	```
    compile project(':react-native-fingerprint-scanner')
  	```

### App Permissions

Add the following permissions to their respective files:

#### Android
In your `AndroidManifest.xml`:

API level 28+ ([Reference](https://developer.android.com/reference/android/Manifest.permission#USE_BIOMETRIC))
```xml
<uses-permission android:name="android.permission.USE_BIOMETRIC" />
```

API level <28 ([Reference](https://developer.android.com/reference/android/Manifest.permission#USE_FINGERPRINT))
```xml
<uses-permission android:name="android.permission.USE_FINGERPRINT" />
```
#### iOS
In your `Info.plist`:

```xml
<key>NSFaceIDUsageDescription</key>
<string>$(PRODUCT_NAME) requires FaceID access to allows you quick and secure access.</string>
```

### Extra Configuration

1. Make sure the following versions are all correct in `android/app/build.gradle`
    ```
    // API v29 enables FaceId
    android {
        compileSdkVersion 29
        buildToolsVersion "29.0.2"
    ...
        defaultConfig {
          targetSdkVersion 29
    ```

2. Add necessary rules to `android/app/proguard-rules.pro` if you are using proguard:
    ```
    # MeiZu Fingerprint

    // REMOVED in 4.0.0
    -keep class com.fingerprints.service.** { *; }
    -dontwarn com.fingerprints.service.**

    # Samsung Fingerprint

    // REMOVED in 4.0.0
    -keep class com.samsung.android.sdk.** { *; }
    -dontwarn com.samsung.android.sdk.**
    ```

## Compatibility

* For Gradle < 3 you MUST install react-native-fingerprint-scanner at version <= 2.5.0
* For RN >= 0.57 and/or Gradle >= 3 you MUST install react-native-fingerprint-scanner at version >= 2.6.0
* For RN >= 0.60 you MUST install react-native-fingerprint-scanner at version >= 3.0.0

## Example

[Example Source Code](https://github.com/hieuvp/react-native-fingerprint-scanner/tree/master/examples)

**iOS Implementation**
```javascript
import React, { Component } from 'react';
import PropTypes from 'prop-types';
import { AlertIOS } from 'react-native';
import FingerprintScanner from 'react-native-fingerprint-scanner';

class FingerprintPopup extends Component {

  componentDidMount() {
    FingerprintScanner
      .authenticate({ description: 'Scan your fingerprint on the device scanner to continue' })
      .then(() => {
        this.props.handlePopupDismissed();
        AlertIOS.alert('Authenticated successfully');
      })
      .catch((error) => {
        this.props.handlePopupDismissed();
        AlertIOS.alert(error.message);
      });
  }

  render() {
    return false;
  }
}

FingerprintPopup.propTypes = {
  handlePopupDismissed: PropTypes.func.isRequired,
};

export default FingerprintPopup;
```

**Android Implementation**
```javascript
import React, { Component } from 'react';
import PropTypes from 'prop-types';

import {
  Alert,
} from 'react-native';
import FingerprintScanner from 'react-native-fingerprint-scanner';

import ShakingText from './ShakingText.component';
import styles from './FingerprintPopup.component.styles';

class FingerprintPopup extends Component {

  constructor(props) {
    super(props);
    this.state = { errorMessage: undefined };
  }

  componentDidMount() {
    FingerprintScanner
      .authenticate({ titleText: "Log in with Biometrics"})
      .then(() => {
        this.props.handlePopupDismissed();
        Alert.alert('Fingerprint Authentication', 'Authenticated successfully');
      })
      .catch((error) => {
        this.setState({ errorMessage: error.message });
        this.description.shake();
      });
  }

  componentWillUnmount() {
    FingerprintScanner.release();
  }

  render() {
    const { errorMessage } = this.state;
    const { handlePopupDismissed } = this.props;

    // as of 4.0.0, Android provides a native prompt via BiometricPrompt
    return null;
  }
}

FingerprintPopup.propTypes = {
  handlePopupDismissed: PropTypes.func.isRequired,
};

export default FingerprintPopup;
```

## API

### `isSensorAvailable()`: (Android, iOS)
Checks if Fingerprint Scanner is able to be used by now.

- Returns a `Promise<string>`
- `biometryType: String` - The type of biometric authentication supported by the device.
  - iOS: biometryType = 'Touch ID', 'Face ID'
  - Android: biometryType = 'Biometrics'
- `error: FingerprintScannerError { name, message, biometric }` - The name and message of failure and the biometric type in use.

```javascript
componentDidMount() {
  FingerprintScanner
    .isSensorAvailable()
    .then(biometryType => this.setState({ biometryType }))
    .catch(error => this.setState({ errorMessage: error.message }));
}
```

### `authenticate({ description, fallbackEnabled })`: (iOS)
Starts Fingerprint authentication on iOS.

- Returns a `Promise`
- `description: String` - the string to explain the request for user authentication.
- `fallbackEnabled: Boolean` - default to `true`, whether to display fallback button (e.g. Enter Password).

```javascript
componentDidMount() {
  FingerprintScanner
    .authenticate({ description: 'Scan your fingerprint on the device scanner to continue' })
    .then(() => {
      this.props.handlePopupDismissed();
      AlertIOS.alert('Authenticated successfully');
    })
    .catch((error) => {
      this.props.handlePopupDismissed();
      AlertIOS.alert(error.message);
    });
}
```

### `authenticate({ onAttempt })`: (Android)
Starts Fingerprint authentication on Android.

- Returns a `Promise`
- `titleText: String` the title text to display in the native Android popup

```javascript
componentDidMount() {
  FingerprintScanner
    .authenticate({ titleText: "Log in with Biometrics" })
    .then(() => {
      this.props.handlePopupDismissed();
      Alert.alert('Fingerprint Authentication', 'Authenticated successfully');
    })
    .catch((error) => {
      this.setState({ errorMessage: error.message });
      this.description.shake();
    });
}
```

### `release()`: (Android)
Stops fingerprint scanner listener, releases cache of internal state in native code, and cancels native prompt if visible.

- Returns a `Void`

```javascript
componentWillUnmount() {
  FingerprintScanner.release();
}
```

### `Types of Biometrics`

| Value | OS | Description|
|---|---|---|
| Touch ID | iOS | |
| Face ID | iOS | |
| Biometrics | Android | Refers to the biometric set as preferred on the device |

### `Errors`

| Name | Message |
|---|---|
| AuthenticationNotMatch | No match |
| AuthenticationFailed | Authentication was not successful because the user failed to provide valid credentials |
| AuthenticationTimeout | Authentication was not successful because the operation timed out |
| AuthenticationProcessFailed | 'Sensor was unable to process the image. Please try again |
| UserCancel | Authentication was canceled by the user - e.g. the user tapped Cancel in the dialog |
| UserFallback | Authentication was canceled because the user tapped the fallback button (Enter Password) |
| SystemCancel | Authentication was canceled by system - e.g. if another application came to foreground while the authentication dialog was up |
| PasscodeNotSet | Authentication could not start because the passcode is not set on the device |
| DeviceLocked | Authentication was not successful, the device currently in a lockout of 30 seconds |
| DeviceLockedPermanent | Authentication was not successful, device must be unlocked via password |
| DeviceOutOfMemory | Authentication could not proceed because there is not enough free memory on the device |
| HardwareError | A hardware error occurred |
| FingerprintScannerUnknownError | Could not authenticate for an unknown reason |
| FingerprintScannerNotSupported | Device does not support Fingerprint Scanner |
| FingerprintScannerNotEnrolled  | Authentication could not start because Fingerprint Scanner has no enrolled fingers |
| FingerprintScannerNotAvailable | Authentication could not start because Fingerprint Scanner is not available on the device |

## License

MIT
