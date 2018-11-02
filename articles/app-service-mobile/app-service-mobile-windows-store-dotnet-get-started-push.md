---
title: Leküldéses értesítések hozzáadása az univerzális Windows Platform (UWP-) alkalmazáshoz |} A Microsoft Docs
description: Ismerje meg az Azure App Service Mobile Apps és az Azure Notification Hubs használata leküldéses értesítések küldéséhez univerzális Windows Platform (UWP) alkalmazásába.
services: app-service\mobile,notification-hubs
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: c137f6cfdff9d043a656783f38ec9b894e644338
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913019"
---
# <a name="add-push-notifications-to-your-windows-app"></a>Leküldéses értesítések hozzáadása Windows-alkalmazáshoz

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban a leküldéses értesítések hozzáadása az [Windows gyors üzembe helyezési](app-service-mobile-windows-store-dotnet-get-started.md) projekthez, hogy a leküldéses értesítést küld az eszköz minden alkalommal, amikor a rendszer beszúr egy rekordot.

Ha nem használja a letöltött gyorsútmutató-kiszolgálói projektet, szüksége lesz a leküldéses értesítési kiterjesztési csomag. Lásd: [használható a .NET háttérkiszolgáló-SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) további információt.

## <a name="configure-hub"></a>Egy értesítési központ konfigurálása

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Alkalmazás regisztrálása leküldéses értesítésekhez

A Microsoft Store az alkalmazás beküldése, majd konfigurálja a integrálni kell [Windows értesítési szolgáltatása (WNS)](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) leküldéses küldéséhez.

1. A Visual Studio Megoldáskezelőben kattintson a jobb gombbal az UWP-alkalmazásprojektet, kattintson a **Store** > **a Store alkalmazás hozzárendelése...** .

    ![Alkalmazás társítása a Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)

