---
title: Leküldéses értesítések hozzáadása az UWP alkalmazáshoz
description: Megtudhatja, hogy az Azure App Service Mobile Apps és az Azure Notification Hubs használatával hogyan küldhet leküldéses értesítéseket az univerzális Windows-platform (UWP) alkalmazásnak.
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 6f200e9649a00bfe890d46f86e62404f1a7e844f
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366292"
---
# <a name="add-push-notifications-to-your-windows-app"></a>Leküldéses értesítések hozzáadása a Windows-alkalmazáshoz

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban leküldéses értesítéseket ad hozzá a [Windows gyorsindítási](app-service-mobile-windows-store-dotnet-get-started.md) projektjéhez, így a rendszer leküldéses értesítést küld az eszköznek minden alkalommal, amikor egy rekordot beszúr.

Ha nem használja a letöltött gyorsindítási kiszolgálóprojektet, szüksége lesz a leküldéses értesítésbővítmény csomagra. További [információ: A .NET háttérkiszolgáló SDK-ja az Azure Mobile Apps alkalmazáshoz](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) című témakörben található.

## <a name="configure-a-notification-hub"></a><a name="configure-hub"></a>Értesítési központ konfigurálása

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Alkalmazás regisztrálása leküldéses értesítésekhez

El kell küldenie az alkalmazást a Microsoft Store-ba, majd úgy kell konfigurálnia a kiszolgálóprojektet, hogy integrálódjon a [Windows leküldéses értesítési szolgáltatásokkal (WNS)](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) a leküldéses küldéshez.

1. A Visual Studio Solution Explorer programban kattintson a jobb gombbal az UWP alkalmazásprojektre, és kattintson az **Áruházbeli** > **alkalmazás és az áruház társítása parancsra...**.

    ![Alkalmazás társítása a Microsoft Store-hoz](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)

