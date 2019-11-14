---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: 3086d15ba541aa7f08f983dac4bc363f43248a9e
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74062890"
---
## <a name="webapi-project"></a>WebAPI projekt
1. A Visual Studióban nyissa meg a **felhasználók értesítése** oktatóanyagban létrehozott **AppBackend** projektet.
2. A Notifications.cs-ben cserélje le a teljes **értesítési** osztályt a következő kódra. Ügyeljen rá, hogy a helyőrzőket cserélje le a kapcsolati sztringre (teljes hozzáféréssel) az értesítési központ és a hub nevénél. Ezeket az értékeket a [Azure Portal](https://portal.azure.com)lehet beszerezni. Ez a modul mostantól az elküldött különböző biztonságos értesítéseket is megjeleníti. A teljes implementációban az értesítések tárolása egy adatbázisban történik. az egyszerűség kedvéért ebben az esetben a memóriában tároljuk őket.
   
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
                var notification = new Notification
                {
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

1. A NotificationsController.cs-ben cserélje le a kódot a **NotificationsController** osztály definíciójában a következő kódra. Ez az összetevő olyan módszert valósít meg, amellyel az eszköz biztonságosan lekérheti az értesítést, és lehetővé teszi az eszközök biztonságos leküldését is (ebben az oktatóanyagban). Vegye figyelembe, hogy amikor értesítést küld az értesítési központnak, csak egy nyers értesítést küldünk az értesítés AZONOSÍTÓjának (és nincs tényleges üzenet):
   
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


Vegye figyelembe, hogy a `Post` metódus most nem küld bejelentési értesítést. Nyers értesítést küld, amely csak az értesítési azonosítót tartalmazza, és nem bizalmas tartalmat. Ügyeljen arra is, hogy a küldési műveletet jegyezze fel azon platformok esetében, amelyekhez nem rendelkezik az értesítési központban konfigurált hitelesítő adatokkal, mivel ezek hibákat eredményeznek.

1. Most újra telepítjük ezt az alkalmazást egy Azure-webhelyre, hogy minden eszközről elérhetővé váljon. Kattintson jobb gombbal az **AppBackend** projektre, és válassza a **Publish** (Közzététel) lehetőséget.
2. A közzétételi célként válassza az Azure-webhely lehetőséget. Jelentkezzen be az Azure-fiókjával, és válasszon ki egy meglévő vagy új webhelyet, és jegyezze fel a **cél URL** -tulajdonságot a **kapcsolatok** lapon. Ebben az oktatóanyagban a *háttérbeli végpontként* erre az URL-címre fogunk hivatkozni. Kattintson a **Publish** (Közzététel) gombra.