2. A varázslóban kattintson **tovább**, jelentkezzen be Microsoft-fiókjával, adjon meg egy nevet az alkalmazáshoz tartozó **foglaljon le egy új nevet az alkalmazáshoz**, majd kattintson a **tartalék**.
3. Az alkalmazásregisztráció sikeres létrehozása után válassza ki az új alkalmazás nevét, kattintson a **tovább**, és kattintson a **társítása**. Ez hozzáadja a Microsoft Store szükséges regisztrációs adatokat az alkalmazásjegyzékhez.
4. Keresse meg a [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/) , és jelentkezzen be Microsoft-fiókjával. Kattintson az előző lépésben hozzárendelt a Windows Store-alkalmazás.
5. A regisztrációs oldalon jegyezze fel az értéket **titkos alkalmazáskulcsok** és a **csomag biztonsági azonosítója**, amely ezután segítségével konfigurálhatja a mobil-háttéralkalmazás.

    ![Alkalmazás társítása a Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > A titkos ügyfélkulcs és a csomag biztonsági azonosítója fontos biztonsági hitelesítő adatok. Ezeket az értékeket ne ossza meg senkivel, és ne terjessze az alkalmazással. A **alkalmazásazonosító** Microsoft Account hitelesítésének konfigurálása a titkos kulcs szolgál.

[Az App Center](https://docs.microsoft.com/appcenter/sdk/push/uwp#prerequisite---register-your-app-for-windows-notification-services-wns) UWP-alkalmazások leküldéses értesítések konfigurálásával kapcsolatos utasításokat is tartalmaz.

## <a name="configure-the-backend-to-send-push-notifications"></a>A háttérrendszer küldhetők leküldéses értesítések konfigurálása

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>Leküldéses értesítések küldéséhez a kiszolgáló frissítése

Kövesse az alábbi eljárást, amely megfelel a háttérrendszer-projekt típusa&mdash;vagy [.NET-háttérrendszer](#dotnet) vagy [Node.js háttérrendszer](#nodejs).

### <a name="dotnet"></a>.NET-háttéralkalmazás-projekt

1. A Visual Studióban kattintson a jobb gombbal a kiszolgálói projektet, és kattintson a **NuGet-csomagok kezelése**, Microsoft.Azure.NotificationHubs kereséséhez, majd kattintson a **telepítése**. Ez telepíti a Notification Hubs ügyféloldali kódtárral.
2. Bontsa ki a **tartományvezérlők**, nyissa meg a TodoItemController.cs, és adja hozzá a következő using utasításokat:

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. Az a **PostTodoItem** metódus hívása után a következő kód hozzáadása **InsertAsync**:

    ```csharp
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
    ```

    Ez a kód arra utasítja az értesítési központot, a leküldéses értesítés küldése új elem után beszúrási.

4. Tegye közzé újra a kiszolgálói projektet.

### <a name="nodejs"></a>NODE.js háttérrendszer-projekt
1. Ha ezt még nem tette meg, [töltse le a gyorsindítási projekt](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) vagy más használatát a [online szerkesztő az Azure Portalon](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Cserélje le a meglévő kód a todoitem.js fájlban a következőket:

    ```javascript
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
    ```

    Ez, amely tartalmazza a item.text, amikor a rendszer beszúr egy új teendő elem WNS bejelentési értesítést küld.

3. Ha szerkeszti a fájlt a helyi számítógépen, tegye közzé újra a kiszolgálói projektet.

## <a id="update-app"></a>Leküldéses értesítések hozzáadása az alkalmazáshoz
Ezt követően az alkalmazásnak regisztrálnia kell a leküldéses értesítések az indításhoz. Ha a hitelesítés már engedélyezve van, győződjön meg arról, hogy a felhasználó bejelentkezik a leküldéses értesítések regisztrálása előtt.

1. Nyissa meg a **App.xaml.cs** fájl projektre, és adja hozzá a következő `using` utasításokat:

    ```csharp
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    ```

2. Ugyanebben a fájlban adja hozzá a következő **InitNotificationsAsync** metódusdefiníciót a **alkalmazás** osztály:

    ```csharp
    private async Task InitNotificationsAsync()
    {
        // Get a channel URI from WNS.
        var channel = await PushNotificationChannelManager
            .CreatePushNotificationChannelForApplicationAsync();

        // Register the channel URI with Notification Hubs.
        await App.MobileService.GetPush().RegisterAsync(channel.Uri);
    }
    ```

    Ez a kód a regisztrációban az alkalmazás lekéri a WNS-ből, és ezután regisztrálja az App Service Mobile Apps, hogy a regisztrációban.

3. Felső részén a **OnLaunched** eseménykezelővel **App.xaml.cs**, adja hozzá a **aszinkron** módosító metódus definíciójának, és adja hozzá a következő hívást az új  **InitNotificationsAsync** módot, az alábbi példában látható módon:

    ```csharp
    protected async override void OnLaunched(LaunchActivatedEventArgs e)
    {
        await InitNotificationsAsync();

        // ...
    }
    ```

    Ez garantálja, hogy a rövid élettartamú amiatt regisztrálva van-e az alkalmazás minden indításakor.

4. Építse újra az UWP-alkalmazásprojektet. Az alkalmazás készen áll bejelentési értesítéseket fogadni.

## <a id="test"></a>Teszt leküldéses értesítések az alkalmazásban

[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>Következő lépések

További információ a leküldéses értesítéseket:

* [A felügyelt ügyfelek használata az Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications) sablonok többplatformos leküldések és honosított leküldéses értesítést küldhet rugalmasságot biztosítanak. Útmutató a sablonok regisztrálásához.
* [Leküldéses értesítés eseményadatokat](../notification-hubs/notification-hubs-push-notification-fixer.md) számos okból miért értesítések kerülhetnek, vagy nem végül az eszközökön. Ez a témakör bemutatja, hogyan elemezheti, és döntse el, a leküldéses értesítési hibák okának.

Vegye figyelembe, hogy továbblépne be az alábbi oktatóanyagok egyikét:

* [Hitelesítés hozzáadása alkalmazáshoz](app-service-mobile-windows-store-dotnet-get-started-users.md) Ismerje meg, hogyan hitelesítheti az alkalmazás felhasználóit egy identitásszolgáltatóval.
* [Offline szinkronizálás engedélyezése az alkalmazás számára](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) megtudhatja, hogyan adhat offline támogatást alkalmazásához egy Mobile Apps-háttéralkalmazás segítségével. A kapcsolat nélküli szinkronizálás a mobilalkalmazást használó végfelhasználók számára lehetővé teszi &mdash;az adatok megtekintését, hozzáadását és módosítását&mdash; akkor is, ha nincs hálózati kapcsolat.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
