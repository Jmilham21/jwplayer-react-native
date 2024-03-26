# jwplayer-react-native
`<JWPlayer>` is a React Native Component that wraps around the JW Player native SDK's. This bridge allows for simple usage of the native SDKs in React Native applications.

## Contents
Table of contents (in no particular order for to level items)
- [Getting started (5 minutes to playback)](#getting-started-5-minutes-to-playback)
  - [Add to project](#add-to-your-project)
  - [Linking / install iOS Pods](#mostly-automatic-installation)
  - [Required Android gradle changes](#android-dependencies)
  - [Usage](#usage)
- [Example app setup](#example-project)
  - [Setup](#running-the-example-project)
  - [Usage](#using-the-example-app)
- [Props](#props)
  - [Required](#required-props)
    - [Config](#config)
  - [Optional](#optional-props)
  - [Callbacks](#callbacks)
- [Advanced Topics](#advanced-topics)
  - [PiP](#pip)
  - [DRM](#drm)
  - [Background Audio](#background-audio)
  - [Casting](#casting)
- [Migration reference from historical library](#migration-reference-old-lib-to-this)
- [Contributing](#contributing)
- [Issues](#issues)

## Getting started (5 minutes to playback)
Assuming you already have a react-native application, these instructions will help you install, implement, and configure the `<JWPlayer>` in your application.

### Add to your project
Add this library to your application through [npm]('https://www.npmjs.com/') or [yarn]('https://yarnpkg.com/')
`npm i jwplayer-react-native --save`
or 
`yarn add jwplayer-react-native`

### Mostly automatic installation

For iOS you have to run `cd ios/` && `pod install`.

For Android the package is automatically linked.

#### iOS Dependencies
TODO -- ASK iOS team if any?
For iOS you have to run `cd ios/` && `pod install`.


#### Android Dependencies
The JW Player maven repository will need to be added to your Android applications configuration. Depending on the configuration of your application (Groovy vs Kotlin DSL) and versioning of Gradle, add the following lines wherever you add remote repositories, such as `android/build.gradle`:

```
maven {
    url 'https://mvn.jwplayer.com/content/repositories/releases/'
}
```

Example using Groovy in `android/build.gradle`:

```
allprojects {
    repositories {
        mavenLocal()
        maven {
            // All of React Native (JS, Obj-C sources, Android binaries) is installed from npm
            url("$rootDir/../node_modules/react-native/android")
        }
        maven {
            // Android JSC is installed from npm
            url("$rootDir/../node_modules/jsc-android/dist")
        }

        google()
        maven { url 'https://jitpack.io' }
        // Add these lines
        maven{
            url 'https://mvn.jwplayer.com/content/repositories/releases/'
        }
    }
}
```

### Usage

```javascript
...

import JWPlayer, { JWPlayerState } from 'react-native-jw-media-player';

...

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  player: {
    flex: 1,
  },
});

...
  const playlist = [{
    file: 'myfile.mp4',
    image: 'myPoster.jpg',
    tracks: [{
      file: 'https://mySubtitles.vtt',
      label: 'English',
      kind: 'captions',
      default: true
    }],
  },
  {
    file: 'mySecondFile.mp4',
    image: 'MysecondFilesPoster.jpg',
  }];


const config = {
  license:
    Platform.OS === 'android'
      ? 'YOUR_ANDROID_SDK_KEY'
      : 'YOUR_IOS_SDK_KEY',
  backgroundAudioEnabled: true,
  autostart: true,
  styling: {
    colors: {
      timeslider: {
        rail: "0000FF",
      },
    },
  },
  playlist: [playlistItem],
}

...

async isPlaying() {
  const playerState = await this.JWPlayer.playerState();
  return playerState === JWPlayerState.JWPlayerStatePlaying;
}

...

render() {

...

<View style={styles.container}>
  <JWPlayer
    ref={p => (this.JWPlayer = p)}
    style={styles.player}
    config={config}
    onBeforePlay={() => this.onBeforePlay()}
    onPlay={() => this.onPlay()}
    onPause={() => this.onPause()}
    onIdle={() => console.log("onIdle")}
    onPlaylistItem={event => this.onPlaylistItem(event)}
    onSetupPlayerError={event => this.onPlayerError(event)}
    onPlayerError={event => this.onPlayerError(event)}
    onBuffer={() => this.onBuffer()}
    onTime={event => this.onTime(event)}
    onFullScreen={() => this.onFullScreen()}
    onFullScreenExit={() => this.onFullScreenExit()}
  />
</View>

...

}
```

## Example Project
The `Example` project in this repository shows a few basic implementations of the `<JWPlayer>` view. It is also a great starting place for reproducing issues (link to issues tab -- TODO).

### Running the example project
1. Checkout this repository.
2. Go to `Example` directory and run `yarn` or `npm i`
3. Go to `Example/ios` and install Pods with `pod install`
4. Open `RNJWPlayer.xcworkspace` with XCode. // TODO confirm with iOS... this doesn't feel necessary to me
5. Add your JW SDK license in `App.js` under the `config `prop.
6. Build and run the app to your preferred platform `yarn android` or `yarn ios` or with any specific `react-native` command you prefer

### Using of the Example App
Use the `Example` application to:
- Test your PR or changes
- Validate your configuration in a known working application
- For submitting bugs as a sanitary place to demonstrate [issues](#issues)

## Props
This wrapper implements the native methods exposed by the [Android](https://sdk.jwplayer.com/android/v4/reference/com/jwplayer/pub/api/JsonHelper.html) and [iOS](https://sdk.jwplayer.com/ios/v4/reference/Classes/JWJSONParser.html) SDK for parsing JSON objects into player configs. This allows for easy parsing of the [JW Delivery API](https://docs.jwplayer.com/platform/reference/embed-content-with-the-delivery-api#delivery-api-v2-endpoints) into easy to use configurations.  

Ideally, you will pass a response from a [playlist](https://docs.jwplayer.com/platform/reference/get_v2-playlists-playlist-id) into the `playlist` field for the required `config` prop.

You have the ability to manually create a `JwPlaylistItem` as defined in the [typescript definitions](index.d.ts), or to use the now legacy `PlaylistItem`

### Required Props
What you must supply in order to get started:
- [config](#config) is the only required prop for a `<JWPlayer>` view to operate. It's an overloaded prop, so see the definitions for required inner-fields. 

### Optional Props
- [style](docs/LEGACY_README.md#styling)
- `controls` -- Should the controls show or not true/false
- `forceLegacyConfig` -- Tells the wrapper to use the legacy `config` prop type only

#### Config
Create config definition for dAPI style vs [legacy](docs/LEGACY_README.md#Config)

`config` can be a `JwConfig`, or a now legacy `Config` as defined [here](docs/LEGACY_README.md#Config).

The `JwConfig` is derived from the JWP Deliver API response, and matches a similiar pattern to the JWP web player.

|Field                             |Description        |Type|Optional|Platform Specific|
|----------------------------------|-------------------|----|--------|-----------------|
|pid                               |player ID          |string|TRUE    |                 |
|mute                              |                   |boolean|TRUE    |                 |
|forceLegacyConfig                 |non-jw json        |boolean|TRUE    |                 |
|useTextureView                    |                   |boolean|TRUE    |A                |
|autostart                         |                   |boolean|TRUE    |                 |
|nextupoffset                      |                   |string &#124; number|TRUE    |                 |
|repeat                            |                   |boolean|TRUE    |                 |
|allowCrossProtocolRedirectsSupport|                   |boolean|TRUE    |A                |
|displaytitle                      |                   |boolean|TRUE    |                 |
|displaydescription                |                   |boolean|TRUE    |                 |
|stretching                        |                   |JwStretching|TRUE    |                 |
|thumbnailPreview                  |                   |JwThumbnailPreview|TRUE    |                 |
|preload                           |                   |boolean|TRUE    |                 |
|playlist                          |                   |JwPlaylistItem[ ] &#124; string|TRUE    |                 |
|sources                           |                   |JwSource[ ]|TRUE    |                 |
|file                              |                   |string|TRUE    |                 |
|playlistIndex                     |                   |number|TRUE    |                 |
|related                           |                   |JwRelatedConfig|TRUE    |                 |
|uiConfig                          |                   |JwUiConfig|TRUE    |                 |
|logoView                          |                   |JwLogoView|TRUE    |                 |
|advertising                       |                   |JwAdvertisingConfig|TRUE    |                 |
|playbackRates                     |                   |number[ ]|TRUE    |                 |
|playbackRateControls              |                   |boolean|TRUE    |                 |
|license                           |non-jw json        |string|FALSE   |                 |


### Callbacks

Explain the callbacks/listeners and what can be expected from them (copy as much as possible from native docs)

##### All Callbacks with data are wrapped in native events for instance this is how to get the data from `onTime` callback ->

```javascript
  onTime(event) {
    const {position, duration} = event.nativeEvent;
  }
```

These definitions can vary based on version of the SDK and platform, so always seek the most up to date information from the platform docs ([iOS](https://sdk.jwplayer.com/ios/v4/reference/index.html)/[Android](https://sdk.jwplayer.com/android/v4/reference/index.html))

See available callback list [here](docs/LEGACY_README.md#available-callbacks)

## Migration Reference (old lib to this)
Unsure if this should have it's own section but explaining the change from OU's repo to ours might help with adoption and getting started

The name change is biggest difference to begin, as well as `npm` registry. To get started with this package, old references will need to be removed/updated to this.

**This**: `jwplayer-react-native`
**Old**: `react-native-jw-media-player`

Some mention of props change (non-breaking if using `forceLegacyConfig = true` to ensure logic flow)

## Advanced Topics
A place left open to define more complex use cases. Can link to example code, specialty .md files for more info, or a combination.
### PiP

It's recommended read and understand the requirements for both native platforms ([Android](https://docs.jwplayer.com/players/docs/android-invoke-picture-in-picture-playback)/[iOS](https://docs.jwplayer.com/players/docs/ios-invoke-picture-in-picture-playback)) before implementing.

Picture in picture mode is enabled by JW on iOS for the PlayerViewController, however when setting the `viewOnly` prop to true you will also need to set the `pipEnabled` prop to true, and call the `togglePIP` method to enable / disable PIP.

For Android you will have to add the following code in the activity where the player lives, like `MainActivity.java`

```java
@Override
public void onPictureInPictureModeChanged(boolean isInPictureInPictureMode, Configuration newConfig) {
  super.onPictureInPictureModeChanged(isInPictureInPictureMode, newConfig);

  Intent intent = new Intent("onPictureInPictureModeChanged");
  intent.putExtra("isInPictureInPictureMode", isInPictureInPictureMode);
  intent.putExtra("newConfig", newConfig);
  this.sendBroadcast(intent);
}
```
### DRM

Checkout the official DRM docs [iOS](https://developer.jwplayer.com/jwplayer/docs/ios-play-drm-protected-content) & [Android](https://developer.jwplayer.com/jwplayer/docs/android-play-drm-protected-content).

There is only support for **Fairplay** on iOS and **Widevine** for Android.

The simplest and ideal usage is to pass the returned value from a [JWP signed URL](https://docs.jwplayer.com/platform/docs/protection-studio-drm-generate-a-signed-content-url-for-drm-playback) to the `<JWPlayer>` as a `config`. **Do not sign and store your API secerets from your application**

If you use a different provider for DRM or this does not work for your use case, conforming to a similiar format as a JWP signed URL response (adding `drm` field to the `sources` for a playlist item) is also optimal.

See [here](docs/LEGACY_README.md#drm) for instructions on using the legacy DRM props

Checkout the `DRMExample` in the `Example` app. (The `DRMExample` cannot be run in the Simulator, and the window will not show on an Android Emulator).


### Background Audio

This package supports Background audio sessions by setting the `backgroundAudioEnabled` prop on the [Config](#config), just follow the JWPlayer docs for background session.

[Android](https://docs.jwplayer.com/players/docs/android-enable-background-audio): this package handles background audio playing in Android. You shouldn't have to make any additional changes.

[iOS](https://docs.jwplayer.com/players/docs/ios-player-backgrounding-reference)

For iOS you will have to enable `audio` in **Signing & Capabilities** under `background modes`.

### Casting

JWPlayer enables casting by default with a casting button (if you pass the `viewOnly` prop in the player config on iOS then you will need to enable casting by yourself).

###### iOS

1: Follow the instruction [here](https://developer.jwplayer.com/jwplayer/docs/ios-enable-casting-to-chromecast-devices) on the official JWPlayer site.

2: Add `$RNJWPlayerUseGoogleCast = true` to your Podfile, this will install `google-cast-sdk` pod.

3: Edit your `Info.plist` with the following values:

```
'NSBluetoothAlwaysUsageDescription' => 'We will use your Bluetooth for media casting.',
'NSBluetoothPeripheralUsageDescription' => 'We will use your Bluetooth for media casting.',
'NSLocalNetworkUsageDescription' => 'We will use the local network to discover Cast-enabled devices on your WiFi network.',
'Privacy - Local Network Usage Description' => 'We will use the local network to discover Cast-enabled devices on your WiFi network.'
'NSMicrophoneUsageDescription' => 'We will use your Microphone for media casting.'
```

4: Enable _Access WiFi Information_ capability under `Signing & Capabilities`

###### Android
1. Add `RNJWPlayerUseGoogleCast = true` to your *app/build.gradle* in `ext {}`.
2. Add `com.google.android.gms:play-services-cast-framework:21.3.0` to your Android **app/build.gradle**
3. Create a class that overrides `OptionsProvider` in **your** Android codebase
    1. See reference file `android/src/main/java/com/appgoalz/rnjwplayer/CastOptionsProvider.java`
    2. Replace `.setTargetActivityClassName(RNJWPlayerView.class.getName())` with your player Activity
    3. Modify the file with any options necessary for your use case
4. Add the meta-data to your `AndroidManifest.xml` like below, ensuring 
```xml
    <meta-data
        android:name="com.google.android.gms.cast.framework.OPTIONS_PROVIDER_CLASS_NAME"
        android:value="path.to.CastOptionsProvider" />
```
5. Casting should now be enabled

### Advertising

When using an **IMA** or **DAI** ad client you need to do some additional setup.

- **iOS**: Add `$RNJWPlayerUseGoogleIMA = true` to your Podfile, this will add `GoogleAds-IMA-iOS-SDK` pod.

- **Android**: Add `RNJWPlayerUseGoogleIMA = true` in your *app/build.gradle* `ext {}` this will add `'com.google.ads.interactivemedia.v3:interactivemedia:3.31.0'`
        and `'com.google.android.gms:play-services-ads-identifier:18.0.1'`.

## Contributing
- Boiler plate about contributing to an OSS project. 
- Expectations (need help) are for work for all, not just for you
    - Code shouldn't be a one-off solution for your use case
- PRs should coorelate to an open Issue unless there's a good reason (we don't want phantom PRs appearing changing the wheel if it's not required)
- Keep work small as required. Large PRs aren't fun for anyone

## Issues
- Follow format for Bugs/Features/Questions. 
- Provide as much information as possible
- For quickest support, reach out to JW support via __ after creating an issue on this repo. (Yet to be confirmed as an ok path with Support, but imo this is ideal)
- If submitting a bug, always attempt to recreate the issue in our `Example` app
