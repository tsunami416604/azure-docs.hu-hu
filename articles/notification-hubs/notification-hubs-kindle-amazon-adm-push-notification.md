---
title: Leküldéses értesítések küldése Kindle-alkalmazásokba az Azure Notification Hubs használatával | Microsoft Docs
description: Ebből az oktatóanyagból elsajátíthatja, hogy miként használható az Azure Notification Hubs leküldéses értesítések küldéséhez Kindle-alkalmazások esetén.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 346fc8e5-294b-4e4f-9f27-7a82d9626e93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-kindle
ms.devlang: Java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/29/2019
ms.author: jowargo
ms.openlocfilehash: edd0e12460e07cfd2990cc43a9056ed06b84fb1d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926802"
---
# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Ismerkedés a Notification Hubs szolgáltatással Kindle-alkalmazásokhoz

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Ebből az oktatóanyagból elsajátíthatja, hogy miként használható az Azure Notification Hubs leküldéses értesítések küldéséhez Kindle-alkalmazások esetén. Létre fog hozni egy üres Kindle-alkalmazást, amely leküldéses értesítéseket fogad az Amazon Device Messaging (ADM) használatával.

Ebben az oktatóanyagban kódot fog létrehozni/frissíteni az alábbi feladatok végrehajtásához:

> [!div class="checklist"]
> * Új fájl hozzáadása a fejlesztői portálhoz
> * API-kulcs létrehozása
> * Hozzon létre, és a egy értesítési központ konfigurálása
> * Az alkalmazás beállítása
> * Az ADM üzenetkezelőjének létrehozása
> * Az API-kulcs hozzáadása az alkalmazáshoz
> * Az alkalmazás futtatása
> * Tesztértesítés küldése

## <a name="prerequisites"></a>Előfeltételek

