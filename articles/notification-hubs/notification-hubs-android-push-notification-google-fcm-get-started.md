---
title: Leküldéses értesítések küldése Android-alkalmazásokba az Azure Notification Hubs és a Firebase Cloud Messaging használatával | Microsoft Docs
description: Ebből az oktatóanyagból elsajátíthatja, hogyan küldhet leküldéses értesítéseket Android-eszközökre az Azure Notification Hubs és a Google Firebase Cloud Messaging használatával.
services: notification-hubs
documentationcenter: android
keywords: leküldéses értesítések,leküldéses értesítés,android leküldéses értesítés,fcm,firebase cloud messaging
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/05/2019
ms.author: jowargo
ms.openlocfilehash: d2de4a4be8838cf696d2d3ed6589e8f154a6ca05
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55959834"
---
# <a name="tutorial-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-firebase-cloud-messaging"></a>Oktatóanyag: Az Azure Notification Hubs és a Google Firebase Cloud Messaging leküldéses értesítések Android-eszközök

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Az oktatóanyag bemutatja, hogyan használható az Azure Notification Hubs és a Firebase Cloud Messaging (FCM) leküldéses értesítések Android-alkalmazásokba küldéséhez. Ebben az oktatóanyagban létrehoz egy üres Android-alkalmazást, amely leküldéses értesítéseket fogad a Firebase Cloud Messaging (FCM) használatával.

Az oktatóanyag teljes kódja [itt](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStartedFirebase) tölthető le a GitHubról.

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Android Studio-projekt létrehozása.
> * A Firebase Cloud Messaginget támogató Firebase-projekt létrehozása.
> * Értesítési központ létrehozása.
> * Az alkalmazás csatlakoztatása az értesítési központhoz.
> * Az alkalmazás tesztelése.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free/).

* A fent említett aktív Azure-fiókon kívül az oktatóanyaghoz az [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797) legújabb verziójára van szükség.
* Android 2.3 vagy újabb a Firebase Cloud Messaging esetében.
* A Google-tárház 27-es vagy újabb verziója a Firebase Cloud Messaging esetében.
* A Google Play-szolgáltatások 9.0.2-es vagy újabb verziója a Firebase Cloud Messaging esetében.
* Ennek az oktatóanyagnak az elvégzése előfeltétel minden további, Android-alkalmazásokkal kapcsolatos Notification Hubs-oktatóanyag elvégzéséhez.

## <a name="create-an-android-studio-project"></a>Android Studio-projekt létrehozása

