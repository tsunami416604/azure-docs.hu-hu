---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 9330488debb0b7611fdd63656e4c555566c749dd
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060489"
---
### <a name="configure-required-android-packages"></a>A szükséges Android-csomagok konfigurálása

A csomag [automatikusan össze van kapcsolva](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md) az alkalmazás létrehozásakor. A konfigurációs folyamat befejezéséhez néhány további lépést is megtalál.

### <a name="configure-android-manifest"></a>Android-jegyzékfájl konfigurálása

Az "Android/app/src/Main/AndroidManifest.xml" területen ellenőrizze a csomag nevét, engedélyeit és a szükséges szolgáltatásokat. Győződjön meg arról, hogy regisztrálta `RNPushNotificationPublisher` és `RNPushNotificationBootEventReceiver` befogadója, valamint regisztrálta a `RNPushNotificationListenerService` szolgáltatást. Az értesítések metaadatait a leküldéses értesítések megjelenésének testreszabására lehet használni.

  ```xml
  <manifest xmlns:android="http://schemas.android.com/apk/res/android"
      package="YOUR_PACKAGE_NAME">

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>

        <application
          android:name=".MainApplication"
          android:label="@string/app_name"
          android:usesCleartextTraffic="true"
          android:icon="@mipmap/ic_launcher"
          android:roundIcon="@mipmap/ic_launcher_round"
          android:allowBackup="false"
          android:theme="@style/AppTheme">

          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_channel_name"
                      android:value="PushDemo Channel"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_channel_description"
                      android:value="PushDemo Channel Description"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_foreground"
                      android:value="true"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_color"
                      android:resource="@android:color/white"/>

          <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationPublisher" />
          <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationBootEventReceiver">
              <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
              </intent-filter>
          </receiver>

          <service
              android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationListenerService"
              android:exported="false" >
              <intent-filter>
                  <action android:name="com.google.firebase.MESSAGING_EVENT" />
              </intent-filter>
          </service>

          <activity
            android:name=".MainActivity"
            android:label="@string/app_name"
            android:configChanges="keyboard|keyboardHidden|orientation|screenSize|uiMode"
            android:launchMode="singleTask"
            android:windowSoftInputMode="adjustResize">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
          </activity>
          <activity android:name="com.facebook.react.devsupport.DevSettingsActivity" />
        </application>

  </manifest>
  ```

### <a name="configure-google-services"></a>A Google Services konfigurálása

Az "Android/app/Build. gradle"-ben regisztrálja a Google-szolgáltatásokat:

```gradle
dependencies {
  ...
  implementation 'com.google.firebase:firebase-analytics:17.3.0'
  ...
}

apply plugin: 'com.google.gms.google-services'
```

Másolja az FCM telepítésekor letöltött "google-services.json" fájlt az "Android/app/" projekt mappájába.

### <a name="handle-push-notifications-for-android"></a>Leküldéses értesítések kezelése Android rendszerhez

Konfigurálta a meglévő `RNPushNotificationListenerService` szolgáltatást a bejövő Android leküldéses értesítések kezeléséhez. A szolgáltatás korábban már regisztrálva lett az alkalmazás jegyzékfájljában. Feldolgozza a beérkező értesítéseket, és a natív platformra reagáló natív részre küldi a proxykat. Nincs szükség további lépésekre.
