---
title: "Ismerkedés az Azure Notification Hubs Univerzális Windows-platformon futó alkalmazásokkal való használatával | Microsoft Docs"
description: "Ebből az oktatóanyagból elsajátíthatja, hogyan használható az Azure Notification Hubs leküldéses értesítések univerzális platformon futó Windows-alkalmazásra történő küldéséhez."
services: notification-hubs
documentationcenter: windows
author: ysxu
manager: erikre
editor: erikre
ms.assetid: cf307cf3-8c58-4628-9c63-8751e6a0ef43
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: e18a810bcdbd97c79418f53c647df8723ecb6076
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-notification-hubs-for-universal-windows-platform-apps"></a>Ismerkedés a Notification Hubs Univerzális Windows-platformon futó alkalmazásokkal való használatával

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Áttekintés
Ebből a cikkből elsajátíthatja, miként használható az Azure Notification Hubs leküldéses értesítések küldéséhez Univerzális Windows-platformon futó alkalmazások (UWP-alkalmazások) esetén.

A cikkben létrehoz egy üres Windows Áruházbeli alkalmazást, amely leküldéses értesítéseket fogad a Windows leküldéses értesítési szolgáltatásának (WNS) a használatával. Amikor végzett, az értesítési központ használatával leküldéses értesítéseket tud majd küldeni az alkalmazást futtató összes eszközre.

## <a name="before-you-begin"></a>Előkészületek
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Az oktatóanyag teljes kódját megtalálja a [GitHubon](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/GetStartedWindowsUniversal).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyaghoz az alábbiakra lesz szükség:

* [Microsoft Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs) vagy újabb
* [UWP-alkalmazások telepített fejlesztőeszközei](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)
* Aktív Azure-fiók  
    Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információk: [Ingyenes Azure-próbaverzió](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-store-dotnet-get-started%2F).
* Aktív Windows Áruházbeli fiók

Ennek az oktatóanyagnak az elvégzése előfeltétel minden további, UWP-alkalmazásokkal kapcsolatos Notification Hubs-oktatóanyag elvégzéséhez.

## <a name="register-your-app-for-the-windows-store"></a>Az alkalmazás regisztrálása a Windows Áruházban
A leküldéses értesítések UWP-alkalmazásokba irányuló küldéséhez társítsa az alkalmazást a Windows Áruházhoz. Ezután konfigurálja az értesítési központot a WNS-sel való integrációra.

