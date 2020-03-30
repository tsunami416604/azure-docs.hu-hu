---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 675ad278cb8bdc0ced4eff3bd77572f44c9808fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68857405"
---
Ebben a szakaszban a meglévő mobilalkalmazások háttérprojektjének kódját frissíti, hogy minden alkalommal leküldéses értesítést küldjön, amikor új elemet ad hozzá. Ezt a folyamatot az Azure Notification Hubs [sablonszolgáltatása](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) működteti, amely lehetővé teszi a platformok közötti leküldéseket. A különböző ügyfelek sablonok használatával vannak regisztrálva a leküldéses értesítésekhez, és egyetlen univerzális leküldéses elérhető az összes ügyfélplatformra.

Válasszon az alábbi eljárások közül, amelyek&mdash;megfelelnek a háttérprojekt [Node.js back end](#nodejs) [típusának.](#dotnet)

### <a name="net-back-end-project"></a><a name="dotnet"></a>.NET háttérprojekt

1. A Visual Studio programban kattintson a jobb gombbal a kiszolgálóprojektre. Ezután válassza **a NuGet csomagok kezelése**lehetőséget. Keresse `Microsoft.Azure.NotificationHubs`meg a t, és válassza a **Telepítés**lehetőséget. Ez a folyamat telepíti az értesítési központok könyvtárat az értesítések háttérrendszerről történő küldéséhez.
2. A kiszolgálóprojektben nyissa meg a **Vezérlők** > **TodoItemController.cs.** Ezután adja hozzá a következőket az utasítások használatával:

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

    // Get the Notification Hubs credentials for the mobile app.
    string notificationHubName = settings.NotificationHubName;
    string notificationHubConnection = settings
        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

    // Create a new Notification Hub client.
    NotificationHubClient hub = NotificationHubClient
    .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

    // Send the message so that all template registrations that contain "messageParam"
    // receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string,string> templateParams = new Dictionary<string,string>();
    templateParams["messageParam"] = item.Text + " was added to the list.";

    try
    {
        // Send the push notification and log the results.
        var result = await hub.SendTemplateNotificationAsync(templateParams);

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

    Ez a folyamat sablonértesítést küld, amely tartalmazza az elemet. Szöveg új elem beszúrásakor.

4. Tegye közzé újra a kiszolgálóprojektet.

### <a name="nodejs-back-end-project"></a><a name="nodejs"></a>Node.js háttérprojekt

1. Állítsa be a háttérprojektet.
2. Cserélje le a todoitem.js fájlban lévő kódot a következő kódra:

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see https://aka.ms/nodejshubs.
    logger.info('Running TodoItem.insert');

    // Define the template payload.
    var payload = '{"messageParam": "' + context.item.text + '" }';  

    // Execute the insert. The insert returns the results as a promise.
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured.
            if (context.push) {
                // Send a template notification.
                context.push.send(null, payload, function (error) {
                    if (error) {
                        logger.error('Error while sending push notification: ', error);
                    } else {
                        logger.info('Push notification sent successfully!');
                    }
                });
            }
            // Don't forget to return the results from the context.execute().
            return results;
        })
        .catch(function (error) {
            logger.error('Error while running context.execute: ', error);
        });
    });

    module.exports = table;  
    ```

    Ez a folyamat sablonértesítést küld, amely új elem beszúrásakor tartalmazza az item.text fájlt.

3. Amikor a helyi számítógépen szerkeszti a fájlt, tegye közzé újra a kiszolgálóprojektet.
