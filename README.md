# React Native Vkontakte login

This module is a wrapper around native VK SDKs for [Android](https://new.vk.com/dev/android_sdk) and [iOS](https://new.vk.com/dev/ios_sdk).

It allows to log in to VK and obtain access token, which you can later use to make VK API calls.

## Installation

`npm install --save react-native-vkontakte-login`

### vk.com

First, configure your vk.com app.

For iOS, you will need to fill in App Bundle for iOS field.

For Android, you will need to fill in Package name, Main Activity, Signing certificate fingerprint fields. To obtain the fingerprint, you can follow the [guide](https://new.vk.com/dev/android_sdk) from VK Android SDK documentation.

<img src="https://raw.githubusercontent.com/doomsower/react-native-vkontakte-login/master/images/vk_app_settings.png" alt="vk settings" />


### Android

1) Run `react-native link` (If you user react-native < 0.26, install [rnpm](https://github.com/rnpm/rnpm) and run `rnpm link`)
2) In your AndroidManifest.xml, add following line inside `<application>` element:
    ```
    <activity android:name="com.vk.sdk.VKServiceActivity" android:label="ServiceActivity" android:theme="@style/VK.Transparent" />
    ```
3) **(Optional)** Add VK_APP_ID to resources (main/res/values/strings.xml) so the module will initialize with it at startup:
    ```
    <integer name="com_vk_sdk_AppId">VK_APP_ID</integer>
    ```

    If you do so, you won't need to call `VKLogin.initialize(vkAppId)` from your JS code.

### iOS

1) This module requires CocoaPods to be used in iOS project. [here](https://facebook.github.io/react-native/docs/embedded-app-ios.html) you can find instructions on how to add CocoaPods to your react-native project.
Add this line

    ```pod 'react-native-vkontakte-login', :path => '../node_modules/react-native-vkontakte-login'```

    to your Podfile (you may need to adjust path if you have non-standard project structure).

    Dont't forget to fix linker errors by adding `$(inherited)` to **Other Linker Flags** in Build Settings:

    <img src="https://raw.githubusercontent.com/doomsower/react-native-vkontakte-login/master/images/other_linker_flags.png" alt="xcode url type" />

2) Add following fragment to your `info.plist`:

    ```
        <key>LSApplicationQueriesSchemes</key>
        <array>
            <string>vk</string>
            <string>vk-share</string>
            <string>vkauthorize</string>
        </array>
    ```

3) To use authorization via VK App you need to setup a url-schema of your application.
Open your application settings then select the Info tab. In the URL Types section click the plus sign.
Enter vk+APP_ID (e.g. vk5514471) to the **Identifier** and **URL Schemes** fields.

    <img src="https://raw.githubusercontent.com/doomsower/react-native-vkontakte-login/master/images/url_types.png" alt="xcode url type" />

    Alternatively, you can add following to your info.plist:

    ```
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleTypeRole</key>
            <string>Editor</string>
            <key>CFBundleURLName</key>
            <string>vk5514471</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>vk5514471</string>
            </array>
        </dict>
    </array>
    ```

4) In your AppDelegate.m, you need to import VK SDK: `#import "VKSdk.h"` and then add following code (both methods are required):

    ```
    //iOS 9 workflow
       - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options {
           [VKSdk processOpenURL:url fromApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
           return YES;
       }

       //iOS 8 and lower
       -(BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation
       {
           [VKSdk processOpenURL:url fromApplication:sourceApplication];
           return YES;
       }
    ```

5) **(Optional)** You can add your VK Application ID to `info.plist` so the module will initialize with it at startup:
    ```
    	<key>VK_APP_ID</key>
    	<integer>5514471</integer>
    ```

    If you do so, you won't need to call `VKLogin.initialize(vkAppId)` from your JS code.

## Usage

Import module in your JS code

```import VKLogin from 'react-native-vkontakte-login';```

It has three methods:

1) `VKLogin.initialize(vkAppId)` - initializes VK SDK with numeric id of your VK application. You only need to call this once before you call `login` or `logout`.

    You can skip this call if you've added your VK App ID to your Android's resources or iOS's info.plist as described in optional steps above.
2) `VKLogin.login(scopesArray)` - opens VK login dialog either via VK mobile app or via WebView (if app is not installed on the device).
`scopesArray` is array which contains VK access permissions as strings. For example, `VKLogin.login(['friends', 'photos', 'email'])`.
List of available permissions can be found [here](https://new.vk.com/dev/permissions)

    This method returns a Promise, which resolves with following object:
    ```
    {
        "access_token": <access token which you can use in your VK API calls>
        "user_id": <VK User ID>
        "expires_in": <acess token expires in>
        "email": <user email, if permissions is given>
        "secret": <secret, if returned>
    }
    ```

3) `VKLogin.logout()` - performs the logout. Returns a promise.

## Example

You can find it [here](https://github.com/doomsower/react-native-vkontakte-login/tree/master/example)
