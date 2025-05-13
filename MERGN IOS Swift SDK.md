This documentation provides integration steps and usage instructions for incorporating the **MERGN SDK** for iOS Swift into your iOS project. Follow the steps below to initialize the SDK, record events, and manage attributes within your application.

## Integration Steps

### 1. Include `mergn_ios` Framework in your Project

To get started with the **MERGN iOS SDK**, you need to integrate the `mergn_ios` framework into your Xcode project. Follow these steps to add the SDK to your project:

- Download the `mergn_ios` framework.
- Drag and drop the framework into your Xcode project.
- Ensure that the framework is properly linked in your project's **Linked Frameworks and Libraries** section.


### 2. Include `APP_GROUP_ID_MERGN` Framework in your Project

To enable seamless data sharing between your app and the Mergn SDK (and its extensions, like Notification Services), you must configure an App Group in every target of your application.

- Key = APP_GROUP_ID_MERGN
- Value = group.{app_bundle_identifier}.mergn // i.e group.com.demo.app.mergn

---

## Usage

### 1. Initialize MERGN SDK Setup

To begin using the MERGN SDK, initialize it in your app by calling the `MergnSDK.initialize()` method. You’ll also need to register your API key using the `EventManager`.

Here’s an example of how to initialize the SDK:

```swift
import mergn_ios

// Use the singleton instance of EventManager
let eventManager = EventManager.shared

// Initialize the SDK with the API key
let apiKey = "Insert Mergn API Key here"
EventManager.shared.registerAPI(clientApiKey:"API KEY")
```
### 2. Record Events


Use the EventManager to record events. Events can be tracked with a name and optional properties, such as user actions or product details.

Here's how to record an event:

```swift
let eventName = "Product_Clicked"
let eventPropertiesMap: [String: String] = ["category": "Chips"]

// Calling sendEvent on the shared instance with event properties
EventManager.shared.sendEvent(eventName: eventName, properties: eventPropertiesMap)
```

### 3. Record Attributes
Attributes are pieces of user-related data, such as email or other custom data points. To record attributes, use the sendAttribute method.

Example:
```swift
EventManager.shared.sendAttribute(attributeName: "AttributeName", attributeValue: "AttributeValue")
```

### 4. Login
When a user logs in, you should record the login event. The postIdentification method allows you to send a unique identity for the user (such as an ID or email) to the MERGN SDK.

```swift
EventManager.shared.postIdentification(identity: "user123@example.com")
```
Unique Identity (mandatory): This value represents the customer's unique identity in your database, such as an ID or email.

### 5. Firebase Token
Share the firebase token when user launches the app and whenever token refreshes in the aplication

```swift
EventManager.shared.firebaseToken(token: token)
```

### 6. Notification Analytics
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
