# MERGN Android Kotlin SDK 

This documentation provides integration steps and usage instructions for incorporating the MERGN SDK for Android Kotlin into your Android project. Follow the steps below to initialize the SDK, record events, and manage attributes within your application.

## Integration Steps

### 1. Include SDK in Your Project

1. Place `mergn_sdk_kotlin_2.2.4.aar` in the `libs` package under your app module.
2. Add the following dependencies in your `build.gradle` (app):

   ```groovy
   implementation files('libs/mergn_sdk_kotlin_2.2.4.aar')
   implementation "androidx.room:room-runtime:2.4.0"
   implementation "com.squareup.retrofit2:retrofit:2.9.0"
   implementation "com.squareup.retrofit2:converter-gson:2.9.0"
   ```

3. Sync the project.

## Usage

### 1. Initiate MERGN SDK Setup

Initialize the `EventManager` in the first activity of your app and register your API Key:

```kotlin
val eventManager = EventManager()
MergnSDK.initialize(application)

val apiKey = "Insert Mergn API Key here"
eventManager.registerApiKey(apiKey, applicationContext)
```

### 2. Record Events

Use the `EventManager` to record events by providing an event name and properties:

```kotlin
val eventManager = EventManager()
val eventName = "Insert Event Name Here"

val eventPropertiesMap: MutableMap<String, String> = HashMap()
eventPropertiesMap["propertyName"] = "PropertyValue"

eventManager.sendEvent(eventName, eventPropertiesMap, activityContext, applicationContext)
```

If the activity context is not available, use this method (note limitations with popups):

```kotlin
eventManager.sendEvent(eventName, eventPropertiesMap, applicationContext)
```

### 3. Record Attributes

Use the `AttributeManager` to record attributes by providing an attribute name and value:

```kotlin
val attributeName = "Insert Attribute Name"
val attributeValue = "Insert Attribute Value"

attributeManager.sendAttribute(context = applicationContext, attributeName, attributeValue)
```

### 4. Login

Record the login event when the user successfully logs in:

```kotlin
val eventManager = EventManager()
eventManager.login("Insert Unique Identity", activityContext)
```

**Unique Identity (mandatory)**: This value represents the customer's unique identity in your database, such as an ID or email.

### 5. Firebase Token Registration

Register the Firebase token to receive MERGN notifications:

```kotlin
val eventManager = EventManager()
eventManager.firebaseToken("Insert Firebase token here", this)
```

This registers the device token in the MERGN database for push notifications. For push notifications, define the following service in your app manifest inside the `<application>` tag:

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

### 6. Firebase Notifications

To use the MERGN notification service, implement the following method:

```kotlin
eventManager.mergnNotification(remoteMessage: RemoteMessage, context: FirebaseMessagingService)
```

Ensure to send the `remoteMessage` object without any modifications. Use the following condition to send the `remoteMessage` object to the MERGN SDK:

```kotlin
if (remoteMessage.getData().get("type").equals("app_sdk")) {
    EventManager().getEventManager().mergnNotification(remoteMessage, this)
}
```

### 7. Set Notification Icon

Notification Icon can be set using the followng method:

```kotlin
 EventManager().setNotificationIcon(R.mipmap.icon,this) // Android resource
```

## Important Cases

There are three scenarios in the app where you need to send sign-in attributes and trigger the login event of the MERGN SDK:

1. When a new user creates a new account.
2. When existing users log into the app.
3. When the user is already logged in (important for capturing data of users who logged in previous versions of the app where the MERGN SDK was not integrated).
4. Whenever Identity updates, make sure 
```kotlin
val eventManager = EventManager()
eventManager.login("Insert Unique Identity", activityContext)
```
should be called before setting up the attributes.

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
## Backup rules

Add the following rules in the following files in xml package to exclude mergn sharedprefences and database backup.

```
<data-extraction-rules>
    <cloud-backup>
        <exclude
            domain="sharedpref"
            path="app package name+mergn_prefs.xml" /> --> before + sign add app package name
        <exclude domain="database" path="my_database" />
        <exclude domain="database" path="my_database-wal" />
        <exclude domain="database" path="my_database-shm" />
    </cloud-backup>

    <device-transfer>
        <exclude
            domain="sharedpref"
            path="app package name+mergn_prefs.xml" /> --> before + sign add app package name
        <exclude domain="database" path="my_database" />
        <exclude domain="database" path="my_database-wal" />
        <exclude domain="database" path="my_database-shm" />
    </device-transfer>

</data-extraction-rules>
```



```
<full-backup-content>

    <exclude
        domain="sharedpref"
        path="com.mergn.client+mergn_prefs.xml" />  --> before + sign add app package name
    <exclude domain="database" path="my_database" />
    <exclude domain="database" path="my_database-wal" />
    <exclude domain="database" path="my_database-shm" />
</full-backup-content>
```

Add following in the application manifest

  ``` 
   <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules">
   ```
