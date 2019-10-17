---
title: Leküldéses értesítések hozzáadása a Univerzális Windows-platform (UWP) alkalmazáshoz | Microsoft Docs
description: Megtudhatja, hogyan küldhet leküldéses értesítéseket a Univerzális Windows-platform (UWP) alkalmazásnak Azure App Service Mobile Apps és Azure Notification Hubs használatával.
services: app-service\mobile,notification-hubs
documentationcenter: windows
author: elamalani
manager: crdun
editor: ''
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 80723bc8e7685c66117e71d87cbf64710ab4507a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388631"
---
# <a name="add-push-notifications-to-your-windows-app"></a>Leküldéses értesítések hozzáadása a Windows-alkalmazáshoz

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> A Visual Studio App Center támogatja a végpontok közötti, valamint az integrált szolgáltatásközpont és a mobilalkalmazás közötti fejlesztést. A fejlesztők **buildelési**, **tesztelési** és **elosztási** szolgáltatásokkal állíthatják be a folyamatos integrációval és szolgáltatásnyújtással kapcsolatos folyamatot. Az alkalmazás üzembe helyezése után a fejlesztők **elemzési** és **diagnosztikai** szolgáltatásokkal monitorozhatják az alkalmazás állapotát és használatát, illetve **leküldéses** szolgáltatással kommunikálhatnak a felhasználókkal. Emellett a fejlesztők a **Hitelesítés** szolgáltatással hitelesíthetik felhasználóikat, az **Adatok** szolgáltatással pedig megőrizhetik és szinkronizálhatják az alkalmazásadatokat a felhőben.
>
> Ha szeretné integrálni a Cloud Servicest a mobil alkalmazásban, regisztráljon [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban leküldéses értesítéseket ad hozzá a [Windows gyors üzembe helyezési](app-service-mobile-windows-store-dotnet-get-started.md) projekthez, hogy a rendszer minden egyes rekord beszúrásakor leküldéses értesítést küldjön az eszköznek.

Ha nem a letöltött gyors üzembe helyezési kiszolgáló projektet használja, szüksége lesz a leküldéses értesítési bővítmény csomagra. További információért lásd: [Az Azure-hoz készült .net backend Server SDK használata Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

## <a name="configure-hub"></a>Értesítési központ konfigurálása

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Alkalmazás regisztrálása leküldéses értesítésekhez

Be kell küldenie az alkalmazást a Microsoft Storeba, majd konfigurálnia kell a kiszolgálói projektet, hogy integrálható legyen a [Windows Notification Services (WNS)](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) szolgáltatással a leküldéses küldéshez.

1. A Visual Studio Megoldáskezelő kattintson a jobb gombbal a UWP alkalmazás-projektre, majd kattintson az **áruház** > **alkalmazás hozzárendelése az áruházhoz..** . elemre.

    ![Alkalmazás hozzárendelése Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)

2. A varázslóban kattintson a **tovább**gombra, jelentkezzen be a Microsoft-fiókba, írja be az alkalmazás nevét a **foglaláshoz**, és kattintson a **tartalék**elemre.
3. Az alkalmazás regisztrációjának sikeres létrehozása után jelölje ki az új alkalmazás nevét, kattintson a **tovább**, majd a **hozzárendelés**elemre. Ezzel hozzáadja a szükséges Microsoft Store regisztrációs adatokat az alkalmazás-jegyzékfájlhoz.
4. Navigáljon az [alkalmazás regisztrációs portálra](https://apps.dev.microsoft.com/) , és jelentkezzen be a Microsoft-fiók. Kattintson az előző lépésben társított Windows áruházbeli alkalmazásra.
5. A regisztráció lapon jegyezze fel az **alkalmazás titkai** és a **csomag biztonsági azonosítója**területen található értéket, amelyet a következő lépésben fog használni a Mobile apps-háttér konfigurálásához.

    ![Alkalmazás hozzárendelése Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > A titkos ügyfélkulcs és a csomag biztonsági azonosítója fontos biztonsági hitelesítő adatok. Ezeket az értékeket ne ossza meg senkivel, és ne terjessze az alkalmazással. A Microsoft-fiók hitelesítésének konfigurálásához használja a Secret ( **alkalmazás) azonosítót** .

[App Center](https://docs.microsoft.com/appcenter/sdk/push/uwp#prerequisite---register-your-app-for-windows-notification-services-wns) a UWP-alkalmazások leküldéses értesítésekhez való konfigurálására vonatkozó utasításokat is tartalmaz.

## <a name="configure-the-backend-to-send-push-notifications"></a>A háttér konfigurálása leküldéses értesítések küldéséhez

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>A kiszolgáló frissítése leküldéses értesítések küldéséhez

Használja az alábbi eljárást, amely megfelel a háttérbeli projekttípus @ no__t-0either [.net backend](#dotnet) vagy [Node. js-háttérnek](#nodejs).

### <a name="dotnet"></a>.NET-háttérbeli projekt

1. A Visual Studióban kattintson a jobb gombbal a kiszolgálói projektre, majd kattintson a **NuGet-csomagok kezelése**elemre, keresse meg a Microsoft. Azure. NotificationHubs, majd kattintson a **telepítés**gombra. Ezzel telepíti a Notification Hubs ügyféloldali függvénytárat.
2. Bontsa ki a **vezérlők**csomópontot, nyissa meg a TodoItemController.cs, és adja hozzá a következő using utasításokat:

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. A **PostTodoItem** metódusban adja hozzá a következő kódot a **InsertAsync**hívása után:

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

    Ez a kód azt jelzi, hogy az értesítési központ leküldéses értesítést küld az új elemek beszúrása után.

4. A kiszolgálói projekt ismételt közzététele.

### <a name="nodejs"></a>Node. js háttérbeli projekt
1. Állítsa be a háttér-projektet.
2. Cserélje le a todoitem. js fájl meglévő kódját a következőre:

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

    Ez egy WNS-bejelentési értesítést küld, amely tartalmazza az item. Text karakterláncot új teendő beszúrásakor.

3. Ha a helyi számítógépen szerkeszti a fájlt, tegye közzé újra a kiszolgálói projektet.

## <a id="update-app"></a>Leküldéses értesítések hozzáadása az alkalmazáshoz
Ezután az alkalmazásnak regisztrálnia kell a leküldéses értesítésekhez az indításkor. Ha már engedélyezte a hitelesítést, győződjön meg arról, hogy a felhasználó bejelentkezik, mielőtt megpróbál regisztrálni a leküldéses értesítésekre.

1. Nyissa meg a **app.XAML.cs** projektfájlt, és adja hozzá a következő `using` utasítást:

    ```csharp
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    ```

2. Ugyanebben a fájlban adja hozzá a következő **initnotificationsasync metódusdefiníciót** metódus-definíciót az **app** osztályhoz:

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

    Ez a kód lekéri az alkalmazás regisztrációban szereplő URI a WNS-ből, majd regisztrálja a regisztrációban szereplő URI a App Service Mobile alkalmazásban.

3. A **app.XAML.cs** **OnLaunched** -eseménykezelő tetején adja hozzá az **aszinkron** módosítót a metódus-definícióhoz, és adja hozzá a következő hívást az új **initnotificationsasync metódusdefiníciót** metódushoz, ahogy az az alábbi példában is látható:

    ```csharp
    protected async override void OnLaunched(LaunchActivatedEventArgs e)
    {
        await InitNotificationsAsync();

        // ...
    }
    ```

    Ez garantálja, hogy a rövid élettartamú regisztrációban szereplő URI az alkalmazás minden indításakor regisztrálva van.

4. Hozza létre újra a UWP-alkalmazás projektjét. Az alkalmazás készen áll bejelentési értesítéseket fogadni.

## <a id="test"></a>Leküldéses értesítések tesztelése az alkalmazásban

[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>Következő lépések

További információ a leküldéses értesítésekről:

* [Az Azure Mobile apps felügyelt ügyfelének használata](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications) A sablonok rugalmasságot biztosítanak a platformok közötti leküldések és honosított leküldések küldéséhez. Útmutató a sablonok regisztrálásához.
* [Leküldéses értesítésekkel kapcsolatos problémák diagnosztizálása](../notification-hubs/notification-hubs-push-notification-fixer.md) Számos oka lehet annak, hogy az értesítések miért kerülhetnek el vagy nem állnak le az eszközökön. Ez a témakör bemutatja, hogyan elemezheti és derítheti ki a leküldéses értesítések meghibásodásának okát.

Tekintse át a következő oktatóanyagok egyikét:

* [Hitelesítés hozzáadása alkalmazáshoz](app-service-mobile-windows-store-dotnet-get-started-users.md) Ismerje meg, hogyan hitelesítheti az alkalmazás felhasználóit egy identitásszolgáltatóval.
* [Offline szinkronizálás engedélyezése az alkalmazáshoz](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Ismerje meg, hogyan adhat offline támogatást alkalmazásához egy Mobile apps-háttér használatával. A kapcsolat nélküli szinkronizálás a mobilalkalmazást használó végfelhasználók számára lehetővé teszi &mdash;az adatok megtekintését, hozzáadását és módosítását&mdash; akkor is, ha nincs hálózati kapcsolat.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
