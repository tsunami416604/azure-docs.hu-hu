---
title: Ismerkedés az Azure Mobile Engagement univerzális Windows-alkalmazásokkal való használatával
description: Ismerje meg, hogyan használható az Azure Mobile Engagement az univerzális Windows-alkalmazásokhoz kapcsolódó elemzésekkel és leküldéses értesítésekkel.
services: mobile-engagement
documentationcenter: windows
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 48103867-7f64-4646-b019-42bd797d38e2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: ed6f3e0a369157bbf4c89982f8e1acbc1ed51340
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-windows-universal-apps"></a>Ismerkedés az Azure Mobile Engagement univerzális Windows-alkalmazásokkal való használatával
> [!IMPORTANT]
> Az Azure Mobile Engagement támogatása 2018. március 31-én megszűnik. Az oldal nem sokkal ezután törlésre kerül.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Ebben a témakörben elsajátíthatja, hogy miként használható az Azure Mobile Engagement az alkalmazáshasználat megértéséhez, valamint leküldéses értesítések univerzális Windows-alkalmazásba történő küldéséhez szegmentált felhasználók számára.
Ez az oktatóanyag a Mobile Engagementet használó egyszerű küldési forgatókönyvet mutat be. Ennek során létrehoz egy üres univerzális Windows-alkalmazást, amely alapszintű alkalmazáshasználati adatokat gyűjt, és leküldéses értesítéseket fogad a Windows értesítési szolgáltatása (WNS) használatával.

