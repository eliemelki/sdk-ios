# ProxSee SDK for iOS

The following document provides background information on the ProxSee SDK as well as outlines setup and usage instructions.

The content in this document is divided into the following sections:

- [Section 1: Introducing the ProxSee SDK](#section-1-introducing-the-proxsee-sdk)
    - [Background](#background)
    - [How Does the ProxSee SDK Work?](#how-does-the-proxsee-sdk-work)
    - [Key Concepts](#key-concepts)
        - [Beacon](#beacon) 
        - [Virtual Beacon](#virtual-beacon)
            - [Deployment](#deployment)
            - [Limitations](#limitations)
            - [Accuracy](#accuracy)
        - [Locations](#locations)
        - [Tags](#tags)
        - [Metadata](#metadata)
        - [Check-In/Check-Out](#check-incheck-out)
- [Section 2: Implementing the ProxSee SDK in an iOS Project](#section-2-implementing-the-proxsee-sdk-in-an-ios-project)
    - [Prerequisites](#prerequisites)
    - [Generate a Mobile API Key](#generate-a-mobile-api-key)
    - [Add the ProxSee SDK to Your iOS Project](#add-the-proxsee-sdk-to-your-ios-project)
    - [Launch the ProxSee SDK](#launch-the-proxsee-sdk)
- [Section 3: Using the ProxSee SDK](#section-3-using-the-proxsee-sdk)
    - [Handle Tag Changeset Notifications](#handle-tag-changeset-notifications)
        - [Listen for Tag Changeset Notifications](#listen-for-tag-changeset-notifications)
        - [Receive Tag Changeset Notifications](#receive-tag-changeset-notifications)
        - [Remove the Observer](#remove-the-observer)
    - [Enable/Disable the ProxSee SDK](#enabledisable-the-proxsee-sdk)
        - [Enable the ProxSee SDK](#enable-the-proxsee-sdk)
        - [Disable the ProxSee SDK](#disable-the-proxsee-sdk)
    - [Update Metadata](#update-metadata)
- [Section 4: FAQs](#section-4-faqs)

 
## Section 1: Introducing the ProxSee SDK

### Background

The ProxSee SDK takes the complexities out of beacon interaction and provides you with a simplified interface to quickly integrate iBeacon™ and virtual beacon (geo-fence) monitoring into your mobile application.

Combined with the ProxSee Admin Portal, the ProxSee SDK allows you to create and manage tags, listen for, receive, and respond to tag changeset notifications, add and associate user metadata to check-ins, and mine resultant data according to your needs (e.g., to determine wait times, travel patterns).

### How Does the ProxSee SDK Work? 

Once initialized, the ProxSee SDK associates a unique identifier with the user's mobile device, which is used in all communications to the central platform. The ProxSee SDK starts monitoring beacons/virtual beacons and sends check-in/check-out information to the central platform whenever an enter or exit event is detected:

- **Enter event**: The user approaches the beacon or enters the virtual beacon (geo-fence) circular boundary. The ProxSee SDK sends check-in information to the central platform when an enter event is detected.
- **Exit event**: The user moves away from the beacon or exists the virtual beacon (geo-fence) circular boundary. The ProxSee SDK sends check-out information to the central platform when an exit event is detected.  

Along with monitoring the beacons/virtual beacons, the ProxSee SDK also queries the central platform for tag information associated with a beacon/virtual beacon and automatically loads and caches information about nearby beacons/virtual beacons.

The ProxSee SDK allows your application to:

- **Listen For and Receive Tag Changeset Notifications**: Your application can listen for and receive tag changeset notifications sent by the ProxSee SDK. You can update the tags and positional information associated to a beacon/virtual beacon through the ProxSee Admin Portal without having to update your ProxSee SDK or the physical, deployed beacons. See [Handle Tag Changeset Notifications](#handle-tag-changeset-notifications).
- **Enable/Disable the ProxSee SDK**: The ProxSee SDK monitors beacons/virtual beacons, broadcasts check-ins/check-outs, send tag changeset notifications, and update metadata. At any point in your application, you can enable/disable the ProxSee SDK, which in turn enables/disables monitoring. See [Enable/Disable the ProxSee SDK](#enabledisable-the-proxsee-sdk). 
- **Update Metadata**: You can send additional information about a user such as account information and user IDs to the ProxSee SDK. When the ProxSee SDK receives metadata it associates it with the user's check-ins, which helps you identify users and devices among the collected data. See [Update Metadata](#update-metadata). 

### Key Concepts

#### Beacon

Also referred to as a "physical beacon" or an "iBeacon™", this is the physical device that you deploy and that the user's mobile device detects. Unlike location-based services on a mobile device, beacon ranging is fairly precise (essentially serving as "indoor GPS") and low-power, leading to its use indoors and where fine-tuned location context is desired.

#### Virtual Beacon
 
A virtual beacon is a geo-fence that behaves  based on the user crossing a circular boundary on a map rather than nearing a physical beacon. s such, it can serve as a less accurate beacon in locations the customer may not have the access/permission to add a physical device. 
 
Virtual beacons work in concert with physical beacons. In order to work properly, virtual beacons should be placed on the map in such a way that a user would hit a physical beacon before hitting a virtual beacon. For example:
 
- **Bad Placement**: Putting a virtual beacon in the parking lot of a mall and a physical beacon inside the mall would not allow you to detect people approaching from the parking lot.  The ProxSee SDK would send notifications of users hitting the beacon in the mall first, not in the parking lot. 
- **Good Placement**: Putting a virtual beacon on Rent a Car Road in Las Vegas and a physical beacon inside the arrival area of the Las Vegas airport would allow you to detect people coming off of an airplane and then detect those that went to the rental car area. 

###### Deployment
 
- For best results, virtual beacons should be deployed with a medium or greater range.
- Virtual beacons should be placed in areas where a user is likely to remain or traverse for several seconds/minutes.

##### Limitations
 
- You are limited to 5 virtual beacons per location.
- The outer radius of a virtual beacon must be more than 200 meters in distance from the outer radius of any other beacon (physical or virtual).

##### Accuracy 
 
The accuracy of virtual beacons is based on the GPS/network provider; however, the following factors may affect the accuracy of a virtual beacon:
 
- Area obstructions
- The capabilities of the user’s mobile device
- The geo-location abilities of the user’s mobile device
- Indoor placement (Note: This may significantly affect the accuracy of the virtual beacon)


The ProxSee SDK is also expected to receive a location update whenever the device is moved approximately 100 meters. in general, the closer you are to a beacon the more accurate the reported distance. 
 
Because of the factors mentioned above, it’s not possible to provide specific numbers for accuracy. On average, the measurement error can be 20-30% of the actual distance. You can increase signal reliability by increasing the Broadcasting Power. For greater accuracy, the use of beacons (physical devices) is recommended.


#### Locations

Locations within the ProxSee platform group beacons/virtual beacons by region and establish a default tag for each beacon/virtual beacon within the region. Locations also play a part in the caching of beacon data.

To add/delete locations, see the Locations section of the ProxSee Admin Portal. 

#### Tags

Tags are simply short descriptions in the form of hashtags that are associated to beacons/virtual beacons for identification and classification purposes.

By default, each beacon/virtual beacon has a “#<Location>” (with <Location> being the Location of the beacon/virtual beacon) tag. 

You can associate the same tag with multiple beacons/virtual beacons.  or instance, you may place beacons at all of your exits and associated them all with an  "#Exit" tag. However, if a user moves between two beacons associated with the same tag, the ProxSee SDK will not create a tag changeset notification. A tag changeset notification is only generated when the tags change (e.g., tags were removed from or added to the beacon/virtual beacon). 

To add and assign/remove tags, see the Tags section of the ProxSee Admin Portal. 

#### Metadata

Metadata can be used to provide additional information about a user and their device, such as user ID and user preferences. The metadata sent to the ProxSee SDK is then associated to each check-in by the user’s device.

See the [Update Metadata](#update-metadata) section in this document for instructions on how to add metadata.  

#### Check-In/Check-Out

A checkin-in/check-out forms a tuple for a device event and helps track enter and exit events.  
When tracking enter (check-in) and exit (check-out) events, keep in mind:
You may have a check-in without a corresponding check-out. The most common cause of this is network interruptions.  

When using virtual beacons, check-ins are more likely to occur than check-outs.

Data is stored during both a check-in and a check-out.

- **Check-In**: The majority of information is stored during a check-in and includes: 
    - the time
    - the device's unique ID (UUID)
    - additional system information, including  the version of the SDK used 
- **Check-Out**: Only the check-out time is updated and stored..

## Section 2: Implementing the ProxSee SDK in an iOS Project

Incorporating the ProxSee SDK into your iOS project is a simple three-step process:

- [Generate a Mobile API Key](#generate-a-mobile-api-key)
- [Add the ProxSee SDK to your iOS Project](#add-the-proxsee-sdk-to-your-ios-project)
- [Launch the ProxSee SDK](#launch-the-proxsee-sdk)

### Prerequisites

The ProxSee SDK requires:

- An active Bluetooth service in order to function with the beacons/virtual beacons
- Active Location services in order to function with virtual beacons
- An Internet connection 

### Generate a Mobile API Key

In order to use the ProxSee SDK, you will need to generate a Mobile API Key.

1. Navigate to the ProxSee portal at [https://app.proxsee.io/#/login](#https://app.proxsee.io/#/login).
2. From the login page, enter your username and password and then click **Login**.
3. From the navigation bar on the left side of the screen, click **Applications**.
4. Click **Create Application**.
5. From the **API Type** section, select **Mobile**.
6. Copy the generated GUID. This is your Mobile API Key. 

**Note**: If you have multiple applications, you may wish to generate a unique Mobile API Key for each one.

### Add the ProxSee SDK to Your iOS Project

Once you have generated a Mobile API Key you can begin the process to add the ProxSee SDK to your iOS project.

**Note**: The ProxSee SDK is available on CocoaPods at [https://cocoapods.org/pods/ProxSeeSDK](#https://cocoapods.org/pods/ProxSeeSDK).

1. Add the **LXProxSeeSDK.framework** file to your project.
2. Open your project settings and click the **Build Phases** tab.
3. In the **Link Binary with Libraries** section, click the **+** button and add the **CoreLocation.framework**.
4. Click the **Build Settings** tab.
5. In the **Other Linker Flags** section, add **-ObjC**.
6. Add the following keys/values to your **Info.plist** file. These keys/values represent the text shown to users when they are prompted to allow location access. As of iOS 8, the ProxSee SDK requires these Location Services keys/values to be enabled in order to work properly.

| Key | Type | Value |
|:-----------|------------:|:------------:|
| NSLocationAlwaysUsageDescription       |        String |     _Your location prompt message_    |


### Launch the ProxSee SDK

Once you have added the ProxSee SDK as a dependency in your iOS project, you can initialize the ProxSee SDK using the Mobile API Key you generated in a previous step. 

On the initial launch, the ProxSee SDK will be ON by default and will start automatically. 
After the initial launch, on any app restart followed by a call for launchProxSeeWithApiKey, an attempt will be made to start the ProxSee SDK depending on the SDK mode. Calling launchProxSeeWithApiKey more than once has no effect.

**Note**: In the following code, replace “YourApiKey” with the Mobile API Key you generated in a previous step.

```
#import <LXProxSeeSDK/LXProxSeeSDK.h>

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
[LXProxSeeSDKManager launchProxSeeWithApiKey:@"YourApiKey"];
return YES;
}

```

## Section 3: Using the ProxSee SDK

The following actions can be performed within the ProxSee SDK:

- [Handle Tag Changeset Notifications](#handle-tag-changeset-notifications)
- [Enable/Disable the ProxSee SDK](#enabledisable-the-proxsee-sdk)
- [Update Metadata](#update-metadata)

### Handle Tag Changeset Notifications

Any object can receive tag changeset notifications. Notifications are only sent when tags are changed (added or removed from the beacon/virtual beacon). The ProxSee SDK sends an LXProxSeeNotificationObject which includes the new and previous tag changesets along with the date captured for each changeset.

There are three steps to handling tag changeset notifications:

- [Listen for Tag Changeset Notifications](#listen-for-tag-changeset-notifications)
- [Receive Tag Changeset Notifications](#receive-tag-changeset-notifications)
- [Remove the Observer](#remove-the-observer)

#### Listen for Tag Changeset Notifications

To start listening for tag changeset notifications, execute the following:

```
#import <LXProxSeeSDK/LXProxSeeSDK.h>

[self addProxSeeNotifcationObserver];  

```


#### Receive Tag Changeset Notifications

To receive tag changeset notifications, implement the following method:

```
- (void) didChangeTagsSet:(LXProxSeeNotificationObject *)proximityNotificationObject {
//execute your code
}

```
#### Remove the Observer

You also need to make sure to remove the observer before your object is deallocated or your app will crash:

```
[self removeProxSeeNotificationObserver];

```

### Enable/Disable the ProxSee SDK

At any point of the application lifecycle you can enable or disable the ProxSee SDK. By disabling the ProxSee SDK, you will stop it from:

- Monitoring beacons/virtual beacons
- Broadcasting check-ins/check-outs
- Notifying your application about tag changesets
- Updating metadata

Any explicit change to isMonitoringEnabled, will change the SDK mode accordingly. This will be used later on app restart when the launchProxSeeWithApiKey method is called to determine if the ProxSee SDK should start. 

#### Enable the ProxSee SDK

To enable the ProxSee SDK and in turn enable beacon/virtual beacon monitoring, check-in/check-out broadcasts, tag changeset notifications, and metadata updates, execute the following:

```
[LXProxSeeSDKManager sharedInstance].isMonitoringEnabled = YES;   

```
#### Disable the ProxSee SDK

To disable the ProxSee SDK and in turn disable the monitoring of beacons as well as stop check-in/check-out broadcasts, tag changeset notifications, and metadata updates, execute the following:

```
[LXProxSeeSDKManager sharedInstance].isMonitoringEnabled = NO;   

```
 
### Update Metadata

While the function used is named updateMetadata, be aware that each update is stored as its own record and is fully versioned. As such, it is recommended that you only send metadata when it has changed. If the ProxSee SDK detects that the metadata has not changed from the previous request, it will not send the metadata; however, it will return a successful result to its CompletionHandler.

The following example depicts how to add metadata (e.g., user IDs, user preferences), which will get associated to every check-in by the user. 

**Note**: The key-value pairs are stored as a JSON object in the central platform. You may wish to design your representation so that it can be easily queried for reporting purposes. 

```
[[LXProxSeeSDKManager sharedInstance] updateMetadata:@{ @"key" : @"value" } completionHandler:^(BOOL success, NSError *error) {
if (!success)
{
NSLog(@"Failed to update metadata %@",error);
}
else
{
NSLog(@"Successfully updated metadata");
}
}];

```

## Section 4: FAQs

**Will the ProxSee SDK impact my mobile phone’s battery?** 

Yes. The ProxSee SDK will draw approximately 1-2% of the mobile phone’s battery. 

**How long does it take for the ProxSee SDK to detect a beacon?**

- **Beacons**: 0 to a few seconds. 
- **Virtual beacons**: The detection of virtual beacons is based on movement and location changes. The ProxSee SDK is expected to detect a virtual beacon whenever the mobile device is moved approximately 100 meters. 

**As a third-party developer using the ProxSee SDK, do I need to do anything if my application is rebooted?**

Refer to the [Launch the ProxSee SDK](#launch-the-proxsee-sdk) section for details.

**What happens when Bluetooth is disabled?**

Scanning for physical beacons is paused while scanning for virtual beacons will continue. Once Bluetooth is re-enabled, scanning for physical beacons will resume. Note: The ProxSee SDK must have monitoring enabled in order to receive events.

**What happens when Location is disabled?**

Scanning for both physical beacons and virtual beacons will be paused. Once location is turned back on, scanning for both physical beacons and virtual beacons will resume. Note: The ProxSee SDK must have monitoring enabled in order to receive events.  


