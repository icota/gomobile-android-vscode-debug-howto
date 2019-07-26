# How to debug Go mobile apps on Android with Visual Studio Code

- Stop gradle from stripping your libgojni.so files of debug symbols by adding `packagingOptions.doNotStrip "**/*.so"` to `app/build.gradle` 

- Push the gdbserver binary from Android NDK's `prebuild` directory to the device:
```
~/Android/Sdk/ndk-bundle/prebuilt/android-x86_64/gdbserver$ ~/Android/Sdk/platform-tools/adb push gdbserver /data/local/tmp
~/Android/Sdk/platform-tools/adb shell "chmod 777 /data/local/tmp/gdbserver"
```

- Restart adbd as root:
```
~/Android/Sdk/platform-tools/adb root
```

- Forward a port that you will use for gdbserver:
```
~/Android/Sdk/platform-tools/adb forward tcp:1337 tcp:1337
```

- Attach gdbserver to the process you wish to debug:
```
~/Android/Sdk/platform-tools/adb shell
generic_x86_64:/ # su
generic_x86_64:/ # set enforce 0
generic_x86_64:/ # /data/local/tmp/gdbserver :1337 --attach $(ps -A | grep your.android.application.id | awk '{print $2}')
```

- Install Native Debug by running `ext install webfreak.debug` in Visual Studio Code and add a configuration such as:
```
{
    "type": "gdb",
    "request": "attach",
    "name": "Attach to gdbserver",
    "executable": "./bin/executable",
    "target": ":1337",
    "remote": true,
    "cwd": "${workspaceRoot}",
    "valuesFormatting": "parseText"
}
```

- Enjoy!
