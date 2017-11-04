Az eljárás a háttér-projekt típusának megfelelő&mdash;vagy [.NET háttér](#dotnet) vagy [Node.js háttér](#nodejs).

### <a name="dotnet"></a>.NET háttér-projekt
1. A Visual Studióban, kattintson a jobb gombbal a projekt, és kattintson **NuGet-csomagok kezelése**. Keresse meg `Microsoft.Azure.NotificationHubs`, és kattintson a **telepítése**. Ez telepíti a Notification Hubs ügyféloldali kódtárára.
2. A tartományvezérlők mappában nyissa meg a TodoItemController.cs, és adja hozzá a következő `using` utasításokat:

        using Microsoft.Azure.Mobile.Server.Config;
        using Microsoft.Azure.NotificationHubs;
3. Cserélje le a `PostTodoItem` metódus a következő kóddal:  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
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

            // Android payload
            var androidNotificationPayload = "{ \"data\" : {\"message\":\"" + item.Text + "\"}}";

            try
            {
                // Send the push notification and log the results.
                var result = await hub.SendGcmNativeNotificationAsync(androidNotificationPayload);

                // Write the success result to the logs.
                config.Services.GetTraceWriter().Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                // Write the failure result to the logs.
                config.Services.GetTraceWriter()
                    .Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

4. A projekt közzé.

### <a name="nodejs"></a>NODE.js háttér-projekt
1. Ha még nem tette meg, [a gyorsútmutató-projekt letöltése](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart), vagy más használja a [az Azure portálon online szerkesztő](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Cserélje le a meglévő kódot a todoitem.js fájlban a következő:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');

        var table = azureMobileApps.table();

        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK,
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');

        // Define the GCM payload.
        var payload = {
            "data": {
                "message": context.item.text
            }
        };   

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a GCM native notification.
                    context.push.gcm.send(null, payload, function (error) {
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

    Ez egy új teendőelemet behelyezésekor a item.text tartalmazó GCM értesítést küld.
3. A fájlt a helyi számítógépen szerkesztésekor közzé a projekt.
