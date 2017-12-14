## <a name="create-the-webapi-project"></a>A WebAPI projekt létrehozása
A következő szakaszok tárgyalják egy új ASP.NET WebAPI háttér létrehozását. Ez a folyamat három fő célja van:

* **Ügyfelek hitelesítése**: hozzáadhat egy üzenetkezelő később az ügyfélkérelmek és a felhasználót társítja ahhoz a kérelmet.

* **Értesítések a WebAPI használatával háttér regisztrálható**: kezelni egy ügyféleszközön, ha értesítést szeretne kapni az új regisztrációk vezérlő hozzáadása. A hitelesített felhasználónév automatikusan hozzáadódik a regisztrációt, mint egy [címke](https://msdn.microsoft.com/library/azure/dn530749.aspx).

* **Értesítések küldése ügyfelek**: is lehetővé teszik a felhasználók, eszközök és-ügyfelek a címke társított egy biztonságos leküldéses indításához vezérlő hozzáadása. 

Az új ASP.NET WebAPI háttér létrehozása a következő módon: 

> [!IMPORTANT]
> Visual Studio 2015-öt használ, vagy a korábban, az oktatóanyag elindítása előtt győződjön meg arról, hogy telepítette a legújabb verzióra a NuGet-Csomagkezelőt a Visual Studio. 
>
>Az ellenőrzéshez indítsa el a Visual Studiót. Az a **eszközök** menü **bővítmények és frissítések**. Keresse meg **NuGet-Csomagkezelő** az Ön verziójában a Visual Studio, és győződjön meg arról, hogy rendelkezik a legújabb verzióra. Ha a verzió nem a legújabb, távolítsa el azt, és a NuGet Package Manager, majd telepítse újra.
 
![][B4]

> [!NOTE]
> Ellenőrizze, hogy telepítette-e a Visual Studio webhely-üzembehelyezési [Azure SDK-ját](https://azure.microsoft.com/downloads/).
> 
> 

1. Indítsa el a Visual Studiót vagy a Visual Studio Expresst. 

2. Válassza ki **Server Explorer**, és jelentkezzen be az Azure-fiókjával. A webhely erőforrások létrehozásához a fiókjában, be kell jelentkeznie.

3. A Visual Studio válassza **fájl** > **új** > **projekt**, bontsa ki a **sablonok**, bontsa ki a **Visual C#**, majd válassza ki **webes** és **ASP.NET Web Application**.

4. Az a **neve** mezőbe írja be **AppBackend**, majd válassza ki **OK**. 
   
    ![Az új projekt ablakról][B1]

5. Az a **új ASP.NET projekt** ablakban válassza ki a **Web API** jelölje be a jelölőnégyzetet, majd válassza ki **OK**.
   
    ![Az új ASP.NET projekt ablakról][B2]

6. Az a **konfigurálása a Microsoft Azure Web Apps** ablakban válasszon ki egy előfizetést, majd a a **App Service-csomag** listában, tegye a következők egyikét:

    * Válassza ki az app service-csomag már létrehozta. 
    * Válassza ki **hozzon létre egy új app service-csomag**, majd hozzon létre egyet. 
    
  Az oktatóanyag elvégzéséhez nincs szükség adatbázisra. Az app service-csomag kiválasztása után válassza ki a **OK** a projekt létrehozásához.
   
    ![A Microsoft Azure-webalkalmazás konfigurálása ablak][B5]

## <a name="authenticate-clients-to-the-webapi-back-end"></a>A WebAPI háttér az ügyfelek hitelesítése
Ebben a szakaszban hoz létre egy új üzenetkezelő osztályt **AuthenticationTestHandler** az új háttér. Ez az osztály van származtatva [DelegatingHandler](https://msdn.microsoft.com/library/system.net.http.delegatinghandler.aspx) és a hozzá egy üzenetkezelő, hogy az összes feldolgozható kéri, hogy a háttérben történő származnak. 

1. A Megoldáskezelőben kattintson a jobb gombbal a **AppBackend** projektre, válassza **Hozzáadás**, majd válassza ki **osztály**. 
 
2. Az új osztály neve **AuthenticationTestHandler.cs**, majd válassza ki **Hozzáadás** az osztály létrehozásához. Ez az osztály használatával hitelesíti a felhasználókat *az egyszerű hitelesítés* az egyszerűség érdekében. Az alkalmazás használhat egyetlen hitelesítési séma.

3. Az AuthenticationTestHandler.cs osztályban adja hozzá a következő `using`-utasításokat:
   
        using System.Net.Http;
        using System.Threading;
        using System.Security.Principal;
        using System.Net;
        using System.Text;
        using System.Threading.Tasks;

4. AuthenticationTestHandler.cs, cserélje le a `AuthenticationTestHandler` osztály definícióját a következő kóddal: 
   
    A kezelő fogja engedélyezni a kérelmet, az alábbi három feltétel teljesülése esetén:
   
   * A kérelemben egy *engedélyezési* fejléc. 
   * A kérés *alapszintű* hitelesítést használ. 
   * A felhasználónév és a jelszó karakterlánc azonos.
     
  Minden más esetben a rendszer a kérést elutasítja. Ez nem egy valós hitelesítési és engedélyezési módszer, Csak egy nagyon egyszerű példa ehhez az oktatóanyaghoz.
     
  Ha a kérelemüzenet hitelesítése és engedélyezése által `AuthenticationTestHandler`, az egyszerű hitelesítés felhasználói csatolva van a jelenlegi kérelem a [HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.current.aspx). Felhasználói adatokat a HttpContext osztály által használható egy másik vezérlő (RegisterController) később hozzáadása egy [címke](https://msdn.microsoft.com/library/azure/dn530749.aspx) az értesítési regisztrációs kérésre.
     
       public class AuthenticationTestHandler : DelegatingHandler
       {
           protected override Task<HttpResponseMessage> SendAsync(
           HttpRequestMessage request, CancellationToken cancellationToken)
           {
               var authorizationHeader = request.Headers.GetValues("Authorization").First();
     
               if (authorizationHeader != null && authorizationHeader
                   .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
               {
                   string authorizationUserAndPwdBase64 =
                       authorizationHeader.Substring("Basic ".Length);
                   string authorizationUserAndPwd = Encoding.Default
                       .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
                   string user = authorizationUserAndPwd.Split(':')[0];
                   string password = authorizationUserAndPwd.Split(':')[1];
     
                   if (verifyUserAndPwd(user, password))
                   {
                       // Attach the new principal object to the current HttpContext object
                       HttpContext.Current.User =
                           new GenericPrincipal(new GenericIdentity(user), new string[0]);
                       System.Threading.Thread.CurrentPrincipal =
                           System.Web.HttpContext.Current.User;
                   }
                   else return Unauthorized();
               }
               else return Unauthorized();
     
               return base.SendAsync(request, cancellationToken);
           }
     
           private bool verifyUserAndPwd(string user, string password)
           {
               // This is not a real authentication scheme.
               return user == password;
           }
     
           private Task<HttpResponseMessage> Unauthorized()
           {
               var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
               var tsc = new TaskCompletionSource<HttpResponseMessage>();
               tsc.SetResult(response);
               return tsc.Task;
           }
       }
     
     > [!NOTE]
     > Biztonsági Megjegyzés: A `AuthenticationTestHandler` osztály nem biztosítja az IGAZ hitelesítés. A rendszer azt csak az alapszintű hitelesítés utánzására használja, és nem tekinthető biztonságosnak. Az éles alkalmazásokban és szolgáltatásokban implementálnia kell egy biztonságos hitelesítési mechanizmust.                
     > 
     > 
5. Az üzenetkezelő regisztrálásához adja hozzá a következő kódot végén a `Register` metódust a **App_Start/WebApiConfig.cs** osztály:
   
        config.MessageHandlers.Add(new AuthenticationTestHandler());

6. Mentse a módosításokat.

## <a name="register-for-notifications-by-using-the-webapi-back-end"></a>Regisztrálhat a WebAPI háttér használatával
Ebben a szakaszban hozzá egy új tartományvezérlő a WebAPI hátteret a tanúsítványigénylések egy felhasználó és az értesítések egy eszköz regisztrálása az ügyféloldali kódtár a notification hubs használatával. A vezérlő ad hozzá egy felhasználói kód a felhasználó hitelesítése és csatlakozik a HttpContext osztály által `AuthenticationTestHandler`. A címke `"username:<actual username>"` karakterlánc-formátumú lesz.

1. A Megoldáskezelőben kattintson a jobb gombbal a **AppBackend** projektre, majd válassza ki **NuGet-csomagok kezelése**.

2. A bal oldali panelen válassza ki a **Online** , majd a a **keresési** mezőbe írja be **Microsoft.Azure.NotificationHubs**.

3. Az eredmények listájában válassza **Microsoft Azure Notification Hubs**, majd válassza ki **telepítése**. A telepítés befejezéséhez, és zárja be a NuGet-Csomagkezelő ablak.
   
    Ez a művelet hozzáad egy, az Azure Notification Hubs SDK-ra mutató hivatkozást a <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet-csomag</a> használatával.

4. Hozzon létre egy új osztály fájlt az értesítési központban az értesítések küldéséhez használt kapcsolatot jelöli. A Megoldáskezelőben kattintson a jobb gombbal a **modellek** mappára, válassza **Hozzáadás**, majd válassza ki **osztály**. Az új osztály neve **Notifications.cs**, majd válassza ki **Hozzáadás** az osztály létrehozásához. 
   
    ![Az új elem hozzáadása ablak][B6]

5. Adja hozzá a következő `using`-utasítást a Notifications.cs fájl elejéhez:
   
        using Microsoft.Azure.NotificationHubs;

6. Cserélje le a `Notifications` osztály definícióját a következő kódra, és a két helyőrzőket cserélje le a kapcsolati karakterlánc (teljes hozzáférés) az értesítési központban és a központ nevét (rendelkezésre álló [Azure-portálon](http://portal.azure.com)):
   
        public class Notifications
        {
            public static Notifications Instance = new Notifications();
   
            public NotificationHubClient Hub { get; set; }
   
            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>", 
                                                                             "<hub name>");
            }
        }
7. Ezután hozzon létre egy új tartományvezérlő nevű **RegisterController**. A Megoldáskezelőben kattintson a jobb gombbal a **tartományvezérlők** mappára, válassza **Hozzáadás**, majd válassza ki **vezérlő**. 

8. Válassza ki **Web API 2 vezérlő - üres**, majd válassza ki **Hozzáadás**.
   
    ![A hozzáadása Scaffold ablak][B7]
   
9. Az a **vezérlőnév** mezőbe írja be **RegisterController** az új osztály neve, majd válassza ki a **Hozzáadás**.

    ![A vezérlő hozzáadása ablak][B8]

10. Adja hozzá a következő `using`-utasításokat a RegisterController.cs fájlhoz:
   
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.NotificationHubs.Messaging;
        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

11. Adja hozzá a következő kódot a(z) `RegisterController` osztálydefiníciójához: Vegye figyelembe, hogy ezzel a kóddal, azt hozzá egy felhasználói kód a HttpContext kapcsolt felhasználó. A felhasználó hitelesítése és a HttpContext osztály által hozzáadott, üzenetszűrő csatolt `AuthenticationTestHandler`. További választható ellenőrzéseket is hozzáadhat, amelyekkel ellenőrizheti, hogy a felhasználó jogosult-e a kért címkékre történő regisztráláshoz.
   
        private NotificationHubClient hub;
   
        public RegisterController()
        {
            hub = Notifications.Instance.Hub;
        }
   
        public class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }
   
        // POST api/register
        // This creates a registration id
        public async Task<string> Post(string handle = null)
        {
            string newRegistrationId = null;
   
            // make sure there are no existing registrations for this push handle (used for iOS and Android)
            if (handle != null)
            {
                var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);
   
                foreach (RegistrationDescription registration in registrations)
                {
                    if (newRegistrationId == null)
                    {
                        newRegistrationId = registration.RegistrationId;
                    }
                    else
                    {
                        await hub.DeleteRegistrationAsync(registration);
                    }
                }
            }
   
            if (newRegistrationId == null) 
                newRegistrationId = await hub.CreateRegistrationIdAsync();
   
            return newRegistrationId;
        }
   
        // PUT api/register/5
        // This creates or updates a registration (with provided channelURI) at the specified id
        public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
        {
            RegistrationDescription registration = null;
            switch (deviceUpdate.Platform)
            {
                case "mpns":
                    registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "wns":
                    registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "apns":
                    registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "gcm":
                    registration = new GcmRegistrationDescription(deviceUpdate.Handle);
                    break;
                default:
                    throw new HttpResponseException(HttpStatusCode.BadRequest);
            }
   
            registration.RegistrationId = id;
            var username = HttpContext.Current.User.Identity.Name;
   
            // add check if user is allowed to add these tags
            registration.Tags = new HashSet<string>(deviceUpdate.Tags);
            registration.Tags.Add("username:" + username);
   
            try
            {
                await hub.CreateOrUpdateRegistrationAsync(registration);
            }
            catch (MessagingException e)
            {
                ReturnGoneIfHubResponseIsGone(e);
            }
   
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
        // DELETE api/register/5
        public async Task<HttpResponseMessage> Delete(string id)
        {
            await hub.DeleteRegistrationAsync(id);
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
        private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
        {
            var webex = e.InnerException as WebException;
            if (webex.Status == WebExceptionStatus.ProtocolError)
            {
                var response = (HttpWebResponse)webex.Response;
                if (response.StatusCode == HttpStatusCode.Gone)
                    throw new HttpRequestException(HttpStatusCode.Gone.ToString());
            }
        }
12. Mentse a módosításokat.

## <a name="send-notifications-from-the-webapi-back-end"></a>Értesítések küldése a WebAPI háttér
Ebben a szakaszban vegyen fel egy új vezérlőt, amely elérhetővé teszi lehetővé az ügyféleszközök elküldeni egy értesítést. Az értesítés használó Azure Notification Hubs szolgáltatás könyvtár ASP.NET WebAPI háttérben lévő felhasználónév címke alapul.

1. Hozzon létre egy másik új tartományvezérlő nevű **NotificationsController** létrehozott ugyanúgy **RegisterController** az előző szakaszban.

2. Adja hozzá a következő `using`-utasításokat a NotificationsController.cs fájlhoz:
   
        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

3. Adja hozzá a következő metódust a **NotificationsController** osztály:
   
    Ez a kód elküldi egy értesítési típus, amely a Platform Notification szolgáltatás (PNS) alapú `pns` paraméter. A(z) `to_tag` értékét a rendszer az üzenet *felhasználónév*-címkéjének beállítására használja. A címkének egyeznie kell egy aktív értesítésiközpont-regisztráció felhasználónév-címkéjével. Az értesítési üzenetet a rendszer a POST-kérés törzséből nyeri ki, és a cél PNS-nek megfelelően formázza. 
   
    A pns-sel, amelyek a támogatott eszközök használják, ha értesítést szeretne kapni, attól függően, hogy az értesítések többféle formátumúak által támogatott. Például Windows-eszközökön, használhatja a [WNS-sel való értesítési poharad](https://msdn.microsoft.com/library/windows/apps/br230849.aspx) , amely közvetlenül egy másik PNS nem támogatja. Ilyen esetben a háttér kell formáznia a támogatott értesítést a vonatkozó értesítést a pns-sel eszközök támogatásához. Akkor használja a megfelelő küldjön az API a [NotificationHubClient osztály](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.notificationhubclient_methods.aspx).
   
        public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag)
        {
            var user = HttpContext.Current.User.Identity.Name;
            string[] userTag = new string[2];
            userTag[0] = "username:" + to_tag;
            userTag[1] = "from:" + user;
   
            Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;
            HttpStatusCode ret = HttpStatusCode.InternalServerError;
   
            switch (pns.ToLower())
            {
                case "wns":
                    // Windows 8.1 / Windows Phone 8.1
                    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + 
                                "From " + user + ": " + message + "</text></binding></visual></toast>";
                    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
                    break;
                case "apns":
                    // iOS
                    var alert = "{\"aps\":{\"alert\":\"" + "From " + user + ": " + message + "\"}}";
                    outcome = await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);
                    break;
                case "gcm":
                    // Android
                    var notif = "{ \"data\" : {\"message\":\"" + "From " + user + ": " + message + "\"}}";
                    outcome = await Notifications.Instance.Hub.SendGcmNativeNotificationAsync(notif, userTag);
                    break;
            }
   
            if (outcome != null)
            {
                if (!((outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Abandoned) ||
                    (outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Unknown)))
                {
                    ret = HttpStatusCode.OK;
                }
            }
   
            return Request.CreateResponse(ret);
        }

4. Futtassa az alkalmazást, és ellenőrizze eddigi munkája pontosságának, jelölje be a **F5** kulcs. Az alkalmazást egy webböngészőben megnyílik, és az ASP.NET-kezdőlap jelenik meg. 

## <a name="publish-the-new-webapi-back-end"></a>Az új WebAPI háttér közzététele
Ezután az alkalmazást telepít egy Azure-webhelyen elérhető összes eszközökről. 

1. Kattintson a jobb gombbal a **AppBackend** projektre, majd válassza ki **közzététel**.

2. Válassza ki **Microsoft Azure App Service** a közzétételi célként, és válassza ki, **közzététel**.  
    Az App Service létrehozása ablak nyílik meg. Itt az összes szükséges Azure-erőforrást az ASP.NET web app Azure rendszerben való futtatásra is létrehozhat.

    ![A Microsoft Azure App Service csempe][B15]

3. Az a **létrehozása az App Service** ablakban válassza ki az Azure-fiókjával. Válassza ki **típusának módosítása** > **webalkalmazás**. Tartsa meg az alapértelmezett **webalkalmazásnév**, majd válassza ki a **előfizetés**, **erőforráscsoport**, és **App Service-csomag**. 

4. Kattintson a **Létrehozás** gombra.

5. Jegyezze fel a **Summary** (Összegzés) szakaszban lévő **Site URL** (Webhely URL-címe) tulajdonságot. Az URL-címe a *háttér-végpont* az oktatóanyag későbbi. 

6. Válassza ki **közzététele**.

A varázsló befejezése után az ASP.NET web app közzéteszi az Azure-ba, és majd az alapértelmezett böngésző megnyitja az alkalmazást.  Az alkalmazás az Azure App Service szolgáltatások megtekinthető.

Az URL-cím a webalkalmazás korábban megadott nevét használja, a következő formátumban: http://<alkalmazás_neve>.azurewebsites.net.

[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/publish-to-app-service.png
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG
