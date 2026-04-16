1. enable usb debugging
2. find package by```
```
adb shell cmd package list packages -u 
```
3. find your package /// use grep
4. reinstall by ```adb shell cmd package install-existing <package_name>
   
5. Uninstall 
```
adb shell pm uninstall <package-name>
```
