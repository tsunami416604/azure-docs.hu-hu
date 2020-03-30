---
author: sethmanheim
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: sethm
ms.openlocfilehash: 7fd161c90234d45a6751f173ba3685ee8c392c87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74260761"
---
## <a name="webapi-project"></a>WebAPI projekt

1. A Visual Studióban nyissa meg a **Felhasználók értesítése** oktatóanyagban létrehozott **AppBackend** projektet.
2. A Notifications.cs cserélje le a teljes **Értesítés** osztályt a következő kódra. Ügyeljen arra, hogy cserélje le a helyőrzők a kapcsolati karakterlánc (teljes hozzáféréssel) az értesítési központ, és a hub nevét. Ezeket az értékeket az [Azure Portalon](https://portal.azure.com)szerezheti be. Ez a modul most a különböző biztonságos értesítéseket jelöli, amelyeket a rendszer küld. A teljes megvalósítás során az értesítések egy adatbázisban tárolódnak; az egyszerűség kedvéért, ebben az esetben tároljuk őket a memóriában.
   
   ```csharp
    public class Notification
    {
        public int Id { get; set; }
        public string Payload { get; set; }
        public bool Read { get; set; }
    }

    public class Notifications
    {
        public static Notifications Instance = new Notifications();

        private List<Notification> notifications = new List<Notification>();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",     "{hub name}");
        }

        public Notification CreateNotification(string payload)
        {
            var notification = new Notification() {
            Id = notifications.Count,
            Payload = payload,
            Read = false
            };

            notifications.Add(notification);

            return notification;
        }

        public Notification ReadNotification(int id)
        {
            return notifications.ElementAt(id);
        }
    }
    ```

1. NotificationsController.cs cserélje le a **NotificationsController** osztálydefinícióban lévő kódot a következő kódra. Ez az összetevő megvalósítja a módját, hogy az eszköz biztonságosan lekérje az értesítést, és lehetővé teszi (az oktatóanyag alkalmazásában) az eszközök biztonságos leküldéses indítását. Ne feledje, hogy amikor elküldi az értesítést az értesítési központ, mi csak küld egy nyers értesítést az azonosítót az értesítés (és nem a tényleges üzenet):
   
   ```csharp
    public NotificationsController()
    {
        Notifications.Instance.CreateNotification("This is a secure notification!");
    }

    // GET api/notifications/id
    public Notification Get(int id)
    {
        return Notifications.Instance.ReadNotification(id);
    }

    public async Task<HttpResponseMessage> Post()
    {
        var secureNotificationInTheBackend = Notifications.Instance.CreateNotification("Secure confirmation.");
        var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

        // windows
        var rawNotificationToBeSent = new Microsoft.Azure.NotificationHubs.WindowsNotification(secureNotificationInTheBackend.Id.ToString(),
                        new Dictionary<string, string> {
                            {"X-WNS-Type", "wns/raw"}
                        });
        await Notifications.Instance.Hub.SendNotificationAsync(rawNotificationToBeSent, usernameTag);

        // apns
        await Notifications.Instance.Hub.SendAppleNativeNotificationAsync("{\"aps\": {\"content-available\": 1}, \"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}", usernameTag);

        // gcm
        await Notifications.Instance.Hub.SendGcmNativeNotificationAsync("{\"data\": {\"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}}", usernameTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

Ne feledje, hogy a `Post` módszer most nem küld bejelentési értesítést. Nyers értesítést küld, amely csak az értesítési azonosítót tartalmazza, és nem tartalmaz bizalmas tartalmat. Győződjön meg arról is, hogy megjegyzést fűz a küldési művelethez azokhoz a platformokhoz, amelyekhez nincs konfigurálva hitelesítő adatok az értesítési központban, mivel azok hibákat eredményeznek.

1. Most újra telepítjük ezt az alkalmazást egy Azure-webhelyre annak érdekében, hogy minden eszközről elérhetővé tesszük. Kattintson jobb gombbal az **AppBackend** projektre, és válassza a **Publish** (Közzététel) lehetőséget.
2. Válassza ki az Azure-webhelyet közzétételi célként. Jelentkezzen be Azure-fiókjával, és válasszon ki egy meglévő vagy új webhelyet, és jegyezze fel a **cél URL-tulajdonságát** a **Kapcsolat** lapon. Ezt az URL-címet az oktatóanyag későbbi részében *a háttérvégpontként* fogjuk hivatkozni. Kattintson a **Publish** (Közzététel) gombra.
