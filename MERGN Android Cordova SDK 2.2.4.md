# MERGN KOTLIN SDK CORDOVA 2.8.1

This documentation provides integration steps and usage instructions for incorporating the MERGN CORDOVA SDK 2.8.1 (Android/IOS) into your project. Follow these steps to initialize the SDK setup, record events, and manage attributes within your application.

## Integration Steps

### 1. Include SDK in Your Project

1. Place `mergn_sdk_cordova_2.1.1.aar` in the following directory:
   ```
   platforms/android/app/src/main/libs
   ```

### 2. Add Dependencies in `build.gradle` (app)

Add the following dependencies to your `build.gradle` (app):

```groovy
implementation files('src/main/libs/mergn-sdk-kotlin-cordova-2.4.4.aar') // Add latest version
implementation "androidx.room:room-runtime:2.4.0"
implementation "com.squareup.retrofit2:retrofit:2.9.0"
implementation "com.squareup.retrofit2:converter-gson:2.9.0"
implementation "com.android.installreferrer:installreferrer:2.2"
implementation "androidx.work:work-runtime-ktx:2.7.0"
```

### 3. Sync the Project

Make sure to sync your project after adding the dependencies.

### 4. Add the Cordova Plugin

Run the following command in the root directory of your Cordova project:

```bash
cordova plugin add cordova-plugin-mergn@2.2.1  // Add Latest Version
```

### 5. Update `config.xml`

Add the following feature in your `config.xml`:

```xml
<feature name="Mergn">
  <param name="android-package" value="nl.xservices.plugins.Mergn" />
</feature>
```

### 6. Include Mergn JavaScript in Your HTML

Add the following script tag to your `index.html`:

```html
<script type="text/javascript" src="js/Mergn.js"></script>
```

## Usage

### 1. Initiate MERGN SDK Setup

To initialize the SDK, use the following command:

```javascript
window.plugins.mergn.RegisterApi({
    apiKey: "Enter API Key Here"
});
```

### 2. Record Events

To record events, provide the event name and properties:

```javascript
var eventPropertiesMap = new Map(); // In case of no property
// var eventPropertiesMap = { // In case of properties
//     "propertyName": "value"
// };
var eventPropertiesJSON = JSON.stringify(eventPropertiesMap);

window.plugins.mergn.PerformEvent({
    eventName: "PropertyName",
    eventPropertiesMap: eventPropertiesJSON
});
```

### 3. Record Attributes

To record attributes, provide the attribute name and value:

```javascript
window.plugins.mergn.SendAttribute({
    attributeName: "AttributeName",
    attributeValue: "AttributeValue"
});
```

### 4. Login

To record the login event when the user successfully logs in, use the following method:

```javascript
window.plugins.mergn.Login({
    uniqueIdentifier: "Unique Identifier"
});
```

**Unique Identity (mandatory)**: This value is the customer's unique identity in your database, such as an ID or email.
