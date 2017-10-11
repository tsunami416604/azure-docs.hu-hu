



Csak meg kell adnia a tulajdonságait sablon értesítéseket küld, amikor a mi esetünkben küldünk a tulajdonságkészletbe honosított verziója az aktuális híreket tartalmazó példány:

    {
        "News_English": "World News in English!",
        "News_French": "World News in French!",
        "News_Mandarin": "World News in Mandarin!"
    }


Ez a szakasz bemutatja, hogyan küldhetők értesítések egy konzolalkalmazás használatával

A befoglalt kódot közzéteszi Windows áruház és az iOS-eszközökre, mivel a háttérkiszolgálón is szórási bármely, a támogatott eszközök.

### <a name="to-send-notifications-using-a-c-console-app"></a>Egy C# Konzolalkalmazás használatával az értesítések küldéséhez
Módosítsa a `SendTemplateNotificationAsync` módszer a korábban létrehozott kódot a következő konzol alkalmazás. Figyelje meg, hogyan ebben az esetben nincs szükség a különböző területi beállításokhoz és platformokra több értesítések küldéséhez.

        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub = 
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");

            // Sending the notification as a template notification. All template registrations that contain 
            // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
            // This includes APNS, GCM, WNS, and MPNS template registrations.
            Dictionary<string, string> templateParams = new Dictionary<string, string>();

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
            var locales = new string[] { "English", "French", "Mandarin" };

            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";

                // Sending localized News for each tag too...
                foreach( var locale in locales)
                {
                    string key = "News_" + locale;

                    // Your real localized news content would go here.
                    templateParams[key] = "Breaking " + category + " News in " + locale + "!";
                }

                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
        }


Vegye figyelembe, hogy a egyszerű hívás fog továbbítani az honosított adat híreket, **összes** az eszközök, függetlenül a platform, az értesítési központ hoz létre, és kézbesíti az összes eszközt egy adott előfizetni a megfelelő natív forgalma címke.

### <a name="sending-the-notification-with-mobile-services"></a>A Mobile Services értesítés küldése
A Mobile Service ütemező a következő parancsfájlt használhatja:

    var azure = require('azure');
    var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
    var notification = {
            "News_English": "World News in English!",
            "News_French": "World News in French!",
            "News_Mandarin", "World News in Mandarin!"
    }
    notificationHubService.send('World', notification, function(error) {
        if (!error) {
            console.warn("Notification successful");
        }
    });


