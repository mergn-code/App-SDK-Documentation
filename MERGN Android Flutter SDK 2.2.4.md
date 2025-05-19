# Flutter Kotlin SDK 2.2.4

This documentation provides integration steps and usage instructions for incorporating the Flutter Kotlin SDK 2.2.4 into your Flutter Android project. Follow these steps to initialize the SDK, record events, and manage attributes within your application.

## Integration Steps

### 1. Include SDK in Your Project

1. Place maven `{ url 'https://jitpack.io' }` in android project level `buid.gradle`.

```kotlin
 allprojects {
    repositories {
        google()
        mavenCentral()
        maven { url 'https://jitpack.io' }
    }
}
```
2. Add `mergn_flutter_plugin_sdk:` in pubsec.yml
   
### 3. Include `APP_GROUP_ID_MERGN` from Xcode in your Project

To enable seamless data sharing between your app and the Mergn SDK (and its extensions, like Notification Services), you must configure an App Group in every target of your application.

- Key = APP_GROUP_ID_MERGN //Key Name should be same
- Value = group.{app_bundle_identifier}.mergn // i.e group.com.demo.app.mergn

That is a necessary step if using Notification Services in iOS Project.



## Usage

### 1. Initialize SDK

Initialize the SDK.

```kotlin
MergnSDK.initialize(application);
```

### 2. Register API Key

Register your API Key:

```kotlin
MergnChannel.registerAPICall("API KEY");
```

### 4. Record Events

Use the `EventManager` to record events by providing an event name and properties:

```kotlin
String eventName = "Event Name";
// Map<String, String> eventProperties = {"propertyName": "PropertyValue"}; // Optional property setup
Map<String, String> eventProperties = Map(); // For empty properties
MergnChannel.sendEvent(eventName, eventProperties);
```

### 5. Record Attributes

Use the `AttributeManager` to record attributes by providing an attribute name and value:

```kotlin
String attributeName = "Email"; // eventName.text.toString()
String attributeValue = "fluttersdk@mergn.com";
MergnChannel.sendAttribute(attributeName, attributeValue);
```

### 6. Login

Record the login event when the user successfully logs in:

```
MergnChannel.login("fluttersdk@mergn.com");  // Add unique Identifier
```

**Unique Identity (mandatory)**: This value represents the customer's unique identity in your database, such as an ID or email.

### 7. Firebase Token Registration

Register the Firebase token to receive MERGN notifications:

```
MergnChannel.firebaseToken(fcmToken.toString());
```

This method should be called in any place where you would potentially land. Also, call this in `onNewToken()` and `onRefreshToken()` in your Firebase service.

### 8. Notification Analytics
Call the mentioned method in following methods

```swift

func userNotificationCenter(_ center: UNUserNotificationCenter, willPresent notification: UNNotification, withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        EventManager.shared.notificationViewed(notificationData: notification.request) // Mergn Call
    }

 func userNotificationCenter(_ center: UNUserNotificationCenter, didReceive response: UNNotificationResponse, withCompletionHandler completionHandler: @escaping () -> Void) {
        EventManager.shared.notificationTapped(notificationData: response.notification.request) // Mergn Call
    }

```

There are two mergn calls in different methods (Notification View and Notification Tapped).

### Important Case

There are three scenarios in the app where you need to send sign-in attributes and trigger the login event of the MERGN SDK:

1. When a new user creates a new account.
2. When existing users log into the app.
3. When the user is already logged in (important for capturing data of users who logged in previous versions of the app where the MERGN SDK was not integrated).
4. Whenever Identity updates, make sure
```
MergnChannel.login("fluttersdk@mergn.com");  // Add unique Identifier
```
should be called before setting up the attributes.

## Proguard Rules

Add the following Proguard rules while making a release build

```pro
# With R8 full mode generic signatures are stripped for classes that are not
 # kept. Suspend functions are wrapped in continuations where the type argument
 # is used.
 -keep,allowobfuscation,allowshrinking class kotlin.coroutines.Continuation

 # R8 full mode strips generic signatures from return types if not kept.
 -if interface * { @retrofit2.http.* public * *(...); }
 -keep,allowoptimization,allowshrinking,allowobfuscation class <3>

 # With R8 full mode generic signatures are stripped for classes that are not kept.
 -keep,allowobfuscation,allowshrinking class retrofit2.Response
