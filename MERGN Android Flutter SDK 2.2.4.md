# Flutter Kotlin SDK 2.2.4

This documentation provides integration steps and usage instructions for incorporating the Flutter Kotlin SDK 2.2.4 into your Flutter Android project. Follow these steps to initialize the SDK, record events, and manage attributes within your application.

## Integration Steps

### 1. Include SDK in Your Project

1. Place `mergn_sdk_flutter_2.2.4.aar` in the `libs` package under your app module.
2. Add the following dependencies in your `build.gradle` (app):

   ```groovy
   implementation files('libs/mergn_sdk_flutter_2.2.4.aar')
   implementation "androidx.room:room-runtime:2.4.0"
   implementation "com.squareup.retrofit2:retrofit:2.9.0"
   implementation "com.squareup.retrofit2:converter-gson:2.9.0"
   ```

3. Sync the project.

## Usage

### 1. Initialize and Register Channel in Main Activity

Initialize the SDK and register the method channel in your main activity:

```kotlin
MergnSDK.initialize(application)
registerMergnMethodChannel(flutterEngine, this, application, applicationContext)
```

### 2. Add Mergn Method Call Handler

Add `MergnMethodCallHandler.kt` in the `mergnflutter` package and create the `MergnChannel` file in the `libs` package.

### 3. Initiate MERGN SDK Setup

Initialize the event manager and register your API Key:

```kotlin
MergnChannel.registerAPICall("Add Mergn API here");
```

### 4. Record Events

Use the `EventManager` to record events by providing an event name and properties:

```kotlin
String eventName = "Cart_Cancel_Item";
// Map<String, String> eventProperties = {"propertyName": "PropertyValue"}; // Optional property setup
Map<String, String> eventProperties = Map();
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

```kotlin
String uniqueIdentity = "fluttersdk@mergn.com";
MergnChannel.login(uniqueIdentity);
```

**Unique Identity (mandatory)**: This value represents the customer's unique identity in your database, such as an ID or email.

### 7. Firebase Token Registration

Register the Firebase token to receive MERGN notifications:

```kotlin
MergnChannel.firebaseToken(fcmToken.toString());
```

This method should be called in any place where you would potentially land. Also, call this in `onNewToken()` and `onRefreshToken()` in your Firebase service.

For push notifications, define the following service in your app manifest inside the `<application>` tag:

```xml
<service
    android:name="com.mergn.insights.firebaseservices.FireBaseMessagingService"
    android:exported="true"
    android:enabled="true">
    <intent-filter>
        <action android:name="com.google.firebase.MESSAGING_EVENT" />
    </intent-filter>
</service>
```

### Important Case

There are three scenarios in the app where you need to send sign-in attributes and trigger the login event of the MERGN SDK:

1. When a new user creates a new account.
2. When existing users log into the app.
3. When the user is already logged in (important for capturing data of users who logged in previous versions of the app where the MERGN SDK was not integrated).

## Proguard Rules

Add the following Proguard rules while making a release build or app bundle:

```pro
-keep public class com.mergn.insights.classes.** {
    public protected *;
}
-keep class com.mergn.insights.networkservices.API.** { *; }
-keep class com.mergn.insights.networkservices.requests.** { *; }
-keep class com.mergn.insights.networkservices.responses.** { *; }
-keep class com.mergn.insights.networkservices.RetrofitClient { *; }
-keep class com.mergn.insights.firebaseservices.** { *; }
-keep class com.mergn.insights.views.** { *; }
```
