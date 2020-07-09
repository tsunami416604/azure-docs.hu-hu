---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 11bd05231aa3b5b336400d2434e63e57807a7e69
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147804"
---
### <a name="add-the-google-services-json-file"></a>A Google Services JSON-fájljának hozzáadása

1. **Vezérlő**  +  **Kattintson** az **Android** mappára, majd válassza a **Megnyitás lehetőséget Android Studio**. Ezután váltson a **projekt** nézetre (ha még nem tette meg).

1. Keresse meg a [Firebase-konzolon](https://console.firebase.google.com)a **PushDemo** -projekt beállításakor korábban letöltött fájl *google-services.js* . Ezután húzza az **alkalmazás** -modul gyökérkönyvtárára (**Android**  >  **Android**  >  -**alkalmazás**).

### <a name="configure-build-settings-and-permissions"></a>Build beállításainak és engedélyeinek konfigurálása

1. Váltson a **projekt** nézetre **Androidra**.

1. Nyissa meg **AndroidManifest.xml**, majd a záró címke előtt adja hozzá az **Internet** és **READ_PHONE_STATE** engedélyeket az **alkalmazás** elem után **</manifest>** .

    ```xml
    <manifest>
        <application>...</application>
        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    </manifest>
    ```

### <a name="add-the-firebase-sdks"></a>A Firebase SDK-k hozzáadása

1. A **Android Studio**nyissa meg a projekt szintű **Build. gradle** fájlt (**gradle-parancsfájlok**  >  **Build. gradle (projekt: Android)**). Győződjön meg arról, hogy rendelkezik a "com. Google. GM: Google-Services" osztályútvonal a ``buildscript``  >  **függőségek** csomópontban.

    ```json
    buildscript {

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
      }

      dependencies {
        // ...

        // Add the following line:
        classpath 'com.google.gms:google-services:4.3.3'  // Google Services plugin
      }
    }

    allprojects {
      // ...

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
        // ...
      }
    }
    ```

    > [!NOTE]
    > Az **Android-projekt**létrehozásakor ellenőrizze, hogy a [Firebase-konzolon](https://console.firebase.google.com) megadott utasítások szerint a legújabb verzióra hivatkozik-e.

1. Az App-Level **Build. gradle** fájlban (**gradle-parancsfájlok**  >  **Build. gradle (Module: app)**), alkalmazza a **Google Services gradle beépülő**modulját. Alkalmazza a beépülő modult közvetlenül az **Android** -csomópont fölé.

    ```json
    // ...

    // Add the following line:
    apply plugin: 'com.google.gms.google-services'  // Google Services plugin

    android {
      // ...
    }
    ```

1. Ugyanebben a fájlban a **függőségek** csomópontban adja hozzá a **Cloud Messaging** Android-könyvtár függőségét.

    ```json
    dependencies {
        // ...
        implementation 'com.google.firebase:firebase-messaging:20.2.0'
    }
    ```

    > [!NOTE]
    > A [Cloud Messaging Android-ügyfél dokumentációjában](https://firebase.google.com/docs/cloud-messaging/android/client)ellenőrizze, hogy a legújabb verzióra hivatkozik-e.

1. Mentse a módosításokat, majd kattintson a **szinkronizálás most** gombra (az eszköztár parancssorból), vagy a **Gradle-fájlokkal szinkronizálja a projektet**.

### <a name="handle-push-notifications-for-android"></a>Leküldéses értesítések kezelése Android rendszerhez

1. A **Android Studioban** **Control**  +  **kattintson** a **com. <your_organization>. pushdemo** csomag mappájára (**app**  >  **src**  >  **Main**  >  **Kotlin**), majd válassza a **csomag** lehetőséget az **új** menüből. Adja meg a **szolgáltatásokat** a név mezőben, majd **nyomja le az ENTER**billentyűt.

1. **Vezérlő**  +  Kattintson a **szolgáltatások** mappára, válassza az **új** menü **Kotlin fájl/osztály** **elemét** . Adja meg a **DeviceInstallationService** nevet, **majd nyomja le az ENTER**billentyűt.

1. Implementálja a **DeviceInstallationService** a következő kóddal.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import android.annotation.SuppressLint
    import android.content.Context
    import android.provider.Settings.Secure
    import com.google.android.gms.common.ConnectionResult
    import com.google.android.gms.common.GoogleApiAvailability
    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    @SuppressLint("HardwareIds")
    class DeviceInstallationService {

        companion object {
            const val DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
            const val GET_DEVICE_ID = "getDeviceId"
            const val GET_DEVICE_TOKEN = "getDeviceToken"
            const val GET_DEVICE_PLATFORM = "getDevicePlatform"
        }

        private var context: Context
        private var deviceInstallationChannel : MethodChannel

        val playServicesAvailable
            get() = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context) == ConnectionResult.SUCCESS

        constructor(context: Context, flutterEngine: FlutterEngine) {
            this.context = context
            deviceInstallationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, DEVICE_INSTALLATION_CHANNEL)
            deviceInstallationChannel.setMethodCallHandler { call, result -> handleDeviceInstallationCall(call, result) }
        }

        fun getDeviceId() : String
            = Secure.getString(context.applicationContext.contentResolver, Secure.ANDROID_ID)

        fun getDeviceToken() : String {
            if(!playServicesAvailable) {
                throw Exception(getPlayServicesError())
            }

            // TODO: Revisit once we have created the PushNotificationsFirebaseMessagingService
            val token = "Placeholder_Get_Value_From_FirebaseMessagingService_Implementation"

            if (token.isNullOrBlank()) {
                throw Exception("Unable to resolve token for FCM.")
            }

            return token
        }

        fun getDevicePlatform() : String = "fcm"

        private fun handleDeviceInstallationCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                GET_DEVICE_ID -> {
                    result.success(getDeviceId())
                }
                GET_DEVICE_TOKEN -> {
                    getDeviceToken(result)
                }
                GET_DEVICE_PLATFORM -> {
                    result.success(getDevicePlatform())
                }
                else -> {
                    result.notImplemented()
                }
            }
        }

        private fun getDeviceToken(result: MethodChannel.Result) {
            try {
                val token = getDeviceToken()
                result.success(token)
            }
            catch (e: Exception) {
                result.error("ERROR", e.message, e)
            }
        }

        private fun getPlayServicesError(): String {
            val resultCode = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context)

            if (resultCode != ConnectionResult.SUCCESS) {
                return if (GoogleApiAvailability.getInstance().isUserResolvableError(resultCode)){
                    GoogleApiAvailability.getInstance().getErrorString(resultCode)
                } else {
                    "This device is not supported"
                }
            }

            return "An error occurred preventing the use of push notifications"
        }
    }
    ```

    > [!NOTE]
    > Ez az osztály megvalósítja a platform-specifikus partnert a `com.<your_organization>.pushdemo/deviceinstallation` csatornához. Ez az alkalmazás a **DeviceInstallationService. DART**-ban található, a dobogón belüli részében lett meghatározva. Ebben az esetben a rendszer a közös kódból érkező hívásokat a natív gazdagépre hívja. Ne felejtse el lecserélni **<your_organization>** a saját szervezetére, bárhol is legyenek.
    >
    > Ez az osztály egyedi azonosítót biztosít (a [Secure. AndroidId](https://developer.android.com/reference/android/provider/Settings.Secure#ANDROID_ID)használatával) az értesítési központ regisztrációs adatforgalmának részeként.

1. Adjon hozzá egy másik **Kotlin fájlt/osztályt** a *NotificationRegistrationService*nevű **Services** mappához, majd adja hozzá a következő kódot.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodChannel

    class NotificationRegistrationService {

        companion object {
            const val NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
            const val REFRESH_REGISTRATION = "refreshRegistration"
        }

        private var notificationRegistrationChannel : MethodChannel

        constructor(flutterEngine: FlutterEngine) {
            notificationRegistrationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationRegistrationService.NOTIFICATION_REGISTRATION_CHANNEL)
        }

        fun refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, null)
        }
    }
    ```

    > [!NOTE]
    > Ez az osztály megvalósítja a platform-specifikus partnert a `com.<your_organization>.pushdemo/notificationregistration` csatornához. Ez az alkalmazás a **NotificationRegistrationService. DART**-ban található, a dobogón belüli részében lett meghatározva. Ebben az esetben a rendszer a natív gazdagépről érkező hívásokat a közös kódra kéri. Még egyszer ügyeljen arra, hogy a **<your_organization>** a saját szervezete helyére cserélje le, bárhol is legyen használva.

