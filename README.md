# Loom Network SDK for Unity3d

This repo contains the SDK code and a **Unity 2017.3** project that provides examples.

The SDK currently supports the following Unity targets:
- Desktop Win/MacOS/Linux
- Android
- iOS


## Requirements

- Unity 2017.4.1 or later.
- `Build Settings` -> `Player Settings` -> `Configuration` set as follows:
- `Scripting Runtime Version`: `Experimental (.NET 4.6 Equivalent)`
- `API Compatibility Level`: `.NET 4.6`

## Overview

`AuthClient` should be used to obtain a `Identity`, once you have a `Identity` you
can use the associated private key to sign and then commit transactions using `LoomChainClient`.

## Authorization Flows

To obtain a `Identity` you must first obtain an access token from Auth0, the exact process
depends on the platform.

### Desktop Windows / Mac / Linux

`AuthClient` will wait for an HTTP request on `http://127.0.0.1:9999/auth/auth0/`, then it will
open a new browser window and load the Auth0 sign-in page (using the default system web browser).
At this point the user should be directed to switch to the browser to sign-in, when they do so
successfully Auth0 will redirect to the aforementioned URL, and `AuthClient` will fetch or
create a `Identity`.

### Android

`AuthClient` will open the default system browser to the Auth0 hosted login page, once the user
signs in the browser will redirect the user back to the Unity app. `AuthClient` will then fetch
or create a `Identity`.

You must add an Auth0 redirect activity to the Android manifest, and set the `host`, `pathPrefix`,
and `scheme` to match the Auth0 redirect URL specified when creating a new instance of `AuthClient`.
For example:

```xml
<activity android:name="com.auth0.android.provider.RedirectActivity" tools:node="replace">
<intent-filter>
<action android:name="android.intent.action.VIEW" />
<category android:name="android.intent.category.DEFAULT" />
<category android:name="android.intent.category.BROWSABLE" />
<data
android:host="loomx.auth0.com"
android:pathPrefix="/android/io.loomx.unity_sample/callback"
android:scheme="io.loomx.unity3d" />
</intent-filter>
</activity>
```

If you don't have a custom manifest you can use `Assets/Plugins/Android/AndroidManifest.xml` in
this repo as a starting point, at the very least you will need to update the `package`, and the
`data` parameters.

### iOS

`AuthClient` will open the Safari WebView to the Auth0 hosted login page, once the user
signs in the browser will redirect the user back to the Unity app. `AuthClient` will then fetch
or create a `Identity`.

Requirements:

    iOS 9 or later
    Xcode 8
    Swift 3.0
    SimpleKeychain framework
    Auth0 framework

Set the mimium target SDK to 9.0 in Player settings befor building for iOS platform

iOS build scripts adds following changes to the Xcode project, make sure to update it if you use a custom project :

    Swift bridging header : Libraries/LoomSDK/ios/LoomSDKSwift-Bridging-Header.h
    Swift prebuild header: LoomSDKSwift.h
    Swift version - 3.0
    Simplekeychain prebuilt framework
    Auth0 prebuilt framework
    Custom handler for application:openUrl for Unity application 
    Custom URL Scheme with "Auth0" as name and "$PRODUCT_BUNDLE_ID" as scheme in info.plist
    Objective C and Swift source files in Library/LoomSDK/iOS/


## Samples


When you run the sample scene you will see two buttons that are hooked up to call the
corresponding methods in `Assets/authSample.cs`, these must be pressed in the correct order:
1. Press the `Sign In` button, this should open a new browser window, once you've signed up/in
you should see the text above the button change to `Signed in as ...`.
3. You can press the `Sign Out` button after signing in to delete user identity. 

## Dependencies

For ease of use all necessary prebuilt dependencies are located in the `Assets/Plugins` directory in
this repo, the assemblies are built to target `Any CPU`. The rest of this section contains some
useful notes in case you need to rebuild the dependencies.

### Auth0.Authentication and Auth0.Core

The Auth0 assemblies only target .NET Standard 1.1/2, and at the time of writing Unity 2017.3 only
targets .NET Framework 4.6, so attempting to install Auth0 via NuGet in the SDK project fails.
Building the assemblies from source and copying them into the SDK project also fails
(with `error CS0012: The type 'System.Object' is defined in an assembly that is not referenced`).
To get around these issues the Auth0 assemblies must be rebuilt from the `net4.6-target`
branch in this repo https://github.com/loomnetwork/auth0.net - then copy the built assemblies
from `src/Auth0.AuthenticationApi/bin/Release/net46` to the Unity project.

### Chaos.NaCl

Build from source https://github.com/CodesInChaos/Chaos.NaCl - then copy the build assemblies from
`Chaos.NaCl/Chaos.NaCl/bin/Release` to `Assets/Plugins`.


## Unity package build scripts

build.cmd - Windows script that builds unity package with content from Assets directory
build.sh - OSX/Linux script that builds unity package with content from Assets directory

Both scripts use UNITY_PATH variable as path to Unity. 
For default installation it should point to :
    '/Applications/Unity/Unity.app/Contents/MacOS/Unity' - OSX
    'C:\Program Files\Unity\Editor\Unity.exe' - Windows
    
    
    


