---
title: "Ismerkedés az Azure Notification Hubs Baiduval való használatával | Microsoft Docs"
description: "Ebből az oktatóanyagból elsajátíthatja, hogyan használható az Azure Notification Hubs leküldéses értesítések Android-eszközökre történő küldéséhez a Baidu segítségével."
services: notification-hubs
documentationcenter: android
author: ysxu
manager: erikre
editor: 
ms.assetid: 23bde1ea-f978-43b2-9eeb-bfd7b9edc4c1
ms.service: notification-hubs
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: mobile-baidu
ms.workload: mobile
ms.date: 08/19/2016
ms.author: yuaxu
ms.openlocfilehash: df3bbda15e1245b6068c2b8290d0c96856051f1f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-notification-hubs-using-baidu"></a>Ismerkedés a Notification Hubs Baiduval való használatával
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Áttekintés
A felhőalapú Baidu-értesítés egy kínai felhőszolgáltatás, amellyel leküldéses értesítések küldhetők mobileszközökre. Ez a szolgáltatás Kínában használható hatékonyan, ahol a leküldéses értesítések Androidra történő kézbesítése összetett feladat. Ennek az az oka, hogy különböző alkalmazás-áruházak és leküldési szolgáltatások érhetők el, továbbá a GCM-hez (Google Cloud Messaginghez) nem csatlakozó Android-eszközök is használatban vannak.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyaghoz a következőkre lesz szükség:

* Android SDK (feltételezzük, hogy Eclipse-et használ), amelyet az <a href="http://go.microsoft.com/fwlink/?LinkId=389797">Android webhelyéről</a> tölthet le
* [Mobile Services Android SDK]
* [Baidu Push Android SDK]

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F).
> 
> 

## <a name="create-a-baidu-account"></a>Baidu-fiók létrehozása
A Baidu használatához Baidu-fiókkal kell rendelkeznie. Ha már rendelkezik ilyen fiókkal, jelentkezzen be a [Baidu portálra], és ugorjon a következő lépésre. Egyéb esetben a következőkben leírtak szerint hozhat létre Baidu-fiókot.  

1. Lépjen a [Baidu portálra], és kattintson a **登录** (**Bejelentkezés**) hivatkozásra. A fiókregisztrálás megkezdéséhez kattintson a **立即注册** gombra.
   
   ![][1]
2. A kért adatok – telefonszám/e-mail-cím, jelszó és ellenőrzőkód – megadása után kattintson a **Signup** (Regisztráció) elemre.
   
   ![][2]
3. A rendszer elküld egy e-mailt a megadott címre, amelyben megtalálható a Baidu-fiók aktiválására szolgáló hivatkozás.
   
   ![][3]
4. Jelentkezzen be az e-mail fiókjába, nyissa meg a Baidu aktiválási levelét, majd kattintson az aktiválási hivatkozásra a Baidu-fiók aktiválásához.
   
   ![][4]

A Baidu-fiók aktiválása után jelentkezzen be a [Baidu portálra].

## <a name="register-as-a-baidu-developer"></a>Regisztráció Baidu-fejlesztőként
1. A [Baidu portálra] való bejelentkezés után kattintson a **更多>>** (**továbbiak**) gombra.
   
      ![][5]
2. Görgessen lefelé a **站长与开发者服务** ((Webmesteri és fejlesztői szolgáltatások)) szakaszban, majd kattintson a **百度开放云平台** (**Baidu nyitott felhőplatform**) elemre.
   
      ![][6]
3. A következő oldalon kattintson a jobb felső sarokban található **开发者服务** (**Fejlesztői szolgáltatások**) elemre.
   
      ![][7]
4. A következő oldalon kattintson a **注册开发者** (**Regisztrált fejlesztők**) pontra a jobb felső sarokban található menüben.
   
      ![][8]
5. Adja meg a nevét, a leírást és a telefonszámot, amelyre meg kívánja kapni az ellenőrző SMS-t, majd kattintson az **送验证码** (**Ellenőrzőkód elküldése**) elemre. Nemzetközi telefonszámok esetén az országkódot is meg kell adni zárójelben. Egyesült államokbeli telefonszámok esetén például a következőképpen: **(1)1234567890**.
   
      ![][9]
6. Ezután a rendszer elküld egy ellenőrzőszámot tartalmazó SMS-t, ahogy az alábbi példában is látható:
   
      ![][10]
7. Adja meg az üzenet **验证码** (**Jóváhagyási kód**) részében található ellenőrzőszámot.
8. A fejlesztői regisztrációt a Baidu-szerződés elfogadásával, majd a **提交** (**Küldés**) gombra kattintva véglegesítheti. Sikeres regisztráció esetén a következő oldal jelenik meg:
   
      ![][11]

