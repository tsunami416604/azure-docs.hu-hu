---
title: "Leküldéses értesítések küldése Androidra az Azure Notification Hubs használatával | Microsoft Docs"
description: "Ebből az oktatóanyagból elsajátíthatja, hogyan használható az Azure Notification Hubs leküldéses értesítések Android-eszközökre történő küldéséhez."
services: notification-hubs
documentationcenter: android
keywords: "leküldéses értesítések,leküldéses értesítés,android leküldéses értesítés"
author: ysxu
manager: erikre
editor: 
ms.assetid: 8268c6ef-af63-433c-b14e-a20b04a0342a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: hero-article
ms.date: 07/05/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 84d3aa963f8538c0721f6c0a02cee5b8e7269770
ms.openlocfilehash: 33433fb4e71ea6a88522c0bd50695d271fee1884


---
# <a name="sending-push-notifications-to-android-with-azure-notification-hubs"></a>Leküldéses értesítések küldése Androidra az Azure Notification Hubs használatával
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Áttekintés
> [!IMPORTANT]
> Ez a témakör a leküldéses értesítések Google Cloud Messaging (GCM) használatával történő küldését mutatja be. Ha a Google Firebase Cloud Messaging (FCM) szolgáltatását használja, tekintse meg a [Sending push notifications to Android with Azure Notification Hubs and FCM](notification-hubs-android-push-notification-google-fcm-get-started.md) (Leküldéses értesítések küldése Androidra az Azure Notification Hubs és a FCM használatával).
> 
> 

Ez az oktatóanyag azt mutatja be, hogy hogyan használható az Azure Notification Hubs leküldéses értesítések küldésére Android-alkalmazásokba.
Létre fog hozni egy üres Android-alkalmazást, amely leküldéses értesítéseket fogad a Google Cloud Messaging (GCM) használatával.

[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Az oktatóanyag teljes kódja [itt](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStarted) tölthető le a GitHubról.

## <a name="prerequisites"></a>Előfeltételek
> [!IMPORTANT]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started).
> 
> 

