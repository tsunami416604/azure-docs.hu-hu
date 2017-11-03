---
title: "Leküldéses értesítések hozzáadása az univerzális Windows Platform (UWP-) alkalmazás |} Microsoft Docs"
description: "Ismerje meg az Azure App Service Mobile Apps és az Azure Notification Hubs használata leküldéses értesítések küldéséhez az univerzális Windows Platform (UWP) alkalmazást."
services: app-service\mobile,notification-hubs
documentationcenter: windows
author: ysxu
manager: syntaxc4
editor: 
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
ms.openlocfilehash: a14bb0320c1f6a563f766a6a0fad5cf556fe7b70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="add-push-notifications-to-your-windows-app"></a>Leküldéses értesítések hozzáadása a Windows-alkalmazás
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Áttekintés
Ebben az oktatóanyagban leküldéses értesítések hozzáadása a [Windows gyors üzembe helyezési](app-service-mobile-windows-store-dotnet-get-started.md) projektre, hogy egy leküldéses értesítést küld az eszköz minden alkalommal, amikor egy olyan rekordot csatlakoztatva van.

Ha nem használja a letöltött gyors üzembe helyezési kiszolgálóprojektet, szüksége lesz a leküldéses értesítési kiterjesztési csomagot. Lásd: [használható a .NET-háttérrendszer server SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) további információt.

## <a name="configure-hub"></a>Egy értesítési központ konfigurálása
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Alkalmazás regisztrálása leküldéses értesítésekhez
Küldje el a Windows áruház az alkalmazás, akkor a kiszolgáló projekt integrálhatja a Windows értesítési szolgáltatások (WNS) leküldéses küldendő konfigurálásához kell.

1. A Visual Studio Solution Explorerben kattintson a jobb gombbal az UWP-alkalmazásprojektet, kattintson a **tároló** > **az áruház alkalmazás társítása...** .

    ![Windows Áruházbeli alkalmazás társítása](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)
