---
title: Ismerkedés az Azure Mobile Engagement Xamarin.iOS-alkalmazásokkal való használatával
description: Ismerje meg, hogyan használható az Azure Mobile Engagement a Xamarin.iOS-alkalmazásokhoz kapcsolódó elemzésekkel és leküldéses értesítésekkel.
services: mobile-engagement
documentationcenter: xamarin
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 0448209e-fff6-47bd-985c-2cf074bac12f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 78ba376eb4552468810a1de72d53ceb59ec9d395
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-xamarinios-apps"></a>Ismerkedés az Azure Mobile Engagement Xamarin.iOS-alkalmazásokkal való használatával
> [!IMPORTANT]
> Az Azure Mobile Engagement támogatása 2018. március 31-én megszűnik. Az oldal nem sokkal ezután törlésre kerül.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Ebben a témakörben elsajátíthatja, hogy miként használható az Azure Mobile Engagement az alkalmazáshasználat megértéséhez, valamint leküldéses értesítések Xamarin.iOS-alkalmazásba történő küldéséhez szegmentált felhasználók számára.
Ebben az oktatóanyagban létrehoz egy üres Xamarin.iOS-alkalmazást, amely alapszintű adatokat gyűjt, és leküldéses értesítéseket fogad az Apple leküldéses értesítési rendszerének (APNS) használatával.