A fent említett aktív Azure-fiókon kívül csak az [Android Studio](http://go.microsoft.com/fwlink/?LinkId=389797) legújabb verziójára van szükség az oktatóanyaghoz.

Ennek az oktatóanyagnak az elvégzése előfeltétel minden további, Android-alkalmazásokkal kapcsolatos Notification Hubs-oktatóanyag elvégzéséhez.

## <a name="creating-a-project-that-supports-google-cloud-messaging"></a>Google Cloud Messaging szolgáltatást támogató projekt létrehozása
[!INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## <a name="configure-a-new-notification-hub"></a>Új értesítési központ konfigurálása
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6.   A **Beállítások** panelen válassza az **Értesítési szolgáltatások**, majd a **Google (GCM)** lehetőséget. Adja meg az API-kulcsot, majd kattintson a **Mentés** gombra.

&emsp;&emsp;![Azure Notification Hubs – Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Az értesítési központ konfigurálva lett a GCM-mel való együttműködésre, és rendelkezik a kapcsolati karakterláncokkal az alkalmazás regisztrálására értesítések fogadásához és leküldéses értesítések küldéséhez.

## <a name="a-idconnecting-appaconnect-your-app-to-the-notification-hub"></a><a id="connecting-app"></a>Az alkalmazás csatlakoztatása az értesítési központhoz
### <a name="create-a-new-android-project"></a>Új Android-projekt létrehozása
1. Az Android Studióban indítson el egy új Android Studio-projektet.
   
     ![Android Studio – új projekt][13]
2. Válassza ki a **Phone and Tablet** (Telefon és táblagép) helyigényt és a támogatni kívánt **Minimum SDK** csomagot. Ezután kattintson a **Next** (Tovább) gombra.
   
     ![Android Studio – projektlétrehozási munkafolyamat][14]
3. Fő tevékenységként válassza az **Empty Activity** (Üres tevékenység) lehetőséget, és kattintson a **Next** (Tovább), majd a **Finish** (Befejezés) gombokra.

### <a name="add-google-play-services-to-the-project"></a>Google Play-szolgáltatások felvétele a projektbe
[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="adding-azure-notification-hubs-libraries"></a>Azure Notification Hubs-kódtárak felvétele
1. Az **alkalmazás** `Build.Gradle` fájljában adja hozzá az alábbi sorokat a **dependencies** (függőségek) szakaszhoz.
   
        compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
        compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
2. A **dependencies** (függőségek) szakasz után vegye fel az alábbi tárhelyet.
   
        repositories {
            maven {
                url "http://dl.bintray.com/microsoftazuremobile/SDK"
            }
        }

### <a name="updating-the-androidmanifestxml"></a>Az AndroidManifest.xml fájl frissítése.
1. A GCM támogatásának biztosításához létre kell hoznunk egy Példányazonosító-figyelő szolgáltatást a kódban, amely a [regisztrációs jogkivonatok lekérésére](https://developers.google.com/cloud-messaging/android/client#sample-register) szolgál a [Google példányazonosító API-jának](https://developers.google.com/instance-id/) használatával. Ebben az oktatóanyagban az osztály neve `MyInstanceIDService` lesz. 
   
    Adja hozzá az alábbi szolgáltatásdefiníciót az AndroidManifest.xml fájlhoz, az `<application>` címkén belül. Cserélje le a `<your package>` helyőrzőt az `AndroidManifest.xml` fájl felső részén látható tényleges csomagnévre.
   
        <service android:name="<your package>.MyInstanceIDService" android:exported="false">
            <intent-filter>
                <action android:name="com.google.android.gms.iid.InstanceID"/>
            </intent-filter>
        </service>
2. Miután a példányazonosító API megküldte a GCM regisztrációs jogkivonatot, azzal történik majd az [Azure Notification Hub-regisztráció](notification-hubs-push-notification-registration-management.md). A regisztrációt a háttérben egy `RegistrationIntentService` nevű `IntentService` használatával támogatjuk. Ez a szolgáltatás a [GCM regisztrációs jogkivonat frissítését](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) is el fogja végezni.
   
    Adja hozzá az alábbi szolgáltatásdefiníciót az AndroidManifest.xml fájlhoz, az `<application>` címkén belül. Cserélje le a `<your package>` helyőrzőt az `AndroidManifest.xml` fájl felső részén látható tényleges csomagnévre. 
   
        <service
            android:name="<your package>.RegistrationIntentService"
            android:exported="false">
        </service>
3. Az értesítések fogadásához egy fogadót is meghatározunk. Adja hozzá az alábbi fogadódefiníciót az AndroidManifest.xml fájlhoz, az `<application>` címkén belül. Cserélje le a `<your package>` helyőrzőt az `AndroidManifest.xml` fájl felső részén látható tényleges csomagnévre.
   
        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>
4. Az `</application>` címke alatt vegye fel az alábbi GCM-kompatibilis kötelező engedélyeket. Győződjön meg arról, hogy lecserélte a `<your package>` helyőrzőt az `AndroidManifest.xml` fájl felső részén látható csomagnévre.
   
    További információk ezekről az engedélyekről: [GCM-ügyfélalkalmazás beállítása Androidhoz](https://developers.google.com/cloud-messaging/android/client#manifest).
   
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
   
        <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
        <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

### <a name="adding-code"></a>Kód felvétele
1. A Projekt nézetben bontsa ki a következőt: **app** > **src** > **main** > **java**. Kattintson a jobb gombbal a **java** területen látható csomagmappára, kattintson a **New** (Új), majd a **Java Class** (Java-osztály) elemre. Adjon hozzá egy új, `NotificationSettings` nevű osztályt. 
   
    ![Android Studio – új Java-osztály][6]
   
    Győződjön meg arról, hogy frissítette ezt a három helyőrzőt a `NotificationSettings` osztály alábbi kódjában:
   
   * **SenderId**: A [Google Cloud Console-on](http://cloud.google.com/console) korábban beszerzett projektszám.
   * **HubListenConnectionString**: A központ **DefaultListenAccessSignature** kapcsolati karakterlánca. Ez a kapcsolati karakterlánc az [Azure Portal] a központ **Beállítások** paneljének **Hozzáférési házirendek** elemére kattintva másolható át.
   * **HubName**: Az [Azure Portal] a központ paneljén megjelenő értesítési központ nevét használja.
     
     `NotificationSettings` kód:
     
       public class NotificationSettings {
     
           public static String SenderId = "<Your project number>";
           public static String HubName = "<Your HubName>";
           public static String HubListenConnectionString = "<Your default listen connection string>";
       }
2. A fenti lépések használatával vegyen fel egy másik új, `MyInstanceIDService` nevű osztályt. Ez lesz az általunk megvalósított Példányazonosító figyelőszolgáltatás.
   
    Ennek az osztálynak a kódja meghívja az `IntentService` szolgáltatást a [GCM-jogkivonat frissítésére](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) a háttérben.
   
        import android.content.Intent;
        import android.util.Log;
        import com.google.android.gms.iid.InstanceIDListenerService;

        public class MyInstanceIDService extends InstanceIDListenerService {

            private static final String TAG = "MyInstanceIDService";

            @Override
            public void onTokenRefresh() {

                Log.i(TAG, "Refreshing GCM Registration Token");

                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        };


1. Adjon hozzá a projekthez egy másik új, `RegistrationIntentService` nevű osztályt. Ez lesz az `IntentService` azon megvalósítása, amely elvégzi a [GCM-jogkivonat frissítését](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) és [az értesítési központon történő regisztrációt](notification-hubs-push-notification-registration-management.md).
   
    Ehhez az osztályhoz az alábbi kódokat használja.
   
        import android.app.IntentService;
        import android.content.Intent;
        import android.content.SharedPreferences;
        import android.preference.PreferenceManager;
        import android.util.Log;
   
        import com.google.android.gms.gcm.GoogleCloudMessaging;
        import com.google.android.gms.iid.InstanceID;
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
   
                try {
                    InstanceID instanceID = InstanceID.getInstance(this);
                    String token = instanceID.getToken(NotificationSettings.SenderId,
                            GoogleCloudMessaging.INSTANCE_ID_SCOPE);        
                    Log.i(TAG, "Got GCM Registration Token: " + token);
   
                    // Storing the registration id that indicates whether the generated token has been
                    // sent to your server. If it is not stored, send the token to your server,
                    // otherwise your server should have already received the token.
                    if ((regID=sharedPreferences.getString("registrationID", null)) == null) {        
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.i(TAG, "Attempting to register with NH using token : " + token);
   
                        regID = hub.register(token).getRegistrationId();
   
                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1", "tag2").getRegistrationId();
   
                        resultString = "Registered Successfully - RegId : " + regID;
                        Log.i(TAG, resultString);        
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                    } else {
                        resultString = "Previously Registered Successfully - RegId : " + regID;
                    }
                } catch (Exception e) {
                    Log.e(TAG, resultString="Failed to complete token refresh", e);
                    // If an exception happens while fetching the new token or updating our registration data
                    // on a third-party server, this ensures that we'll attempt the update at a later time.
                }
   
                // Notify UI that registration has completed.
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(resultString);
                }
            }
        }
2. A `MainActivity` osztályban adja hozzá az alábbi `import` utasításokat az osztálydeklaráció feletti részhez.
   
        import com.google.android.gms.common.ConnectionResult;
        import com.google.android.gms.common.GoogleApiAvailability;
        import com.google.android.gms.gcm.*;
        import com.microsoft.windowsazure.notifications.NotificationsManager;
        import android.util.Log;
        import android.widget.TextView;
        import android.widget.Toast;
3. Adja hozzá az alábbi privát tagokat az osztály felső részén. Ezeket a [Google Play-szolgáltatások rendelkezésre állásának ellenőrzésére fogjuk használni a Google által javasolt módon.](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk)
   
        public static MainActivity mainActivity;
        public static Boolean isVisible = false;    
        private GoogleCloudMessaging gcm;
        private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
4. A `MainActivity` osztályban adja hozzá az alábbi metódust a Google Play-szolgáltatások rendelkezésre állásához. 
   
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
5. A `MainActivity` osztályban vegye fel az alábbi kódot, amellyel a Google Play-szolgáltatások még azelőtt ellenőrizhetők, mielőtt az `IntentService` meghívásával beszerzi a GCM regisztrációs jogkivonatát, illetve regisztrál az értesítési központban.
   
        public void registerWithNotificationHubs()
        {
            Log.i(TAG, " Registering with Notification Hubs");
   
            if (checkPlayServices()) {
                // Start IntentService to register this application with GCM.
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        }
6. A `MainActivity` osztály `OnCreate` metódusában vegye fel az alábbi kódot, ha el kívánja indítani a regisztrációt a tevékenység létrehozásakor.
   
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
   
            mainActivity = this;
            NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
            registerWithNotificationHubs();
        }
7. Az alkalmazás állapotának ellenőrzéséhez és az alkalmazásban történő állapotjelentéshez adja hozzá ezeket a további metódusokat a `MainActivity` osztályhoz.
   
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
8. A `ToastNotify` metódus a *„Hello World”* `TextView` vezérlőt használja az állapot és az értesítések folyamatos jelentéséhez az alkalmazásban. Az activity_main.xml elrendezésben vegye fel az alábbi azonosítót ehhez a vezérlőhöz.
   
       android:id="@+id/text_hello"
9. Ezután hozzáadjuk az AndroidManifest.xml fájlban meghatározott fogadó alosztályát. Adjon hozzá a projekthez egy másik új, `MyHandler` nevű osztályt.
10. Vegye fel a következő importálási utasításokat a `MyHandler.java` felső részén:
    
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import com.microsoft.windowsazure.notifications.NotificationsHandler;
11. Vegye fel az alábbi, `MyHandler` osztályhoz tartozó kódot, amely így a `com.microsoft.windowsazure.notifications.NotificationsHandler` alosztálya lesz.
    
    Ez a kód felülírja az `OnReceive` metódust, így a kezelő jelentést küld a kapott értesítésekről. A kezelő az Android-értesítéskezelőnek is elküldi a leküldéses értesítést a `sendNotification()` metódus használatával. A `sendNotification()` metódust akkor kell végrehajtani, ha az alkalmazás nem fut, és értesítés érkezik.
    
        public class MyHandler extends NotificationsHandler {
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
                NotificationCompat.Builder mBuilder =
                        new NotificationCompat.Builder(ctx)
                                .setSmallIcon(R.mipmap.ic_launcher)
                                .setContentTitle("Notification Hub Demo")
                                .setStyle(new NotificationCompat.BigTextStyle()
                                        .bigText(msg))
                                .setSound(defaultSoundUri)
                                .setContentText(msg);
    
                mBuilder.setContentIntent(contentIntent);
                mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }
        }
12. Az Android Studio menüsorában kattintson a **Build** > **Rebuild Project** (Projekt újraépítése) elemre annak ellenőrzéséhez, hogy a kód nem tartalmaz-e hibát.

## <a name="sending-push-notifications"></a>Leküldéses értesítések küldése
A leküldéses értesítések fogadásának az alkalmazásban való teszteléséhez értesítéseket küldhet az [Azure Portal] – keresse az alábbiakban látható **Hibaelhárítás** szakaszt a központ paneljén.

![Azure Notification Hubs – küldés tesztelése](./media/notification-hubs-android-get-started/notification-hubs-test-send.png)

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="optional-send-push-notifications-directly-from-the-app"></a>(Nem kötelező) Leküldéses értesítések küldése közvetlenül az alkalmazásból
Az értesítések elküldése általában háttérkiszolgáló használatával történik. Bizonyos esetekben előfordulhat, hogy közvetlenül az ügyfélalkalmazásból kíván leküldéses értesítéseket küldeni. Ez a szakasz az ügyfélből, az [Azure Notification Hub REST API](https://msdn.microsoft.com/library/azure/dn223264.aspx) használatával történő értesítésküldést mutatja be.

1. Az Android Studio projektnézetében bontsa ki a következőt: **App** > **src** > **main** > **res** > **layout**. Nyissa meg az `activity_main.xml` elrendezésfájlt, majd kattintson a **Text** (Szöveg) fülre a fájl szöveges tartalmának frissítéséhez. Frissítse az alábbi kóddal, amely új `Button` és `EditText` vezérlőket vesz fel, amelyekkel a leküldéses értesítési üzenetek elküldhetők az értesítési központba. A kódot az alsó részen, közvetlenül a `</RelativeLayout>` rész elé adja hozzá.
   
        <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true"
        android:onClick="sendNotificationButtonOnClick" />
   
        <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_above="@+id/sendbutton"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="42dp"
        android:hint="@string/notification_message_hint" />
2. Az Android Studio projektnézetében bontsa ki a következőt: **App** > **src** > **main** > **res** > **values**. Nyissa meg a `strings.xml` fájlt, és adja hozzá a `Button` és az `EditText` vezérlő által hivatkozott karakterláncértékeket. A fájl alján, közvetlenül a `</resources>` rész előtt adja hozzá az értékeket.
   
        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>
3. A `NotificationSetting.java` fájlban adja hozzá az alábbi beállítást a `NotificationSettings` osztályhoz.
   
    Frissítse a `HubFullAccess` fájlt a központ **DefaultFullSharedAccessSignature** kapcsolati karakterláncával. Ez kapcsolati karakterlánc az [Azure Portal] az értesítési központ **Beállítások** paneljének **Hozzáférési házirendek** elemére kattintva másolható át.
   
        public static String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";
4. A `MainActivity.java` fájlban adja hozzá az alábbi `import` utasításokat a `MainActivity` osztály felett.
   
        import java.io.BufferedOutputStream;
        import java.io.BufferedReader;
        import java.io.InputStreamReader;
        import java.io.OutputStream;
        import java.net.HttpURLConnection;
        import java.net.URL;
        import java.net.URLEncoder;
        import javax.crypto.Mac;
        import javax.crypto.spec.SecretKeySpec;
        import android.util.Base64;
        import android.view.View;
        import android.widget.EditText;
5. A `MainActivity.java` fájlban adja hozzá az alábbi tagokat az `MainActivity` osztály felső részén.    
   
        private String HubEndpoint = null;
        private String HubSasKeyName = null;
        private String HubSasKeyValue = null;
6. Az értesítési központba történő üzenetküldéshez létre kell hoznia egy SaS-jogkivonatot, amely hitelesíti a POST-kéréseket. Ez a kapcsolati karakterlánc legfontosabb adatainak elemzésével, majd az SaS-jogkivonat létrehozásával történik, ahogyan az az [általánosan használt fogalmakat ismertető](http://msdn.microsoft.com/library/azure/dn495627.aspx) REST API-referenciában szerepel. Az alábbi kód egy megvalósítási példát szemléltet.
   
    A `MainActivity.java` fájlban adja hozzá a `MainActivity` osztályhoz az alábbi metódust a kapcsolati karakterlánc elemzéséhez.
   
        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
         * to parse the connection string so a SaS authentication token can be
         * constructed.
         *
         * @param connectionString This must be the DefaultFullSharedAccess connection
         *                         string for this example.
         */
        private void ParseConnectionString(String connectionString)
        {
            String[] parts = connectionString.split(";");
            if (parts.length != 3)
                throw new RuntimeException("Error parsing connection string: "
                        + connectionString);
   
            for (int i = 0; i < parts.length; i++) {
                if (parts[i].startsWith("Endpoint")) {
                    this.HubEndpoint = "https" + parts[i].substring(11);
                } else if (parts[i].startsWith("SharedAccessKeyName")) {
                    this.HubSasKeyName = parts[i].substring(20);
                } else if (parts[i].startsWith("SharedAccessKey")) {
                    this.HubSasKeyValue = parts[i].substring(16);
                }
            }
        }
7. A `MainActivity.java` fájlban adja hozzá a `MainActivity` osztályhoz az alábbi metódust egy SaS hitelesítési jogkivonat létrehozásához.
   
        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
         * construct a SaS token from the access key to authenticate a request.
         *
         * @param uri The unencoded resource URI string for this operation. The resource
         *            URI is the full URI of the Service Bus resource to which access is
         *            claimed. For example,
         *            "http://<namespace>.servicebus.windows.net/<hubName>"
         */
        private String generateSasToken(String uri) {
   
            String targetUri;
            String token = null;
            try {
                targetUri = URLEncoder
                        .encode(uri.toString().toLowerCase(), "UTF-8")
                        .toLowerCase();
   
                long expiresOnDate = System.currentTimeMillis();
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60 * 1000;
                long expires = expiresOnDate / 1000;
                String toSign = targetUri + "\n" + expires;
   
                // Get an hmac_sha1 key from the raw key bytes
                byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
                SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");
   
                // Get an hmac_sha1 Mac instance and initialize with the signing key
                Mac mac = Mac.getInstance("HmacSHA256");
                mac.init(signingKey);
   
                // Compute the hmac on input data bytes
                byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));
   
                // Using android.util.Base64 for Android Studio instead of
                // Apache commons codec
                String signature = URLEncoder.encode(
                        Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");
   
                // Construct authorization string
                token = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
            } catch (Exception e) {
                if (isVisible) {
                    ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
                }
            }
   
            return token;
        }
8. A `MainActivity.java` fájlban adja hozzá az alábbi metódust a `MainActivity` osztályhoz a **Send Notification** (Értesítés küldése) gomb kezeléséhez, és küldje el a leküldéses értesítési üzenetet a beépített REST API használatával.
   
        /**
         * Send Notification button click handler. This method parses the
         * DefaultFullSharedAccess connection string and generates a SaS token. The
         * token is added to the Authorization header on the POST request to the
         * notification hub. The text in the editTextNotificationMessage control
         * is added as the JSON body for the request to add a GCM message to the hub.
         *
         * @param v
         */
        public void sendNotificationButtonOnClick(View v) {
            EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
            final String json = "{\"data\":{\"message\":\"" + notificationText.getText().toString() + "\"}}";
   
            new Thread()
            {
                public void run()
                {
                    try
                    {
                        // Based on reference documentation...
                        // http://msdn.microsoft.com/library/azure/dn223273.aspx
                        ParseConnectionString(NotificationSettings.HubFullAccess);
                        URL url = new URL(HubEndpoint + NotificationSettings.HubName +
                                "/messages/?api-version=2015-01");
   
                        HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();
   
                        try {
                            // POST request
                            urlConnection.setDoOutput(true);
   
                            // Authenticate the POST request with the SaS token
                            urlConnection.setRequestProperty("Authorization", 
                                generateSasToken(url.toString()));
   
                            // Notification format should be GCM
                            urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");
   
                            // Include any tags
                            // Example below targets 3 specific tags
                            // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                            // urlConnection.setRequestProperty("ServiceBusNotification-Tags", 
                            //        "tag1 || tag2 || tag3");
   
                            // Send notification message
                            urlConnection.setFixedLengthStreamingMode(json.length());
                            OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
                            bodyStream.write(json.getBytes());
                            bodyStream.close();
   
                            // Get reponse
                            urlConnection.connect();
                            int responseCode = urlConnection.getResponseCode();
                            if ((responseCode != 200) && (responseCode != 201)) {
                                BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                                String line;
                                StringBuilder builder = new StringBuilder("Send Notification returned " +
                                        responseCode + " : ")  ;
                                while ((line = br.readLine()) != null) {
                                    builder.append(line);
                                }
   
                                ToastNotify(builder.toString());
                            }
                        } finally {
                            urlConnection.disconnect();
                        }
                    }
                    catch(Exception e)
                    {
                        if (isVisible) {
                            ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
                        }
                    }
                }
            }.start();
        }

## <a name="testing-your-app"></a>Az alkalmazás tesztelése
#### <a name="push-notifications-in-the-emulator"></a>Leküldéses értesítések az emulátorban
A leküldéses értesítések emulátorban végzett teszteléséhez győződjön meg arról, hogy az emulátor rendszerképe támogatja az alkalmazáshoz kiválasztott Google API-szintet. Ha a rendszerkép nem támogatja a natív Google API-kat, a **SERVICE\_NOT\_AVAILABLE** (a szolgáltatás nem érhető el) kivételt adja vissza a rendszer.

A fentieken túl győződjön meg arról is, hogy a **Settings** (Beállítások)  > **Accounts** (Fiókok) területen hozzáadta a Google-fiókját a futó emulátorhoz. Ellenkező esetben a GCM-regisztrációs kísérletek **AUTHENTICATION\_FAILED** (sikertelen hitelesítés) kivételt eredményezhetnek.

#### <a name="running-the-application"></a>Az alkalmazás futtatása
1. Futtassa az alkalmazást, és figyelje meg, hogy sikeres regisztráció esetén jelentést kap a regisztrációs azonosítóról.
   
       ![Testing on Android - Channel registration][18]
2. Adjon meg egy értesítési üzenetet, amelyet a központon regisztrált minden Android-eszközre el kíván küldeni.
   
       ![Testing on Android - sending a message][19]
3. Nyomja le a **Send Notification** (Értesítés küldése) gombot. Az alkalmazást futtató összes eszközön megjelenik egy `AlertDialog`-példány a leküldéses értesítési üzenettel együtt. Az alkalmazással nem rendelkező, de a leküldéses értesítésekre korábban már regisztrált eszközök az Android értesítéskezelőjén keresztül kapják meg az értesítést. Ezek az értesítések a bal felső sarokból lefelé pöccintve tekinthetők meg.
   
       ![Testing on Android - notifications][21]

## <a name="next-steps"></a>Következő lépések
Következő lépésként javasoljuk [A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára] oktatóanyag megtekintését. Ebben bemutatjuk, hogy hogyan küldhet értesítéseket ASP.NET-háttérrendszerből adott felhasználók számára címkék használatával.

Ha a felhasználókat érdeklődési körök alapján szeretné szegmentálni, olvassa el a [Use Notification Hubs to send breaking news] (Friss hírek küldése Notification Hubs használatával) című oktatóanyagot.

A Notification Hubs használatával kapcsolatban a [Notification Hubs használatával] foglalkozó témakörben tekinthet meg további általános információt.

<!-- Images. -->
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png

[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png

[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png


<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Classic Portal]: https://manage.windowsazure.com/
[Notification Hubs használatával]: http://msdn.microsoft.com/library/jj927170.aspx
[A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Azure Portal]: https://portal.azure.com



<!--HONumber=Jan17_HO1-->


