---
title: "Ismerkedés az Azure Mobile Engagement Xamarin.Android-alkalmazásokkal való használatával"
description: "Ismerje meg, hogyan használható az Azure Mobile Engagement a Xamarin.Android-alkalmazásokhoz kapcsolódó elemzésekkel és leküldéses értesítésekkel."
services: mobile-engagement
documentationcenter: xamarin
author: piyushjo
manager: erikre
editor: 
ms.assetid: fb68cf98-08a2-41b5-8e59-757469de3fe7
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 06/16/2016
ms.author: piyushjo
ms.openlocfilehash: 7b3d01b32c2d5a40448fc22861cd45f612238f2f
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-xamarinandroid-apps"></a>Ismerkedés az Azure Mobile Engagement Xamarin.Android-alkalmazásokkal való használatával
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Ebben a témakörben elsajátíthatja, hogy miként használható az Azure Mobile Engagement az alkalmazás használatának megértéséhez, valamint leküldéses értesítések Xamarin.Android-alkalmazásba történő küldéséhez a szegmentált felhasználók számára.
Ez az oktatóanyag a Mobile Engagementet használó egyszerű küldési forgatókönyvet mutat be. Az oktatóanyagban létrehoz egy üres Xamarin.Android-alkalmazást, amely alapszintű adatokat gyűjt, és leküldéses értesítéseket fogad a Google Cloud Messaging (GCM) használatával.