> [!NOTE]
> Az Azure Mobile Engagement szolgáltatást 2018 márciusától megszüntetjük, és jelenleg csak meglévő ügyfelek számára érhető el. További információkért lásd: [Mobile Engagement](https://azure.microsoft.com/services/mobile-engagement/).

## <a name="prerequisites"></a>Előfeltételek
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

## <a name="set-up-mobile-engagement-for-your-windows-universal-app"></a>A Mobile Engagement beállítása az univerzális Windows-alkalmazáshoz
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Az alkalmazás csatlakoztatása a Mobile Engagement háttérrendszeréhez
Ez az oktatóanyag egy „alapszintű integrációt” mutat be, ami minimálisan szükséges az adatok gyűjtéséhez és leküldéses értesítés küldéséhez. A teljes integrációs dokumentáció itt található: [Mobile Engagement univerzális Windows SDK-integráció](mobile-engagement-windows-store-sdk-overview.md).

Létre fog hozni egy alapszintű alkalmazást a Visual Studio segítségével az integráció bemutatásához.

### <a name="create-a-windows-universal-app-project"></a>Univerzális Windows-alkalmazás projekt létrehozása
A következő lépések a Visual Studio 2015 használatát feltételezik, de a lépések a Visual Studio korábbi verziói esetén is hasonlóak.

1. Indítsa el a Visual Studiót, és a **Kezdőképernyőn** válassza a **New Project** (Új projekt) lehetőséget.
2. Az előugró ablakban válassza a **Windows** -> **Universal** -> **Blank App (Universal Windows)** (Windows > Univerzális > Üres alkalmazás (Univerzális Windows)) lehetőséget. Adja meg az alkalmazás **nevét** és a **megoldás nevét**, majd kattintson az **OK** gombra.

    ![][1]

Most létrehozott egy új univerzális Windows-alkalmazás projektet, amelybe integrálni fogjuk az Azure Mobile Engagement SDK-t.

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Az alkalmazás csatlakoztatása a Mobile Engagement háttérrendszeréhez
1. Telepítse a [MicrosoftAzure.MobileEngagement] NuGet-csomagot a projektben. Ha a Windows és a Windows Phone platformot is célozza, ezt mindkét projekt esetében el kell végeznie. A Windows 8.x és a Windows Phone 8.1 esetében ugyanaz a NuGet-csomag mindkét projektben elhelyezi a megfelelő platformspecifikus bináris fájlokat.
2. Nyissa meg a **Package.appxmanifest** csomagot, és ellenőrizze, hogy az tartalmazza a következő képességet:

        Internet (Client)

    ![][2]
3. Most másolja a korábban a Mobile Engagement-alkalmazáshoz kimásolt kapcsolati karakterláncot, és illessze be a `Resources\EngagementConfiguration.xml` fájlba, a `<connectionString>` és a `</connectionString>` címke közé:

    ![][3]

    > [!TIP]
    > Ha az alkalmazása a Windows és a Windows Phone platformot is célozza, hozzon létre két Mobile Engagement-alkalmazást – egyet-egyet mindegyik támogatott platformhoz. Ezzel a két alkalmazással biztosítható, hogy megfelelő közönségszegmentálást hozhasson létre, és megfelelően célzott értesítéseket küldhessen mindegyik platformra.

    > [!IMPORTANT]
    > A NuGet nem másolja automatikusan a Windows 10 UWP alkalmazásban található SDK-erőforrásokat. Ezt manuálisan kell megtenni a Nuget csomag telepítésekor megjelenő utasításoknak (readme.txt) megfelelően.  

1. Az `App.xaml.cs` fájlban:

    a. Adja hozzá a következő `using` utasítást:

            using Microsoft.Azure.Engagement;

    b. Adja hozzá az Engagementet inicializáló metódust:

           private void InitEngagement(IActivatedEventArgs e)
           {
             EngagementAgent.Instance.Init(e);

             //... rest of the code
           }

    c. Inicializálja az SDK-t az **OnLaunched** metódusban:

            protected override void OnLaunched(LaunchActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

    c. Szúrja be a következőt az **OnActivated** metódusba (ha a metódus nem található, vegye fel):

            protected override void OnActivated(IActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

## <a id="monitor"></a>Valós idejű figyelés engedélyezése
Az adatok küldésének megkezdéséhez és annak biztosításához, hogy a felhasználók aktívak, legalább egy képernyőt (tevékenységet) el kell küldenie a Mobile Engagement háttérrendszere számára.

1. A **MainPage.xaml.cs** fájlban adja hozzá a következő `using` utasítást:

    using Microsoft.Azure.Engagement.Overlay;
2. Cserélje le a **MainPage** alaposztályának eddigi **Page** értékét a következőre: **EngagementPageOverlay**:

        class MainPage : EngagementPageOverlay
3. Az `MainPage.xaml` fájlban:

    a. Adja hozzá a következőt a névtér-deklarációkhoz:

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

    b. Az XML-címke nevének **Page** részét cserélje le a következőre: **engagement:EngagementPageOverlay**.

> [!IMPORTANT]
> Ha az oldala felülírja az `OnNavigatedTo` metódust, hívja meg a következőt: `base.OnNavigatedTo(e)`. Ellenkező esetben a tevékenységről nem készül jelentés (az `EngagementPage` meghívja a `StartActivity` tevékenységet az `OnNavigatedTo` metódusán belül). Ez különösen a Windows Phone-projektek esetében fontos, ahol az alapértelmezett sablonok egy `OnNavigatedTo` metódussal rendelkeznek.
>
> **Univerzális Windows 10-alkalmazások** esetében a fent említett módszer helyett használja [a Windows Universal Apps Engagement SDK-val elérhető fejlett jelentéskészítési képességekről szóló](mobile-engagement-windows-store-advanced-reporting.md) cikk „Ajánlott módszer: a laposztályok túlterhelése” című szakaszában javasolt módszert.

## <a id="monitor"></a>Az alkalmazás csatlakoztatása a valós idejű megfigyeléshez
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Leküldéses értesítések és alkalmazáson belüli üzenetek engedélyezése
A Mobile Engagement lehetővé teszi a felhasználókkal folytatott interakciót és a felhasználók elérését a kampányok részeként megjelenő leküldéses értesítésekkel és alkalmazáson belüli üzenetekkel. Ez a modul REACH (Elérés) néven érhető el a Mobile Engagement portálon.
Az alábbi szakaszok állítják be az alkalmazást a fogadásukra.

### <a name="enable-your-app-to-receive-wns-push-notifications"></a>WNS leküldéses értesítések fogadásának engedélyezése az alkalmazásban
1. A `Package.appxmanifest` fájlban, az **Application** (Alkalmazás) lap **Notifications** (Értesítések) részében a **Toast capable:** (Bejelentések engedélyezése:) beállításnál adja meg a **Yes** (Igen) értéket.

    ![][5]

### <a name="initialize-the-reach-sdk"></a>A REACH SDK inicializálása
Az `App.xaml.cs` fájlban, az **InitEngagement** függvényben hívja meg az **EngagementReach.Instance.Init(e);** elemet közvetlenül az ügynök inicializálása után:

        private void InitEngagement(IActivatedEventArgs e)
        {
           EngagementAgent.Instance.Init(e);
           EngagementReach.Instance.Init(e);
        }

Készen áll a bejelentés elküldésére. A következő lépésben ellenőrizni fogjuk, hogy ezt az alapszintű integrációt megfelelően végezte-e el.

### <a name="grant-access-to-mobile-engagement-to-send-notifications"></a>Értesítésküldési hozzáférés biztosítása a Mobile Engagement számára
1. Nyissa meg a [Windows fejlesztői központot] a webböngészőjében, és jelentkezzen be, vagy ha szükséges, hozzon létre egy fiókot.
2. Kattintson a jobb felső sarokban lévő **Irányítópult** elemre, majd kattintson a bal oldali panelmenüben az **Új alkalmazás létrehozása** lehetőségre.

    ![][9]
3. Hozza létre az alkalmazást a neve lefoglalásával.

    ![][10]
4. Miután létrehozta az alkalmazást, lépjen a bal oldali menü **Szolgáltatások -> Leküldéses értesítések** részébe.

    ![][11]
5. A Leküldéses értesítések szakaszban kattintson a **Live Services webhelye** hivatkozásra.

    ![][12]
6. Ezzel a Leküldési hitelesítő adatok részhez lép. Ellenőrizze, hogy az **Alkalmazásbeállítások** szakaszban van-e, majd másolja ki a **Csomag biztonsági azonosítóját** és a **Titkos ügyfélkulcsot**.

    ![][13]
7. Lépjen a **Beállítások** részre a Mobile Engagement portálon, majd kattintson a bal oldalon a **Natív leküldés** szakaszra. Ezután kattintson a **Szerkesztés** gombra, és adja meg a **Csomag biztonsági azonosítója (SID)** és a **Titkos kulcs** értékét az itt látható módon:

    ![][6]
8. Végül ellenőrizze, hogy a Visual Studio alkalmazás társítva van-e az újonnan létrehozott alkalmazással az alkalmazás-áruházban. A Visual Studióban kattintson a következőkre: **Associate app with the store** (Alkalmazás társítása az Áruházzal).

    ![][7]

## <a id="send"></a>Értesítés küldése az alkalmazásnak
[!INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Ha az alkalmazás fut, egy alkalmazásbeli értesítést kell látnia. Ha azonban az alkalmazás be van zárva, egy bejelentési értesítést fog látni.
Ha alkalmazásbeli értesítés jelenik meg, és nem bejelentési értesítés, és az alkalmazást hibakeresési módban futtatja a Visual Studióban, akkor kattintson az eszköztár **Lifecycle events -> Suspend** (Életciklus-események -> Felfüggesztés) lehetőségére, hogy az alkalmazás biztosan fel legyen függesztve. Ha akkor kattintott a Kezdőképernyő gombra, miközben az alkalmazás hibakeresését végzi a Visual Studióban, az nem mindig lesz felfüggesztve, és bár egy alkalmazásbeli értesítés megjelenik, bejelentési értesítés nem.  

![][8]

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9864592
[Windows fejlesztői központot]: https://dev.windows.com
[Windows Universal Apps - Overlay integration]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/universal-app-creation.png
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/associate-app-store.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/vs-suspend.png
[9]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_create_app.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_app_name.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push.png
[12]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_1.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_creds.png
