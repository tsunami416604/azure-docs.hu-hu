---
title: Ismerkedés az Azure Mobile Engagement Windows Phone Silverlight-alkalmazásokkal való használatával
description: Ismerje meg, hogyan használható az Azure Mobile Engagement a Windows Phone Silverlight-alkalmazásokhoz kapcsolódó elemzésekkel és leküldéses értesítésekkel.
services: mobile-engagement
documentationcenter: windows
author: piyushjo
manager: erikre
editor: ''
ms.assetid: aa34692f-87f7-47c6-a20c-a1972750bc25
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 9fb1426e66df6cd8085342743b7d045c297743e5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-windows-phone-silverlight-apps"></a>Ismerkedés az Azure Mobile Engagement Windows Phone Silverlight-alkalmazásokkal való használatával
> [!IMPORTANT]
> Az Azure Mobile Engagement támogatása 2018. március 31-én megszűnik. Az oldal nem sokkal ezután törlésre kerül.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Ebben a témakörben elsajátíthatja, hogy miként használható az Azure Mobile Engagement az alkalmazáshasználat megértéséhez, valamint leküldéses értesítések Windows Phone Silverlight-alkalmazások szegmentált felhasználói számára való küldéséhez.
Ez az oktatóanyag a Mobile Engagementet használó egyszerű küldési forgatókönyvet mutat be. Az oktatóanyagban létrehoz egy üres Windows Phone Silverlight-alkalmazást, amely alapszintű adatokat gyűjt, és leküldéses értesítéseket fogad a Microsoft leküldéses értesítéseket kezelő szolgáltatása (MPNS) használatával.

> [!NOTE]
> Az Azure Mobile Engagement szolgáltatást 2018 márciusától megszüntetjük, és jelenleg csak meglévő ügyfelek számára érhető el. További információkért lásd: [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

> [!NOTE]
> A Windows Phone 8.1-es és korábbi verziójú projektek nem támogatottak a Visual Studio 2017-ben.  További információ: [A Visual Studio 2017 platform célcsoportkezelése és kompatibilitása](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

> [!NOTE]
> A Windows Phone 8.1 (nem Silverlight) rendszerre vonatkozó útmutatásért lásd az [univerzális Windows-alkalmazásokkal kapcsolatos oktatóanyagot](mobile-engagement-windows-store-dotnet-get-started.md).
> 
> 

Az oktatóanyaghoz az alábbiakra lesz szükség:

* Visual Studio 2013
* [MicrosoftAzure.MobileEngagement] NuGet-csomag

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-phone-get-started).
> 
> 

## <a id="setup-azme"></a>A Mobile Engagement beállítása a Windows Phone-alkalmazáshoz
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Az alkalmazás csatlakoztatása a Mobile Engagement háttérrendszeréhez
Ez az oktatóanyag egy „alapszintű integrációt” mutat be, ami minimálisan szükséges az adatok gyűjtéséhez és leküldéses értesítés küldéséhez. A teljes integrációs dokumentáció itt található: [Mobile Engagement Windows Phone SDK-integráció](mobile-engagement-windows-phone-sdk-overview.md)

Létre fogunk hozni egy alapszintű alkalmazást a Visual Studio segítségével az integráció bemutatásához.

### <a name="create-a-new-windows-phone-silverlight-project"></a>Új Windows Phone Silverlight-projekt létrehozása
A következő lépések a Visual Studio 2015 használatát feltételezik, de a lépések a Visual Studio korábbi verziói esetén is hasonlóak. 

1. Indítsa el a Visual Studiót, és a **Kezdőképernyőn** válassza a **New Project** (Új projekt) lehetőséget.
2. Az előugró ablakban válassza a **Windows 8** -> **Windows Phone** -> **Blank App (Windows Phone Silverlight)** (Windows 8 > Windows Phone > Üres alkalmazás (Windows Phone Silverlight)) lehetőséget. Adja meg az alkalmazás **nevét** és a **megoldás nevét**, majd kattintson az **OK** gombra.
   
    ![][1]
3. Választhat, hogy a cél a **Windows Phone 8.0** vagy a **Windows Phone 8.1** legyen.

Létrehozott egy új Windows Phone Silverlight-alkalmazást, amelybe integrálni fogjuk az Azure Mobile Engagement SDK-t.

### <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Az alkalmazás csatlakoztatása a Mobile Engagement háttérrendszeréhez
1. Telepítse a [MicrosoftAzure.MobileEngagement] NuGet-csomagot a projektben.
2. Nyissa meg a `WMAppManifest.xml` fájlt a Properties mappában, és ellenőrizze, hogy a `<Capabilities />` címkében deklarálva vannak-e a következők (ha nem, adja hozzá őket):
   
        <Capability Name="ID_CAP_NETWORKING" />
        <Capability Name="ID_CAP_IDENTITY_DEVICE" />
   
    ![][2]
3. Most illessze be a korábban a Mobile Engagement-alkalmazáshoz kimásolt kapcsolati karakterláncot a `Resources\EngagementConfiguration.xml` fájlba, a `<connectionString>` és a `</connectionString>` címke közé:
   
    ![][3]
4. Az `App.xaml.cs` fájlban:
   
    a. Adja hozzá a következő `using` utasítást:
   
            using Microsoft.Azure.Engagement;
   
    b. Inicializálja az SDK-t az `Application_Launching` metódusban:
   
            private void Application_Launching(object sender, LaunchingEventArgs e)
            {
              EngagementAgent.Instance.Init();
            }
   
    c. Szúrja be az alábbiakat az `Application_Activated` elembe:
   
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
               EngagementAgent.Instance.OnActivated(e);
            }