> [!NOTE]
> Az Azure Mobile Engagement szolgáltatást 2018 márciusától megszüntetjük, és jelenleg csak meglévő ügyfelek számára érhető el. További információkért lásd: [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

Az oktatóanyaghoz az alábbiakra lesz szükség:

* [Xamarin Studio](http://xamarin.com/studio). Használhatja a Visual Studiót is a Xamarinhoz, de ez az oktatóanyag a Xamarin Studiót használja. A telepítési útmutatás itt található: [Setup and Install for Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (A Visual Studio és a Xamarin beállítása és telepítése).
* [Mobile Engagement Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-android-get-started).
> 
> 

## <a id="setup-azme"></a>A Mobile Engagement beállítása az Android-alkalmazáshoz
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Az alkalmazás csatlakoztatása a Mobile Engagement háttérrendszeréhez
Ez az oktatóanyag egy „alapszintű integrációt” mutat be, ami minimálisan szükséges az adatok gyűjtéséhez és leküldéses értesítés küldéséhez. 

Létre fogunk hozni egy alapszintű alkalmazást a Xamarin Studio segítségével az integráció bemutatásához.

### <a name="create-a-new-xamarinandroid-project"></a>Új Xamarin.Android-projekt létrehozása
1. Indítsa el a **Xamarin Studiót**, lépjen a **File** -> **New** -> **Solution** (Fájl > Új > Megoldás) lehetőségre. 
   
    ![][1]
2. Válassza az **Android App** (Android-alkalmazás) lehetőséget, majd győződjön meg arról, hogy a választott nyelv a **C#**, és kattintson a **Next** (Tovább) gombra.
   
    ![][2]
3. Töltse ki az **App Name** (Alkalmazás neve) és az **Organization Identifier** (Szervezeti azonosító) mezőt. Jelölje be a **Google Play Services** (Google Play-szolgáltatások) jelölőnégyzetet, majd kattintson a **Next** (Tovább) gombra. 
   
    ![][3]
4. Frissítse a **Project Name** (Projekt neve), a **Solution Name** (Megoldás neve) és a **Location** (Hely) értékét, ha szükséges, majd kattintson a **Create** (Létrehozás) gombra.
   
    ![][4]

A Xamarin Studio létrehozza az alkalmazást, amelybe integrálni fogjuk a Mobile Engagementet. 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Az alkalmazás csatlakoztatása a Mobile Engagement háttérrendszeréhez
1. Kattintson a jobb gombbal a **Packages** mappára a Solution (Megoldás) ablakban, és válassza az **Add Packages...** (Csomagok hozzáadása...) lehetőséget.
   
    ![][5]
2. Keresse meg a **Microsoft Azure Mobile Engagement Xamarin SDK**-t, és adja hozzá a megoldásához.  
   
    ![][6]
3. Nyissa meg a **MainActivity.cs** fájlt, és adja hozzá a következő using utasításokat:
   
        using Microsoft.Azure.Engagement;
        using Microsoft.Azure.Engagement.Activity;
4. Az `OnCreate` metódusban adja hozzá a következőt a Mobile Engagement háttérrendszerhez való csatlakozás inicializálásához. Győződjön meg arról, hogy hozzáadta a **ConnectionString** karakterláncot. 
   
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.ConnectionString = "YourConnectionStringFromAzurePortal";
        EngagementAgent.Init(engagementConfiguration);

### <a name="add-permissions-and-a-service-declaration"></a>Engedélyek és szolgáltatásdeklaráció hozzáadása
1. Nyissa meg a **Manifest.xml** fájlt a Properties mappában. Válassza ki a Source (Forrás) lapot az XML-forrás közvetlen frissítéséhez.
2. Adja hozzá a projekt Manifest.xml fájljához (amely a **Properties** mappában található) a következő engedélyeket, közvetlenül az `<application>` címke előtt vagy után:
   
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
3. Adja hozzá a következőt az `<application>` és az `</application>` címke között az ügynökszolgáltatás deklarálásához:
   
        <service
             android:name="com.microsoft.azure.engagement.service.EngagementService"
             android:exported="false"
             android:label="<Your application name>"
             android:process=":Engagement"/>
4. A beillesztett kódban adja meg a megfelelő `"<Your application name>"` nevet a label értékeként. A név a **Settings** (Beállítások) menüben jelenik meg, amelyben a felhasználók megtekinthetik az eszközön futó szolgáltatásokat. A label értékeként megadhatja például a „Szolgáltatás” szót.

### <a name="send-a-screen-to-mobile-engagement"></a>Képernyő küldése a Mobile Engagement számára
Az adatok küldésének megkezdéséhez és annak biztosításához, hogy a felhasználók aktívak, legalább egy képernyőt el kell küldenie a Mobile Engagement háttérrendszere számára. Ennek elvégzéséhez biztosítsa, hogy a `MainActivity` az `EngagementActivity` elemtől örököl az `Activity` helyett.

    public class MainActivity : EngagementActivity

Ha az `EngagementActivity` elemtől nem lehet örökölni, akkor hozzá kell adnia a `.StartActivity` és `.EndActivity` metódust az `OnResume` és `OnPause` elemhez.  

        protected override void OnResume()
            {
                EngagementAgent.StartActivity(EngagementAgentUtils.BuildEngagementActivityName(Java.Lang.Class.FromType(this.GetType())), null);
                base.OnResume();             
            }

            protected override void OnPause()
            {
                EngagementAgent.EndActivity();
                base.OnPause();            
            }

## <a id="monitor"></a>Az alkalmazás csatlakoztatása a valós idejű megfigyeléshez
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Leküldéses értesítések és alkalmazáson belüli üzenetek engedélyezése
A Mobile Engagement lehetővé teszi a felhasználókkal folytatott interakciót és a felhasználók elérését a kampányok részeként megjelenő leküldéses értesítésekkel és alkalmazáson belüli üzenetekkel. Ez a modul REACH (Elérés) néven érhető el a Mobile Engagement portálon.
Az alábbi szakaszok állítják be az alkalmazást a fogadásukra.

[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[!INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[1]: ./media/mobile-engagement-xamarin-android-get-started/1.png
[2]: ./media/mobile-engagement-xamarin-android-get-started/2.png
[3]: ./media/mobile-engagement-xamarin-android-get-started/3.png
[4]: ./media/mobile-engagement-xamarin-android-get-started/4.png
[5]: ./media/mobile-engagement-xamarin-android-get-started/5.png
[6]: ./media/mobile-engagement-xamarin-android-get-started/6.png
