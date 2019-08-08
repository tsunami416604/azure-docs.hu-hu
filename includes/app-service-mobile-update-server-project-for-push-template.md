---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 675ad278cb8bdc0ced4eff3bd77572f44c9808fc
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857405"
---
Ebben a szakaszban a meglévő Mobile Apps háttér-projektben szereplő kódot frissíti egy leküldéses értesítés elküldéséhez minden alkalommal, amikor új elem kerül felvételre. Ezt a folyamatot az Azure Notification Hubs [sablon](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) funkciója működteti, amely lehetővé teszi a platformok közötti leküldést. A különböző ügyfelek regisztrálva vannak a leküldéses értesítésekhez a sablonok használatával, és egyetlen univerzális leküldéses lehetőség az összes ügyfél-platformon elérhető.

Válassza ki az alábbi eljárások egyikét, amely megfelel a háttérben futó projektnek&mdash;, vagy a [.net back end](#dotnet) vagy a [Node. js háttérrendszer](#nodejs).

### <a name="dotnet"></a>.NET háttérrendszer-projekt

1. A Visual Studióban kattintson a jobb gombbal a kiszolgálói projektre. Ezután válassza a **NuGet-csomagok kezelése**lehetőséget. Keresse meg `Microsoft.Azure.NotificationHubs`a elemet, majd válassza a **telepítés**lehetőséget. Ez a folyamat telepíti a Notification Hubs könyvtárat a háttérbeli értesítések küldéséhez.
2. A kiszolgálói projektben nyissa meg a **vezérlők** > **TodoItemController.cs**. Ezután adja hozzá a következő using utasításokat:

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

    Ez a folyamat az adott tételt tartalmazó sablon-értesítést küld. Új elemek beszúrásakor megjelenő szöveg

4. A kiszolgálói projekt ismételt közzététele.

### <a name="nodejs"></a>Node. js háttérrendszer-projekt

1. Állítsa be a háttér-projektet.
2. Cserélje le a todoitem. js fájl meglévő kódját a következő kódra:

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

    Ez a folyamat olyan sablon-értesítést küld, amely tartalmazza az item. Text karakterláncot új elemek beszúrásakor.

3. Amikor szerkeszti a fájlt a helyi számítógépen, tegye közzé újra a kiszolgálói projektet.
