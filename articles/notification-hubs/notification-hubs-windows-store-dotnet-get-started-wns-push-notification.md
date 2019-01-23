---
title: Értesítések küldése az Univerzális Windows-platformon alapuló alkalmazásoknak az Azure Notification Hubs használatával | Microsoft Docs
description: Ebből az oktatóanyagból elsajátíthatja, hogyan használható az Azure Notification Hubs leküldéses értesítések univerzális platformon futó Windows-alkalmazásra történő küldéséhez.
services: notification-hubs
documentationcenter: windows
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: cf307cf3-8c58-4628-9c63-8751e6a0ef43
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: jowargo
ms.openlocfilehash: 17c5a4445eca0fb05fb72281dd400d896ad3a090
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54446000"
---
# <a name="tutorial-send-notifications-to-universal-windows-platform-apps-by-using-azure-notification-hubs"></a>Oktatóanyag: Értesítések küldéséhez univerzális Windows-Platformos alkalmazások az Azure Notification Hubs használatával

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

- **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) megkezdése előtt.
- [Microsoft Visual Studio Community 2015 vagy újabb](https://www.visualstudio.com/products/visual-studio-community-vs).
- [UWP-alkalmazások telepített fejlesztőeszközei](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)
- Aktív Windows Áruházbeli fiók

Ennek az oktatóanyagnak az elvégzése előfeltétel minden további, UWP-alkalmazásokkal kapcsolatos Notification Hubs-oktatóanyag elvégzéséhez.

## <a name="create-an-app-in-windows-store"></a>Alkalmazás létrehozása a Windows Áruházban

A leküldéses értesítések UWP-alkalmazásokba irányuló küldéséhez társítsa az alkalmazást a Windows Áruházhoz. Ezután konfigurálja az értesítési központot a WNS-sel való integrációra.

1. Nyissa meg a [Windows fejlesztőközpontját](https://dev.windows.com/overview), jelentkezzen be Microsoft-fiókjával, majd kattintson a **Create a new app** (Új alkalmazás létrehozása) gombra.

    ![Új alkalmazás gomb](./media/notification-hubs-windows-store-dotnet-get-started/windows-store-new-app-button.png)
2. Írja be az alkalmazás nevét, majd válassza a **Reserve product name** (Terméknév lefoglalása) lehetőséget. Ezzel létrehoz egy új Windows Áruházbeli regisztrációt az alkalmazás számára.

    ![Áruházbeli alkalmazásnév](./media/notification-hubs-windows-store-dotnet-get-started/store-app-name.png)
3. Bontsa ki a **Alkalmazáskezelés**válassza **WNS/MPNS**, majd válassza ki **Live Services webhely**. Jelentkezzen be Microsoft-fiókjába. Megnyílik az **Application Registration Portal** (Alkalmazásregisztrációs portál) egy új lapon. Másik lehetőségként közvetlenül is megnyithatja [Application Registration Portal](https://apps.dev.microsoft.com) (Alkalmazásregisztrációs portált) lapját az alkalmazás nevére kattintva.

    ![A WNS/MPNS lap](./media/notification-hubs-windows-store-dotnet-get-started/wns-mpns-page.png)
4. Jegyezze fel az **Application Secret** (Alkalmazáskulcs) jelszót és a **Package security identifier (SID)** (Csomag biztonsági azonosítója (SID)) értékét.

    >[!WARNING]
    >Az alkalmazáskulcs és a csomag biztonsági azonosítója fontos biztonsági hitelesítő adatok. Ezeket az értékeket ne ossza meg senkivel, és ne terjessze az alkalmazással.

## <a name="create-a-notification-hub"></a>Értesítési központ létrehozása

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-wns-settings-for-the-hub"></a>A központ WNS-beállításainak konfigurálása

1. Az a **értesítési beállítások** kategória, jelölje be **Windows (WNS)**.
2. Adja meg a **Package SID** (Csomag biztonsági azonosítója) és a **Security Key** (Biztonsági kulcs) előző szakaszban feljegyzett értékét.
3. Kattintson az eszköztár **Mentés** elemére.

    ![A Csomag biztonsági azonosítója és a Biztonsági kulcs mezők](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Ezzel konfigurálta az értesítési központot a WNS-sel folytatott együttműködésre, és rendelkezésére állnak a kapcsolati sztringek az alkalmazás regisztrálásához és értesítések küldéséhez.

## <a name="create-a-sample-windows-app"></a>Windows-mintaalkalmazás létrehozása

1. A Visual Studióban nyissa meg a **fájl** menüjében válassza **új**, majd válassza ki **projekt**.
2. Az a **új projekt** párbeszédpanelen a következő lépéseket:

    1. Bontsa ki a **Visual C#** elemet.
    2. Válassza a **Windows Universal** (Univerzális Windows-platform) lehetőséget.
    3. Válassza a **Blank App (Universal Windows)** (Üres alkalmazás (Univerzális Windows-platform)) elemet.
    4. Adjon meg a projekt **nevét**.
    5. Kattintson az **OK** gombra.

        ![New Project (Új projekt) párbeszédpanel](./media/notification-hubs-windows-store-dotnet-get-started/new-project-dialog.png)
3. A **target** (cél) és a **minimum** (minimális) platformverzió esetében fogadja el az alapértelmezett értékeket, majd kattintson az **OK** gombra.
4. A Megoldáskezelőben kattintson a jobb gombbal a Windows Áruházbeli alkalmazás projektjére, válassza a **Store** (Áruház), majd az **Associate App with the Store** (Alkalmazás társítása az Áruházzal) lehetőséget. Megjelenik az **Associate Your App with the Windows Store** (Alkalmazás társítása a Windows Áruházzal) varázsló.
5. A varázslóban jelentkezzen be Microsoft-fiókjával.
6. Jelölje ki a 2. lépésben regisztrált alkalmazást, majd válassza a **Next** (Tovább), végül az **Associate** (Társítás) lehetőséget. Ezzel hozzáadja a szükséges Windows Áruházbeli regisztrációs adatokat az alkalmazásjegyzékhez.
7. A Visual Studióban kattintson a jobb gombbal a megoldásra, majd válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget. Megnyílik a **Manage NuGet Packages** (NuGet-csomagok kezelése) ablak.
8. A keresőmezőben adja meg a **WindowsAzure.Messaging.Managed** kifejezést, válassza az **Install** (Telepítés) lehetőséget, és fogadja el a használati feltételeket.

    ![A NuGet-csomagok kezelése ablak][20]

    A művelet a [Microsoft.Azure.NotificationHubs NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs) használatával letölti és telepíti a Windows rendszerhez készült Azure Notification Hubs kódtárat, és hozzáad egy rá mutató referenciát is.
9. Nyissa meg a `App.xaml.cs` fájl projektre, majd adja hozzá az alábbi utasításokat:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.UI.Popups;
    ```

10. A projekt `App.xaml.cs` fájlt, keresse meg a `App` osztályt, és adja hozzá a következő `InitNotificationsAsync` metódusdefiníciót:

    ```csharp
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
    ```

    Ez a kód lekéri a csatorna URI azonosítóját az alkalmazás számára a WNS-ből, majd regisztrálja a csatorna URI azonosítóját az értesítési központban.

    >[!NOTE]
    > Cserélje le a `hub name` helyőrzőt az értesítési központ Azure Portalon megjelenő nevére. Továbbá cserélje le a kapcsolati karakterlánc helyőrzőjét az `DefaultListenSharedAccessSignature` beszerzett kapcsolati karakterláncot a **hozzáférési szabályzatok** az előző szakaszban az értesítési központ lapján.

11. Felső részén a `OnLaunched` eseménykezelővel `App.xaml.cs`, adja hozzá a következő hívást az új `InitNotificationsAsync` módszer:

    ```csharp
    InitNotificationsAsync();
    ```

    Ez a művelet biztosítja, hogy a csatorna URI azonosítója legyen regisztrálva az értesítési központ minden alkalommal, amikor az alkalmazás elindul.

12. Az alkalmazás futtatásához nyomja le a billentyűzet **F5** kulcsot. A regisztrációs kulcsot tartalmazó párbeszédpanel jelenik meg. A párbeszédpanel bezárásához kattintson a **OK**.

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
4. Az értesítési üzenet jelenik meg: **Tesztüzenet** az asztalon.

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
