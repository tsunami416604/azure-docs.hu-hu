---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: b8cf4217ca6c80be998b92e71c3ba29c4f68bce2
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52270373"
---
## <a name="webapi-project"></a>WebAPI-projekt
1. A Visual Studióban nyissa meg a **AppBackend** a létrehozott projekt a **– felhasználók értesítése** oktatóanyag.
2. Cserélje le az egész Notifications.cs **értesítések** osztályban az alábbi kódra. Győződjön meg arról, cserélje le a helyőrzőket az értesítési központot, és a központ neve (a teljes hozzáférés) kapcsolati karakterláncra. Szerezheti be ezeket az értéteket a [az Azure portal](http://portal.azure.com). Ez a modul mostantól jelöli a különböző biztonságos értesítéseket küld el. Egy teljes körű megvalósítás az értesítéseket fog tárolja egy adatbázisban; Az egyszerűség kedvéért ebben az esetben tároljuk őket a memóriában.
   
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

1. A notificationscontroller.cs fájlhoz, cserélje le a kódot a **NotificationsController** osztálydefiníciót az alábbi kódra. Ez az összetevő valósítja meg az eszköz biztonságos beolvasni az értesítést úgy, és azt is lehetővé teszi (a jelen oktatóanyagban), az eszközök biztonságos leküldést indíthassanak. Vegye figyelembe, hogy amikor az értesítési központ az értesítést küld, azt csak nyers értesítés küldése azonosítójú értesítés (és nem tényleges üzenet):
   
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


Vegye figyelembe, hogy a `Post` metódus már nem küld egy bejelentési értesítést. Csak az értesítés azonosítója és a nem bizalmas tartalmat tartalmazó nyers értesítést küld. Ügyeljen arra, hogy hozzá megjegyzéseket a küldési művelet a platformokhoz, amelyhez nem rendelkezik hitelesítő adatai az értesítési központ konfigurálva, hogy hibákat eredményez.

1. Most már azt fogja újra az alkalmazást üzembe helyezni egy Azure-webhelyen annak érdekében, hogy minden eszközről elérhető. Kattintson jobb gombbal az **AppBackend** projektre, és válassza a **Publish** (Közzététel) lehetőséget.
2. A közzétételi célként válassza ki Azure-webhelyen. Jelentkezzen be az Azure-fiókjával, és válassza ki egy meglévő vagy új webhely, és jegyezze fel a a **cél URL-címe** tulajdonságot a **kapcsolat** fülre. Az oktatóanyag további részében erre az URL-címre fogunk hivatkozni a *háttérrendszer végpontjaként*. Kattintson a **Publish** (Közzététel) gombra.

