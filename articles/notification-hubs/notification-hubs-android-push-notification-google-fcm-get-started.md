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
ms.date: 04/30/2019
ms.author: jowargo
ms.openlocfilehash: c21b1e38077575fc49221150a61693a23aa408a3
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509107"
---
# <a name="tutorial-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-firebase-cloud-messaging"></a>Oktatóanyag: Az Azure Notification Hubs és a Google Firebase Cloud Messaging leküldéses értesítések Android-eszközök

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Az oktatóanyag bemutatja, hogyan használható az Azure Notification Hubs és a Firebase Cloud Messaging (FCM) leküldéses értesítések Android-alkalmazásokba küldéséhez. Ebben az oktatóanyagban létrehoz egy üres Android-alkalmazást, amely leküldéses értesítéseket fogad a Firebase Cloud Messaging (FCM) használatával.

Ez az oktatóanyag teljes kódja letölthető [a Githubról](https://github.com/Azure/azure-notificationhubs-android/tree/master/samples/FCMTutorialApp).

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Android Studio-projekt létrehozása.
> * A Firebase Cloud Messaginget támogató Firebase-projekt létrehozása.
> * Eseményközpont létrehozásához.
> * Az alkalmazás csatlakoztatása a hubhoz.
> * Az alkalmazás tesztelése.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free/). 

A következőkre is szüksége lesz: 

* A legújabb [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797)
* Android 2.3 vagy újabb a Firebase Cloud Messaging esetében
* Google-tárház változat 27-es vagy újabb a Firebase Cloud Messaging esetében
* A Google Play-szolgáltatások 9.0.2-es vagy újabb a Firebase Cloud Messaging esetében

Az oktatóanyag végrehajtása feltétele az Android-alkalmazások az összes további Notification hubs szolgáltatással kapcsolatos.

## <a name="create-an-android-studio-project"></a>Android Studio-projekt létrehozása

