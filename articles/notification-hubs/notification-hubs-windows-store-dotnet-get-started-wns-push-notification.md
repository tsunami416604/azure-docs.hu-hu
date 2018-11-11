---
title: Értesítések küldése az Univerzális Windows-platformon alapuló alkalmazásoknak az Azure Notification Hubs használatával | Microsoft Docs
description: Ebből az oktatóanyagból elsajátíthatja, hogyan használható az Azure Notification Hubs leküldéses értesítések univerzális platformon futó Windows-alkalmazásra történő küldéséhez.
services: notification-hubs
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: cf307cf3-8c58-4628-9c63-8751e6a0ef43
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 37d9f433f39b60609870913a13f23e5a6e419476
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246497"
---
# <a name="tutorial-send-notifications-to-universal-windows-platform-apps-by-using-azure-notification-hubs"></a>Oktatóanyag: Értesítések küldése az Univerzális Windows-platformon alapuló alkalmazásoknak az Azure Notification Hubs használatával

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Ebben az oktatóanyagban egy értesítési központot hoz létre leküldéses értesítések küldéséhez az Univerzális Windows-platformra (UWP) épülő alkalmazásba. Egy üres Windows Áruházbeli alkalmazást fog létrehozni, amely leküldéses értesítéseket fogad a Windows leküldéses értesítési szolgáltatásának (WNS) használatával. Ezt követően az értesítési központ használatával leküldéses értesítéseket tud majd küldeni az alkalmazást futtató összes eszközre.

> [!NOTE]
> Az oktatóanyag teljes kódját megtalálja a [GitHubon](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/GetStartedWindowsUniversal).

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Alkalmazás létrehozása a Windows Áruházban
> * Értesítési központ létrehozása
> * Windows-mintaalkalmazás létrehozása
> * Tesztértesítések küldése


