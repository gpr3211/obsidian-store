```
go run -tags debug .
```
Show Layout lines


## RUN IN BROWSER

Fyne applications can also be run over the web using a standard web browser! A web app created with Fyne will bundle a WebAssembly binary which will run in most modern browsers

To prepare your app to be used over the web we use the “fyne” cli app again, which has a “serve” command for quick testing

```
go install fyne.io/fyne/v2/cmd/fyne@latest
fyne serve
```

You will see, after a few moments, that a web server has been started on port :8080. Just open your web browser to https://localhost:8080 and you can use your app!

### Packaging for web distribution

The `fyne serve` command is great for local testing, but just like other platforms you’ll want to be able to distribute your app as well. To prepare the files for upload just use the `fyne package` command like with regular [Packaging](https://docs.fyne.io/started/packaging).

```
fyne package -os web
```

### Demo

You can see a Fyne app in action to test on any of your devices by visiting [demo.fyne.io](https://demo.fyne.io/).

### Limitations

As of release v2.5.0 the web driver is fully supported but is not yet 100% complete, so your app may not be able to use the following features:

- File open/save dialog
- Storage of Documents
## MOBILE PACKAGING 
Your Fyne app code will work out of the box as mobile apps, just as it did for desktop. However it is a little more complex to package the code for distribution. This page will explore the process to do just that to get your app on iOS and Android.

Firstly you will need some more development tools installed for mobile packaging to complete. For Android builds you must have the Android SDK and NDK installed with appropriate environment set up so that the tools (such as `adb`) can be found on the command line. To build iOS apps you will need Xcode installed on your macOS computer as well as the command line tools optional package.

Once you have a working development environment the packaging is simple. To build an application for Android and iOS the following commands will do everything for you. Be sure to have a unique application identifier as it is unwise to change these after your first release.

You need to export ndk Android SDK and NDK paths
```
 export	ANDROID_HOME="/home/pavlin/Android/Sdk"
 export ANDROID_NDK_HOME="/home/pavlin/Android/Sdk/ndk/27.0.12077973"

```
```
fyne package -os android -appID com.example.myapp -icon mobileIcon.png
fyne package -os ios -appID com.example.myapp -icon mobileIcon.png
```

After these commands have completed (which may take some time on first compilation) you will see two new files in your directory, `myapp.apk` and `myapp.app`. You will see that the latter has the same name as a darwin application bundle - don’t get them confused as they will not work on the other platform.

To install the android app on your phone or a simulator simply call:

```
adb install myapp.apk
```

For iOS to install on device open Xcode and choose the “Devices and Simulators” menu item within the “Window” menu. Then find your phone and drag the `myapp.app` icon onto your app list.

If you want to install on a simulator make sure to package your application using `iossimulator` vs `ios`

```
fyne package -os iossimulator -appID com.example.myapp -icon mobileIcon.png
```

Afterwards you can use the command line tools as follows:

```
xcrun simctl install booted myapp.app
```