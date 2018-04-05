---
title: Ismerkedés az Azure Mobile Engagement Android-alkalmazásokkal való használatával
description: Ismerje meg, hogyan használható az Azure Mobile Engagement az Android-alkalmazásokhoz kapcsolódó elemzések és leküldéses értesítések tekintetében.
services: mobile-engagement
documentationcenter: android
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 3c286c6d-cfef-4e3e-9b2c-715429fe82db
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: hero-article
ms.date: 08/10/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 4eda8129e03e4880e168f7c154c7236da028e81e
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-android-apps"></a>Ismerkedés az Azure Mobile Engagement Android-alkalmazásokkal való használatával
> [!IMPORTANT]
> Az Azure Mobile Engagement támogatása 2018. március 31-én megszűnik. Az oldal nem sokkal ezután törlésre kerül.
> 


[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Ebben a témakörben elsajátíthatja, hogy miként használható az Azure Mobile Engagement az alkalmazás használatának megértéséhez, valamint leküldéses értesítések Android-alkalmazásba történő küldéséhez a szegmentált felhasználók számára.
Ez az oktatóanyag a Mobile Engagementet használó egyszerű küldési forgatókönyvet mutat be. Az oktatóanyagban létrehoz egy üres Android-alkalmazást, amely alapszintű adatokat gyűjt, és leküldéses értesítéseket fogad a Google Cloud Messaging (GCM) használatával.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóprogram elvégzéséhez szükség lesz az [Android Developer Tools](https://developer.android.com/sdk/index.html) eszközökre, amelyek az Android Studio integrált fejlesztőkörnyezetet és a legújabb Android platformot foglalják magukban.

Szükséges hozzá a [Mobile Engagement Android SDK](https://aka.ms/vq9mfn) is.

> [!IMPORTANT]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started).
>
>

## <a name="set-up-mobile-engagement-for-your-android-app"></a>A Mobile Engagement beállítása az Android-alkalmazáshoz
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Az alkalmazás csatlakoztatása a Mobile Engagement háttérrendszeréhez
Ez az oktatóanyag egy „alapszintű integrációt” mutat be, ami minimálisan szükséges az adatok gyűjtéséhez és leküldéses értesítés küldéséhez. Létre fog hozni egy alapszintű alkalmazást az Android Studio segítségével az integráció bemutatásához.

A teljes integrációs dokumentáció itt található: [Mobile Engagement Android SDK-integráció](mobile-engagement-android-sdk-overview.md).

### <a name="create-an-android-project"></a>Android-projekt létrehozása
1. Indítsa el az **Android Studio** alkalmazást, majd az előugró ablakban válassza a **Start a new Android Studio project** (Új Android Studio-projekt indítása) elemet.

    ![][1]
2. Adjon meg egy alkalmazásnevet és egy vállalati tartományt. Jegyezze fel a megadott információkat, mert később még szüksége lesz rájuk. Kattintson a **Tovább** gombra.

    ![][2]
3. Jelölje ki a kívánt méretet és API-szintet, majd kattintson a **Next** (Tovább) gombra.

   > [!NOTE]
   > A Mobile Engagement legalább 10-es API-szintet igényel (Android 2.3.3).
   >
   >

    ![][3]
4. Válassza a **Blank Activity** (Üres tevékenység) elemet, amely ezen alkalmazás egyetlen képernyője lesz, majd kattintson a **Next** (Tovább) gombra.

    ![][4]
5. Végül hagyja az alapértelmezett értékeket, és kattintson a **Finish** (Befejezés) gombra.

    ![][5]

Az Android Studio létrehozza a bemutatóalkalmazást, amelybe integrálni fogjuk a Mobile Engagementet.

### <a name="include-the-sdk-library-in-your-project"></a>Az SDK-könyvtár hozzáadása a projekthez
1. Töltse le a [Mobile Engagement Android SDK](https://aka.ms/vq9mfn)-t.
2. Bontsa ki az archívumfájlt a számítógép egyik mappájába.
3. Azonosítsa az SDK aktuális verziójához tartozó .jar könyvtárat, és másolja a vágólapra.

      ![][6]
4. Lépjen a **Project** (Projekt) szakaszhoz (1), és illessze be a .jar fájlt a „libs” mappába (2).

      ![][7]
5. Szinkronizálja a projektet a könyvtár betöltéséhez.

      ![][8]

### <a name="connect-your-app-to-mobile-engagement-backend-with-the-connection-string"></a>Az alkalmazás csatlakoztatása a Mobile Engagement háttérrendszeréhez a kapcsolati karakterlánc segítségével
1. Másolja az alábbi kódsorokat a tevékenység létrehozásának helyén (csak egy helyre kell beilleszteni az alkalmazásba, általában a fő tevékenységnél). Ezen példaalkalmazás esetén nyissa meg az src -> main -> java mappában található MainActivity tevékenységet, és adja hozzá a következőket:

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
        EngagementAgent.getInstance(this).init(engagementConfiguration);
2. Oldja fel a hivatkozásokat az Alt+Enter billentyűkombináció lenyomásával vagy a következő importálási utasítások hozzáadásával:

        import com.microsoft.azure.engagement.EngagementAgent;
        import com.microsoft.azure.engagement.EngagementConfiguration;
3. Lépjen vissza az Azure Portalra az alkalmazás **Connection Info** (Kapcsolati adatok) lapjáról, és másolja a **Kapcsolati karakterláncot**.

      ![](../../includes/media/mobile-engagement-create-app-in-portal-new/app-connection-info.png)

4. Illessze be azt a `setConnectionString` paraméterbe az alábbi kódban látható teljes karakterláncot lecserélve:

        engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### <a name="add-permissions-and-a-service-declaration"></a>Engedélyek és szolgáltatásdeklaráció hozzáadása
1. Adja hozzá a projekt Manifest.xml fájljához a következő engedélyeket, közvetlenül az `<application>` címke előtt vagy után:

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
2. Adja hozzá a következő kódot az `<application>` és a `</application>` címke között az ügynökszolgáltatás deklarálásához:

        <service
             android:name="com.microsoft.azure.engagement.service.EngagementService"
             android:exported="false"
             android:label="<Your application name>"
             android:process=":Engagement"/>
3. A beillesztett kódban cserélje le a `"<Your application name>"` paramétert abban a címkében, amely a **Settings** (Beállítások) menüben jelenik meg, és amelyben a felhasználók megtekinthetik az eszközön futó szolgáltatásokat. A label értékeként megadhatja például a „Szolgáltatás” szót.

### <a name="send-a-screen-to-mobile-engagement"></a>Képernyő küldése a Mobile Engagement számára
Az adatok küldésének megkezdéséhez és annak biztosításához, hogy a felhasználók aktívak, legalább egy képernyőt (tevékenységet) el kell küldenie a Mobile Engagement háttérrendszere számára.

Lépjen a **MainActivity.java** fájlhoz, és adja hozzá a következőt a **MainActivity** alaposztály lecseréléséhez az **EngagementActivity** osztályra:

    public class MainActivity extends EngagementActivity {

> [!NOTE]
> Ha az alaposztály nem *Activity*, a [speciális Android-jelentéskészítéssel](mobile-engagement-android-advanced-reporting.md) foglalkozó témakörben tájékozódhat a más osztályoktól való öröklésről.
>
>

Az alábbi sort tegye megjegyzésbe ezen egyszerű forgatókönyv esetén:

    // setSupportActionBar(toolbar);

Ha meg akarja tartani az alkalmazásban az `ActionBar` értéket, tekintse meg a [speciális Android-jelentéskészítéssel](mobile-engagement-android-advanced-reporting.md) foglalkozó témakört.

## <a name="connect-app-with-real-time-monitoring"></a>Az alkalmazás csatlakoztatása a valós idejű megfigyeléshez
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="enable-push-notifications-and-in-app-messaging"></a>Leküldéses értesítések és alkalmazáson belüli üzenetek engedélyezése
A Mobile Engagement a kampányok során lehetővé teszi a felhasználókkal folytatott interakciót és a felhasználók ELÉRÉSÉT leküldéses értesítésekkel és alkalmazáson belüli üzenetekkel. Ez a modul REACH (Elérés) néven érhető el a Mobile Engagement portálon.
Az alábbi szakaszban állíthatja be az alkalmazást a fogadásukra.

### <a name="copy-sdk-resources-in-your-project"></a>SDK-erőforrások másolása a projektben
1. Lépjen vissza a letöltött SDK-tartalomhoz, és másolja a **res** mappát.

    ![][10]
2. Lépjen vissza az Android Studio alkalmazásba, jelölje ki a projektfájlok **main** könyvtárát, majd illessze be az erőforrásoknak a projekthez való hozzáadásához.

    ![][11]

[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[!INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

## <a name="next-steps"></a>További lépések
Az [Android SDK-val](mobile-engagement-android-sdk-overview.md) foglalkozó témakörben tájékozódhat bővebben az SDK integrálásával kapcsolatban.

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png