## <a name="prerequisites"></a>Előfeltételek
- **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
- [Microsoft Visual Studio Community 2015 vagy újabb](https://www.visualstudio.com/products/visual-studio-community-vs).
- [UWP-alkalmazások telepített fejlesztőeszközei](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)
- Aktív Windows Áruházbeli fiók

Ennek az oktatóanyagnak az elvégzése előfeltétel minden további, UWP-alkalmazásokkal kapcsolatos Notification Hubs-oktatóanyag elvégzéséhez.

## <a name="create-an-app-in-windows-store"></a>Alkalmazás létrehozása a Windows Áruházban
A leküldéses értesítések UWP-alkalmazásokba irányuló küldéséhez társítsa az alkalmazást a Windows Áruházhoz. Ezután konfigurálja az értesítési központot a WNS-sel való integrációra.

1. Nyissa meg a [Windows fejlesztőközpontját](https://dev.windows.com/overview), jelentkezzen be Microsoft-fiókjával, majd kattintson a **Create a new app** (Új alkalmazás létrehozása) gombra.

    ![Új alkalmazás gomb](./media/notification-hubs-windows-store-dotnet-get-started/windows-store-new-app-button.png)
1. Írja be az alkalmazás nevét, majd válassza a **Reserve product name** (Terméknév lefoglalása) lehetőséget. Ezzel létrehoz egy új Windows Áruházbeli regisztrációt az alkalmazás számára.

    ![Áruházbeli alkalmazásnév](./media/notification-hubs-windows-store-dotnet-get-started/store-app-name.png)
1. Bontsa ki az **App Management** (Alkalmazáskezelés) menüpontot, válassza a **WNS/MPNS**, majd a **WNS/MPNS** elemet, végül a kattintson a **Live Services site** (Live Services-hely) hivatkozásra. Jelentkezzen be Microsoft-fiókjába. Megnyílik az **Application Registration Portal** (Alkalmazásregisztrációs portál) egy új lapon. Másik lehetőségként közvetlenül is megnyithatja [Application Registration Portal](https://apps.dev.microsoft.com) (Alkalmazásregisztrációs portált) lapját az alkalmazás nevére kattintva.

    ![A WNS/MPNS lap](./media/notification-hubs-windows-store-dotnet-get-started/wns-mpns-page.png)
1.   Jegyezze fel az **Application Secret** (Alkalmazáskulcs) jelszót és a **Package security identifier (SID)** (Csomag biztonsági azonosítója (SID)) értékét.

        >[!WARNING]
        >Az alkalmazáskulcs és a csomag biztonsági azonosítója fontos biztonsági hitelesítő adatok. Ezeket az értékeket ne ossza meg senkivel, és ne terjessze az alkalmazással.

## <a name="create-a-notification-hub"></a>Értesítési központ létrehozása
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


### <a name="configure-wns-settings-for-the-hub"></a>A központ WNS-beállításainak konfigurálása

1. Válassza a **Windows (WNS)** lehetőséget a **NOTIFICATION SETTINGS** (Értesítési beállítások) kategóriában. 
2. Adja meg a **Package SID** (Csomag biztonsági azonosítója) és a **Security Key** (Biztonsági kulcs) előző szakaszban feljegyzett értékét. 
3. Válassza az eszköztár **Save** (Mentés) elemét.

    ![A Csomag biztonsági azonosítója és a Biztonsági kulcs mezők](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Ezzel konfigurálta az értesítési központot a WNS-sel folytatott együttműködésre, és rendelkezésére állnak a kapcsolati sztringek az alkalmazás regisztrálásához és értesítések küldéséhez.

## <a name="create-a-sample-windows-app"></a>Windows-mintaalkalmazás létrehozása
1. A Visual Studióban válassza a **File** (Fájl) elemet, mutasson a **New** (Új) lehetőségre, majd kattintson a **Project** (Projekt) elemre. 
2. A **New Project** (Új projekt) párbeszédablakban hajtsa végre az alábbi lépéseket: 

    1. Bontsa ki a **Visual C#** elemet.
    2. Válassza a **Windows Universal** (Univerzális Windows-platform) lehetőséget. 
    3. Válassza a **Blank App (Universal Windows)** (Üres alkalmazás (Univerzális Windows-platform)) elemet. 
    4. Adjon meg a projekt **nevét**. 
    5. Kattintson az **OK** gombra. 

        ![New Project (Új projekt) párbeszédpanel](./media/notification-hubs-windows-store-dotnet-get-started/new-project-dialog.png)
1. A **target** (cél) és a **minimum** (minimális) platformverzió esetében fogadja el az alapértelmezett értékeket, majd kattintson az **OK** gombra. 
2. A Megoldáskezelőben kattintson a jobb gombbal a Windows Áruházbeli alkalmazás projektjére, válassza a **Store** (Áruház), majd az **Associate App with the Store** (Alkalmazás társítása az Áruházzal) lehetőséget. Megjelenik az **Associate Your App with the Windows Store** (Alkalmazás társítása a Windows Áruházzal) varázsló.
3. A varázslóban jelentkezzen be Microsoft-fiókjával.
4. Jelölje ki a 2. lépésben regisztrált alkalmazást, majd válassza a **Next** (Tovább), végül az **Associate** (Társítás) lehetőséget. Ezzel hozzáadja a szükséges Windows Áruházbeli regisztrációs adatokat az alkalmazásjegyzékhez.
5. A Visual Studióban kattintson a jobb gombbal a megoldásra, majd válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget. Megnyílik a **Manage NuGet Packages** (NuGet-csomagok kezelése) ablak.
6. A keresőmezőben adja meg a **WindowsAzure.Messaging.Managed** kifejezést, válassza az **Install** (Telepítés) lehetőséget, és fogadja el a használati feltételeket.
   
    ![A NuGet-csomagok kezelése ablak][20]
   
    A művelet a [Microsoft.Azure.NotificationHubs NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs) használatával letölti és telepíti a Windows rendszerhez készült Azure Notification Hubs kódtárat, és hozzáad egy rá mutató referenciát is.

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
    >* Továbbá cserélje le a kapcsolati sztring helyőrzőjét az egyik előző szakaszban az értesítési központ **Hozzáférési szabályzatok** oldaláról beszerzett **DefaultListenSharedAccessSignature** kapcsolati sztringre.
   > 
   > 
5. Az App.xaml.cs fájlban található **OnLaunched** eseménykezelő tetején adja hozzá a következő hívást az új **InitNotificationsAsync** metódushoz:
   
        InitNotificationsAsync();
   
    Ez a művelet biztosítja azt, hogy a csatorna URI azonosítója legyen regisztrálva az értesítési központban az alkalmazás minden indításakor.

6. Az alkalmazás futtatásához nyomja le az **F5** billentyűt. Megjelenik egy párbeszédpanel a regisztrációs kulccsal. A párbeszédpanel bezárásához kattintson az **OK** gombra. 

    ![A regisztráció sikerült](./media/notification-hubs-windows-store-dotnet-get-started/registration-successful.png)

Az alkalmazás készen áll bejelentési értesítéseket fogadni.

## <a name="send-test-notifications"></a>Tesztértesítések küldése
Ha gyorsan le szeretné tesztelni, hogyan fogadja az alkalmazás az értesítéseket, értesítéseket küldhet az [Azure Portalról](https://portal.azure.com/). 

1. Az Azure Portalon váltson az Áttekintés lapra, majd kattintson az eszköztár **Tesztküldés** gombjára.     

    ![Tesztküldés gomb](./media/notification-hubs-windows-store-dotnet-get-started/test-send-button.png)
2. A **Tesztküldés** ablakban hajtsa végre az alábbi műveleteket: 
    1. A **Platformok** beállításnál válassza a **Windows** lehetőséget.
    2. Az **Értesítés típusa** beállításnál válassza a **Bejelentés** lehetőséget. 
    3. Kattintson a **Küldés** gombra. 
    
        ![A Tesztküldés panel](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)
3. A Küldés művelet eredményét az ablak alján található **Eredmény** listában láthatja. Emellett egy figyelmeztető üzenet is megjelenik. 

    ![A Küldés művelet eredménye](./media/notification-hubs-windows-store-dotnet-get-started/result-of-send.png)
1. Az asztalon megjelenik egy értesítés, amely a **Tesztüzenet** szöveget tartalmazza. 

    ![Értesítési üzenet](./media/notification-hubs-windows-store-dotnet-get-started/test-notification-message.png)


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban szórásos értesítéseket küldött az összes Windows rendszerű eszközre a portál vagy egy konzolalkalmazás használatával. Ha szeretné megtudni, hogy hogyan küldhet leküldéses értesítéseket adott eszközökre, lépjen tovább a következő oktatóanyagra: 

> [!div class="nextstepaction"]
>[Leküldéses értesítések küldése adott eszközökre](
notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)


<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->

[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md

[toast catalog]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[tile catalog]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[badge overview]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx
 
