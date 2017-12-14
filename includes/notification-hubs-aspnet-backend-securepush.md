## <a name="webapi-project"></a>WebAPI projekt
1. A Visual Studióban nyissa meg a **AppBackend** a projekt a **felhasználók értesítése** oktatóanyag.
2. Notifications.cs, cserélje le a teljes **értesítések** osztály a következő kóddal. Ne felejtse el a helyőrzőket cserélje le a kapcsolati karakterlánc (teljes hozzáférés) az értesítési központot, és a központ nevét. Ezt úgy szerezheti be ezeket az értékeket a [Azure-portálon](http://portal.azure.com). Ez a modul most a különböző biztonságos értesítések küldendő jelöli. A megvalósítás az értesítések tárolódnak adatbázis; Az egyszerűség kedvéért ebben az esetben tároljuk őket a memóriában.
   
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

1. NotificationsController.cs, cserélje le a kódot a **NotificationsController** osztály definícióját a következő kóddal. Ez az összetevő valósítja meg az értesítés biztonságosan beolvasása az eszköz olyan módon, és is lehetővé teszi a (a jelen oktatóanyag céljából) való biztonságos leküldéses az eszközökön. Vegye figyelembe, hogy az értesítési központnak küldött, amikor csak értesítést fog kapni, a nyers azonosítójú az értesítés (és nem tényleges üzenet):
   
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


Vegye figyelembe, hogy a `Post` metódus most nem küld egy bejelentési értesítést. Az értesítés-azonosítója és a nem bizalmas tartalmat tartalmazó nyers értesítést küld. Emellett győződjön meg arról, a küldési művelet, amelynek nincs konfigurálva az értesítési központ, a hitelesítő adatokat, azok hibákat eredményez a platformok megtételére.

1. Most azt újra telepíti ezt a webalkalmazást az Azure-webhely annak érdekében, hogy minden eszköz érhető el. Kattintson jobb gombbal az **AppBackend** projektre, és válassza a **Publish** (Közzététel) lehetőséget.
2. A közzétételi célként válassza az Azure webhelyén. Jelentkezzen be az Azure-fiókjával, és válassza ki a meglévő vagy új webhely létrehozása, és jegyezze fel a a **URL-címre** tulajdonságot a **kapcsolat** fülre. Az oktatóanyag további részében erre az URL-címre fogunk hivatkozni a *háttérrendszer végpontjaként*. Kattintson a **Publish** (Közzététel) gombra.

