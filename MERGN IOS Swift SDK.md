# MERGN IOS 1.0.4

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

### 7. Notification Extension Service for the Mergn Image work

```swift

class NotificationService: UNNotificationServiceExtension {

    var contentHandler: ((UNNotificationContent) -> Void)?
    var bestAttemptContent: UNMutableNotificationContent?

    override func didReceive(_ request: UNNotificationRequest, withContentHandler contentHandler: @escaping (UNNotificationContent) -> Void) {
        self.contentHandler = contentHandler
        bestAttemptContent = (request.content.mutableCopy() as? UNMutableNotificationContent)
        
        EventManager.shared.notificationViewed(notificationData: request)

        // Check for the media URL
        if let mediaUrlString = request.content.userInfo["image"] as? String,
           let mediaUrl = URL(string: mediaUrlString) {
            
            // Download the image
            downloadImage(from: mediaUrl) { imageData in
                if let imageData = imageData {
                    // Attach the image as a big picture to the notification
                    self.attachBigImageToNotification(imageData: imageData)
                }
                
                // Call the content handler to deliver the notification
                contentHandler(self.bestAttemptContent!)
            }
        } else {
            // If no image, just return the original notification content
            contentHandler(self.bestAttemptContent!)
        }
    }

    override func serviceExtensionTimeWillExpire() {
        // Called when the extension is about to time out
        if let contentHandler = contentHandler, let bestAttemptContent = bestAttemptContent {
            contentHandler(bestAttemptContent)
        }
    }

    // Helper function to download the image
    func downloadImage(from url: URL, completion: @escaping (Data?) -> Void) {
        let task = URLSession.shared.dataTask(with: url) { data, response, error in
            if let error = error {
                print("Error downloading image: \(error)")
                completion(nil)
            } else {
                completion(data)
            }
        }
        task.resume()
    }

    // Helper function to save the image to a temporary directory
    func saveImageToTempDirectory(data: Data) -> URL {
        let tempDirectory = FileManager.default.temporaryDirectory
        let fileURL = tempDirectory.appendingPathComponent(UUID().uuidString + ".jpg")
        try? data.write(to: fileURL)
        return fileURL
    }

    // Helper function to attach the image as a big picture to the notification
    func attachBigImageToNotification(imageData: Data) {
        let imageURL = saveImageToTempDirectory(data: imageData)

        if let attachment = try? UNNotificationAttachment(identifier: "image", url: imageURL, options: nil) {
            // Attach the image to the notification content
            bestAttemptContent?.attachments = [attachment]
        }
    }
}

```

### 8. Add groups

Add the group name as 
group.client App Bundle identifier
```swift
i.e. if App Bundle identifier will be **com.demo.ios**
```

```swift
group name will be 
**group.com.demo.ios**
```

inside info.plist
add the following Dictionary

```swift
Key= APP_GROUP_ID_MERGN
value: This should be same as group name i.e. **group.com.demo.ios**
```







