Ebben a szakaszban a kód a meglévő Mobile Apps háttér-projekt egy leküldéses értesítést küldeni, minden alkalommal, amikor egy új listaelem frissítenie. Ez működteti a [sablon](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) funkció az Azure Notification Hubs, platformfüggetlen leküldéses értesítések engedélyezése. A különböző ügyfelek regisztrálva van a leküldéses értesítések sablonokkal, és egyetlen univerzális leküldéses el tudja érni a ügyfélplatformokon összes.

Válasszon egyet az alábbi eljárások a háttér-projekt típusának megfelelő&mdash;vagy [.NET háttér](#dotnet) vagy [Node.js háttér](#nodejs).

### <a name="dotnet"></a>.NET háttér-projekt
1. A Visual Studióban, kattintson a jobb gombbal a projekt, és kattintson a **NuGet-csomagok kezelése**. Keresse meg `Microsoft.Azure.NotificationHubs`, és kattintson a **telepítése**. Ez telepíti a háttérből értesítések küldése a Notification Hubs könyvtárban.
2. A kiszolgáló projektben nyissa meg a **tartományvezérlők** > **TodoItemController.cs**, és adja hozzá a következő using utasításokat:

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

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Sending the message so that all template registrations that contain "messageParam"
        // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
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

    Ezt az elemet tartalmazó sablon értesítést küld. Szöveg, ha egy új elem szerepel.
4. A projekt közzé.

### <a name="nodejs"></a>NODE.js háttér-projekt
1. Ha még nem tette meg, [a gyors üzembe helyezés háttér-projekt letöltése](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart), vagy más használja a [az Azure portálon online szerkesztő](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Cserélje le a meglévő kódot a todoitem.js a következő:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');

        var table = azureMobileApps.table();

        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK,
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');

        // Define the template payload.
        var payload = '{"messageParam": "' + context.item.text + '" }';  

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
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
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
        });

        module.exports = table;  

    Ez egy új cikk behelyezésekor a item.text tartalmazó sablon értesítést küld.
3. A fájlt a helyi számítógépen szerkesztésekor közzé a projekt.
