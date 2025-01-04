# MERGN Java SDK

This documentation provides integration steps and usage instructions for incorporating the MERGN SDK for Java 2.2.4 into your Android project. Follow the steps below to initialize the SDK, record events, and manage attributes within your application.

## Integration Steps

### 1. Include SDK in Your Project

1. Place `mergn-sdk-java-2.2.8.aar` in the `libs` package under your app module.
2. Add the following dependencies in your `build.gradle` (app):

   ```groovy
   implementation files('libs/mergn-sdk-java-2.2.8.aar')
   implementation "androidx.room:room-runtime:2.4.0"
   implementation "com.squareup.retrofit2:retrofit:2.9.0"
   implementation "com.squareup.retrofit2:converter-gson:2.9.0"
   implementation 'com.android.installreferrer:installreferrer:2.2'
   ```

3. Sync the project.

## Usage

### 1. Initiate MERGN SDK Setup

Initialize the `EventManager` in the first activity of your app and register your API Key:

```java
EventManager eventManager = new EventManager();  
eventManager.registerApiKey("Enter API KEY", getApplicationContext());
```

### 2. Record Events

Use the `EventManager` to record events by providing an event name and properties:

```java
String eventName = "Enter your event name"; // Event Name
Map<String, String> eventPropertiesMap = new HashMap<>(); // Map for event properties
eventPropertiesMap.put("Customer Id", "5070"); // Add event
eventManager.sendEvent(eventName, eventPropertiesMap, activityContext); // Send event to SDK
```

### 3. Record Attributes

Use the `AttributeManager` to record attributes by providing an attribute name and value:

```java
AttributeManager attributeManager = new AttributeManager();
String attributeName = "Email";
String attributeValue = "javasdk@mergn.com";
attributeManager.sendAttribute(getApplicationContext(), attributeName, attributeValue); 
// Send attribute to SDK
```

### 4. Login

Record the login event when the user successfully logs in:

```java
eventManager.login("Unique_Identity");
```

**Unique Identity (mandatory)**: This value represents the customer's unique identity in your database, such as an ID or email.

### 5. Firebase Token Registration

Register the Firebase token to receive MERGN notifications:

```java
EventManager eventManager = new EventManager();
eventManager.firebaseToken("Insert Firebase token here", this);
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

```java
eventManager.mergnNotification(remoteMessage, this);
```

Ensure to share the `remoteMessage` object without modifications and use the following condition to send the `remoteMessage` object to the MERGN SDK:

```java
if (remoteMessage.getData().get("type").equals("app_sdk")) {
    EventManager().getEventManager().mergnNotification(remoteMessage, this);
}
```

## Important Cases

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
