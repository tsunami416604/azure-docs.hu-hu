---
title: Leküldéses értesítések küldése Androidra az Azure Értesítési központok és a Firebase használatával | Microsoft dokumentumok
description: Ebből az oktatóanyagból elsajátíthatja, hogyan küldhet leküldéses értesítéseket Android-eszközökre az Azure Notification Hubs és a Google Firebase Cloud Messaging használatával.
services: notification-hubs
documentationcenter: android
keywords: leküldéses értesítések,leküldéses értesítés,android leküldéses értesítés,fcm,firebase cloud messaging
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/11/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 09/11/2019
ms.openlocfilehash: e6bc4ed94c8b8e62740f81497231a163283ebcb7
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521550"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-firebase"></a>Oktatóanyag: Leküldéses értesítések küldése Android-eszközökre a Firebase használatával

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Notification Hubs és a Firebase Cloud Messaging (FCM) segítségével értesítéseket egy Android-alkalmazásba. Ebben az oktatóanyagban létrehoz egy üres Android-alkalmazást, amely leküldéses értesítéseket fogad a Firebase Cloud Messaging (FCM) használatával.

Az oktatóanyag elkészült kódja letölthető [a GitHubról.](https://github.com/Azure/azure-notificationhubs-android/tree/master/FCMTutorialApp)

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Android Studio-projekt létrehozása.
> * A Firebase Cloud Messaginget támogató Firebase-projekt létrehozása.
> * Hozzon létre egy központot.
> * Csatlakoztassa az alkalmazást a hubhoz.
> * Az alkalmazás tesztelése.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free/). 

A következő elemekre is szüksége van: 

* A legújabb verzió az [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797)
* Android 2.3 vagy újabb a Firebase Cloud Messaging hez
* A Google Repository 27-es vagy újabb felülvizsgálata a Firebase Cloud Messaging esetében
* 9.0.2-es vagy újabb Google Play-szolgáltatások a Firebase Cloud Messaging hez

Az oktatóanyag befejezése előfeltétele az összes többi Értesítési központ oktatóanyagának az Android-alkalmazásokhoz.

## <a name="create-an-android-studio-project"></a>Android Studio-projekt létrehozása