2. A varázslóban kattintson **következő**, jelentkezzen be Microsoft-fiókjával, adjon meg egy nevet az alkalmazáshoz a **lefoglalni egy új alkalmazás neve**, majd kattintson a **tartalék**.
3. Az alkalmazás-regisztráció sikeres létrehozása után válassza ki az új alkalmazás nevére, kattintson a **következő**, és kattintson a **társítása**. Ezzel hozzáadja a szükséges Windows Áruházbeli regisztrációs adatokat az alkalmazásjegyzékhez.  
4. Keresse meg a [Windows fejlesztői központ](https://dev.windows.com/en-us/overview)Bejelentkezés Microsoft-fiókjával, kattintson az új alkalmazás regisztrációs **alkalmazásaimat**, majd bontsa ki a **szolgáltatások**  >   **Leküldéses értesítések**.
5. Az a **leküldéses értesítések** kattintson **Live Services webhely** alatt **Microsoft Azure Mobile Services**.
6. A regisztrációs lapon jegyezze fel az érték a **alkalmazás titkos kulcsok** és a **CSOMAGAZONOSÍTÓT**, amely ezután használhatja a mobil-háttéralkalmazás konfigurálása.

    ![Windows Áruházbeli alkalmazás társítása](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > A titkos ügyfélkulcs és a csomag biztonsági azonosítója fontos biztonsági hitelesítő adatok. Ezeket az értékeket ne ossza meg senkivel, és ne terjessze az alkalmazással. A **alkalmazásazonosító** Microsoft Account hitelesítés konfigurálása a titkos kulcs használatos.
   >
   >

## <a name="configure-the-backend-to-send-push-notifications"></a>Leküldéses értesítések küldéséhez a háttérkiszolgáló beállítása
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>Leküldéses értesítések küldéséhez a kiszolgáló frissítése
Kövesse az alábbi eljárást a háttéralkalmazás-projekt típusának megfelelő&mdash;vagy [.NET-háttérrendszer](#dotnet) vagy [Node.js-háttéralkalmazáshoz](#nodejs).

### <a name="dotnet"></a>.NET-háttéralkalmazás-projekt
1. A Visual Studióban, kattintson a jobb gombbal a projekt, és kattintson a **NuGet-csomagok kezelése**, Microsoft.Azure.NotificationHubs keresni, majd kattintson a **telepítése**. Ez telepíti a Notification Hubs ügyféloldali kódtárára.
2. Bontsa ki a **tartományvezérlők**, nyissa meg a TodoItemController.cs, és adja hozzá a következő using utasításokat:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;
3. Az a **PostTodoItem** módszer, vegye fel a következő kód hívása után **InsertAsync**:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create the notification hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Define a WNS payload
        var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
                                + item.Text + @"</text></binding></visual></toast>";
        try
        {
            // Send the push notification.
            var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Ezt a kódot az értesítési központnak a leküldéses értesítés küldése után egy új cikk beszúrási jelzi.
4. A projekt közzé.

### <a name="nodejs"></a>NODE.js háttérrendszer-projekt
1. Ha még nem tette meg, [a gyorsútmutató-projekt letöltése](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) vagy más használja a [az Azure portálon online szerkesztő](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Cserélje le a meglévő kódot a todoitem.js fájlban a következő:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');

        var table = azureMobileApps.table();

        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK,
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');

        // Define the WNS payload that contains the new item Text.
        var payload = "<toast><visual><binding template=\ToastText01\><text id=\"1\">"
                                    + context.item.text + "</text></binding></visual></toast>";

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a WNS native toast notification.
                    context.push.wns.sendToast(null, payload, function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.info('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
        });

        module.exports = table;

    Ez egy új teendőelemet behelyezésekor a item.text tartalmazó WNS bejelentési értesítést küld.
3. A fájlt a helyi számítógépen szerkesztésekor közzé a projekt.

## <a id="update-app"></a>Leküldéses értesítések hozzáadása az alkalmazáshoz
Az alkalmazás ezután a leküldéses értesítések indításkor kell regisztrálni. Ha a hitelesítés már engedélyezve van, győződjön meg arról, hogy a felhasználó bejelentkezik a leküldéses értesítések regisztrálása előtt.

1. Nyissa meg a **App.xaml.cs** le a fájlt, és adja hozzá a következő `using` utasításokat:

        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
2. Ugyanebben a fájlban adja hozzá a következő **InitNotificationsAsync** metódusdefiníciót is az **App** osztály:

        private async Task InitNotificationsAsync()
        {
            // Get a channel URI from WNS.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register the channel URI with Notification Hubs.
            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }

    Ez a kód lekéri az alkalmazás ChannelURI a wns-ből, és majd regisztrálja a ChannelURI az App Service Mobile Apps.
3. Tetején a **OnLaunched** eseménykezelő a **App.xaml.cs**, adja hozzá a **aszinkron** módosító metódus definition, és adja hozzá a következő hívást az új  **InitNotificationsAsync** metódust, az alábbi példában látható módon:

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

            // ...
        }

    Ez biztosítja, hogy, hogy a rövid élettartamú ChannelURI regisztrálva van-e az alkalmazás minden indításakor.
4. Az UWP-alkalmazás projekt újraépítése. Az alkalmazás készen áll bejelentési értesítéseket fogadni.

## <a id="test"></a>Teszt leküldéses értesítések az alkalmazásban
[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>Következő lépések
További tudnivalók a leküldéses értesítések:

* [A felügyelt ügyfelek használata az Azure Mobile Apps-alkalmazásokhoz](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications)  
  Sablonok rugalmasan, a platformok közötti leküldéses értesítések és honosított leküldéses értesítések küldését. Ismerje meg, hogyan kell regisztrálni a sablonokat.
* [Leküldéses értesítési eseményadatokat](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Oka különböző miért kerülhetnek vagy értesítések nem végül az eszközökön. Ez a témakör bemutatja, hogyan elemezheti és mérje fel, az alapvető ok leküldéses értesítés sikertelen.

Vegye figyelembe, hogy valamelyik az alábbi oktatóanyagok folytatása:

* [Hitelesítés hozzáadása az alkalmazáshoz](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Ismerje meg, hogyan hitelesítheti az alkalmazás felhasználóit egy identitásszolgáltatóval.
* [Az offline szinkronizálás engedélyezése az alkalmazás számára](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Ismerje meg, hogyan adhat offline támogatást alkalmazásához egy Mobile Apps-háttéralkalmazás segítségével. A kapcsolat nélküli szinkronizálás a mobilalkalmazást használó végfelhasználók számára lehetővé teszi &mdash;az adatok megtekintését, hozzáadását és módosítását&mdash; akkor is, ha nincs hálózati kapcsolat.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