## <a name="create-a-baidu-cloud-push-project"></a>Felhőalapú Baidu-értesítési projekt létrehozása
Felhőalapú Baidu-értesítési projekt létrehozásakor megkapja az alkalmazásazonosítóját, az API-kulcsot és a titkos kulcsot.

1. A [Baidu portálra] való bejelentkezés után kattintson a **更多>>** (**továbbiak**) gombra.
   
      ![][5]
2. Görgessen lefelé a **站长与开发者服务** (**Webmesteri és fejlesztői szolgáltatások**) szakaszban, majd kattintson a **百度开放云平台** (**Baidu nyitott felhőplatform**) elemre.
   
      ![][6]
3. A következő oldalon kattintson a jobb felső sarokban található **开发者服务** (**Fejlesztői szolgáltatások**) elemre.
   
      ![][7]
4. A következő oldalon kattintson a **云推送** (**Felhőalapú leküldés**) gombra a **云服务** (**Felhőszolgáltatások**) szakaszban.
   
      ![][12]
5. A fejlesztői regisztráció után a felső menüben megjelenik a **管理控制台** (**Felügyeleti konzol**) elem. Kattintson a **开发者服务管理** (**Fejlesztői szolgáltatások kezelése**) parancsra.
   
      ![][13]
6. A következő oldalon kattintson a **创建工程** (**Projekt létrehozása**) elemre.
   
      ![][14]
7. Adjon meg egy alkalmazásnevet, majd kattintson a **创建** (**Létrehozás**) parancsra.
   
      ![][15]
8. A felhőalapú Baidu-értesítési projekt sikeres létrehozása után megjelenő oldalon megtalálja az **alkalmazásazonosítót**, az **API-kulcsot** és a **titkos kulcsot**. Jegyezze fel az API- és a titkos kulcsot, mert ezekre később szüksége lesz.
   
      ![][16]
9. A bal oldali ablaktábla **云推送** (**Felhőalapú leküldés**) elemére kattintva konfigurálhatja a projektet a leküldéses értesítésekhez.
   
      ![][31]
10. A következő oldalon kattintson a **推送设置** (**Beállítások leküldése**) gombra.
    
    ![][32]  
11. A konfigurációs oldal **应用包名** (**Alkalmazáscsomag**) mezőjébe írja be az Android-projektben használni kívánt csomagnevet, majd kattintson a **保存设置** (**Mentés**) parancsra.  
    
    ![][33]

Ekkor megjelenik a **保存成功！** (**Sikeresen mentve!**) üzenet.

## <a name="configure-your-notification-hub"></a>Az értesítési központ konfigurálása
1. Jelentkezzen be a [klasszikus Azure portálra], majd kattintson az **+ÚJ** gombra a képernyő elemre.
2. Kattintson az **App Services**, a **Service Bus**, a **Notification Hub**, végül pedig a **Gyors létrehozás** elemre.
3. Adja meg a **Notification Hub** nevét, válassza ki a **Régiót** és az értesítési központ létrehozásához használni kívánt **Névteret**, majd kattintson az **Új értesítési központ létrehozása** elemre.  
   
      ![][17]
4. Kattintson arra a névtérre, amelyben létrehozta az értesítési központot, majd a felül látható **Notification Hubs** elemre.
   
      ![][18]
5. Válassza ki a létrehozott értesítési központot, majd a felső menüben kattintson a **Konfigurálás** parancsra.
   
      ![][19]
6. Görgessen lefelé a **Baidu-értesítések beállításai** szakaszban, majd adja meg a Baidu-konzolon korábban beszerzett API-kulcsot és titkos kulcsot a felhőalapú Baidu-értesítési projekthez. Kattintson a **Save** (Mentés) gombra.
   
      ![][20]
7. Kattintson az értesítési központ felső részén látható **Irányítópult** elemre, majd a **Kapcsolati karakterlánc megtekintése** parancsra.
   
      ![][21]
8. Jegyezze fel a **Kapcsolati adatok elérése** ablakban látható **DefaultListenSharedAccessSignature** és **DefaultFullSharedAccessSignature** kapcsolati karakterláncokat.
   
    ![][22]

## <a name="connect-your-app-to-the-notification-hub"></a>Az alkalmazás csatlakoztatása az értesítési központhoz
1. Az Eclipse ADT-ben hozzon létre egy új Android-projektet (**File** (Fájl)  > **New** (Új)  > **Android Application Project** (Android-alkalmazásprojekt)).
   
    ![][23]
