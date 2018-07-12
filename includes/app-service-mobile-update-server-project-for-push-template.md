Ebben a szakaszban frissítse kód a meglévő Mobile Apps háttér-projekthez, hogy a leküldéses értesítés küldése, amikor új elem kerül. Ez a folyamat működteti a [sablon](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) funkció lehetővé teszi a platformfüggetlen Azure Notification hubs leküldéses értesítések. A különböző ügyfelek részére regisztrálva van a leküldéses értesítéseket a sablonok használatával, és a egy egyetlen univerzális leküldéses minden ügyfél platformon is elérhető.

Válasszon egyet a következő eljárásokkal, amely megfelel a háttér-projekt típusa&mdash;vagy [.NET-háttéralkalmazás](#dotnet) vagy [Node.js háttérrendszer](#nodejs).

### <a name="dotnet"></a>.NET-háttérrendszer projekt
1. A Visual Studióban kattintson a jobb gombbal a kiszolgálói projektet. Válassza ki **NuGet-csomagok kezelése**. Keresse meg `Microsoft.Azure.NotificationHubs`, majd válassza ki **telepítése**. Ez a folyamat a Notification Hubs kódtárat az értesítések küldése a háttérrendszerből telepíti.
2. Nyissa meg a kiszolgálói projekt **tartományvezérlők** > **TodoItemController.cs**. Ezután adja hozzá a következő using utasításokat:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;
3. Az a **PostTodoItem** metódus hívása után a következő kód hozzáadása **InsertAsync**:  

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

    Ez a folyamat, amelyik tartalmazza a sablon értesítést küld. Ha új elem szerepel szöveg.
4. Tegye közzé újra a kiszolgálói projektet.

### <a name="nodejs"></a>NODE.js háttérrendszer projekt
1. Ha ezt még nem tette meg, [a rövid útmutató háttér-projekt letöltése](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart), vagy más használatát a [az Azure Portalon online szerkesztő](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Cserélje le a meglévő kódot todoitem.js a következő kódot:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');

        var table = azureMobileApps.table();

        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK,
        // see http://aka.ms/nodejshubs.
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

    Ez a folyamat, amely tartalmazza a item.text, amikor új elem kerül beillesztésre oda a sablon értesítést küld.
3. Ha szerkeszti a fájlt a helyi számítógépen, tegye közzé újra a kiszolgálói projektet.