1. Adjon hozzá egy másik **Kotlin fájlt/osztályt** a *NotificationActionService*nevű **Services** mappához, majd adja hozzá a következő kódot.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    class NotificationActionService {
        companion object {
            const val NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
            const val TRIGGER_ACTION = "triggerAction"
            const val GET_LAUNCH_ACTION = "getLaunchAction"
        }

        private var notificationActionChannel : MethodChannel
        var launchAction : String? = null

        constructor(flutterEngine: FlutterEngine) {
            notificationActionChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationActionService.NOTIFICATION_ACTION_CHANNEL)
            notificationActionChannel.setMethodCallHandler { call, result -> handleNotificationActionCall(call, result) }
        }

        fun triggerAction(action: String) {
            notificationActionChannel.invokeMethod(NotificationActionService.TRIGGER_ACTION, action)
        }

        private fun handleNotificationActionCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                NotificationActionService.GET_LAUNCH_ACTION -> {
                    result.success(launchAction)
                }
                else -> {
                    result.notImplemented()
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Ez az osztály megvalósítja a platform-specifikus partnert a `com.<your_organization>.pushdemo/notificationaction` csatornához. Ez az alkalmazás a **NotificationActionService. DART**-on belüli, a feltört részében lett meghatározva. Ebben az esetben a hívásokat mindkét irányban lehet elvégezni. Ne felejtse el lecserélni **<your_organization>** a saját szervezetére, bárhol is legyenek.

1. Vegyen fel egy új **Kotlin-fájlt/osztályt** a **com. <your_organization>. pushdemo** nevű csomagba *, amelyet*a következő kód használatával implementál.

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.os.Handler
    import android.os.Looper
    import com.google.firebase.messaging.FirebaseMessagingService
    import com.google.firebase.messaging.RemoteMessage
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService

    class PushNotificationsFirebaseMessagingService : FirebaseMessagingService() {

        companion object {
            var token : String? = null
            var notificationRegistrationService : NotificationRegistrationService? = null
            var notificationActionService : NotificationActionService? = null
        }

        override fun onNewToken(token: String) {
            PushNotificationsFirebaseMessagingService.token = token
            notificationRegistrationService?.refreshRegistration()
        }

        override fun onMessageReceived(message: RemoteMessage) {
            message.data.let {
                Handler(Looper.getMainLooper()).post {
                    notificationActionService?.triggerAction(it.getOrDefault("action", null))
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Ez az osztály felelős az értesítések kezelésére, amikor az alkalmazás az előtérben fut. Feltételesen hívja meg a **triggerAction** a **NotificationActionService** , ha a **onMessageReceived**-ben kapott értesítési adattartalomban egy művelet szerepel. Ez a **refreshRegistration** is meghívja a **NotificationRegistrationService** , amikor az **Firebase** -tokent újra létrehozza a **onNewToken** függvény felülbírálásával.
    >
    > Ha még egyszer használja, ügyeljen arra, hogy a saját szervezetének **<your_organization>** cserélje le, bárhol is legyenek.

1. A **AndroidManifest.xml** (**app**  >  **src**  >  **Main**) területen adja hozzá a **PushNotificationsFirebaseMessagingService** az **alkalmazás** elem aljához a `com.google.firebase.MESSAGING_EVENT` leképezési szűrővel.

    ```xml
    <manifest>
        <application>
            <!-- EXISTING MANIFEST CONTENT -->
             <service
                android:name="com.<your_organization>.pushdemo.PushNotificationsFirebaseMessagingService"
                android:exported="false">
                <intent-filter>
                    <action android:name="com.google.firebase.MESSAGING_EVENT" />
                </intent-filter>
            </service>
        </application>
    </manifest>
    ```

1. A **DeviceInstallationService**-ben ellenőrizze, hogy az alábbi importálások szerepelnek-e a fájl elején.

    ```kotlin
    package com.<your_organization>.pushdemo
    import com.<your_organization>.pushdemo.services.PushNotificationsFirebaseMessagingService
    ```

    > [!NOTE]
    > Cserélje le a **<your_organization>t** a saját szervezeti értékére.

1. Frissítse a helyőrző szövegét *Placeholder_Get_Value_From_FirebaseMessagingService_Implementation* , hogy lekérje a jogkivonat értékét a **PushNotificationFirebaseMessagingService**.

    ```kotlin
    fun getDeviceToken() : String {
        if(!playServicesAvailable) {
            throw Exception(getPlayServicesError())
        }

        // Get token from the PushNotificationsFirebaseMessagingService.token field.
        val token = PushNotificationsFirebaseMessagingService.token

        if (token.isNullOrBlank()) {
            throw Exception("Unable to resolve token for FCM.")
        }

        return token
    }
    ```

1. A **MainActivity**-ben ellenőrizze, hogy az alábbi importálások szerepelnek-e a fájl elején.

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.content.Intent
    import android.os.Bundle
    import com.google.android.gms.tasks.OnCompleteListener
    import com.google.firebase.iid.FirebaseInstanceId
    import com.<your_organization>.pushdemo.services.DeviceInstallationService
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService
    import io.flutter.embedding.android.FlutterActivity
    ```

    > [!NOTE]
    > Cserélje le a **<your_organization>t** a saját szervezeti értékére.

1. Adjon hozzá egy változót, amely a **DeviceInstallationService**mutató hivatkozást tárolja.

    ```kotlin
    private lateinit var deviceInstallationService: DeviceInstallationService
    ```

1. Vegyen fel egy **processNotificationActions** nevű függvényt, és győződjön meg arról, hogy a **szándéknak** van-e extra értékű nevű **művelete**. A művelet feltételes elindítása vagy tárolása későbbi használatra, ha a művelet feldolgozás alatt áll az alkalmazás indításakor.

    ```kotlin
     private fun processNotificationActions(intent: Intent, launchAction: Boolean = false) {
        if (intent.hasExtra("action")) {
            var action = intent.getStringExtra("action");

            if (action.isNotEmpty()) {
                if (launchAction) {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.launchAction = action
                }
                else {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.triggerAction(action)
                }
            }
        }
    }
    ```

1. Bírálja felül a **onNewIntent** függvényt a **processNotificationActions**meghívásához.

    ```kotlin
    override fun onNewIntent(intent: Intent) {
        super.onNewIntent(intent)
        processNotificationActions(intent)
    }
    ```

    > [!NOTE]
    > Mivel a **MainActivity** **LaunchMode** a **SingleTop**értékre van állítva, **a rendszer a** meglévő **tevékenység** példányát a **onNewIntent** függvényen keresztül küldi el a **onCreate** függvény helyett, ezért a **onCreate** és a **onNewIntent** függvényben is be kell állítania a bejövő **leképezést** .

1. Bírálja felül a **onCreate** függvényt, állítsa a **deviceInstallationService** a **deviceInstallationService**új példányára.

    ```kotlin
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        flutterEngine?.let {
            deviceInstallationService = DeviceInstallationService(context, it)
        }
    }
    ```

1. Állítsa be a **notificationActionService** és a **notificationRegistrationService** tulajdonságot a **PushNotificationFirebaseMessagingServices**.

    ```kotlin
    flutterEngine?.let {
      deviceInstallationService = DeviceInstallationService(context, it)
      PushNotificationsFirebaseMessagingService.notificationActionService = NotificationActionService(it)
      PushNotificationsFirebaseMessagingService.notificationRegistrationService = NotificationRegistrationService(it)
    }
    ```

1. Ugyanebben a függvényben feltételesen hívja meg a **FirebaseInstanceId. getInstance (). instanceId**. A **OnCompleteListener** meghívása előtt állítsa be a **PushNotificationFirebaseMessagingService** létrejövő **jogkivonat** értékét a **refreshRegistration**.

    ```kotlin
    if(deviceInstallationService?.playServicesAvailable) {
        FirebaseInstanceId.getInstance().instanceId
            .addOnCompleteListener(OnCompleteListener { task ->
                if (!task.isSuccessful)
                    return@OnCompleteListener

                PushNotificationsFirebaseMessagingService.token = task.result?.token
                PushNotificationsFirebaseMessagingService.notificationRegistrationService?.refreshRegistration()
            })
    }
    ```

1. Továbbra is a **onCreate**, hívja meg a **processNotificationActions** a függvény végén. Az *igaz* érték használata a *launchAction* argumentumhoz, amely jelzi, hogy ez a művelet feldolgozás alatt áll az alkalmazás indításakor.

    ```kotlin
    processNotificationActions(this.intent, true)
    ```

> [!NOTE]
> Minden egyes futtatásakor újra regisztrálnia kell az alkalmazást, és le kell állítania egy hibakeresési munkamenetből, hogy továbbra is megkapja a leküldéses értesítéseket.