2. Adjon meg egy **alkalmazásnevet**, és győződjön meg arról, hogy a **Minimum Required SDK** (minimális SDK-követelmény) verziószáma **API 16: Android 4.1**.
   
    ![][24]
3. Kattintson a **Next** (Tovább) gombra, majd folytassa a varázsló használatát, amíg a **Create Activity** (Tevékenység létrehozása) ablak meg nem jelenik. Győződjön meg arról, hogy a **Blank Activity** (Üres tevékenység) lehetőség van kiválasztva, majd válassza a **Befejezés** lehetőséget egy új Android-alkalmazás létrehozásához.
   
    ![][25]
4. Győződjön meg arról, hogy a **Project Build Target** (Projekt célbuildje) beállítás értéke megfelelő.
   
    ![][26]
5. A [Bintrayen elérhető Notification-Hubs-Android-SDK](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4) **Files** (Fájlok) lapjáról töltse le a notification-hubs-0.4.jar fájlt. Adja hozzá a fájlt az Eclipse-projekt **libs** mappájához, majd frissítse a *libs* mappát.
6. Töltse le és csomagolja ki a [Baidu Push Android SDK] fájlt, nyissa meg a **libs** mappát, majd másolja a **pushservice-x.y.z** jar-fájlt és az **armeabi** & **mips** mappákat az Android-alkalmazás **libs** mappájába.
7. Nyissa meg az Android-projekt **AndroidManifest.xml** fájlját, majd adja hozzá a Baidu SDK által igényelt engedélyeket.
   
        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.WRITE_SETTINGS" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
        <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
        <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
        <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
        <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />
8. Adja hozzá az **android:name** tulajdonságot az **AndroidManifest.xml** fájl **application** eleméhez, a *yourprojectname* (például **com.example.BaiduTest**) elem helyett. Győződjön meg arról, hogy ez a projektnév megegyezik a Baidu-konzol konfigurálásakor megadottal.
   
        <application android:name="yourprojectname.DemoApplication"
9. Cserélje le a *yourprojectname* (például **com.example.BaiduTest**) részt a következő konfigurációra az alkalmazáselemben a **.MainActivity** tevékenységelem után:
   
        <receiver android:name="yourprojectname.MyPushMessageReceiver">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
                <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
                <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
            </intent-filter>
        </receiver>
   
        <receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
                <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
                <action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
            </intent-filter>
        </receiver>
   
        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>
        </receiver>
   
        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1"  >
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
            </intent-filter>
        </service>
10. Adjon hozzá egy új, **ConfigurationSettings.java** nevű osztályt a projekthez.
    
     ![][28]
    
     ![][29]
11. Adja hozzá a következő kódot:
    
        public class ConfigurationSettings {
                public static String API_KEY = "...";
                public static String NotificationHubName = "...";
                public static String NotificationHubConnectionString = "...";
            }
    
    Állítsa az **API_KEY** paraméter értékét a felhőalapú Baidu-projektről korábban lekért **NotificationHubName** értékre, illetve adja meg a klasszikus Azure portálról származó értesítésiközpont-nevet, a **NotificationHubConnectionString** és a DefaultListenSharedAccessSignature értéket.
12. Adjon hozzá egy új, **DemoApplication.java** nevű osztályt, majd adja hozzá a következő kódot:
    
        import com.baidu.frontia.FrontiaApplication;
    
        public class DemoApplication extends FrontiaApplication {
            @Override
            public void onCreate() {
                super.onCreate();
            }
        }