> [!NOTE]
> Az Azure Mobile Engagement szolgáltatást 2018 márciusától megszüntetjük, és jelenleg csak meglévő ügyfelek számára érhető el. További információkért lásd: [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

Az oktatóanyaghoz az alábbiakra lesz szükség:

* [Xamarin Studio](http://xamarin.com/studio). Használhatja a Visual Studiót is a Xamarinhoz, de ez az oktatóanyag a Xamarin Studiót használja. A telepítési útmutatás itt található: [Setup and Install for Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (A Visual Studio és a Xamarin beállítása és telepítése). 
* [Mobile Engagement Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-ios-get-started).
> 
> 

## <a id="setup-azme"></a>A Mobile Engagement beállítása az iOS-alkalmazáshoz
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Az alkalmazás csatlakoztatása a Mobile Engagement háttérrendszeréhez
Ez az oktatóanyag egy „alapszintű integrációt” mutat be, ami minimálisan szükséges az adatok gyűjtéséhez és leküldéses értesítés küldéséhez.

Létre fogunk hozni egy alapszintű alkalmazást a Xamarin segítségével az integráció bemutatásához:

### <a name="create-a-new-xamarinios-project"></a>Új Xamarin.iOS-projekt létrehozása
1. Indítsa el a Xamarin Studiót. Lépjen a **File** -> **New** -> **Solution** (Fájl > Új > Megoldás) lehetőségre. 
   
    ![][1]
2. Válassza a **Single View App** (Egynézetes alkalmazás) lehetőséget, majd ellenőrizze, hogy a választott nyelv a **C#**-e, és kattintson a **Next** (Tovább) gombra.
   
    ![][2]
3. Töltse ki az **App Name** (Alkalmazás neve) és az **Organization Identifier** (Szervezeti azonosító) mezőt, majd kattintson a **Next** (Tovább) gombra. 
   
    ![][3]
   
   > [!IMPORTANT]
   > Győződjön meg róla, hogy az iOS-alkalmazás majdani üzembe helyezéséhez használandó közzétételi profil olyan alkalmazásazonosítót használ, amely pontosan megegyezik az itt szereplő csomagazonosítóval. 
   > 
   > 
4. Frissítse a **Project Name** (Projekt neve), a **Solution Name** (Megoldás neve) és a **Location** (Hely) értékét, ha szükséges, majd kattintson a **Create** (Létrehozás) gombra.
   
    ![][4]

A Xamarin Studio létrehozza a bemutatóalkalmazást, amelybe integrálni fogjuk a Mobile Engagementet. 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Az alkalmazás csatlakoztatása a Mobile Engagement háttérrendszeréhez
1. Kattintson a jobb gombbal a **Packages** mappára a Solution (Megoldás) ablakban, és válassza az **Add Packages...** (Csomagok hozzáadása...) lehetőséget.
   
    ![][5]
2. Keresse meg a **Microsoft Azure Mobile Engagement Xamarin SDK**-t, és adja hozzá a megoldásához.  
   
    ![][6]
3. Nyissa meg az **AppDelegate.cs** fájlt, és adja hozzá a következő using utasítást:
   
        using Microsoft.Azure.Engagement.Xamarin;
4. A **FinishedLaunching** metódusban adja hozzá a következőket a Mobile Engagement háttérrendszeréhez való csatlakozás inicializálásához. Győződjön meg arról, hogy hozzáadta a **ConnectionString** karakterláncot. Ez a kód egy helyőrző **NotificationIcon** elemet is használ, amelyet a Mobile Engagement SDK ad hozzá, és amelyet érdemes lecserélni. 
   
        EngagementConfiguration config = new EngagementConfiguration {
                        ConnectionString = "YourConnectionStringFromAzurePortal",
                        NotificationIcon = UIImage.FromBundle("close")
                    };
        EngagementAgent.Init (config);

## <a id="monitor"></a>Valós idejű figyelés engedélyezése
Az adatok küldésének megkezdéséhez és annak biztosításához, hogy a felhasználók aktívak, legalább egy képernyőt el kell küldenie a Mobile Engagement háttérrendszere számára.

1. Nyissa meg a **ViewController.cs** fájlt, és adja hozzá a következő using utasítást:
   
        using Microsoft.Azure.Engagement.Xamarin;
2. Cserélje le az osztályt, amelytől a `ViewController` örököl, a `UIViewController` osztályról az `EngagementViewController` osztályra. 

## <a id="monitor"></a>Az alkalmazás csatlakoztatása a valós idejű megfigyeléshez
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Leküldéses értesítések és alkalmazáson belüli üzenetek engedélyezése
A Mobile Engagement lehetővé teszi a felhasználókkal folytatott interakciót és a felhasználók elérését a kampányok részeként megjelenő leküldéses értesítésekkel és alkalmazáson belüli üzenetekkel. Ez a modul REACH (Elérés) néven érhető el a Mobile Engagement portálon.
Az alábbi szakaszok állítják be az alkalmazást a fogadásukra.

### <a name="modify-your-application-delegate"></a>Az alkalmazás delegáltjának módosítása
1. Nyissa meg az **AppDelegate.cs** fájlt, és adja hozzá a következő using utasítást:
   
        using System; 
2. Most a `FinishedLaunching` metóduson belül adja hozzá az alábbiakat a leküldéses üzenetek regisztrálásához a következő után: `EngagementAgent.init(...)`
   
        if (UIDevice.CurrentDevice.CheckSystemVersion(8,0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                (UIUserNotificationType.Badge |
                    UIUserNotificationType.Sound |
                    UIUserNotificationType.Alert),
                null);
            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        else
        {
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (
                UIRemoteNotificationType.Badge |
                UIRemoteNotificationType.Sound |
                UIRemoteNotificationType.Alert);
        }
3. Végül frissítse vagy vegye fel a következő metódusokat:
   
        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, 
            Action<UIBackgroundFetchResult> completionHandler)
        {
            EngagementAgent.ApplicationDidReceiveRemoteNotification(userInfo, completionHandler);
        }
   
        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            // Register device token on Engagement
            EngagementAgent.RegisterDeviceToken(deviceToken);
        }
   
        public override void FailedToRegisterForRemoteNotifications(UIApplication application, NSError error)
        {
            Console.WriteLine("Failed to register for remote notifications: Error '{0}'", error);
        }
4. A megoldás **Info.plist** fájljában ellenőrizze, hogy a **csomagazonosító** megegyezik-e az **alkalmazásazonosítóval**, amely az Apple fejlesztési központján belüli létrehozási profiljában szerepel. 
   
    ![][7]
5. Ugyanebben az **Info.plist** fájlban ellenőrizze, hogy kiválasztotta-e az **Enable Background Modes** (Háttérmódok engedélyezése) és a **Remote Notifications** (Távoli értesítések) lehetőségeket. 
   
     ![][8]
6. Futtassa az alkalmazást azon az eszközön, amelyet ehhez a közzétételi profilhoz társított. 

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[1]: ./media/mobile-engagement-xamarin-ios-get-started/new-solution.png
[2]: ./media/mobile-engagement-xamarin-ios-get-started/app-type.png
[3]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-name.png
[4]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-confirm.png
[5]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget.png
[6]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget-azme.png
[7]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-confirm-bundle.png
[8]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-configure-push.png
