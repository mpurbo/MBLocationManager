MBLocationManager
=================

Location manager provides convenient and easy-to-use access to latest iOS device location. It wraps CoreLocation
with convenient singleton class, relieving you of keeping reference to location manager object.

Main features:
* subscribe to location updates with 3 lines of code
* two modes of operation: GPS (accurate with low battery performance) and cell towers (inaccurate with better battery performance)
* use CoreLocation's distance filters and accuracy modes
* easy pause and resume on app going to background or foreground
* instantiate location manager once, use it wherever you need by subscribing to notification


## Installation
Installation should be done via Cocoa Pods. 

Install CocoaPods if you didn't yet:
```bash
sudo gem install cocoapods
pod setup
```

Change to the directory of your Xcode project, and Create and Edit your ``Podfile`` and add following line
```
pod 'MBLocationManager', '~> 0.1.0'
```

```bash
pod install
```

Open your project in Xcode from the .xcworkspace file (not the usual project file)


## Usage


#### 1. Start tracking location 
To start tracking location on the device, call ``startLocationUpdates:distanceFilter:accuracy`` from 
``application:didFinishLaunchingWithOptions`` in the AppDelegate.h

```objectivec
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    [[MBLocationManager sharedManager] startLocationUpdates:kMBLocationManagerModeStandard
                                             distanceFilter:kCLDistanceFilterNone
                                                   accuracy:kCLLocationAccuracyThreeKilometers];

    return YES;
}
```

#### 2. Subscribe to the notification events
From the viewcontroller where you intend to use a location of the device, subscribe to notification 
which is triggered whenever a new location is detected. When notification is posted, access 
MBLocationManager's property ``currentLocation``

```objectivec
- (void)viewDidLoad
{
    [super viewDidLoad];

    [[NSNotificationCenter defaultCenter] addObserver:self
                                             selector:@selector(changeLocation:)
                                                 name:kMBLocationManagerNotificationLocationUpdatedName
                                               object:nil];
}

-(void)changeLocation:(NSNotification*)notification
{
    CLLocation *location = [[MBLocationManager sharedManager] currentLocation];
    NSLog(@"Location changed to %@", self.locationLabel.text);
}
```

#### 3. (Optional) Pause location updates when in background
To improve battery life, you might prevent app from tracking location changes when
entering background.

```objectivec
- (void)applicationDidEnterBackground:(UIApplication *)application
{
   [[MBLocationManager sharedManager] stopLocationUpdates];
}

- (void)applicationWillEnterForeground:(UIApplication *)application
{
    [[MBLocationManager sharedManager] startLocationUpdates];
}
- (void)applicationWillTerminate:(UIApplication *)application
{
    [[MBLocationManager sharedManager] stopLocationUpdates];
}
```

## Fine-tune CoreLocation settings
By accessing ``locationManager`` property you are able to fine-tune CoreLocation settings, such as
``activityType`` and ``pausesLocationUpdatesAutomatically``. 

CoreLocation documentation:
https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/cl/CLLocationManager