13. Vegyen fel egy másik új, **MyPushMessageReceiver.java** nevű osztályt, és adja hozzá az alábbi kódot. Ez az osztály kezeli a Baidu leküldési kiszolgálóról kapott leküldéses értesítéseket.
    
        import java.util.List;
        import android.content.Context;
        import android.os.AsyncTask;
        import android.util.Log;
        import com.baidu.frontia.api.FrontiaPushMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub;
    
        public class MyPushMessageReceiver extends FrontiaPushMessageReceiver {
            /** TAG to Log */
            public static NotificationHub hub = null;
            public static String mChannelId, mUserId;
            public static final String TAG = MyPushMessageReceiver.class
                    .getSimpleName();
    
            @Override
            public void onBind(Context context, int errorCode, String appid,
                    String userId, String channelId, String requestId) {
                String responseString = "onBind errorCode=" + errorCode + " appid="
                        + appid + " userId=" + userId + " channelId=" + channelId
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
                mChannelId = channelId;
                mUserId = userId;
    
                try {
                    if (hub == null) {
                        hub = new NotificationHub(
                                ConfigurationSettings.NotificationHubName,
                                ConfigurationSettings.NotificationHubConnectionString,
                                context);
                        Log.i(TAG, "Notification hub initialized");
                    }
                } catch (Exception e) {
                   Log.e(TAG, e.getMessage());
                }
    
                registerWithNotificationHubs();
            }
    
            private void registerWithNotificationHubs() {
               new AsyncTask<Void, Void, Void>() {
                  @Override
                  protected Void doInBackground(Void... params) {
                     try {
                         hub.registerBaidu(mUserId, mChannelId);
                         Log.i(TAG, "Registered with Notification Hub - '"
                                 + ConfigurationSettings.NotificationHubName + "'"
                                 + " with UserId - '"
                                 + mUserId + "' and Channel Id - '"
                                 + mChannelId + "'");
                     } catch (Exception e) {
                         Log.e(TAG, e.getMessage());
                     }
                     return null;
                 }
               }.execute(null, null, null);
            }
    
            @Override
            public void onSetTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onSetTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }
    
            @Override
            public void onDelTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onDelTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }
    
            @Override
            public void onListTags(Context context, int errorCode, List<String> tags,
                    String requestId) {
                String responseString = "onListTags errorCode=" + errorCode + " tags="
                        + tags;
                Log.d(TAG, responseString);
            }
    
            @Override
            public void onUnbind(Context context, int errorCode, String requestId) {
                String responseString = "onUnbind errorCode=" + errorCode
                        + " requestId = " + requestId;
                Log.d(TAG, responseString);
            }
    
            @Override
            public void onNotificationClicked(Context context, String title,
                    String description, String customContentString) {
                String notifyString = "title=\"" + title + "\" description=\""
                        + description + "\" customContent=" + customContentString;
                Log.d(TAG, notifyString);
            }
    
            @Override
            public void onMessage(Context context, String message,
                    String customContentString) {
                String messageString = "message=\"" + message + "\" customContentString=" + customContentString;
                Log.d(TAG, messageString);
            }
        }
14. Nyissa meg a **MainActivity.java** fájlt, majd adja hozzá a következőket az **onCreate** metódushoz:
    
            PushManager.startWork(getApplicationContext(),
                    PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);
15. Nyissa meg a következő importálási utasításokat a felső részen:
    
            import com.baidu.android.pushservice.PushConstants;
            import com.baidu.android.pushservice.PushManager;

## <a name="send-notifications-to-your-app"></a>Értesítések küldése az alkalmazásnak
Az értesítések fogadásának az alkalmazásban való gyors teszteléséhez értesítéseket küldhet az [Azure Portalról](https://portal.azure.com/) az értesítési központ **Küldés** gombjával az alábbi képernyőn látható módon:

![](./media/notification-hubs-baidu-get-started/notification-hub-test-send-baidu.png)

A leküldéses értesítések küldése általában olyan háttérszolgáltatásokon keresztül történik egy kompatibilis kódtár használatával, mint a Mobile Services vagy az ASP.NET. Ha a háttérszolgáltatáshoz nem érhető el könyvtár, az értesítési üzenetek küldéséhez használhatja közvetlenül a REST API-t.

Ebben az oktatóanyagban az egyszerűbb megoldást választjuk, és az ügyfélalkalmazást egy konzolalkalmazás értesítési központjának .NET SDK-jával küldött értesítésekkel teszteljük háttérszolgáltatás használata helyett. Az értesítéseknek ASP.NET-háttérrendszerből történő küldéséhez következő lépésként [A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) oktatóanyagot ajánljuk. Értesítések küldéséhez azonban az alábbi megközelítések is alkalmazhatók:

* **REST-felület**: A [REST-felület](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx) használatával bármilyen háttérplatformon támogathatja az értesítéseket.
* **Microsoft Azure Notification Hubs .NET SDK**: A Visual Studio NuGet-csomagkezelőjében futtassa a következő parancsot: [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).
* **Node.js**: [A Notification Hubs használata a Node.js-ből](notification-hubs-nodejs-push-notification-tutorial.md).
* **Mobile Apps**: A [Leküldéses értesítések hozzáadása Mobile Apps-alkalmazáshoz](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md) témakörben találhat példát arra, hogy hogyan küldhetők értesítések a Notification Hubs szolgáltatással integrált Azure App Service Mobile Apps háttéralkalmazásból.
* **Java/PHP**: „A Notification Hubs használata Javából/PHP-ből” ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)) témakörben találhat példát arra, hogyan küldhetők értesítések a REST API-k használatával.