1. Indítsa el az Android Studio.
2. Válassza ki **fájl**, mutasson a **új**, majd válassza ki **új projekt**. 
2. Az a **válassza ki a projekt** lapon jelölje be **üres tevékenység**, majd válassza ki **tovább**. 
3. Az a **projekt konfigurálásához** lapon, tegye a következőket: 
    1. Adjon meg egy nevet az alkalmazásnak.
    2. Adja meg a projektfájlok mentésének helyét. 
    3. Válassza a **Finish** (Befejezés) elemet. 

        ![A projekt konfigurálása)](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>Az FCM-et támogató Firebase-projekt létrehozása

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-hub"></a>A központ konfigurálása

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>A központ Firebase Cloud Messaging-beállításainak konfigurálása

1. A bal oldali panelen a **beállításait,** kiválasztása **Google (GCM/FCM)** . 
2. Adja meg a **kiszolgálókulcs** a korábban mentett FCM-projekt. 
3. Válassza az eszköztár **mentése**. 

    ![Az Azure Notification Hub - Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Az Azure Portalon üzenetet jelenít meg a riasztások, hogy a hub frissítése sikeresen befejeződött. A **mentése** gomb le van tiltva. 

A központ dolgozhat a Firebase Cloud Messaging konfigurálva. Akkor is rendelkezik a kapcsolati karakterláncok, amelyek értesítéseket küld egy eszköz, és regisztrálnia kell egy alkalmazást, értesítések fogadásához szükséges.

## <a id="connecting-app"></a>Az alkalmazás csatlakoztatása az értesítési központhoz

### <a name="add-google-play-services-to-the-project"></a>Google Play-szolgáltatások felvétele a projektbe

[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="add-azure-notification-hubs-libraries"></a>Azure Notification Hubs-kódtárak hozzáadása

1. Az alkalmazás a Build.Gradle fájlban adja hozzá a következő sorokat a dependencies szakaszt.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. A dependencies (függőségek) szakasz után vegye fel az alábbi tárhelyet.

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="add-google-firebase-support"></a>Google Firebase-támogatás hozzáadása

1. Az alkalmazás a Build.Gradle fájlban adja hozzá a következő sorokat a **függőségek** szakaszt, ha azok még nem léteznek. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:17.3.4'
    ```

2. Ha még nem szerepel ott, adja hozzá a beépülő modul következő a fájl végén. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```
3. Válassza ki **szinkronizálás most** az eszköztáron.

### <a name="update-the-androidmanifestxml-file"></a>Az AndroidManifest.xml fájl frissítése

1. Miután megkapta az FCM regisztrációs jogkivonat, használja, azt [az Azure Notification Hubs](notification-hubs-push-notification-registration-management.md). A regisztrációt a háttérben használatával támogatja az `IntentService` nevű `RegistrationIntentService`. Ez a szolgáltatás az FCM regisztrációs jogkivonat is frissíti.

    Adja hozzá az alábbi szolgáltatásdefiníciót az AndroidManifest.xml fájlhoz, az `<application>` címkén belül.

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    ```

2. Is van szükség, értesítések fogadásához egy fogadót meghatározásához. Adja hozzá az alábbi fogadódefiníciót az AndroidManifest.xml fájlhoz, az `<application>` címkén belül. 

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
    > Cserélje le a `<your package NAME>` helyőrzőt a tényleges csomagnevet, az AndroidManifest.xml fájlban tetején jelennek meg.
3. Adja meg a következő szükséges FCM-kapcsolódó engedélyeket az alábbi a `</application>` címke.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="add-code"></a>Kód hozzáadása

1. A Projekt nézetben bontsa ki a következőt: **app** > **src** > **main** > **java**. Kattintson a jobb gombbal a területen látható csomagmappára **java**válassza **új**, majd válassza ki **Java-osztály**. Adja meg **NotificationSettings** a nevét, és válassza ki a **OK**.

    Győződjön meg arról, hogy frissítette ezt a három helyőrzőt a `NotificationSettings` osztály alábbi kódjában:

   * **HubListenConnectionString**: A **DefaultListenAccessSignature** kapcsolati karakterláncára. Kattintva másolhatja a kapcsolati karakterlánc **hozzáférési szabályzatok** a hubon, az a [Azure Portal].
   * **hubName**: A központ lapján megjelenő a központ nevét használja a [Azure Portal].

     `NotificationSettings` kód:

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > Adja meg a **neve** és a **DefaultListenSharedAccessSignature** a központ, mielőtt továbblépne. 

3. Adjon hozzá a projekthez egy másik új, `RegistrationIntentService` nevű osztályt. Ez az osztály megvalósítja a `IntentService` felületet. Is kezeli [az FCM-jogkivonat frissítését](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) és [az értesítési központon történő regisztrációt](notification-hubs-push-notification-registration-management.md).

    Ehhez az osztályhoz az alábbi kódokat használja.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;
    import com.google.android.gms.tasks.OnSuccessListener;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.google.firebase.iid.InstanceIdResult;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    import java.util.concurrent.TimeUnit;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";
        String FCM_token = null;

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
                FirebaseInstanceId.getInstance().getInstanceId().addOnSuccessListener(new OnSuccessListener<InstanceIdResult>() { 
                    @Override 
                    public void onSuccess(InstanceIdResult instanceIdResult) { 
                        FCM_token = instanceIdResult.getToken(); 
                        Log.d(TAG, "FCM Registration Token: " + FCM_token); 
                    } 
                }); 
                TimeUnit.SECONDS.sleep(1);

                // Storing the registration ID that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server.
                // Otherwise, your server should have already received the token.
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

                // Check to see if the token has been compromised and needs refreshing.
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
                // If an exception happens while fetching the new token or updating registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

4. Az a `MainActivity` osztályhoz, adja hozzá a következő `import` utasításokat az osztálydeklaráció feletti.

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
    * it doesn't, display a dialog box that enables  users to download the APK from
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

7. Az a `MainActivity` osztályhoz, adja hozzá a következő kódot, amely a Google Play-szolgáltatások meghívása előtt ellenőrzi a `IntentService` beszerezné az FCM regisztrációs jogkivonatát, és regisztrálnia kell az központban:

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

8. Az a `OnCreate` módszere a `MainActivity` osztályhoz, adja hozzá a következő kódot a tevékenység létrehozásakor a regisztrációs folyamat megkezdéséhez:

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

9. Az alkalmazás alkalmazások állapota és a jelentés állapotának ellenőrzéséhez adja hozzá a további metódusokat `MainActivity`:

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

10. A `ToastNotify` metódus a *„Hello World”* `TextView` vezérlőt használja az állapot és az értesítések folyamatos jelentéséhez az alkalmazásban. Az a **res** > **elrendezés** > **activity_main.xml** elrendezés, adja hozzá az alábbi Azonosítót ehhez a vezérlőhöz.

    ```java
    android:id="@+id/text_hello"
    ```

11. Ezután hozzáadhat alosztályát az AndroidManifest.xml fájlban meghatározott fogadó számára. Adjon hozzá a projekthez egy másik új, `MyHandler` nevű osztályt.

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

13. Adja hozzá a következő kódot a a `MyHandler` osztály, így a alosztálya `com.microsoft.windowsazure.notifications.NotificationsHandler`.

    Ez a kód felülírja az `OnReceive` metódust, így a kezelő jelentést küld a kapott értesítésekről. A kezelő az Android-értesítéskezelőnek is elküldi a leküldéses értesítést a `sendNotification()` metódus használatával. Hívja a `sendNotification()` metódust, amikor az alkalmazás nem fut, és értesítés érkezik.

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

14. Az Android Studióban menüsávban válassza **hozhat létre** > **Rebuild Project** , győződjön meg arról, hogy nincsenek-e hibák a kódban. Ha hibaüzenetet kap a kapcsolatos a `ic_launcher` ikonra, távolítsa el az AndroidManifest.xml fájlban a következő utasítást: 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
15. Futtassa az alkalmazást az eszközön, és győződjön meg arról, hogy azt regisztrálása sikeresen megtörtént az agyhoz.

    > [!NOTE]
    > Regisztráció meghiúsulhat, amíg a kezdeti indítás közben a `onTokenRefresh()` módszer a Példányazonosító-szolgáltatás neve. Az értesítési központban történő regisztráció a frissítés után sikeresen megtörténik.

    ![Az eszköz regisztrálása sikeres](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Értesítések küldésének tesztelése az értesítési központból

A leküldéses értesítéseket küldhet a [Azure Portal] a következő lépések végrehajtásával:

1. Válassza ki az Azure Portalon, az értesítési központ oldalon a hub **Tesztküldés** a a **hibaelhárítás** szakaszban.
3. A **Platformok** beállításnál válassza az **Android** lehetőséget.
4. Kattintson a **Küldés** gombra.  Ön még nem jelenik meg értesítés az Android-eszközön még nem futtatta a mobilalkalmazásban, mert. Miután lefuttatta a mobilalkalmazásban, válassza ki a **küldése** gombot az újbóli az értesítési üzenet jelenik meg.
5. Az eredményt a művelet a lista alján.

    ![Azure Notification Hubs – küldés tesztelése](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. Az értesítési üzenetet lát az eszközén. 

    ![Értesítési üzenet az eszközön](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>Futtassa a mobilalkalmazás-emulátor
Leküldéses értesítések emulátorban tesztelése, előtt győződjön meg arról, hogy az emulátor rendszerképe támogatja-e az alkalmazáshoz kiválasztott Google API-szintet. Ha a rendszerkép nem támogatja a natív Google API-k, kaphat a **szolgáltatás\_nem\_elérhető** kivétel.

Emellett győződjön meg arról, hogy hozzáadott a Google-fiókját a futó emulátorhoz alatt **beállítások** > **fiókok**. Ellenkező esetben az FCM-regisztrációs kísérletek azt eredményezheti, hogy a **hitelesítési\_sikertelen** kivétel.

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban használt Firebase Cloud Messaging értesítéseket küldeni a szolgáltatásban regisztrált minden Android eszközre. Ha szeretné megtudni, hogy hogyan küldhet leküldéses értesítéseket adott eszközökre, lépjen tovább a következő oktatóanyagra:

> [!div class="nextstepaction"]
>[Oktatóanyag: Leküldéses értesítések küldése adott Android-eszközök](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Azure Portal]: https://portal.azure.com
