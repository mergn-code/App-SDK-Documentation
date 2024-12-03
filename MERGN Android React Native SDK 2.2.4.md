# React Native Android SDK 2.2.4

This documentation provides integration steps and usage instructions for incorporating the React Native Android SDK 2.2.4 into your React Native Android project. Follow the steps below to initialize the SDK, record events, and manage attributes within your application.

## Integration Steps

### 1. Include SDK in Your Project

1. Place `mergn-sdk-kotlin-2.2.4-prod.aar` in the `libs` package under your app module.
2. Add the following dependencies in your `build.gradle` (app):

   ```groovy
   implementation files('libs/mergn-sdk-kotlin-2.2.4-prod.aar')
   implementation "androidx.room:room-runtime:2.4.0"
   implementation "com.squareup.retrofit2:retrofit:2.9.0"
   implementation "com.squareup.retrofit2:converter-gson:2.9.0"
   ```

3. Sync the project.

## Usage

### 1. Add mergn pacakge

 
  override val reactNativeHost: ReactNativeHost = ReactNativeHostWrapper(
        
        this,
        object : DefaultReactNativeHost(this) {
         
          override fun getPackages(): List<ReactPackage> {
            val packages: MutableList<ReactPackage> = PackageList(this).packages
            packages.add(MergnPackage())
            return packages
          }

          override fun getJSMainModuleName(): String = ".expo/.virtual-metro-entry"

          override fun getUseDeveloperSupport(): Boolean = BuildConfig.DEBUG

          override val isNewArchEnabled: Boolean = BuildConfig.IS_NEW_ARCHITECTURE_ENABLED
          override val isHermesEnabled: Boolean = BuildConfig.IS_HERMES_ENABLED
      }
  )
          
### 2. Initialize and Register Channel in Main Activity

Initialize the SDK and register the method channel in the `onCreate` method of your main activity:

```java
MergnSDK.initialize(this);
```

### 3. Add Required Files

- Add `MergnModule` and `MergnPackage` files, which are provided separately in `Mergn-files.zip`.
- Place these files in the main package: `app/src/main/'project-package'`.
- Add `MergnModule.js` in the React Native project root path.

### 4. Initiate MERGN SDK Setup

Import `MergnModule` and register your API Key on the first page of your app:

```javascript
import MergnModule from '../MergnModule';

MergnModule.registerApi('Insert API key provided by MERGN');
```

### 5. Record Events

Use the `EventManager` to record events by providing an event name and properties:

```javascript
var eventPropertiesMap = {}; // For empty properties
var eventPropertiesMap1 = {"category": "Wooden"}; // For a single property
var eventPropertiesMap2 = {"category": "Wooden", "name": "Chairs"}; // For multiple properties

var eventName = "Enter Event Name";
const message = await MergnModule.performEvent(eventName, eventPropertiesMap);
```

### 6. Record Attributes

Use the `AttributeManager` to record attributes by providing an attribute name and value:

```javascript
var attributeName = "Email";
var attributeValue = "react_android@mergn.com";
const message = await MergnModule.setAttribute(attributeName, attributeValue);
```

### 7. Login

Record the login event when the user successfully logs in:

```javascript
var uniqueIdentifier = "react_android@mergn.com";
const message = await MergnModule.login(uniqueIdentifier);
```

**Unique Identity (mandatory)**: This value represents the customer's unique identity in your database, such as an ID or email.

### 8. Firebase Token Registration

Register the Firebase token to receive MERGN notifications:

```javascript
const message = await MergnModule.setFirebaseToken(fcmTokenMergn);
```

This method should be triggered in every place where users can land on app launch. Also, call this in `onNewToken()` and `onRefreshToken()` in your Firebase service.

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

<receiver
    android:enabled="true"
    android:exported="true"
    android:name="com.mergn.insights.firebaseservices.NotificationClickReceiver">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</receiver>
```

### Important Cases

There are three scenarios in the app where you need to send attributes and trigger the login event of the MERGN SDK:

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