## <a name="optional-send-notifications-from-a-net-console-app"></a>(Nem kötelező) Értesítések küldése .NET-konzolalkalmazásból.
Ebben a szakaszban az értesítések .NET-konzolalkalmazásból történő küldését mutatjuk be.

1. Hozzon létre egy új Visual C#-konzolalkalmazást:
   
    ![][30]
2. A Package Manager Console (Csomagkezelő konzol) ablakban az **Alapértelmezett projekt** értékeként adja meg az új konzolalkalmazás-projektet, majd a konzolablakban hajtsa végre az alábbi parancsot:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
    Ez az utasítás hozzáad egy, az Azure Notification Hubs SDK-ra mutató hivatkozást a <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet-csomaggal</a>.
   
    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)
3. Nyissa meg a **Program.cs** fájlt, és adja hozzá a következő using utasítást:
   
        using Microsoft.Azure.NotificationHubs;
4. A `Program` osztályban adja hozzá a következő metódust, és cserélje le a *DefaultFullSharedAccessSignatureSASConnectionString* és a *NotificationHubName* elemeket a saját értékeire.
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
            string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
            var result = await hub.SendBaiduNativeNotificationAsync(message);
        }
5. Adja hozzá a következő sorokat a **Main** metódushoz:
   
         SendNotificationAsync();
         Console.ReadLine();

## <a name="test-your-app"></a>Az alkalmazás tesztelése
Ha egy valódi telefonon kívánja tesztelni az alkalmazást, csak csatlakoztassa a telefont a számítógéphez egy USB-kábellel. Ez a művelet feltölti az alkalmazást a csatlakoztatott telefonra.

Az alkalmazás az emulátorral való teszteléséhez kattintson a **Futtatás** elemre az Eclipse felső eszköztárán, majd válassza ki az alkalmazást: ez elindítja az emulátort, majd betölti és futtatja az alkalmazást.

Az alkalmazás lekéri a „userId” és a „channelId” azonosítókat a Baidu leküldéses értesítési szolgáltatásból, és regisztrál az értesítési központban.

Tesztértesítést a klasszikus Azure portál hibakeresési lapjáról küldhet. Ha a .NET konzolalkalmazást a Visual Studióhoz készítette, az alkalmazás futtatásához csak nyomja le az F5 billentyűt a Visual Studióban. Az alkalmazás elküld egy értesítést, amely az eszköz vagy az emulátor felső értesítési területén jelenik meg.

<!-- Images. -->
[1]: ./media/notification-hubs-baidu-get-started/BaiduRegistration.png
[2]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png
[3]: ./media/notification-hubs-baidu-get-started/BaiduConfirmation.png
[4]: ./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png
[5]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png
[6]: ./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png
[7]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperServices.png
[8]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperRegistration.png
[9]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationInput.png
[10]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationConfirmation.png
[11]: ./media/notification-hubs-baidu-get-started/BaiduDevConfirmationFinal.png
[12]: ./media/notification-hubs-baidu-get-started/BaiduCloudPush.png
[13]: ./media/notification-hubs-baidu-get-started/BaiduDevSvcMgmt.png
[14]: ./media/notification-hubs-baidu-get-started/BaiduCreateProject.png
[15]: ./media/notification-hubs-baidu-get-started/BaiduCreateProjectInput.png
[16]: ./media/notification-hubs-baidu-get-started/BaiduProjectKeys.png
[17]: ./media/notification-hubs-baidu-get-started/AzureNHCreation.png
[18]: ./media/notification-hubs-baidu-get-started/NotificationHubs.png
[19]: ./media/notification-hubs-baidu-get-started/NotificationHubsConfigure.png
[20]: ./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png
[21]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionStringView.png
[22]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionString.png
[23]: ./media/notification-hubs-baidu-get-started/EclipseNewProject.png
[24]: ./media/notification-hubs-baidu-get-started/EclipseProjectCreation.png
[25]: ./media/notification-hubs-baidu-get-started/EclipseBlankActivity.png
[26]: ./media/notification-hubs-baidu-get-started/EclipseProjectBuildProperty.png
[27]: ./media/notification-hubs-baidu-get-started/EclipseBaiduReferences.png
[28]: ./media/notification-hubs-baidu-get-started/EclipseNewClass.png
[29]: ./media/notification-hubs-baidu-get-started/EclipseConfigSettingsClass.png
[30]: ./media/notification-hubs-baidu-get-started/ConsoleProject.png
[31]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig1.png
[32]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig2.png
[33]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig3.png

<!-- URLs. -->
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Baidu Push Android SDK]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[klasszikus Azure portálra]: https://manage.windowsazure.com/
[Baidu portálra]: http://www.baidu.com/