1. Indítsa el az Android Studio.
2. Válassza ki **fájl** menüben mutasson a **új**, és válassza ki **új projekt**. 
2. Az a **válassza ki a projekt** lapon jelölje be **üres tevékenység**, és válassza ki **tovább**. 
3. Az a **projekt konfigurálásához** lapon, tegye a következőket: 
    1. Adjon meg egy **neve** az alkalmazáshoz.
    2. Adja meg a projektfájlok mentési helyét. 
    3. Válassza a **Finish** (Befejezés) elemet. 

        ![A projekt konfigurálása)](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>Az FCM-et támogató Firebase-projekt létrehozása

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Értesítési központ konfigurálása

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>A központ Firebase Cloud Messaging-beállításainak konfigurálása

1. Válassza ki **Google (GCM/FCM)** alatt **beállítások** a bal oldali menüben. 
2. Illessze be a **kiszolgálókulcs** a korábban mentett FCM-projekt. 
3. Selct **mentése** az eszköztáron. 

    ![Az Azure Notification Hubs – Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Riasztások, hogy a notification hubs sikeresen frissítve az üzenet jelenik meg. A **mentése** gomb le van tiltva. 

Az értesítési központ konfigurálva lett a Firebase Cloud Messaginggel való együttműködésre, és rendelkezik a kapcsolati sztringekkel az alkalmazás regisztrálására értesítések fogadásához és leküldéses értesítések küldéséhez.

## <a id="connecting-app"></a>Az alkalmazás csatlakoztatása az értesítési központhoz

### <a name="add-google-play-services-to-the-project"></a>Google Play-szolgáltatások felvétele a projektbe

[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="adding-azure-notification-hubs-libraries"></a>Azure Notification Hubs-kódtárak felvétele

1. Az **alkalmazás** `Build.Gradle` fájljában adja hozzá az alábbi sorokat a **dependencies** (függőségek) szakaszhoz.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. A **dependencies** (függőségek) szakasz után vegye fel az alábbi tárhelyet.

    ```gradle
    repositories {
        maven {
            url "http://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="add-google-firebase-support"></a>Google Firebase-támogatás hozzáadása

1. Az a `Build.Gradle` fájlt a **alkalmazás**, adja hozzá a következő sorokat a **függőségek** szakaszt, ha még nem létezik. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.0'
    ```

2. Ha még nem létezik, adja hozzá a következő beépülő modul a fájl végén. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```

### <a name="updating-the-androidmanifestxml"></a>Az androidmanifest.xml fájl frissítése

1. Az FCM támogatásának biztosításához létre kell hoznia egy példányazonosító-figyelő szolgáltatást a kódban, amely a [regisztrációs jogkivonatok lekérésére](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register) szolgál a [Google FirebaseInstanceId API-jának](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId) használatával. Ebben az oktatóanyagban az osztály neve `MyInstanceIDService`.

    Adja hozzá az alábbi szolgáltatásdefiníciót az AndroidManifest.xml fájlhoz, az `<application>` címkén belül.

    ```xml
    <service android:name=".MyInstanceIDService">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
        </intent-filter>
    </service>
    ```

2. Miután a FirebaseInstanceId API megküldte a FCM-regisztrációs jogkivonatot, ezt használhatja az [Azure Notification Hub-regisztrációhoz](notification-hubs-push-notification-registration-management.md). A regisztrációt a háttérben egy `RegistrationIntentService` nevű `IntentService` használatával támogatja. Ez a szolgáltatás az FCM regisztrációs jogkivonat frissítését is el fogja végezni.

    Adja hozzá az alábbi szolgáltatásdefiníciót az AndroidManifest.xml fájlhoz, az `<application>` címkén belül.

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    ```

3. Egy fogadót is meg kell határoznia az értesítések fogadásához. Adja hozzá az alábbi fogadódefiníciót az AndroidManifest.xml fájlhoz, az `<application>` címkén belül. 

    ```xml
    <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <category android:name="<your package name>" />
        </intent-filter>
    </receiver>
    ```

    > [!IMPORTANT]
    > Cserélje le a `<your package NAME>` helyőrzőt az `AndroidManifest.xml` fájl felső részén látható tényleges csomagnévre.
4. Válassza ki **szinkronizálás most** az eszköztáron.
5. Adja meg a következő szükséges FCM-kapcsolódó engedélyeket **alább a** `</application>` címke.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="adding-code"></a>Kód felvétele

1. A Projekt nézetben bontsa ki a következőt: **app** > **src** > **main** > **java**. Kattintson a jobb gombbal a **java** területen látható csomagmappára, kattintson a **New** (Új), majd a **Java Class** (Java-osztály) elemre. Adja meg `NotificationSettings` a nevét, és válassza a **OK**.

    Győződjön meg arról, hogy frissítette ezt a három helyőrzőt a `NotificationSettings` osztály alábbi kódjában:

   * **HubListenConnectionString**: A **DefaultListenAccessSignature** kapcsolati karakterláncára. Ez a kapcsolati sztring az [Azure Portal] a központ **Hozzáférési házirendek** elemére kattintva másolható át.
   * **hubName**: A központ lapján megjelenő értesítési központ nevét használja a [Azure Portal].

     `NotificationSettings` kód:

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

    > [!IMPORTANT]
    > Adja meg a **neve** és a **DefaultListenSharedAccessSignature** további az értesítési központ előtt a helyen. 
2. Adjon hozzá egy másik osztály nevű `MyInstanceIDService`. Ez az osztály a Példányazonosító figyelőszolgáltatás megvalósítása.

    Ennek az osztálynak a kódja meghívja az `IntentService` szolgáltatást az [FCM-jogkivonat frissítésére](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) a háttérben.

    ```java
    import android.content.Intent;
    import android.util.Log;
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class MyInstanceIDService extends FirebaseInstanceIdService {

        private static final String TAG = "MyInstanceIDService";

        @Override
        public void onTokenRefresh() {

            Log.d(TAG, "Refreshing FCM Registration Token");

            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    };
    ```

3. Adjon hozzá a projekthez egy másik új, `RegistrationIntentService` nevű osztályt. Ez az osztály megvalósítja az `IntentService` felületet, illetve elvégzi [az FCM-jogkivonat frissítését](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) és [az értesítési központon történő regisztrációt](notification-hubs-push-notification-registration-management.md).

    Ehhez az osztályhoz az alábbi kódokat használja.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.microsoft.windowsazure.messaging.NotificationHub;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";

        private NotificationHub hub;

        public RegistrationIntentService() {
            super(TAG);
        }

        @Override
        protected void onHandleIntent(Intent intent) {

            SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
            String resultString = null;
            String regID = null;
            String storedToken = null;

            try {
                String FCM_token = FirebaseInstanceId.getInstance().getToken();
                Log.d(TAG, "FCM Registration Token: " + FCM_token);

                // Storing the registration ID that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server,
                // otherwise your server should have already received the token.
                if (((regID=sharedPreferences.getString("registrationID", null)) == null)){

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                // Check if the token may have been compromised and needs refreshing.
                else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                else {
                    resultString = "Previously Registered Successfully - RegId : " + regID;
                }
            } catch (Exception e) {
                Log.e(TAG, resultString="Failed to complete registration", e);
                // If an exception happens while fetching the new token or updating our registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

4. A `MainActivity` osztályban adja hozzá az alábbi `import` utasításokat az osztálydeklaráció feletti részhez.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    ```

5. Adja hozzá az alábbi tagokat az osztály tetején. Ezeket a mezőket a [Google Play-szolgáltatások rendelkezésre állásának ellenőrzésére használja a Google által javasolt módon](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private static final String TAG = "MainActivity";
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

6. Az a `MainActivity` osztályhoz, adja hozzá a következő metódust a Google Play-szolgáltatások rendelkezésre állásának ellenőrzésére.

    ```java
    /**
    * Check the device to make sure it has the Google Play Services APK. If
    * it doesn't, display a dialog that allows users to download the APK from
    * the Google Play Store or enable it in the device's system settings.
    */

    private boolean checkPlayServices() {
        GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
        int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.SUCCESS) {
            if (apiAvailability.isUserResolvableError(resultCode)) {
                apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                        .show();
            } else {
                Log.i(TAG, "This device is not supported by Google Play Services.");
                ToastNotify("This device is not supported by Google Play Services.");
                finish();
            }
            return false;
        }
        return true;
    }
    ```

7. A `MainActivity` osztályban vegye fel az alábbi kódot, amellyel a Google Play-szolgáltatások még azelőtt ellenőrizhetők, mielőtt az `IntentService` meghívásával beszerezné az FCM regisztrációs jogkivonatát, illetve regisztrálna az értesítési központban.

    ```java
    public void registerWithNotificationHubs()
    {
        if (checkPlayServices()) {
            // Start IntentService to register this application with FCM.
            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    }
    ```

8. A `MainActivity` osztály `OnCreate` metódusában vegye fel az alábbi kódot, ha el kívánja indítani a regisztrációt a tevékenység létrehozásakor.

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        registerWithNotificationHubs();
        MyHandler.createChannelAndHandleNotifications(getApplicationContext());
    }
    ```

9. Az alkalmazás állapotának ellenőrzéséhez és az alkalmazásban történő állapotjelentéshez adja hozzá ezeket a további metódusokat a `MainActivity` osztályhoz.

    ```java
    @Override
    protected void onStart() {
        super.onStart();
        isVisible = true;
    }

    @Override
    protected void onPause() {
        super.onPause();
        isVisible = false;
    }

    @Override
    protected void onResume() {
        super.onResume();
        isVisible = true;
    }

    @Override
    protected void onStop() {
        super.onStop();
        isVisible = false;
    }

    public void ToastNotify(final String notificationMessage) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                TextView helloText = (TextView) findViewById(R.id.text_hello);
                helloText.setText(notificationMessage);
            }
        });
    }
    ```

10. A `ToastNotify` metódus a *„Hello World”* `TextView` vezérlőt használja az állapot és az értesítések folyamatos jelentéséhez az alkalmazásban. Az a **res** -> **elrendezés** -> **activity_main.xml** elrendezés, adja hozzá az alábbi Azonosítót ehhez a vezérlőhöz.

    ```java
    android:id="@+id/text_hello"
    ```

11. Ezután hozzáadja az AndroidManifest.xml fájlban meghatározott fogadó alosztályát. Adjon hozzá a projekthez egy másik új, `MyHandler` nevű osztályt.

12. Vegye fel a következő importálási utasításokat a `MyHandler.java` felső részén:

    ```java
    import android.app.NotificationChannel;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.media.RingtoneManager;
    import android.net.Uri;
    import android.os.Build;
    import android.os.Bundle;
    import android.support.v4.app.NotificationCompat;
    import com.microsoft.windowsazure.notifications.NotificationsHandler;    
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    ```

13. Vegye fel az alábbi, `MyHandler` osztályhoz tartozó kódot, amely így a `com.microsoft.windowsazure.notifications.NotificationsHandler` alosztálya lesz.

    Ez a kód felülírja az `OnReceive` metódust, így a kezelő jelentést küld a kapott értesítésekről. A kezelő az Android-értesítéskezelőnek is elküldi a leküldéses értesítést a `sendNotification()` metódus használatával. A `sendNotification()` metódust akkor kell végrehajtani, ha az alkalmazás nem fut, és értesítés érkezik.

    ```java
    public class MyHandler extends NotificationsHandler {
        public static final String NOTIFICATION_CHANNEL_ID = "nh-demo-channel-id";
        public static final String NOTIFICATION_CHANNEL_NAME = "Notification Hubs Demo Channel";
        public static final String NOTIFICATION_CHANNEL_DESCRIPTION = "Notification Hubs Demo Channel";
    
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;
    
        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("message");
            sendNotification(nhMessage);
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
        }
    
        private void sendNotification(String msg) {
    
            Intent intent = new Intent(ctx, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    
            mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                    intent, PendingIntent.FLAG_ONE_SHOT);
    
            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            NotificationCompat.Builder notificationBuilder = new NotificationCompat.Builder(
                    ctx,
                    NOTIFICATION_CHANNEL_ID)
                    .setContentText(msg)
                    .setPriority(NotificationCompat.PRIORITY_HIGH)
                    .setSmallIcon(android.R.drawable.ic_popup_reminder)
                    .setBadgeIconType(NotificationCompat.BADGE_ICON_SMALL);
    
            notificationBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
        }
    
        public static void createChannelAndHandleNotifications(Context context) {
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                NotificationChannel channel = new NotificationChannel(
                        NOTIFICATION_CHANNEL_ID,
                        NOTIFICATION_CHANNEL_NAME,
                        NotificationManager.IMPORTANCE_HIGH);
                channel.setDescription(NOTIFICATION_CHANNEL_DESCRIPTION);
                channel.setShowBadge(true);
    
                NotificationManager notificationManager = context.getSystemService(NotificationManager.class);
                notificationManager.createNotificationChannel(channel);
                NotificationsManager.handleNotifications(context, "", MyHandler.class);
            }
        }
    }
    ```

14. Az Android Studio menüsorában kattintson a **Build** > **Rebuild Project** (Projekt újraépítése) elemre annak ellenőrzéséhez, hogy a kód nem tartalmaz-e hibát. Ha hibaüzenetet kap a kapcsolatos a `ic_launcher` ikonra, távolítsa el a következő utasítást az AndroidManifest.xml fájlhoz. 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
15. Futtassa az alkalmazást az eszközön, és ellenőrizze hogy annak az értesítési központban regisztrálása sikeresen megtörtént-e.

    > [!NOTE]
    > Az első indítás alkalmával a regisztráció meghiúsulhat, amíg az alkalmazás meghívja a példányazonosító-szolgáltatás `onTokenRefresh()` metódusát. Az értesítési központban történő regisztráció a frissítés után sikeresen megtörténik.

    ![Az eszköz regisztrálása sikeres](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Értesítések küldésének tesztelése az értesítési központból

Az [Azure Portal] az alábbi műveletek végrehajtásával küldhet leküldéses értesítéseket:

1. Az Azure Portalon az a **értesítési központ** az értesítési központot, válassza a lap **Tesztküldés** a a **hibaelhárítás** szakaszban.
3. A **Platformok** beállításnál válassza az **Android** lehetőséget.
4. Kattintson a **Küldés** gombra.  Még nem látja az értesítést az Android-eszközön, mivel nem futtatta rajta a mobilalkalmazást. A mobilalkalmazás futtatása után kattintson ismét a **Küldés** gombra az értesítési üzenet megtekintéséhez.
5. A művelet **eredményeit** a lap alján található listában láthatja.

    ![Azure Notification Hubs – küldés tesztelése](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. A Notification üzenetet lát az eszközén. 

    ![Értesítési üzenet az eszközön](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>Futtassa a mobilalkalmazás-emulátor
A leküldéses értesítések emulátorban végzett teszteléséhez győződjön meg arról, hogy az emulátor rendszerképe támogatja az alkalmazáshoz kiválasztott Google API-szintet. Ha a rendszerkép nem támogatja a natív Google API-k, kaphat a **szolgáltatás\_nem\_elérhető** kivétel.

Emellett győződjön meg arról is, hogy a **Settings** (Beállítások)  > **Accounts** (Fiókok) területen hozzáadta a Google-fiókját a futó emulátorhoz. Ellenkező esetben a GCM-regisztrációs kísérletek **AUTHENTICATION\_FAILED** (sikertelen hitelesítés) kivételt eredményezhetnek.

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban leküldéses értesítéseket küldött Android-eszközökre a Firebase Cloud Messaging használatával. Ha szeretné megtudni, hogy hogyan küldhet leküldéses értesítéseket a Google Cloud Messaging használatával, lépjen tovább a következő oktatóanyagra:

> [!div class="nextstepaction"]
>[Push notifications to Android devices using Google Cloud Messaging](notification-hubs-android-push-notification-google-gcm-get-started.md) (Leküldéses értesítések küldése Android-eszközökre a Google Cloud Messaging használatával)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Azure Portal]: https://portal.azure.com