- [Android Studio](https://developer.android.com/studio/?gclid=CjwKCAjwwZrmBRA7EiwA4iMzBPZ9YYmR0pbb5LtjnWhWCxe8PWrmjmeaR6ad5ksCr_j2mmkVj_-o6hoCAqwQAvD_BwE).
- Kövesse a [Setting Up Your Development Environment](https://developer.amazon.com/docs/fire-tablets/ft-set-up-your-development-environment.html) (A fejlesztési környezet beállítása) című cikk lépéseit a Kindle fejlesztési környezetének beállításához.

## <a name="add-a-new-app-to-the-developer-portal"></a>Új fájl hozzáadása a fejlesztői portálhoz

1. Jelentkezzen be a [Amazon fejlesztői portálján](https://developer.amazon.com/apps-and-games/console/apps/list.html).
2. Válassza ki **új alkalmazás hozzáadása**, majd válassza ki **Android**.  

    ![Új alkalmazás gomb hozzáadása](./media/notification-hubs-kindle-get-started/add-new-app-button.png)
1. Az a **új alkalmazás beküldése** lapon hajtsa végre az alábbi lépéseket a **alkalmazáskulcsot**:
    1. Adjon meg egy nevet a **alkalmazáscímet**.
    2. Válassza ki bármelyik **kategória** (például: oktatási)
    4. Adjon meg egy e-mail címet a **támogatási e-mail cím** mező. 
    5. Kattintson a **Mentés** gombra.

        ![Új alkalmazás Alkalmazásbeküldési oldalára](./media/notification-hubs-kindle-get-started/new-app-submission-page.png) 
2.  Váltson a lap tetején a **Mobile Ads** lapra, és kövesse az alábbi lépéseket: 
    1. Adja meg, hogy az alkalmazás irányul elsősorban gyerek 13. A jelen oktatóanyag esetében válassza ki a **nem**.
    2. Válassza ki **elküldése**. 

        ![A Mobile Ads lap](./media/notification-hubs-kindle-get-started/mobile-ads-page.png)
    3. Másolás a **alkalmazáskulcsot** származó a **Mobile Ads** lap. 

        ![Alkalmazáskulcs](./media/notification-hubs-kindle-get-started/application-key.png)
3.  Válassza ki **alkalmazások és szolgáltatások** menü tetején, és válassza ki az alkalmazást a listából. 

    ![Válassza ki az alkalmazást a listából](./media/notification-hubs-kindle-get-started/all-apps-select.png)
4. Váltson a **Device Messaging** lapra, és kövesse az alábbi lépéseket: 
    1. Válassza ki **hozzon létre egy új biztonsági profilt**.
    2. Adjon meg egy **neve** a biztonsági profil. 
    3. Adja meg **leírás** a biztonsági profil. 
    4. Kattintson a **Mentés** gombra. 
    5. Válassza ki **biztonsági profil megtekintése** az eredmény oldalon. 
5. Most, az a **biztonsági profil** lapon, tegye a következőket: 
    1. Váltson a **Webbeállításainak** lapra, és másolja a **ügyfél-azonosító** és **titkos Ügyfélkód** értéket későbbi használatra. 

        ![Ügyfél-azonosító és titkos kulcs lekérése](./media/notification-hubs-kindle-get-started/client-id-secret.png) 
    2. Váltson a **Android/Kindle beállítások** lapon, és ne zárja be a lapot. A következő szakaszban fogjuk adja meg ezeket az értékeket. 

## <a name="create-an-api-key"></a>API-kulcs létrehozása
1. Nyisson meg egy parancssort rendszergazdai jogosultságokkal.
2. Lépjen az Android SDK mappába.
3. Írja be a következő parancsot:

    ```shell
    keytool -list -v -alias androiddebugkey -keystore ./debug.keystore
    ```
4. A kulcstár (**keystore**) jelszavához az **android** szót adja meg.
5. Másolás a **MD5** és **SHA256** ujjlenyomatok. 
6. A fejlesztői portálon, a biztonsági másolatot a **Android/Kindle beállítások** lapon, tegye a következőket: 
    1. Adjon meg egy **az API-kulcs nevét**. 
    2. Adja meg a **a csomag neve** az alkalmazás (például **com.fabrikam.mykindleapp**) és a **MD5** értéket.
        
        >[!IMPORTANT]
        > Amikor létrehoz egy alkalmazást az Android Studióban, a csomag névvel megadott itt kell használni. 
    1. Illessze be a **MD5 aláírás** korábban vágólapra másolt. 
    2. Illessze be a **SHA256 aláírás** korábban vágólapra másolt.  
    3. Válassza ki **új kulcs létrehozásához**.

        ![Beállítások Android/Kindle - kulcs létrehozása](./media/notification-hubs-kindle-get-started/android-kindle-settings.png)
    4. Most válassza ki **megjelenítése** a listában, az API-kulcs megtekintéséhez. 

        ![Android/Kindle-beállítások – API-kulcs megjelenítése](./media/notification-hubs-kindle-get-started/show-api-key-button.png) 
    5. Az a **API-kulcs részleteinek** ablakban másolja ki az API-kulcsot, majd helyre mentse. Ezután válassza ki **X** jobb felső sarokban az ablak bezárásához. 


## <a name="create-and-configure-a-notification-hub"></a>Hozzon létre, és a egy értesítési központ konfigurálása

1. Kövesse lépéseket a [Azure notification hub létrehozása az Azure Portalon](create-notification-hub-portal.md) értesítési központ létrehozása című cikket. 
2. Válassza ki **Amazon (ADM)** alatt **beállítások** menü.
3. Illessze be a **ügyfél-azonosító** és **titkos Ügyfélkód** korábban mentett. 
4. Válassza az eszköztár **Save** (Mentés) elemét. 

    ![Adm – beállítások egy értesítési központ konfigurálása](./media/notification-hubs-kindle-get-started/configure-notification-hub.png)
5. Válassza ki **hozzáférési szabályzatok** elemre a bal oldali menüben, majd válassza a **másolási** gombjára a kapcsolati karakterláncát a **DefaultListenSharedAccessSignature** házirend. Mentse a kulcsot egy tetszőleges helyre. Ez a forráskód későbbi részében fogja használni. 

    ![Értesítési központ - figyelési kapcsolati karakterlánc](./media/notification-hubs-kindle-get-started/event-hub-listen-connection-string.png)    

## <a name="set-up-your-application"></a>Az alkalmazás beállítása

1. Indítsa el az Android Studio. 
2. Válassza ki **fájl**, mutasson a **új**, majd válassza ki **új projekt**. 
3. Az a **válassza ki a projekt** ablakban, a a **telefon és táblagép** lapon jelölje be **üres tevékenység**, és válassza ki **tovább**. 
4. Az a **projekt konfigurálásához** ablakban tegye a következőket:
    1. Adjon meg egy **az alkalmazás neve**. Előfordulhat, hogy szeretné egyezik, az Amazon fejlesztői portálon létrehozott alkalmazás nevét. 
    2. Adjon meg egy **név a csomag**. 
        
        >[!IMPORTANT]
        >A csomag nevének egyeznie kell a csomag nevét, az Amazon fejlesztői portálon megadott.
    3. Tekintse át, és a fennmaradó értékeket szükség szerint frissítheti. 
    4. Válassza a **Finish** (Befejezés) elemet. 

        ![Android-projekt konfigurálása](./media/notification-hubs-kindle-get-started/new-android-studio-project.png)
5. Töltse le [Amazon fejlesztői SDK for Android](https://developer.amazon.com/sdk-download) könyvtár a merevlemezre. Csomagolja ki az SDK zip-fájlját.
6. Az Android Studióban, módosítsa a mappastruktúra **Android** való **projekt** Ha nincs már beállítva **projekt**. 

    ![Android Studio – Váltás a projekt struktúrája](./media/notification-hubs-kindle-get-started/android-studio-project-view.png)
7. Bontsa ki a **alkalmazás** megtekintéséhez a **libs** mappát a fanézetben.     
8. Fájlkezelő ablakban keresse meg a mappát, ahová letöltötte az Amazon Android SDK-k.
9. Nyomja meg **CTRL** , és húzza a **amazon-device-üzenetkezelés – 1.0.1.jar** fájlt a **lib** csomópontra a fanézetben. 

    ![Android Studio - Add Amazon Device Messaging JAR](./media/notification-hubs-kindle-get-started/drag-drop-amazon-device-messaging-jar.png)
9. Az a **másolási** ablakban válassza **OK**. Ha megjelenik a **áthelyezése** helyett ablak **másolási** ablak, zárja be, majd próbálja húzza műveletet **CTRL** gombokra. 

    ![Android Studio - Copy JAR](./media/notification-hubs-kindle-get-started/copy-jar-window.png)
10. Adja hozzá a következő utasítást a **alkalmazás build.gradle** fájlt a **függőségek** szakasz: `implementation files('libs/amazon-device-messaging-1.0.1.jar')`. 

    ![Android Studio – alkalmazás build.gradle ADM hozzáadása](./media/notification-hubs-kindle-get-started/adm-build-gradle.png)
11. Az a `Build.Gradle` fájlt a **alkalmazás**, adja hozzá a következő sorokat a **függőségek** szakaszban: 

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```
12. Adja hozzá a következő tárházat **után** a **függőségek** szakaszban:

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```
13. A szerkesztőben a **build.gradle** fájlt a **alkalmazás**, jelölje be **szinkronizálás** az eszköztáron. 

    ![Android Studio – az alkalmazás szinkronizálási build.gradle](./media/notification-hubs-kindle-get-started/gradle-sync-now.png)
14. Váltson vissza az Android struktúra, a fanézetben.  Adja hozzá a következő Amazon-névteret a gyökérjegyzék-elemhez:

    ```xml
    xmlns:amazon="http://schemas.amazon.com/apk/res/android"
    ```
   
    ![A jegyzékfájlban amazon-névteret](./media/notification-hubs-kindle-get-started/amazon-namespace-manifest.png)
2. A jegyzékelem első elemeként adja hozzá az engedélyeket. Cserélje le **[YOUR PACKAGE NAME]** a csomag, amellyel az alkalmazás létrehozása.

    ```xml
    <permission
        android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
        android:protectionLevel="signature" />

    <uses-permission android:name="android.permission.INTERNET"/>

    <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

    <!-- This permission allows your app access to receive push notifications
    from ADM. -->
    <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

    <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    ```
3. Szúrja be a következő elemet az alkalmazáselem első gyermekeként. Cserélje le **[YOUR PACKAGE NAME]** az alkalmazást létrehozó csomag nevét. A következő lépésben létre fog hozni a MyADMMessageHandler osztály. 

    ```xml
        <amazon:enable-feature
            android:name="com.amazon.device.messaging"
            android:required="true"/>
        <service
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler"
            android:exported="false" />
        <receiver
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler$Receiver"
            android:permission="com.amazon.device.messaging.permission.SEND" >

            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
                <action android:name="com.amazon.device.messaging.intent.RECEIVE" />
                <!-- Replace the name in the category tag with your app's package name. -->
                <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>
    ```

## <a name="create-your-adm-message-handler"></a>Az ADM üzenetkezelőjének létrehozása

1. Adjon hozzá egy új osztályt a `com.fabrikam.mykindleapp` csomagot a projektben, amely örökli `com.amazon.device.messaging.ADMMessageHandlerBase` , és nevezze el `MyADMMessageHandler`, ahogy az alábbi képen látható:

    ![MyADMMessageHandler osztály létrehozása](./media/notification-hubs-kindle-get-started/create-adm-message-handler.png)

2. Adja hozzá a következő `import` utasításokat a `MyADMMessageHandler` osztály:

    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.support.v4.app.NotificationCompat;
    import android.util.Log;
    import com.amazon.device.messaging.ADMMessageReceiver;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    ```

3. Adja hozzá a következő kódot a létrehozott osztályhoz. Ne felejtse el a `[HUB NAME]` és `[LISTEN CONNECTION STRING]` a notification hub és figyelési kapcsolati karakterlánc nevét: 

    ```java
    public static final int NOTIFICATION_ID = 1;
    private NotificationManager mNotificationManager;
    NotificationCompat.Builder builder;
    private static NotificationHub hub;

    public static NotificationHub getNotificationHub(Context context) {
        Log.v("com.wa.hellokindlefire", "getNotificationHub");
        if (hub == null) {
            hub = new NotificationHub("[HUB NAME]", "[HUB NAMESPACE CONNECTION STRING]", context);
        }
        return hub;
    }

    public MyADMMessageHandler() {
        super("MyADMMessageHandler");
    }

    @Override
    protected void onMessage(Intent intent) {
        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);
    }

    @Override
    protected void onRegistrationError(String s) {

    }

    @Override
    protected void onRegistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).register(s);
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
        }
    }

    @Override
    protected void onUnregistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).unregister();
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
        }
    }

    public static class Receiver extends ADMMessageReceiver
    {
        public Receiver()
        {
            super(MyADMMessageHandler.class);
        }
    }

    private void sendNotification(String msg) {
        Context ctx = getApplicationContext();

        mNotificationManager = (NotificationManager)
                ctx.getSystemService(Context.NOTIFICATION_SERVICE);

        PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);


        NotificationCompat.Builder mBuilder =
                new NotificationCompat.Builder(ctx)
                        .setSmallIcon(R.mipmap.ic_launcher)
                        .setContentTitle("Notification Hub Demo")
                        .setStyle(new NotificationCompat.BigTextStyle()
                                .bigText(msg))
                        .setContentText(msg);

        mBuilder.setContentIntent(contentIntent);
        mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
    }
    ```

## <a name="add-your-api-key-to-your-app"></a>Az API-kulcs hozzáadása az alkalmazáshoz
1. Kövesse az alábbi lépéseket az eszközök mappa hozzáadása a projekthez. 
    1. Váltson a **projekt** megtekintése. 
    2. Kattintson a jobb gombbal **alkalmazás**.
    3. Válassza az **Új** lehetőséget.
    4. Válassza ki **mappa**. 
    5. Ezután válassza ki **eszközök mappa**. 

        ![Adja hozzá az eszközök mappát menü](./media/notification-hubs-kindle-get-started/add-assets-folder-menu.png)    
    6. Az a **összetevő konfigurálása** lapon, tegye a következőket:
        1. Válassza ki **mappa helyének módosítása**
        2. Győződjön meg arról, hogy a mappa beállításai: `src/main/assets`.
        3. Válassza a **Finish** (Befejezés) elemet. 
        
            ![Eszközök mappa konfigurálása](./media/notification-hubs-kindle-get-started/configure-asset-folder.png)
2. Adjon hozzá egy fájlt **api_key.txt** , a **eszközök** mappát. A fanézetben bontsa ki a **alkalmazás**, bontsa ki a **src**, bontsa ki a **fő**, és kattintson a jobb gombbal **eszközök**, mutasson a **új**, majd válassza ki **fájl**. Adja meg **api_key.txt** a fájl nevét. 3. 
5. Másolja a api_key.txt fájlba, az Amazon fejlesztői portálon létrehozott API-kulcsot. 
6. A projekt felépítése. 

## <a name="run-the-app"></a>Az alkalmazás futtatása
1. A felső pöccintsen az Kindle eszközön, és kattintson a **beállítások**, és kattintson a **fiókom** és a egy érvényes Amazon-fiókot regisztrálni.
2. Az Android Studióban futtassa az alkalmazást a Kindle eszközön. 

> [!NOTE]
> Ha probléma merül fel, ellenőrizze az emulátor idejét (vagy az eszközét). Az idő értékének pontosnak kell lennie. A Kindle-emulátor idejének módosításához futtassa a következő parancsot az Android SDK platformeszközök könyvtárából:

```shell
adb shell  date -s "yyyymmdd.hhmmss"
```

## <a name="send-a-notification-message"></a>Értesítési üzenet küldése

Üzenet küldése a .NET használatával:

```csharp
static void Main(string[] args)
{
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

    hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
}
```

Mintakód, lásd: [ebben a példában a Githubon](https://github.com/Azure/azure-notificationhubs-dotnet/blob/master/Samples/SendPushSample/SendPushSample/Program.cs).

![][7]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban szórásos értesítéseket küldött a háttérrendszerben regisztrált Kindle-eszközök mindegyikének. Megtudhatja, hogyan küldhet leküldéses értesítéseket a megadott Kindle-eszközökre, folytassa a következő oktatóanyaggal:  A következő oktatóanyag bemutatja, hogyan küldhet leküldéses értesítéseket a megadott Androidos eszközökre, de használhatja ugyanazt a logikát értesítések leküldése az adott Kindle-eszközökre.

> [!div class="nextstepaction"]
>[Leküldéses értesítések küldése adott eszközökre](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- URLs. -->
[Amazon developer portal]: https://developer.amazon.com/home.html
[download the SDK]: https://developer.amazon.com/public/resources/development-tools/sdk
[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