1. Indítsa el az Android Studio alkalmazást.
2. Válassza **a Fájl**, majd az **Új**pontra, majd az Új **projekt**parancsra. 
2. A **Projekt kiválasztása** lapon válassza a **Tevékenység kiürítése (Ürítés)** lehetőséget, majd a **Tovább**gombot. 
3. A **Projekt konfigurálása** lapon tegye a következő lépéseket: 
    1. Adjon nevet az alkalmazásnak.
    2. Adja meg a projektfájlok mentési helyét. 
    3. Válassza a **Finish** (Befejezés) elemet. 

        ![A projekt konfigurálása)](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>Az FCM-et támogató Firebase-projekt létrehozása

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-hub"></a>Hub konfigurálása

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>A központ Firebase Cloud Messaging-beállításainak konfigurálása

1. A bal oldali ablaktáblában a **Beállítások** csoportban válassza a **Google (GCM/FCM) lehetőséget.** 
2. Adja meg a korábban mentett FCM-projekt **kiszolgálókulcsát.** 
3. Az eszköztáron válassza a **Mentés gombot.** 

    ![Azure értesítési központ – Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Az Azure Portal on egy üzenetet jelenít meg a riasztásokat, hogy a hub sikeresen frissítve lett. A **Mentés** gomb le van tiltva. 

A hub most úgy van beállítva, hogy működjön együtt a Firebase Cloud Messaging szolgáltatással. Olyan kapcsolati karakterláncokkal is rendelkezik, amelyek az értesítések eszközre küldéséhez és az értesítések fogadásához szükséges alkalmazás regisztrálásához szükségesek.

## <a name="connect-your-app-to-the-notification-hub"></a><a id="connecting-app"></a>Az alkalmazás csatlakoztatása az értesítési központhoz

### <a name="add-google-play-services-to-the-project"></a>Google Play-szolgáltatások felvétele a projektbe

1. Az Android Studio alkalmazásban válassza a menü **Eszközök parancsát,** majd az **SDK-kezelő t.** 
2. Válassza ki a projektben használt Android SDK célverzióját. Ezután válassza **a Csomag részleteinek megjelenítése**lehetőséget. 

    ![Android SDK Manager - célverzió kiválasztása](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Válassza a **Google API-k**lehetőséget, ha még nincs telepítve.

    ![Android SDK Manager - Google API-k kiválasztva](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Váltson az **SDK-eszközök** lapra. Ha még nem telepítette a Google Play-szolgáltatásokat, válassza a **Google Play-szolgáltatásokat** az alábbi képen látható módon. Ezután válassza az **Alkalmaz** a telepítéshez lehetőséget. Jegyezze fel az SDK elérési útját, mert szükség lesz rá egy későbbi lépésben.

    ![Android SDK Manager - Google Play Szolgáltatások kiválasztva](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Ha megjelenik a **Módosítás megerősítése** párbeszédpanel, válassza az **OK gombot.** A Komponenstelepítő telepíti a kért összetevőket. Az összetevők telepítése után válassza a **Befejezés** lehetőséget.
4. Az **ÚJ projektek beállításai** párbeszédpanel bezárásához válassza az **OK** gombot.  
1. Nyissa meg az AndroidManifest.xml fájlt, és *application* adja hozzá a következő címkét az alkalmazáscímkéhez.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```


### <a name="add-azure-notification-hubs-libraries"></a>Azure értesítési központok könyvtárainak hozzáadása

1. Az alkalmazás Build.Gradle fájljában adja hozzá a következő sorokat a függőségek szakaszban.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
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

1. Az alkalmazás Build.Gradle fájljában adja hozzá a következő sorokat a **függőségek** szakaszban, ha azok még nem léteznek. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:17.3.4'
    ```

2. Ha még nincs meg, adja hozzá a következő beépülő modult a fájl végén. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```
3. Az eszköztáron válassza a **Szinkronizálás most** lehetőséget.

### <a name="update-the-androidmanifestxml-file"></a>Az AndroidManifest.xml fájl frissítése

1. Miután megkapta az FCM regisztrációs jogkivonatát, az [Azure Értesítési központokkal való regisztrációhoz használhatja.](notification-hubs-push-notification-registration-management.md) Ezt a regisztrációt a háttérben egy `IntentService` elnevezett használatával `RegistrationIntentService`támogatja. Ez a szolgáltatás is frissíti az FCM regisztrációs jogkivonatot. Az értesítések fogadására `FirebaseService` `FirebaseMessagingService` és kezelésére vonatkozó `onMessageReceived` metódust alosztályként elnevezett osztályt is létrehozhat. 

    Adja hozzá az alábbi szolgáltatásdefiníciót az AndroidManifest.xml fájlhoz, az `<application>` címkén belül.

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    <service
        android:name=".FirebaseService"
        android:exported="false">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT" />
        </intent-filter>
    </service>
    ```
3. Adja hozzá a következő szükséges FCM-hez kapcsolódó engedélyeket a `</application>` címke alá.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="add-code"></a>Kód hozzáadása

1. A Project View nézetben bontsa ki az **alkalmazás** > **src** > **main** > **java**. Kattintson a jobb gombbal a csomagmappára **a java**alatt, válassza az **Új**parancsot, majd válassza **a Java-osztály parancsot.** Írja be a név **értesítési beállításait,** majd kattintson az **OK gombra.**

    Győződjön meg arról, hogy frissítette ezt a három helyőrzőt a `NotificationSettings` osztály alábbi kódjában:

   * **HubListenConnectionString**: A központ **DefaultListenAccessSignature** kapcsolati sztringje. A kapcsolati karakterlánc másolásához kattintson az **Access Policies** elemre a hubon az [Azure Portalon.]
   * **HubName**: Használja a hub nevét, amely megjelenik az [Azure Portal]központi lapján.

     `NotificationSettings` kód:

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > Mielőtt továbblépne, írja be a hub **nevét** és **DefaultListenSharedAccessSignature** aláírását. 

2. Adjon hozzá a projekthez egy másik új, `RegistrationIntentService` nevű osztályt. Ez az osztály `IntentService` valósítja meg a felületet. Emellett kezeli [az FCM-jogkivonat frissítését](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) és [az értesítési központtal való regisztrációt.](notification-hubs-push-notification-registration-management.md)

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

3. Az `MainActivity` osztályban adja `import` hozzá a következő állításokat az osztálydeklaráció fölé.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    ```

4. Add hozzá a következő tagokat az osztály elejére. Ezeket a mezőket a [Google Play-szolgáltatások rendelkezésre állásának ellenőrzésére használja a Google által javasolt módon](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private static final String TAG = "MainActivity";
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

5. Az `MainActivity` osztályban add hozzá a következő módszert a Google Play-szolgáltatások elérhetőségének ellenőrzéséhez.

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

6. Az `MainActivity` osztályban adja hozzá a következő kódot, amely `IntentService` ellenőrzi a Google Play-szolgáltatásokat, mielőtt felhívna az FCM regisztrációs token beírásához és regisztráljon a hubon:

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

7. Az `OnCreate` `MainActivity` osztály metódusában adja hozzá a következő kódot a regisztrációs folyamat elindításához a tevékenység létrehozásakor:

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        registerWithNotificationHubs();
        FirebaseService.createChannelAndHandleNotifications(getApplicationContext());
    }
    ```

8. Az alkalmazás állapotának és jelentésállapotának ellenőrzéséhez `MainActivity`adja hozzá az alábbi módszereket a következő konterre:

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

9. A `ToastNotify` metódus a *„Hello World”* `TextView` vezérlőt használja az állapot és az értesítések folyamatos jelentéséhez az alkalmazásban. A **res** > **layout** > **activity_main.xml** elrendezésben adja hozzá a következő azonosítót a vezérlőhez.

    ```java
    android:id="@+id/text_hello"
    ```

    ![Azure Notification Hubs – küldés tesztelése](./media/notification-hubs-android-push-notification-google-fcm-get-started/activity-main-xml.png)

10. Ezután hozzáad egy alosztályt az AndroidManifest.xml fájlban definiált vevőhöz. Adjon hozzá a projekthez egy másik új, `FirebaseService` nevű osztályt.

11. Vegye fel a következő importálási utasításokat a `FirebaseService.java` felső részén:

    ```java
    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;
    import android.util.Log;
    import android.app.NotificationChannel;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.media.RingtoneManager;
    import android.net.Uri;
    import android.os.Build;
    import android.os.Bundle;
    import androidx.core.app.NotificationCompat;
    ```

12. Adja hozzá az `FirebaseService` osztály következő kódját, `FirebaseMessagingService`így az alosztálya.

    Ez a kód `onMessageReceived` felülbírálja a metódust, és jelenti a kapott értesítéseket. a leküldéses értesítést is elküldi az `sendNotification()` Android értesítéskezelőnek a módszer használatával. Hívja `sendNotification()` meg a metódust, ha az alkalmazás nem fut, és értesítést kap.

    ```java
    public class FirebaseService extends FirebaseMessagingService
    {
        private String TAG = "FirebaseService";
    
        public static final String NOTIFICATION_CHANNEL_ID = "nh-demo-channel-id";
        public static final String NOTIFICATION_CHANNEL_NAME = "Notification Hubs Demo Channel";
        public static final String NOTIFICATION_CHANNEL_DESCRIPTION = "Notification Hubs Demo Channel";
    
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        static Context ctx;
    
        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            // ...
    
            // TODO(developer): Handle FCM messages here.
            // Not getting messages here? See why this may be: https://goo.gl/39bRNJ
            Log.d(TAG, "From: " + remoteMessage.getFrom());
    
            String nhMessage;
            // Check if message contains a notification payload.
            if (remoteMessage.getNotification() != null) {
                Log.d(TAG, "Message Notification Body: " + remoteMessage.getNotification().getBody());
    
                nhMessage = remoteMessage.getNotification().getBody();
            }
            else {
                nhMessage = remoteMessage.getData().values().iterator().next();
            }
    
            // Also if you intend on generating your own notifications as a result of a received FCM
            // message, here is where that should be initiated. See sendNotification method below.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
            sendNotification(nhMessage);
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
            ctx = context;
    
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                NotificationChannel channel = new NotificationChannel(
                        NOTIFICATION_CHANNEL_ID,
                        NOTIFICATION_CHANNEL_NAME,
                        NotificationManager.IMPORTANCE_HIGH);
                channel.setDescription(NOTIFICATION_CHANNEL_DESCRIPTION);
                channel.setShowBadge(true);
    
                NotificationManager notificationManager = context.getSystemService(NotificationManager.class);
                notificationManager.createNotificationChannel(channel);
             }
        }
    }
    ```

13. Az Android Studio menüsorán válassza a**Project újraépítése** **lehetőséget,** > hogy megbizonyosodjon arról, hogy nincsenek hibák a kódban. Ha hibaüzenetjelenik meg `ic_launcher` az ikonnal kapcsolatban, távolítsa el a következő utasítást az AndroidManifest.xml fájlból: 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
14. Győződjön meg arról, hogy rendelkezik az alkalmazás futtatásához. Ha nem rendelkezik ilyen, adjunk hozzá egyet az alábbiak szerint:
    1. ![Eszközkezelő megnyitása](./media/notification-hubs-android-push-notification-google-fcm-get-started/open-device-manager.png)
    2. ![Virtuális eszköz létrehozása](./media/notification-hubs-android-push-notification-google-fcm-get-started/your-virtual-devices.PNG)

15. Futtassa az alkalmazást a kiválasztott eszközön, és ellenőrizze, hogy sikeresen regisztrál-e a hubbal.

    > [!NOTE]
    > A regisztráció a kezdeti indítás `onTokenRefresh()` során sikertelen lehet, amíg meg nem hívják a példányazonosító-szolgáltatás metódusát. Az értesítési központban történő regisztráció a frissítés után sikeresen megtörténik.

    ![Az eszköz regisztrálása sikeres](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Értesítések küldésének tesztelése az értesítési központból

Leküldéses értesítéseket küldhet az [Azure Portalról] az alábbi lépések végrehajtásával:

1. Az Azure Portalon, a hub értesítési központlapján válassza a **Tesztelés küldése** lehetőséget a **Hibaelhárítás** szakaszban.
3. A **Platformok** beállításnál válassza az **Android** lehetőséget.
4. Válassza a **Küldés**lehetőséget.  Az Android-eszközön még nem jelenik meg értesítés, mert még nem futtatta rajta a mobilalkalmazást. A mobilalkalmazás futtatása után ismét a **Küldés** gombra kattintva megtekintheti az értesítési üzenetet.
5. A művelet eredményeit a lap alján található listában láthatja.

    ![Azure Notification Hubs – küldés tesztelése](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. Az értesítési üzenet megjelenik az eszközén. 

    ![Értesítési üzenet az eszközön](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>A mobilalkalmazás futtatása emulátorral
Mielőtt tesztelne leküldéses értesítéseket egy emulátoron belül, győződjön meg arról, hogy az emulátor képe támogatja az alkalmazáshoz kiválasztott Google API-szintet. Ha a rendszerkép nem támogatja a natív Google API-kat, előfordulhat, hogy a **SERVICE\_NOT\_AVAILABLE** kivételt kapja.

Győződjön meg arról is, hogy hozzáadta Google-fiókját a futó emulátorhoz a **Beállítások** > **fiókok területen.** Ellenkező esetben az FCM-mel való regisztrációra tett kísérletek a **HITELESÍTÉS\_SIKERTELEN kivételt** eredményezhetik.

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban a Firebase Cloud Messaging segítségével közvetített értesítéseket a szolgáltatásban regisztrált összes Android-eszközre. Ha szeretné megtudni, hogy hogyan küldhet leküldéses értesítéseket adott eszközökre, lépjen tovább a következő oktatóanyagra:

> [!div class="nextstepaction"]
>[Oktatóanyag: Értesítések leküldése adott Android-eszközökre](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Azure-portál]: https://portal.azure.com
