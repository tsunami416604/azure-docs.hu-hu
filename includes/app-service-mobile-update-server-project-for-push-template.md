Ebben a szakaszban a kód a meglévő Mobile Apps háttér-projekt egy leküldéses értesítést küldeni, minden alkalommal, amikor egy új listaelem frissítenie. Ez a folyamat által van-e kapcsolva a [sablon](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) funkció az Azure Notification Hubs, amely lehetővé teszi a platformok közötti leküldéses értesítések. A különböző ügyfelek regisztrálva van a leküldéses értesítések sablonokkal, és egyetlen univerzális leküldéses el tudja érni a ügyfélplatformokon összes.

Válasszon egyet az alábbi eljárások a háttér-projekt típusának megfelelő&mdash;vagy [.NET háttér](#dotnet) vagy [Node.js háttér](#nodejs).

### <a name="dotnet"></a>.NET háttér-projekt
1. A Visual Studióban kattintson a jobb gombbal a projekt. Válassza ki **NuGet-csomagok kezelése**. Keresse meg `Microsoft.Azure.NotificationHubs`, majd válassza ki **telepítése**. Ez a folyamat telepíti a háttérből értesítések küldése a Notification Hubs könyvtárban.
2. A kiszolgáló projektben nyissa meg a **tartományvezérlők** > **TodoItemController.cs**. Majd adja hozzá a következő using utasításokat:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;
3. Az a **PostTodoItem** módszer, vegye fel a következő kód hívása után **InsertAsync**:  

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

    Ez a folyamat, amely tartalmazza az elemet sablon értesítést küld. Szöveg, ha egy új elem szerepel.
4. A projekt közzé.

### <a name="nodejs"></a>NODE.js háttér-projekt
1. Ha még nem tette meg, [a gyors üzembe helyezés háttér-projekt letöltése](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart), vagy más használja a [az Azure portálon online szerkesztő](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. A meglévő kódot a todoitem.js cserélje le a következő kódot:

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

    Ez a folyamat, amely tartalmazza a item.text, ha egy új elem szerepel sablon értesítést küld.
3. Ha manuálisan szerkeszti a fájlt a helyi számítógépen, közzé a projekt.
