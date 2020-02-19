
# iGurme - RN Demo App for Insider SDK 9.0.0
iGurme is a demo app written with react native to test and display features of the Insider Mobile SDK. 
A more thorough SDK documentation can be found at [mobile.useinsider.com/documentation](https://mobile.useinsider.com/documentation).
The UI design of the app can be found from Zeplin: [iGurme Designs](https://app.zeplin.io/project/5b21124ebfecaf4da128c143/dashboard?seid=5b21135237ec881a42b9d07d).
This version of iGurme is developed by Ralfi Salhon. For any questions or concerns, contact <rifat.salhon@tufts.edu>

# Direct Download
- [Android APK Download](https://drive.google.com/file/d/1gn5YrfpqMO_bVyHYcNLgiYx1UP2kon1i/view)
- iOS TestFlight Download (Coming Soon)

# Installation
### Step 1) Clone Using HTTPS or SSH
```sh
$ git clone https://github.com/useinsider/rn-test-app.git
$ git clone git@github.com:useinsider/rn-test-app.git
```

### Step 2) `cd` into repo, install `node_modules`, and link modules
```sh
$ cd rn-test-app
$ npm install
$ react-native link
```

### Step 3) Perform Platform Specific Run Instructions

#### 3.1) iOS
*Requires working from a MacOS, and to have XCode installed*
```sh
$ cd ios 
$ pod install
$ cd ..
$ react-native run-ios
```
Alternatively, you could open the ios/`iGurme.xcworkspace`  file and run the project from there.

**Note: You might need to perform SDK 9.0.0 update manually on iOS. See https://github.com/useinsider/mobile-ios for the SDK.**
If this is the case, you need to delete the `Framework` and `Libraries` folder from under `RNInsider.xcodeproj` on XCode and place `SDK` folder from `mobile-ios` repository under `RNInsider.xcodeproj`. Then rebuild.

#### 3.2) Android
*No additional steps required for Android installation. However, you do need to have an Android emulator installed or a physical device to run the application from.*
```sh
$ react-native run-android
```
To check if your physical device is recognized by react-native:
```sh
$ adb devices
```
The output will be all connected devices. React Native will build to device #1 on the list.

### Got errors while installing/running? :(
In the case of error: `Unable to load script from assets ‘index.android.bundle’` run the following command to run on Android:
```sh
$ react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res
```
Source: https://medium.com/@adityasingh_32512/solved-unable-to-load-script-from-assets-index-android-bundle-bdc5e3a3d5ff
***
In the case of error: `No bundle URL present` close all terminal tabs, and then open one at the root of the project and run:
```sh
$ react-native start --reset-cache
```
***
In the case of error `linker error` on XCode, close all terminal tabs and run this command:
```sh
$ rm -rf node_modules && npm i && react-native link && react-native start --reset-cache
```
then try to run again.

# iGurme file structure
`react-navigation` set up can be found at `App.js`. iGurme uses the `createMaterialBottomTabNavigator` method by `react-native-material-bottom-tabs` to create it's bottom tab structure. The `getIcon(tintColor,  iconName)` method can be used to create new Icons.

The `src` folder contains 4 folders: 

 1. `components`
Has folder names for each tab, and holds all the components for that tab.

 2. `database`
 Has a single file called `items.js` which acts like a database. Items are exported as objects. In the future, if an actual database is used, make sure to modify files src/screens/HomeTab/`Scene.js` and src/components/SearchTab/`VerticalVategorySlider.js` to reflect these changes correctly.
 
 3. `images`
Has folder names for each tab, and holds all the images for that tab. Also has a `shared` folder for shared images.

 4. `screens`
Has folder names for each tab, and holds all the screens for that tab. The default navigation for the stack can be found at `App.js` . This is defined by the `initialRoute` key under the `createStackNavigator` method.

# react-native-insider Integration
The SDK is initialized with the following default values:
```js
const defaultPartnerName = "ibusiness";
const defaultSenderID    = "148378682679";
const defaultEndpoint    = "https://mobile.useinsider.com";
const appGroup           = "group.com.useinsider.iGurmeV3";
```
The `partnerName`, `senderID`, and `endpoint` attributes can be edited from the Developer Options screen. This screen can be accessed by scrolling down from the Profile Tab, and clicking `Developer Options`.

## Deeplinks [#Documentation](https://mobile.useinsider.com/documentation?section=2&subsection=2)
Deeplinks can be used in conjunction with push notifications to direct the user directly to a product page or the cart. For all deeplinks, the key is `product`. The value can be the 3-letter category name `TEA`, `COF`, `CUP`, or `ACC`, followed by a number 1-5. Alternatively, the value can be `cart` which directs the user to the cart page.

| Key | Value | Example | Example Result
|--|--|--|--|
| `product` | `TEA`<1-5> | `TEA2` | Black Tea item page opens
| `product` | `COF`<1-5> | `COF1` | Caffè Americano item page opens
| `product` | `CUP`<1-5> | `CUP4` | Plastic Cups item page opens
| `product` | `ACC`<1-5> | `ACC3` | Wall Sign item page opens
| `product` | `cart` | `cart` | User is redirected to the cart page

## Advanced Push Notifications [#Documentation](https://mobile.useinsider.com/documentation?section=2&subsection=3)
iGurme can utilize Advanced Push Notifications for both iOS and Android.
The `appGroup` preference is for iOS Advanced Push Notifications set up. If you wish to change this value, refer to the General Settings --> Bundle Identifier of iGurme Project Targets --> `NotificationService` and `NotificationContentExtensions`

## Social Proof [#Documentation](https://mobile.useinsider.com/documentation?section=3&subsection=0)
**File**: src/components/HomeTab/`HorizontalItem.js` 
**Function**: `goToItemPage()`
calls the method `RNInsider.tagProduct(item.id);` to activate `Social Proof`

## Geofence [#Documentation](https://mobile.useinsider.com/documentation?section=3&subsection=1)
**File**: src/screens/`LoginScreen.js` 
**Function**: `initiateRNInsider()`
calls the method `RNInsider.startTrackingGeofence();` to activate `Geofence`

Please note that Location permission is not asked by default on Android. iGurme app settings need to be manually modified to enable Geofence tracking.

## Content Optimizer [#Documentation](https://mobile.useinsider.com/documentation?section=3&subsection=2)
**File**: src/screens/CartTab/`Scene.js` 
**Function**: `getButtonStyle()`
```js
RNInsider.getStringWithName("purchase_button_text",  "PLACE ORDER",  "Content", value => {
    this.setState({ buttonText: value });
});
RNInsider.getStringWithName("purchase_button_color",  "orange",  "Content", value => {
    this.setState({ buttonColor: value });
});
```
key:  `purchase_button_text`  value:  `<Any String>`  default:  `PLACE ORDER`  
key:  `purchase_button_color`  value:  `<Any Color, hex or rgb>`  default:  `orange`
***
**File**: src/screens/HomeTab/`Scene.js` 
**Function**: `getBannerImages()`
```js
RNInsider.getStringWithName("banner_images", "tea", "Content", value  =>  {
    if (value == "coffee") { <change banner images to Coffee> }
});
```
key:  `banner_images`  value:  `tea or coffee`  default:  `tea`  
***
**File**: src/screens/`LoginScreen.js` 
**Function**: `getProfileStyle()`
```js
RNInsider.getStringWithName("testStringVariable", "default", "Content", value => {
    this.setState({ profileStyle: value});
});
```
key:  `testStringValue`  value:  `default or alternate`  default:  `default`  

## Tracking Revenue [#Documentation](https://mobile.useinsider.com/documentation?section=3&subsection=3)
**File**: src/screens/CartTab/`Scene.js` 
**Function**: `placeOrder()`
calls the method `RNInsider.trackPurchasedItems(<params>);` for `Tracking Revenue`

## Cart Reminder [#Documentation](https://mobile.useinsider.com/documentation?section=3&subsection=4)
**File**: src/screens/HomeTab/`ItemClicked.js` 
**Function**: `addToCart()`
calls the method `RNInsider.itemAddedToCart(<params>);` for `Cart Reminder`
***
**File**: src/screens/CartTab/`Scene.js` 
**Function**: `deleteItem()`
calls the method `RNInsider.itemRemovedFromCart(<params>);` for `Cart Reminder`
***
**File**: src/screens/CartTab/`Scene.js` 
**Function**: `placeOrder()`
calls the method `RNInsider.cartCleared();` for `Cart Reminder`

## GDPR Compliance [#Documentation](https://mobile.useinsider.com/documentation?section=3&subsection=5)
iGurme does not currently utilize the GDPR Compliance method, the value is sent as `true` by default. This feature will be added in a future update.

## Message Center [#Documentation](https://mobile.useinsider.com/documentation?section=3&subsection=6)
iGurme does not currently utilize the Message Center. This feature will be added in a future update.

## Recommendation Engine [#Documentation](https://mobile.useinsider.com/documentation?section=3&subsection=7)
iGurme does not currently utilize the Recommendation Engine. This feature will be added in a future update.

## Events and Attributes [#Documentation](https://mobile.useinsider.com/documentation?section=3&subsection=8)
`RNInsider.tagEvent()` is used on various instances throughout iGurme.
You can find the implemented Events and Attributes here: [Google Spreadsheet](https://docs.google.com/spreadsheets/u/4/d/1DquddL4eKCFXaO8iweC0eRKuXbkdwYVhG3Ez02N5n2s/edit?usp=drive_web&ouid=106751863831062503258)

## App Tracker [#Documentation](https://mobile.useinsider.com/documentation?section=3&subsection=9)
iGurme does not currently utilize the App Tracker. This feature will be added in a future update.

## Icon Changer (Android Only)
The iGurme app icon can be changed with a push notification. By passing `birthday` as the App Icon preference on Campaign Settings, the icon will be replaced with a blue iGurme logo.

# Developer Options
iGurme app has a secret Developer Options screen. This screen can be accessed by scrolling down on the Profile tab. This page is split up to 5 parts:
### 1) General Settings
This collapsible offers the following options:
 - **Start app on this page?**
	 - Allows the app to automatically redirect to the Developer Options screen when it is relaunched
- **Location enabled?**
	- This option manually notifies the SDK of the user's location preference. Please note that the user still needs to have their Location privacy settings enabled in addition to this option being turned on for Geolocation to work.
- **Push enabled?**
	- This option manually notifies the SDK of the notification preference. Please note that the user still needs to have their Notification settings enabled in addition to this option being turned on for Push Notifications to work.
- **Partner Name**
	- This option allows for dynamically changing the partnerName parameter which gets passed to the `RNInsider.init()` function. *Please note that updating this option requires the app to be relaunched for it to take effect. Also, since the URL Bundle and appGroup parameters cannot be dynamically changed, the email test device adding and advanced push notifications may not work as expected after modifying this option.*
- **FCM Sender ID**
	- This option allows for dynamically changing the senderID parameter which gets passed to the `RNInsider.init()` function. *Please note that updating this option requires the app to be relaunched for it to take effect.*
- **Open App Settings**
	- This option when clicked redirects the user to the native iGurme app settings on iOS and Android.

### 2) Set Endpoint
- This option is used to switch between endpoints, mainly for testing purposes. 2 options are available: 
1) `https://mobile.useinsider.com` 
2) `http://mobile-staging.useinsider.com`

*Please note that updating this option requires the app to be relaunched for it to take effect.*

### 3) Set Attribute
- Use this section to manually set attributes on the app. Once a successful communication is made with the Webpage, its result will be logged to the **Console Log** section.

### 4) Tag Event
- Use this section to manually tag events (+ with parameters if wanted) on the app. Once a successful communication is made with the Webpage, its result will be logged to the **Console Log** section.

### 5) Console Log
- This section logs the results of the requests made from General Settings, Set Endpoint, Set Attribute, and Tag Event. **Please note that this isn't an actual console log, but rather manually prints results based on try/catch blocks.**

# Third-Party Packages Used

| Package | Version | Docs | Usage |
| ------ | ------ | ------ | ------ |
| react-native-app-settings | ^2.0.1 | [README.md](https://github.com/KrazyLabs/react-native-app-settings/blob/master/README.md) | src/screens/ProfileTab/`Developer.js`
| react-native-collapsible | ^1.4.0 | [README.md](https://github.com/oblador/react-native-collapsible/blob/master/README.md)| src/screens/ProfileTab/`Developer.js`
| react-native-default-preference | ^1.3.2 | [README.md](https://github.com/kevinresol/react-native-default-preference/blob/master/README.md)| `App.js` & src/screens/ProfileTab/`Developer.js`
| react-native-insider | ^2.4.1 | [README.md](https://github.com/useinsider/react-native-insider/blob/master/README.md)| **Almost all screens depend on this package**
| react-native-modal-datetime-picker | ^7.4.2 | [README.md](https://github.com/mmazzarolo/react-native-modal-datetime-picker/blob/master/README.md)| src/screens/ProfileTab/`Developer.js`
| react-native-modal-dropdown | ^0.6.2 | [README.md](https://github.com/sohobloo/react-native-modal-dropdown/blob/master/README.md)| src/screens/ProfileTab/`Developer.js` & src/components/CartTab/`BagItemCard.js`
| react-native-paper | ^2.16.0 | [README.md](https://github.com/callstack/react-native-paper/blob/master/README.md)| Used by *react-navigation-material-bottom-tabs*
| react-native-splash-screen | 3.0.6 | [README.md](https://github.com/crazycodeboy/react-native-splash-screen/blob/master/README.md)| src/screens/HomeTab/`Scene.js` & src/screens/`LoginScreen.js`
| react-native-swiper | ^1.5.14 | [README.md](https://github.com/leecade/react-native-swiper/blob/master/README.md)| src/components/HomeTab/`BannerSlider.js`
| react-native-typography | ^1.4.0 | [README.md](https://github.com/hectahertz/react-native-typography/blob/master/README.md)| **Almost all screens depend on this package**
| react-native-vector-icons | ^6.5.0 | [README.md](https://github.com/oblador/react-native-vector-icons/blob/master/README.md)| `App.js` & src/components/CartTab/`BagItemCard.js` & src/components/SearchTab/`SearchBar.js` & src/screens/ProfileTab/`Developer.js`
| react-navigation | ^3.9.1 | [README.md](https://github.com/react-navigation/react-navigation/blob/master/README.md)| **Almost all screens depend on this package**
| react-navigation-material-bottom-tabs | ^1.0.0 | [README.md](https://github.com/react-navigation/material-bottom-tabs/blob/master/README.md)| `App.js`

# Getting new App Builds

For **iOS**, make sure to build the app for `release`, as this will store the images on the device and also will cause the app to perform a lot better. To change the build type from `Debug` to `Release`, press:
`Shift + Command + <` on XCode. Now, change `Build Configuration`
To update TestFlight build, select build device as `Generic iOS Device` then from top navigation select `Product-->Archieve`. You will need the credentials of Evren Baykan to send the build.

For **Android**,  after getting a build, `app-debug.apk` can be located from `android/app/build/outputs/apk/debug/app-debug.apk`. If release build is needed for whatever reason, follow the instructions here to obtain one: [Android Release Build Instructions](https://www.instamobile.io/android-development/generate-react-native-release-build-android/)

# Files
The app icon, google services files, and development certificate can be found from here:
[Google Drive](https://drive.google.com/drive/u/4/folders/1Qfptcx0QDOWsoYdQOsAL72miOqL4HDms) (Requires @useinsider email login to view)

# Other Documentation
Please refer to the [Mobile App show case App - iGurme](https://insider-portal.atlassian.net/wiki/spaces/ProductKB/pages/747667581/INT+Mobile+App+show+case+App+-+iGurme) for additional information and pictures.
#### Last edit by: Ralfi Salhon on August 8th, 2019