## <a id="monitor"></a>Valós idejű figyelés engedélyezése
Az adatok küldésének megkezdéséhez és annak biztosításához, hogy a felhasználók aktívak, legalább egy képernyőt (tevékenységet) el kell küldenie a Mobile Engagement háttérrendszere számára.

1. A MainPage.xaml.cs fájlban adja hozzá a következő `using` utasítást:
   
        using Microsoft.Azure.Engagement;
2. Cserélje le a **MainPage** alaposztályának eddigi **PhoneApplicationPage** értékét a következőre: **EngagementPage**.
   
        class MainPage : EngagementPage 
3. A `MainPage.xml` fájlban:
   
    a. Adja hozzá a következőt a névtér-deklarációkhoz:
   
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
   
    b. Az XML-címke nevének `phone:PhoneApplicationPage` részét cserélje le a következőre: `engagement:EngagementPage`.

## <a id="monitor"></a>Az alkalmazás csatlakoztatása a valós idejű megfigyeléshez
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Leküldéses értesítések és alkalmazáson belüli üzenetek engedélyezése
A Mobile Engagement lehetővé teszi a felhasználókkal folytatott interakciót és a felhasználók elérését a kampányok részeként megjelenő leküldéses értesítésekkel és alkalmazáson belüli üzenetekkel. Ez a modul REACH (Elérés) néven érhető el a Mobile Engagement portálon.
Az alábbi szakaszok állítják be az alkalmazást a fogadásukra.

### <a name="enable-your-app-to-receive-mpns-push-notifications"></a>MPNS leküldéses értesítések fogadásának engedélyezése az alkalmazásban
Adja hozzá a `WMAppManifest.xml` fájlhoz a következő új képességeket:

        ID_CAP_PUSH_NOTIFICATION
        ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

### <a name="initialize-the-reach-sdk"></a>A REACH SDK inicializálása
1. Az `App.xaml.cs` fájlban, az **Application_Launching** függvényben hívja meg az `EngagementReach.Instance.Init();` elemet közvetlenül az ügynök inicializálása után:
   
        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }
2. Az `App.xaml.cs` fájlban, az **Application_Activated** függvényben hívja meg az `EngagementReach.Instance.OnActivated(e);` elemet közvetlenül az ügynök inicializálása után:
   
        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

Készen is van. Most ellenőrizzük, hogy ezt az alapszintű integrációt megfelelően végezte-e el.

## <a id="send"></a>Értesítés küldése az alkalmazásnak
[!INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Az eszközén egy értesítésnek kell megjelennie, amely egy alkalmazásbeli értesítés, ha az alkalmazás meg van nyitva, egyéb esetben pedig az alábbihoz hasonló bejelentési értesítés: 

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK documentation]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png
