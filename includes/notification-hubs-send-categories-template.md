
Ez a szakasz legfrissebb hírek, címkézett sablon értesítéseket küld a .NET-konzolalkalmazásból.

Ha a Microsoft Azure App Service Mobile Apps szolgáltatásának használ, tekintse meg a [leküldéses értesítések hozzáadása Mobile Apps] oktatóanyag, és válassza ki a platformot, az oldal tetején.

Ha azt szeretné, a Java vagy a PHP, tekintse meg a [használata a Notification Hubs Java vagy PHP]. Értesítéseket küldhet a háttérből használatával a [Notification hub REST-felület].

Ha létrehozta az értesítések küldése, ha elvégezte a Konzolalkalmazás [Ismerkedés a Notification Hubs], hagyja ki a 1-3.

1. A Visual Studióban hozzon létre egy új Visual C#-konzolalkalmazást:
   
      ![A Konzolalkalmazás-hivatkozás][13]

2. Válassza ki a Visual Studio főmenü **eszközök** > **Kódtárcsomag-kezelő** > **Csomagkezelő konzol** , majd a konzol a ablakban adja meg a következő karakterláncot:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
3. Válassza ki **meg**.  
    Ez a művelet hozzáad egy, az Azure Notification Hubs SDK-ra mutató hivatkozást a [Microsoft.Azure.Notification Hubs NuGet-csomag] használatával.

4. Nyissa meg a Program.cs fájlt, és adja hozzá a következő `using` utasítást:
   
        using Microsoft.Azure.NotificationHubs;

5. Az a `Program` osztály, adja hozzá a következő metódust, vagy cserélje le, ha már létezik:
   
        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub =
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");
   
            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business",
                                            "Technology", "Science", "Sports"};
   
            // Send the notification as a template notification. All template registrations that contain
            // "messageParam" and the proper tags will receive the notifications.
            // This includes APNS, GCM, WNS, and MPNS template registrations.
   
            Dictionary<string, string> templateParams = new Dictionary<string, string>();
   
            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";
                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
         }
   
    Ez a kód sablon értesítést küld az egyes, a hat címkék a karakterlánc-tömbben. Címkék használata azt biztosítja, hogy az eszköz megkapja az értesítéseket csak a regisztrált kategóriák.

5. Az előzőekben látható kód cserélje le a `<hub name>` és `<connection string with full access>` helyőrzőket az értesítési központ nevére és a kapcsolati karakterláncot *DefaultFullSharedAccessSignature* az értesítési központ az irányítópultról.

6. A **Main** metódusban adja hozzá a következő sorokat:
   
         SendTemplateNotificationAsync();
         Console.ReadLine();

7. A konzol alkalmazás elkészítésére.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Ismerkedés a Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification hub REST-felület]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[leküldéses értesítések hozzáadása Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[használata a Notification Hubs Java vagy PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.Notification Hubs NuGet-csomag]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