1. Ha még nem regisztrálta az alkalmazást, nyissa meg a [Windows fejlesztőközpontját](https://dev.windows.com/overview), jelentkezzen be Microsoft-fiókjával, majd válassza ki az **Új alkalmazás létrehozása** elemet.

2. Írja be az alkalmazás nevét, majd válassza a **Reserve app name** (Alkalmazásnév lefoglalása) lehetőséget. Ezzel létrehoz egy új Windows Áruházbeli regisztrációt az alkalmazás számára.

3. A Visual Studióban hozzon létre egy új Visual C# Áruházbeli alkalmazások projektet az UWP **Blank App** (Üres alkalmazás) sablonnal, majd kattintson az **OK** gombra.

4. A cél- és a minimális platformverziók esetében fogadja el az alapértelmezett értékeket.

5. A Megoldáskezelőben kattintson a jobb gombbal a Windows Áruházbeli alkalmazás projektjére, válassza a **Store** (Áruház), majd az **Associate App with the Store** (Alkalmazás társítása az Áruházzal) lehetőséget.  
    Megjelenik az **Associate Your App with the Windows Store** (Alkalmazás társítása a Windows Áruházzal) varázsló.

6. A varázslóban jelentkezzen be Microsoft-fiókjával.

7. Jelölje ki a 2. lépésben regisztrált alkalmazást, majd válassza a **Next** (Tovább), végül az **Associate** (Társítás) lehetőséget. Ezzel hozzáadja a szükséges Windows Áruházbeli regisztrációs adatokat az alkalmazásjegyzékhez.

8. Visszatérve az új alkalmazás [Windows fejlesztőközpontbeli](http://dev.windows.com/overview) oldalára, válassza a **Services** (Szolgáltatások), a **Push notifications** (Leküldéses értesítések), majd a **WNS/MPNS** lehetőséget.

9. Válassza a **New Notification** (Új értesítés) lehetőséget.

10. Válassza a **Blank (Toast)** (Üres (Bejelentési)) sablont, majd az **OK** gombot.

11. Adja meg az értesítés **nevét** és egy vizualizáció **környezetfüggő** üzenetét, majd válassza a **Save as draft** (Mentés vázlatként) lehetőséget.

12. Nyissa meg az [alkalmazásregisztrációs portált](http://apps.dev.microsoft.com), és jelentkezzen be.

13. Kattintson az alkalmazás nevére. A **Windows Áruház** platformbeállításainál jegyezze fel az **Application Secret** (Alkalmazáskulcs) jelszót és a **Package security identifier (SID)** (Csomag biztonsági azonosítója (SID)) értékét.

    >[!WARNING]
    >Az alkalmazáskulcs és a csomag biztonsági azonosítója fontos biztonsági hitelesítő adatok. Ezeket az értékeket ne ossza meg senkivel, és ne terjessze az alkalmazással.

## <a name="configure-your-notification-hub"></a>Az értesítési központ konfigurálása
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="5">
<li><p>Válassza az <b>Értesítési szolgáltatások</b> > <b>Windows (WNS)</b> elemet, majd írja be az alkalmazáskulcs jelszót a <b>Biztonsági kulcs</b> mezőbe. A <b>Csomag biztonsági azonosítója</b> mezőben adja meg a WNS-ből az előző szakaszban beszerzett értéket, majd válassza a <b>Mentés</b> lehetőséget.</p>
</li>
</ol>

![A Csomag biztonsági azonosítója és a Biztonsági kulcs mezők](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Ezzel konfigurálta az értesítési központot a WNS-sel folytatott együttműködésre, és rendelkezésére állnak a kapcsolati karakterláncok az alkalmazás regisztrálásához és értesítések küldéséhez.

## <a name="connect-your-app-to-the-notification-hub"></a>Az alkalmazás csatlakoztatása az értesítési központhoz
1. A Visual Studióban kattintson a jobb gombbal a megoldásra, majd válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget.  
    Megnyílik a **Manage NuGet Packages** (NuGet-csomagok kezelése) ablak.

2. A keresőmezőben adja meg a **WindowsAzure.Messaging.Managed** kifejezést, válassza az **Install** (Telepítés) lehetőséget, és fogadja el a használati feltételeket.
   
    ![A NuGet-csomagok kezelése ablak][20]
   
    A művelet a [WindowsAzure.Messaging.Managed NuGet-csomag](http://nuget.org/packages/WindowsAzure.Messaging) használatával letölti és telepíti a Windows rendszerhez készült Azure üzenetküldési kódtárat, és hozzáad egy rá mutató hivatkozást is.

3. Nyissa meg az App.xaml.cs projektfájlt, majd adja hozzá a következő `using` utasításokat: 
   
        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;

4. Az App.xaml.cs fájlban emellett adja hozzá az **App** osztályhoz a következő **InitNotificationsAsync** metódusdefiníciót:
   
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            var hub = new NotificationHub("<your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
            var result = await hub.RegisterNativeAsync(channel.Uri);
   
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
   
        }
   
    Ez a kód lekéri a csatorna URI azonosítóját az alkalmazás számára a WNS-ből, majd regisztrálja a csatorna URI azonosítóját az értesítési központban.
   
    >[!NOTE]
    >* A **hub name** helyőrzőt cserélje le az értesítési központ Azure Portalon megjelenő nevére. 
    >* Továbbá cserélje le a kapcsolati karakterlánc helyőrzőjét az egyik előző szakaszban az értesítési központ **Hozzáférési szabályzatok** oldaláról beszerzett **DefaultListenSharedAccessSignature** kapcsolati karakterláncra.
   > 
   > 
5. Az App.xaml.cs fájlban található **OnLaunched** eseménykezelő tetején adja hozzá a következő hívást az új **InitNotificationsAsync** metódushoz:
   
        InitNotificationsAsync();
   
    Ez a művelet biztosítja azt, hogy a csatorna URI azonosítója legyen regisztrálva az értesítési központban az alkalmazás minden indításakor.

6. Az alkalmazás futtatásához nyomja le az **F5** billentyűt. Megjelenik egy párbeszédpanel a regisztrációs kulccsal.

Az alkalmazás készen áll bejelentési értesítéseket fogadni.

## <a name="send-notifications"></a>Értesítések küldése
Ha gyorsan le szeretné tesztelni, hogyan fogadja az alkalmazás az értesítéseket, értesítéseket küldhet az [Azure Portalról](https://portal.azure.com/). Használja az értesítési központ **Tesztküldés** gombját, az alábbi képen látható módon:

![A Tesztküldés panel](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)

A leküldéses értesítések küldése általában olyan háttérszolgáltatásokon keresztül történik egy kompatibilis kódtár használatával, mint a Mobile Services vagy az ASP.NET. Ha a háttérszolgáltatáshoz nem érhető el kódtár, az értesítéseket közvetlenül a REST API használatával is elküldheti. 

Ebben az oktatóanyagban az ügyfélalkalmazás teszteléséhez egy háttérszolgáltatás használata helyett olyan értesítéseket küldünk el, amelyek egy konzolalkalmazás értesítési központjának .NET SDK-ját használják. Az értesítéseknek ASP.NET-háttérrendszerből történő küldéséhez következő lépésként [A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára] oktatóanyagot ajánljuk. Az értesítések küldéséhez az alábbi megközelítéseket is alkalmazhatja:

* **REST-felület**: A [REST-felület](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx) használatával bármilyen háttérplatformon támogathatja az értesítéseket.

* **Microsoft Azure Notification Hubs .NET SDK**: A Visual Studio NuGet-csomagkezelőjében futtassa a következő parancsot: [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node.js**: Lásd a [Notification Hubs Node.js-ből történő használatát](notification-hubs-nodejs-push-notification-tutorial.md) ismertető cikket.
* **Azure Mobile Apps**: A [Leküldéses értesítések hozzáadása Mobile Apps-alkalmazáshoz](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md) témakörben találhat példát arra, hogy hogyan küldhetők értesítések egy, a Notification Hubs szolgáltatással integrált Azure mobilalkalmazásból.

* **Java vagy PHP**: Az értesítések REST API-k használatával való küldését bemutató példákért lásd:
    * [Java](notification-hubs-java-push-notification-tutorial.md)
    * [PHP](notification-hubs-php-push-notification-tutorial.md)

## <a name="optional-send-notifications-from-a-console-app"></a>(Választható) Értesítések küldése konzolalkalmazásból
Ha .NET-konzolalkalmazással szeretne értesítéseket küldeni, tegye a következőket: 

1. Kattintson a jobb gombbal a megoldásra, válassza a **Hozzáadás** > **Új projekt** elemet, majd a **Visual C#** részen válassza a **Windows** és a **Konzolalkalmazás** lehetőséget, majd kattintson az **OK** gombra.
   
    Ezzel hozzáad egy új Visual C#-konzolalkalmazást a megoldáshoz. A projektet hozzáadhatja egy külön megoldásban is.

2. A Visual Studióban válassza a **Tools** (Eszközök), a **NuGet Package Manager** (NuGet-csomagkezelő), majd a **Package Manager Console** (Csomagkezelő konzol) elemet.
   
    A Package Manager Console (Csomagkezelő konzol) megnyílik a Visual Studióban.

3. A Package Manager Console (Csomagkezelő konzol) ablakban az **Alapértelmezett projekt** értékeként adja meg az új konzolalkalmazás-projektet, majd a konzolablakban futtassa az alábbi parancsot:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
    Ez a művelet hozzáad egy, az Azure Notification Hubs SDK-ra mutató hivatkozást a [Microsoft.Azure.Notification Hubs NuGet-csomag](http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) használatával.
   
    ![Az „Alapértelmezett projekt” név](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

4. Nyissa meg a Program.cs fájlt, majd adja hozzá a következő `using` utasítást:
   
        using Microsoft.Azure.NotificationHubs;

5. A **Program** osztályban adja hozzá a következő metódust:
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }
   
    >[!NOTE]
    >* A **hub name** helyőrzőt cserélje le az értesítési központ Azure Portalon megjelenő nevére. 
    >* Cserélje le a kapcsolati karakterlánc helyőrzőjét „Az értesítési központ konfigurálása” szakaszban az értesítési központ **Hozzáférési szabályzatok** oldaláról beszerzett **DefaultFullSharedAccessSignature** kapcsolati karakterláncra.
    >* Olyan kapcsolati karakterláncot használjon, amely nem *figyelési*, hanem *teljes* hozzáféréssel rendelkezik. A Figyelési hozzáféréssel rendelkező kapcsolati karakterláncok nem rendelkeznek jogosultsággal értesítések küldéséhez.
   > 
   > 
6. A **Main** metódusban adja hozzá a következő sorokat:
   
         SendNotificationAsync();
         Console.ReadLine();

7. Kattintson a jobb gombbal a konzolalkalmazás-projektre a Visual Studióban, majd válassza a **Set as StartUp Project** (Beállítás kezdőprojektként) elemet a projekt kezdőprojektként való beállításához. Ezután nyomja le az **F5** billentyűt az alkalmazás futtatásához.
   
    Egy bejelentési értesítést fog kapni az összes regisztrált eszközön. A bejelentési szalagcímre kattintva vagy koppintva a rendszer betölti az alkalmazást.

Az összes támogatott hasznos adatot megtalálja az MSDN [bejelentéskatalógussal], [csempekatalógussal] és a [jelvények áttekintésével] foglalkozó témakörében.

## <a name="next-steps"></a>Következő lépések
Ebben az egyszerű példában értesítéseket küldött az összes Windows rendszerű eszközre a portál vagy egy konzolalkalmazás használatával. Következő lépésként javasoljuk [A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára] oktatóanyag megtekintését. Ebben bemutatjuk, hogy hogyan küldhet értesítéseket ASP.NET-háttérrendszerből címkék használatával adott felhasználók számára.

Ha a felhasználókat érdeklődési körök alapján szeretné szegmentálni, olvassa el a [Use Notification Hubs to send breaking news] (Friss hírek küldése Notification Hubs használatával) című témakört. 

A Notification Hubs használatával kapcsolatban a [Notification Hubs használatával](notification-hubs-push-notification-overview.md) foglalkozó témakörben tekinthet meg további általános információt.

<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->

[A Notification Hubs használata leküldéses értesítések küldéséhez felhasználók számára]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md

[bejelentéskatalógussal]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[csempekatalógussal]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[jelvények áttekintésével]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx
 
