# electron-mas

Check jitsi-meet-electron project

```json
"extendInfo": {
  "NSMicrophoneUsageDescription": "We need access to your microphone so people you talk to in a Videovisit call can hear you.",
  "NSCameraUsageDescription": "Allow your conversational partners to see you in a Videovisit call. You can turn off your video anytime during a call."
},
"mas": {
  "entitlements": "./build/entitlements.mas.plist",
  "entitlementsInherit": "./build/entitlements.mas.inherit.plist"
},
"dmg": {
  "sign": false
}
```

entitlements.mas.plist

````xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>com.apple.security.app-sandbox</key>
    <true/>
    <key>com.apple.security.network.client</key>
    <true/>
    <key>com.apple.security.device.camera</key>
    <true/>
    <key>com.apple.security.device.microphone</key>
    <true/>
  </dict>
</plist>
	```

entitlements.mas.inherit.plist

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>com.apple.security.app-sandbox</key>
    <true/>
    <key>com.apple.security.inherit</key>
    <true/>
  </dict>
</plist>
````
