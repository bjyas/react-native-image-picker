# react-native-image-picker
A React Native module that allows you to use the native UIImagePickerController UI to either select a photo from the device library or directly from the camera, like so:

### iOS
** Requires iOS 8 or higher

<img title="iOS" src="https://github.com/marcshilling/react-native-image-picker/blob/master/images/ios-image.png" width="50%">

### Android
** Requires Api 11 or higher for Android

<img title="Android" src="https://github.com/marcshilling/react-native-image-picker/blob/master/images/android-image.png" width="50%">

## Install

### iOS
1. `npm install react-native-image-picker@latest --save`
2. In the XCode's "Project navigator", right click on your project's Libraries folder ➜ `Add Files to <...>`
3. Go to `node_modules` ➜ `react-native-image-picker` ➜ `ios` ➜ select `UIImagePickerManager.h` and `UIImagePickerManager.m`
4. Make sure `UIImagePickerManager.m` is listed under 'Compile Sources' in your project's 'Build Phases' tab
5. Compile and have fun

### Android
1. `npm install react-native-image-picker@latest --save`

```gradle
// file: android/settings.gradle
...

include ':react-native-image-picker'
project(':react-native-image-picker').projectDir = new File(settingsDir, '../node_modules/react-native-image-picker/android')
```
```gradle
// file: android/app/build.gradle
...

dependencies {
    ...
    compile project(':react-native-image-picker')
}
```
```java
// file: android/app/src/main/java/com/myappli/MainActivity.java
...
import android.content.Intent; // import
import com.imagepicker.ImagePickerPackage; // import

public class MainActivity extends Activity implements DefaultHardwareBackBtnHandler {

    private ReactInstanceManager mReactInstanceManager;
    private ReactRootView mReactRootView;

    // declare package
    private ImagePickerPackage mImagePicker;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        mReactRootView = new ReactRootView(this);

        // instantiate package
        mImagePicker = new ImagePickerPackage(this);

        mReactInstanceManager = ReactInstanceManager.builder()
                .setApplication(getApplication())
                .setBundleAssetName("index.android.bundle")
                .setJSMainModuleName("index.android")
                .addPackage(new MainReactPackage())

                // register package here
                .addPackage(mImagePicker)

                .setUseDeveloperSupport(BuildConfig.DEBUG)
                .setInitialLifecycleState(LifecycleState.RESUMED)
                .build();
        mReactRootView.startReactApplication(mReactInstanceManager, "AwesomeProject", null);
        setContentView(mReactRootView);
    }

    @Override
    public void onActivityResult(final int requestCode, final int resultCode, final Intent data) {
        super.onActivityResult(requestCode, resultCode, data);

        // handle onActivityResult
        mImagePicker.handleActivityResult(requestCode, resultCode, data);
    }
...

```
## Usage
1. In your React Native javascript code, bring in the native module:

  ```javascript
var UIImagePickerManager = require('NativeModules').UIImagePickerManager;
  ```
2. Use it like so:

  When you want to display the picker:
  ```javascript
  var options = {
    title: 'Select Avatar', // specify null or empty string to remove the title
    cancelButtonTitle: 'Cancel',
    takePhotoButtonTitle: 'Take Photo...', // specify null or empty string to remove this button
    chooseFromLibraryButtonTitle: 'Choose from Library...', // specify null or empty string to remove this button
    customButtons: {
      'Choose Photo from Facebook': 'fb', // [Button Text] : [String returned upon selection]
    },
    maxWidth: 100,
    maxHeight: 100,
    quality: 0.2,
    allowsEditing: false, // Built in iOS functionality to resize/reposition the image
    noData: false, // Disables the base64 `data` field from being generated (greatly improves performance on large photos)
    storageOptions: { // if this key is provided, the image will get saved in the documents directory (rather than a temporary directory)
      skipBackup: true, // image will NOT be backed up to icloud
      path: 'images' // will save image at /Documents/images rather than the root
    }
  };

  /**
   * The first arg will be the options object for customization, the second is
   * your callback which sends bool: didCancel, object: response.
   *
   * response.data is the base64 encoded image data
   * response.uri is the uri to the local file asset on the device
   * response.isVertical will be true if the image is vertically oriented
   * response.width & response.height give you the image dimensions
   */

  UIImagePickerManager.showImagePicker(options, (didCancel, response) => {
    console.log('Response = ', response);

    if (didCancel) {
      console.log('User cancelled image picker');
    }
    else {
      if (response.customButton) {
        console.log('User tapped custom button: ', response.customButton);
      }
      else {
        // You can display the image using either:
        const source = {uri: 'data:image/jpeg;base64,' + response.data, isStatic: true};
        const source = {uri: response.uri.replace('file://', ''), isStatic: true};

        this.setState({
          avatarSource: source
        });
      }
    }
  });
  ```
  Then later, if you want to display this image in your render() method:
  ```javascript
  <Image source={this.state.avatarSource} style={styles.uploadAvatar} />
  ```

### Directly Launching the Camera or Image Library

  To Launch the Camera or Image Library directly (skipping the alert dialog) you can
  do the following:
  ```javascript
  // Launch Camera:
  UIImagePickerManager.launchCamera(options, (didCancel, response)  => {
    // Same code as in above section!
  });

  // Open Image Library:
  UIImagePickerManager.launchImageLibrary(options, (didCancel, response)  => {
    // Same code as in above section!
  });
  ```

### Options

option | iOS  | Android
------ | ---- | -------
title | OK | OK
cancelButtonTitle | OK | OK
takePhotoButtonTitle | OK | OK
chooseFromLibraryButtonTitle | OK | OK
customButtons | OK | -
maxWidth | OK | OK
maxHeight | OK | OK
quality | OK | OK
allowsEditing | OK | -
noData | OK | OK
storageOptions | OK | -
