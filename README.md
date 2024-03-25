# jwplayer-react-native
`<JWPlayer>` is a React Native Component that wraps around the JW Player native SDK's. This bridge allows for simple usage of the native SDKs in React Native applications.

## TEMP (DELETE WHEN FINAL)
Table of contents (in no particular order for to level items)
- Getting started (5 minutes to playback)
    - Add to project
    - Linking / install iOS Pods
    - Required Android gradle changes
    - Usage
    - Gotcha's?
- Example app setup
    - Install
    - License key
    - Run command
    - Screens description
- Props
    - New methods allow for dAPI style objects as `config`
    - Legacy props link?
    - Callback information
        - This needs refactoring so we are using the same callbacks for now 
- Tricky bits (Confusing parts)
    - PiP
    - DRM
    - Background Audio
    - Casting
    - Advertising gotcha's
- Migration reference from historical library
    - Mention of old library w/ links?
    - Link to table mappping fields
    - New vs Old
- Contributing
    - We Want You :fingerpoint:
    - How to do it
    - Fork on
- Raising Issues
    - How to do it
    - Formatting
    - Reaching out to Support vs Here

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
// TODO -- Get a better config for demo
const playlistItem = {
  title: 'Track',
  mediaId: -1,
  image: 'http://image.com/image.png',
  description: 'My beautiful track',
  startTime: 0,
  file: 'http://file.com/file.mp3',
  autostart: true,
  repeat: false,
  displayDescription: true,
  displayTitle: true,
  tracks: [
    {
      file: 'http://file.com/english.vtt',
      label: 'en'
    },
    {
      file: 'http://file.com/spanish.srt',
      label: 'es'
    }
  ],
  sources: [
    {
      file: 'http://file.com/file.mp3',
      label: 'audio'
    },
    {
      file: 'http://file.com/file.mp4',
      label: 'video',
      default: true
    }
  ]
}

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

## Props (Huge TODO)
TABLE to be made
Should describe the change to dAPI driven `config` props so we don't have to describe everything?
### Config
config,style,controls,forceLegacyConfig
### Callbacks
Explain the callbacks/listeners and what can be expected from them (copy as much as possible from native docs)

## Migration Reference (old lib to this)
The name change is biggest difference to begin, as well as `npm` registry. To get started with this package, old references will need to be removed/updated to this.
This: jwplayer-react-native
Old: react-native-jw-media-player
Some mention of props change (non-breaking if using `forceLegacyConfig = true` to ensure logic flow)

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