2. A varázslóban kattintson a **Tovább**gombra, jelentkezzen be a Microsoft-fiókjával, írja be az alkalmazás nevét az **Új alkalmazás nevének lefoglalása**mezőbe, majd kattintson a **Foglalás**gombra.
3. Az alkalmazásregisztráció sikeres létrehozása után jelölje ki az új alkalmazás nevét, kattintson a **Tovább**gombra, majd a **Társítás gombra.** Ez hozzáadja a szükséges Microsoft Store regisztrációs adatokat az alkalmazásjegyzékhez.
4. Nyissa meg az [Alkalmazásregisztrációs portált,](https://apps.dev.microsoft.com/) és jelentkezzen be Microsoft-fiókjával. Kattintson az előző lépésben társított Windows Áruházbeli alkalmazásra.
5. A regisztrációs lapon jegyezze fel az **alkalmazástitkos kulcsok** és a **csomag biztonsági azonosítója**alatti értéket, amelyet a következő mobilalkalmazás-háttérkapcsolat konfigurálásához fog használni.

    ![Alkalmazás társítása a Microsoft Store-hoz](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > A titkos ügyfélkulcs és a csomag biztonsági azonosítója fontos biztonsági hitelesítő adatok. Ezeket az értékeket ne ossza meg senkivel, és ne terjessze az alkalmazással. Az **alkalmazásazonosító** a microsoftos fiók hitelesítésének konfigurálásához használatos.

[Az App Center](https://docs.microsoft.com/appcenter/sdk/push/uwp#prerequisite---register-your-app-for-windows-notification-services-wns) utasításokat is tartalmaz az UWP-alkalmazások leküldéses értesítésekhez való konfigurálásához.

## <a name="configure-the-backend-to-send-push-notifications"></a>A háttérkapcsolat beállítása leküldéses értesítések küldésére

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a name="update-the-server-to-send-push-notifications"></a><a id="update-service"></a>A kiszolgáló frissítése leküldéses értesítések küldéséhez

Az alábbi eljárást használja, amely&mdash;megfelel a háttérprojekt [Node.js backend](#nodejs)típusának [.](#dotnet)

### <a name="net-backend-project"></a><a name="dotnet"></a>.NET háttérprojekt

1. A Visual Studio jobb gombbal kattintson a kiszolgálóprojektre, és válassza **a NuGet csomagok kezelése menüpontmenüt,** keresse meg a Microsoft.Azure.NotificationHubs kifejezést, majd kattintson a **Telepítés parancsra.** Ezzel telepíti az értesítési központok ügyfélkönyvtárát.
2. Bontsa ki **a Vezérlők csomópontot,** nyissa meg TodoItemController.cs, és adja hozzá a következőket a utasítások használatával:

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. A **PostTodoItem** metódusban adja hozzá a következő kódot az **InsertAsync**hívása után:

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

    Ez a kód arra utasítja az értesítési központot, hogy küldjön leküldéses értesítést egy új elem beszúrása után.

4. Tegye közzé újra a kiszolgálóprojektet.

### <a name="nodejs-backend-project"></a><a name="nodejs"></a>Node.js háttérprojekt
1. Állítsa be a háttérprojektet.
2. Cserélje le a todoitem.js fájlban lévő kódot a következőkre:

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see https://aka.ms/nodejshubs
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

    Ez egy WNS bejelentési értesítést küld, amely az item.text fájlt tartalmazza, amikor új teendőt szúr be.

3. Amikor a helyi számítógépen szerkeszti a fájlt, tegye közzé újra a kiszolgálóprojektet.

## <a name="add-push-notifications-to-your-app"></a><a id="update-app"></a>Leküldéses értesítések hozzáadása az alkalmazáshoz
Ezután az alkalmazásnak regisztrálnia kell a leküldéses értesítésekre az indításkor. Ha már engedélyezte a hitelesítést, győződjön meg arról, hogy a felhasználó bejelentkezik, mielőtt megpróbálna regisztrálni a leküldéses értesítésekre.

1. Nyissa **meg** a App.xaml.cs projektfájlt, és adja hozzá a következő `using` állításokat:

    ```csharp
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    ```

2. Ugyanebben a fájlban adja hozzá a következő **InitNotificationsAsync** metódusdefiníciót az **alkalmazásosztályhoz:**

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

    Ez a kód lekéri az alkalmazás ChannelURI-ját a WNS-ből, majd regisztrálja a ChannelURI-t az App Service Mobile App alkalmazással.

3. A **App.xaml.cs** **OnLaunched** eseménykezelőjének tetején adja hozzá az **aszinkron** módosítót a metódusdefinícióhoz, és adja hozzá a következő hívást az új **InitNotificationsAsync** metódushoz, mint például a következő példában:

    ```csharp
    protected async override void OnLaunched(LaunchActivatedEventArgs e)
    {
        await InitNotificationsAsync();

        // ...
    }
    ```

    Ez garantálja, hogy a rövid életű ChannelURI regisztrálva van minden alkalommal, amikor az alkalmazás elindul.

4. Építse újra az UWP-alkalmazásprojektet. Az alkalmazás készen áll bejelentési értesítéseket fogadni.

## <a name="test-push-notifications-in-your-app"></a><a id="test"></a>Leküldéses értesítések tesztelése az alkalmazásban

[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a name="next-steps"></a><a id="more"></a>További lépések

További információ a leküldéses értesítésekről:

* [A felügyelt ügyfél használata az Azure Mobile Apps alkalmazásokhoz](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications) A sablonok rugalmasságot biztosítanak a platformfüggetlen leküldések és a honosított leküldések küldéséhez. További információ a sablonok regisztrálásáról.
* [Leküldéses értesítési problémák diagnosztizálása](../notification-hubs/notification-hubs-push-notification-fixer.md) Számos oka lehet annak, hogy az értesítések megszakadhatnak, vagy nem kerülnek az eszközökre. Ez a témakör bemutatja, hogyan elemezheti és találhatja ki a leküldéses értesítési hibák kiváltó okát.

Fontolja meg a folytatást az alábbi oktatóanyagok egyikén:

* [Hitelesítés hozzáadása alkalmazáshoz](app-service-mobile-windows-store-dotnet-get-started-users.md) Ismerje meg, hogyan hitelesítheti az alkalmazás felhasználóit egy identitásszolgáltatóval.
* [Offline szinkronizálás engedélyezése az alkalmazáshoz](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) További információ arról, hogyan adhat hozzá offline támogatást az alkalmazáshoz mobilalkalmazás-háttérrendszer használatával. A kapcsolat nélküli szinkronizálás a mobilalkalmazást használó végfelhasználók számára lehetővé teszi &mdash;az adatok megtekintését, hozzáadását és módosítását&mdash; akkor is, ha nincs hálózati kapcsolat.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
