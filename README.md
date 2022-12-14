# react-native-video360next

To Support Android 0.31 supported
### android/build.gradle
- Add line above "allprojects"
```
def REACT_NATIVE_VERSION = new File(['node', '--print',"JSON.parse(require('fs').readFileSync(require.resolve('react-native/package.json'), 'utf-8')).version"].execute(null, rootDir).text.trim())
```

- Add "configurations.all", "jcenter()" and "jcenter" inside "allprojects"
```
jcenter() 
configurations.all {
    resolutionStrategy {
        force "com.facebook.react:react-native:" + REACT_NATIVE_VERSION 
    }
}
jcenter() {
  content {
    includeModule("com.yqritc", "android-scalablevideoview")
  }
}
```

### android/app/build.gradle 
- Add "packagingOptions" into "android"
```
 packagingOptions {
        pickFirst 'lib/x86/libc++_shared.so'
        pickFirst 'lib/arm64-v8a/libc++_shared.so'
        pickFirst 'lib/x86_64/libc++_shared.so'
        pickFirst 'lib/armeabi-v7a/libc++_shared.so'
        pickFirst 'lib/arm64-v8a/libfbjni.so'
        pickFirst 'lib/armeabi-v7a/libfbjni.so'
        pickFirst 'lib/x86/libfbjni.so'
        pickFirst 'lib/x86_64/libfbjni.so'
    }
```

React Native module to play 360 videos, using the SGPlayer for iOS, and Google VR for Android, this plugin was tested using react native 0.70

[![360](https://raw.githubusercontent.com/Rohit687/react-native-video360next/master/360.png "360")](https://raw.githubusercontent.com/Rohit687/react-native-video360next/master/360.png "360")

## Getting started
`npm i react-native-video360next --save`
or
`yarn add react-native-video360next`

### Notes
- This component was tested in React Native 0.70 with automatic linking, but I think that it should work in older versions linking manually.
- don't forget to run 'pod install' in iOS folder
	cd ios && pod install
- in Android test in a real device or android Q simulator I don't know but VR SDK fails in a lot of simulators but not in real devices.
- in iOS if you want to run in a real device go to Pods-> Development Pods -> react-native-video-360 -> react-native-video360.podspec open it and uncoment production and comment simulator lines, then run 'pod install' again. I wanted to build the framework for all architectures but it didn't worked, ill try to fix this in the future

```
# dev simulator
 # s.ios.vendored_frameworks = 'Frameworks/SGPlayer.framework','Frameworks/SGPlatform.framework'
 # production
 s.ios.vendored_frameworks = 'Frameworks/arm/SGPlayer.framework','Frameworks/arm/SGPlatform.framework'
```
### IOS USAGE
```javascript
<Video360 urlVideo={videoUrl} modeVideo={modeVideo} style={{ flex: 1}} />
```
Video Modes
```
 if(modeVideo == 1)
      _playerView.demoType = DemoType_AVPlayer_VR;
    if(modeVideo == 2)
      _playerView.demoType = DemoType_AVPlayer_VR_Box;
    if(modeVideo == 3)
      _playerView.demoType = DemoType_AVPlayer_Normal;
```
but I use like this, wraping it in a modal that is  triggered by a button that sets the url and the video mode.

```javascript
<Modal>
	<View style={{ flex: 1, backgroundColor: 'black' }}>
		<View style={{ position: 'absolute', top: 20, left: 15, zIndex: 10 }}>
		<TouchableWithoutFeedback onPress={() => { setModalVisible(false)}}>
		<Text style={{ color: 'white' }}>close</Text>
		</TouchableWithoutFeedback>
		</View>
	<Video360 urlVideo={videoUrl} modeVideo={modeVideo} style={{ flex: 1}} />
	</View>
</Modal>

```
#### video modes
```javascript
if(modeVideo == 1)
      _playerView.demoType = DemoType_AVPlayer_VR; // 360
    if(modeVideo == 2)
      _playerView.demoType = DemoType_AVPlayer_VR_Box; // 360 with glases
    if(modeVideo == 3)
      _playerView.demoType = DemoType_AVPlayer_Normal; // normal
```
## Android Usage
same as ios
```javascript
<Video360 urlVideo={videoUrl} modeVideo={modeVideo} style={{ flex: 1}} />
```
it also can have this props
```
volume={1}
displayMode='embedded'
enableInfoButton={true}
enableFullscreenButton={true}
enableCardboardButton={true}
enableTouchTracking={true}
hidesTransitionView={false}
```
I have taken the android part of code from here
https://github.com/altafan/react-native-gvr-video-android/tree/master/android/src/main/java/com/gvrvideo

iOS the player from here
https://github.com/libobjc/SGPlayer


## TODO
For Android you will have to design manually slider.
- Seekbar for andriod is managed by javascript code to manually added using "DeviceEventEmitter"
```
 const [duration, setDuration] = useState({
    total: 0,
    current: 0
  })
```
```
useEffect(() => {
    let showSubscription = null;
    if (Platform.OS == 'android') {
      showSubscription = DeviceEventEmitter.addListener('seekProgressValue', seekProgressValue);
    }

    return () => {
     
      if (showSubscription) {
        showSubscription.remove();
      }
     }

  }, [])
```
```
const seekProgressValue = (event) => {

    if (duration.current != event.progress) {
      setDuration({
        total: event.total - 1,
        current: event.progress
      })
    }

    if (event.isComplete) {
     //--You can close the modal
    }
};
```

