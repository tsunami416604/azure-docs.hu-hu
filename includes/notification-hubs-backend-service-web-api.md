---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: f0e83548d3ba3b353b471e2e3429a23421aec7b2
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081428"
---
### <a name="create-a-web-project"></a>Webes projekt létrehozása

1. A **Visual Studióban**válassza a **fájl**  >  **új megoldás**elemet.

1. Válassza a **.net Core**  >  **app**  >  **ASP.net Core**  >  **API**  >  **tovább**lehetőséget.
  
1. Az **új ASP.net Core webes API konfigurálása** párbeszédpanelen válassza a **.net Core 3,1** **cél keretrendszerét** .

1. Adja meg a *PushDemoApi* a **projekt neveként** , majd válassza a **Létrehozás**lehetőséget.

1. **Command**  +  A sablonos alkalmazás teszteléséhez indítsa el a hibakeresést (**írja be**a parancsot).

    > [!NOTE]
    > A sablonba állított alkalmazás úgy van konfigurálva, hogy a **WeatherForecastController** használja *launchUrl*. Ez a **Tulajdonságok**launchSettings.jsbe értékre van állítva  >  ** **.  
    >
    > Ha a rendszer **érvénytelen fejlesztési tanúsítvánnyal** rendelkező üzenetet kér, a következő üzenet jelenik meg:
    >
    > 1. Az **Igen** gombra kattintva fogadja el a "DotNet dev-certs https" eszköz futtatását a javításhoz. A "DotNet dev-certs https" eszköz ezután megkéri, hogy adjon meg egy jelszót a tanúsítványhoz és a kulcstartóhoz tartozó jelszót.
    >
    > 1. Kattintson az **Igen** gombra, amikor a rendszer felszólítja **az új tanúsítvány telepítésére és megbízhatóságára**, majd adja meg a kulcstartó jelszavát.

1. Bontsa ki a **vezérlők** mappát, majd törölje a **WeatherForecastController.cs**.

1. **WeatherForecast.cs**törlése.

1. **Vezérlő**  +  **Kattintson** a **PushDemoApi** projektre, majd válassza az **új fájl...** lehetőséget a **Hozzáadás** menüből.

1. Állítsa be a helyi konfigurációs értékeket a [Secret Manager eszköz](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=linux#secret-manager)használatával. A titkoknak a megoldásból való leválasztása biztosítja, hogy ne legyenek vége a verziókövetés során. Nyissa meg a **terminált** , majd nyissa meg a projektfájl könyvtárát, és futtassa a következő parancsokat:

    ```bash
    dotnet user-secrets init
    dotnet user-secrets set "NotificationHub:Name" <value>
    dotnet user-secrets set "NotificationHub:ConnectionString" <value>
    ```

    Cserélje le a helyőrző értékeket a saját értesítési központ nevére és a kapcsolatok karakterláncának értékeire. Az [értesítési központ létrehozása](#create-a-notification-hub) című szakaszban tudomást tett róluk. Ellenkező esetben az [Azure](https://portal.azure.com)-ban is megtekintheti őket.

    **NotificationsHub: név**:  
    Tekintse meg az **általános áttekintés**oldalon található *Name* ( **alapvető** információk) összefoglalót.  

    **NotificationHub: ConnectionString**:  
    Lásd: *DefaultFullSharedAccessSignature* a **hozzáférési házirendekben**

    > [!NOTE]
    > Éles forgatókönyvek esetén megtekintheti az [Azure](https://azure.microsoft.com/services/key-vault) kulcstartót a kapcsolódási karakterlánc biztonságos tárolásához. Az egyszerűség kedvéért a titkok bekerülnek a [Azure app Service](https://azure.microsoft.com/services/app-service/) alkalmazás beállításaiba.

### <a name="authenticate-clients-using-an-api-key-optional"></a>Ügyfelek hitelesítése API-kulccsal (nem kötelező)

Az API-kulcsok nem annyira biztonságosak, mint a jogkivonatok, de az oktatóanyag szempontjából elegendőek lesznek. Az API-kulcsok egyszerűen konfigurálhatók a [ASP.net middleware](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/?view=aspnetcore-3.1)használatával.

1. Adja hozzá az **API-kulcsot** a helyi konfigurációs értékekhez.

    ```bash
    dotnet user-secrets set "Authentication:ApiKey" <value>
    ```

    > [!NOTE]
    > A helyőrző értékét cserélje le a saját elemre, és jegyezze fel azt.

1. **Vezérlő**  +  **Kattintson** a **PushDemoApi** projektre, válassza **az új mappa** lehetőséget a **Hozzáadás** menüben, majd kattintson a **Hozzáadás** a *hitelesítés* használatával **mappa neveként**elemre.

1. **Vezérlő**  +  **Kattintson** a **hitelesítési** mappára, majd válassza az **új fájl...** lehetőséget a **Hozzáadás** menüből.

1. Válassza az **általános**  >  **üres osztály**lehetőséget, írja be a *ApiKeyAuthOptions.cs* **nevet**, majd kattintson az **új** alkalmazás hozzáadása elemre.

    ```csharp
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthOptions : AuthenticationSchemeOptions
        {
            public const string DefaultScheme = "ApiKey";
            public string Scheme => DefaultScheme;
            public string ApiKey { get; set; }
        }
    }
    ```

1. Adjon hozzá egy másik **üres osztályt** a *ApiKeyAuthHandler.cs*nevű **hitelesítési** mappához, majd adja hozzá a következő implementációt.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Security.Claims;
    using System.Text.Encodings.Web;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthHandler : AuthenticationHandler<ApiKeyAuthOptions>
        {
            const string ApiKeyIdentifier = "apikey";

            public ApiKeyAuthHandler(
                IOptionsMonitor<ApiKeyAuthOptions> options,
                ILoggerFactory logger,
                UrlEncoder encoder,
                ISystemClock clock)
                : base(options, logger, encoder, clock) {}

            protected override Task<AuthenticateResult> HandleAuthenticateAsync()
            {
                string key = string.Empty;

                if (Request.Headers[ApiKeyIdentifier].Any())
                {
                    key = Request.Headers[ApiKeyIdentifier].FirstOrDefault();
                }
                else if (Request.Query.ContainsKey(ApiKeyIdentifier))
                {
                    if (Request.Query.TryGetValue(ApiKeyIdentifier, out var queryKey))
                        key = queryKey;
                }

                if (string.IsNullOrWhiteSpace(key))
                    return Task.FromResult(AuthenticateResult.Fail("No api key provided"));

                if (!string.Equals(key, Options.ApiKey, StringComparison.Ordinal))
                    return Task.FromResult(AuthenticateResult.Fail("Invalid api key."));

                var identities = new List<ClaimsIdentity> {
                    new ClaimsIdentity("ApiKeyIdentity")
                };

                var ticket = new AuthenticationTicket(
                    new ClaimsPrincipal(identities), Options.Scheme);

                return Task.FromResult(AuthenticateResult.Success(ticket));
            }
        }
    }
    ```

    > [!NOTE]
    > A [hitelesítési kezelő](https://docs.microsoft.com/aspnet/core/security/authentication/?view=aspnetcore-3.1#authentication-handler) olyan típus, amely megvalósítja egy séma viselkedését, ebben az esetben egy egyéni API-kulcs sémát.

1. Adjon hozzá egy másik **üres osztályt** a *ApiKeyAuthenticationBuilderExtensions.cs*nevű **hitelesítési** mappához, majd adja hozzá a következő implementációt.

    ```csharp
    using System;
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public static class AuthenticationBuilderExtensions
        {
            public static AuthenticationBuilder AddApiKeyAuth(
                this AuthenticationBuilder builder,
                Action<ApiKeyAuthOptions> configureOptions)
            {
                return builder
                    .AddScheme<ApiKeyAuthOptions, ApiKeyAuthHandler>(
                        ApiKeyAuthOptions.DefaultScheme,
                        configureOptions);
            }
        }
    }
    ```

    > [!NOTE]
    > Ez a kiterjesztési módszer leegyszerűsíti a **Startup.cs** köztes middleware-konfigurációs kódot, így könnyebben olvasható és általában könnyebben követhető.

1. A **Startup.cs**-ben frissítse a **ConfigureServices** metódust az API-kulcs hitelesítésének konfigurálásához a szolgáltatások hívása alatt **. AddControllers** metódus.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();

        services.AddAuthentication(options =>
        {
            options.DefaultAuthenticateScheme = ApiKeyAuthOptions.DefaultScheme;
            options.DefaultChallengeScheme = ApiKeyAuthOptions.DefaultScheme;
        }).AddApiKeyAuth(Configuration.GetSection("Authentication").Bind);
    }
    ```

1. A **Startup.cs**továbbra is frissíti a **configure** metódust, hogy meghívja a **UseAuthentication** és a **UseAuthorization** bővítmény metódusait az alkalmazás **IApplicationBuilder**. Győződjön meg arról, hogy ezek a metódusok a **UseRouting** és az alkalmazás előtt vannak meghívva **. UseEndpoints**.

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseHttpsRedirection();

        app.UseRouting();

        app.UseAuthentication();

        app.UseAuthorization();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
    }
    ```

    > [!NOTE]
    > A **UseAuthentication** meghívásával regisztrálja a middleware-t, amely a korábban regisztrált hitelesítési sémákat ( **ConfigureServices**) használja. Ezt minden olyan middleware-nek meg kell hívni, amely a hitelesített felhasználóktól függ.

### <a name="add-dependencies-and-configure-services"></a>Függőségek hozzáadása és szolgáltatások konfigurálása

A ASP.NET Core támogatja a [függőségi injektálás (di)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-3.1) szoftver kialakítási mintáját, amely az osztályok és a függőségek közötti [vezérlés (NOB)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elérésének a technikája.  

Az értesítési központ és a [Notification HUBS SDK for háttér-műveletek](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) használata a szolgáltatáson belül van beágyazva. A szolgáltatás regisztrálva van, és elérhetővé válik egy megfelelő absztrakción keresztül.

1. **Vezérlő**  +  **Kattintson** a **függőségek** mappára, majd válassza a **NuGet-csomagok kezelése...** lehetőséget.

1. Keressen rá a **Microsoft. Azure. NotificationHubs** kifejezésre, és ellenőrizze, hogy be van-e jelölve.

1. Kattintson a **csomagok hozzáadása**lehetőségre, majd az **elfogadás** gombra, amikor a rendszer kéri, hogy fogadja el a licencfeltételeket.

1. **Vezérlő**  +  **Kattintson** a **PushDemoApi** projektre, válassza **az új mappa** lehetőséget a **Hozzáadás** menüben, majd kattintson a **Hozzáadás** a *modellek* használatával **mappa neveként**elemre.

1. **Vezérlő**  +  **Kattintson** a **modellek** mappára, majd válassza az **új fájl...** lehetőséget a **Hozzáadás** menüből.

1. Válassza az **általános**  >  **üres osztály**lehetőséget, írja be a *PushTemplates.cs* **nevet**, majd kattintson az **új** alkalmazás hozzáadása elemre.

    ```csharp
    namespace PushDemoApi.Models
    {
        public class PushTemplates
        {
            public class Generic
            {
                public const string Android = "{ \"notification\": { \"title\" : \"PushDemo\", \"body\" : \"$(alertMessage)\"}, \"data\" : { \"action\" : \"$(alertAction)\" } }";
                public const string iOS = "{ \"aps\" : {\"alert\" : \"$(alertMessage)\"}, \"action\" : \"$(alertAction)\" }";
            }

            public class Silent
            {
                public const string Android = "{ \"data\" : {\"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\"} }";
                public const string iOS = "{ \"aps\" : {\"content-available\" : 1, \"apns-priority\": 5, \"sound\" : \"\", \"badge\" : 0}, \"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\" }";
            }
        }
    }
    ```

    > [!NOTE]
    > Ez az osztály tartalmazza az ebben a forgatókönyvben megkövetelt általános és csendes értesítések jogkivonat-alapú értesítési hasznos adatait. A rendszer a [telepítésen](https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.installation?view=azure-dotnet) kívül határozza meg a hasznos adatokat, hogy lehetővé tegye a kísérletezést a meglévő telepítések a szolgáltatáson keresztüli frissítése nélkül. Ebben az oktatóanyagban a telepítésekkel kapcsolatos változások a hatókörön kívül vannak. Éles környezetben vegye figyelembe az [egyéni sablonokat](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).

1. Válassza az **általános**  >  **üres osztály**lehetőséget, írja be a *DeviceInstallation.cs* **nevet**, majd kattintson az **új** alkalmazás hozzáadása elemre.

    ```csharp
    using System.Collections.Generic;
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class DeviceInstallation
        {
            [Required]
            public string InstallationId { get; set; }

            [Required]
            public string Platform { get; set; }

            [Required]
            public string PushChannel { get; set; }

            public IList<string> Tags { get; set; } = Array.Empty<string>();
        }
    }
    ```

1. Adjon hozzá egy másik **üres osztályt** a *NotificationRequest.cs*nevű **modellek** mappához, majd adja hozzá a következő implementációt.

    ```csharp
    using System;

    namespace PushDemoApi.Models
    {
        public class NotificationRequest
        {
            public string Text { get; set; }
            public string Action { get; set; }
            public string[] Tags { get; set; } = Array.Empty<string>();
            public bool Silent { get; set; }
        }
    }
    ```

1. Adjon hozzá egy másik **üres osztályt** a *NotificationHubOptions.cs*nevű **modellek** mappához, majd adja hozzá a következő implementációt.

    ```csharp
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class NotificationHubOptions
        {
            [Required]
            public string Name { get; set; }

            [Required]
            public string ConnectionString { get; set; }
        }
    }
    ```

1. Adjon hozzá egy új mappát a *szolgáltatások*nevű **PushDemoApi** projekthez.

1. Adjon hozzá egy **üres felületet** a *INotificationService.cs*nevű **Services** mappához, majd adja hozzá a következő implementációt.

    ```csharp
    using System.Threading.Tasks;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public interface INotificationService
        {
            Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token);
            Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token);
            Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token);
        }
    }
    ```

1. Adjon hozzá egy **üres osztályt** a *NotificationHubsService.cs*nevű **Services** mappához, majd adja hozzá a következő kódot az **INotificationService** felület megvalósításához:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public class NotificationHubService : INotificationService
        {
            readonly NotificationHubClient _hub;
            readonly Dictionary<string, NotificationPlatform> _installationPlatform;
            readonly ILogger<NotificationHubService> _logger;

            public NotificationHubService(IOptions<NotificationHubOptions> options, ILogger<NotificationHubService> logger)
            {
                _logger = logger;
                _hub = NotificationHubClient.CreateClientFromConnectionString(
                    options.Value.ConnectionString,
                    options.Value.Name);

                _installationPlatform = new Dictionary<string, NotificationPlatform>
                {
                    { nameof(NotificationPlatform.Apns).ToLower(), NotificationPlatform.Apns },
                    { nameof(NotificationPlatform.Fcm).ToLower(), NotificationPlatform.Fcm }
                };
            }

            public async Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(deviceInstallation?.InstallationId) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.Platform) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.PushChannel))
                    return false;

                var installation = new Installation()
                {
                    InstallationId = deviceInstallation.InstallationId,
                    PushChannel = deviceInstallation.PushChannel,
                    Tags = deviceInstallation.Tags
                };

                if (_installationPlatform.TryGetValue(deviceInstallation.Platform, out var platform))
                    installation.Platform = platform;
                else
                    return false;

                try
                {
                    await _hub.CreateOrUpdateInstallationAsync(installation, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(installationId))
                    return false;

                try
                {
                    await _hub.DeleteInstallationAsync(installationId, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token)
            {
                if ((notificationRequest.Silent &&
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
                    (!notificationRequest.Silent &&
                    (string.IsNullOrWhiteSpace(notificationRequest?.Text)) ||
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)))
                    return false;

                var androidPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.Android :
                    PushTemplates.Generic.Android;

                var iOSPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.iOS :
                    PushTemplates.Generic.iOS;

                var androidPayload = PrepareNotificationPayload(
                    androidPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                var iOSPayload = PrepareNotificationPayload(
                    iOSPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                try
                {
                    if (notificationRequest.Tags.Length == 0)
                    {
                        // This will broadcast to all users registered in the notification hub
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, token);
                    }
                    else if (notificationRequest.Tags.Length <= 20)
                    {
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, notificationRequest.Tags, token);
                    }
                    else
                    {
                        var notificationTasks = notificationRequest.Tags
                            .Select((value, index) => (value, index))
                            .GroupBy(g => g.index / 20, i => i.value)
                            .Select(tags => SendPlatformNotificationsAsync(androidPayload, iOSPayload, tags, token));

                        await Task.WhenAll(notificationTasks);
                    }

                    return true;
                }
                catch (Exception e)
                {
                    _logger.LogError(e, "Unexpected error sending notification");
                    return false;
                }
            }

            string PrepareNotificationPayload(string template, string text, string action) => template
                .Replace("$(alertMessage)", text, StringComparison.InvariantCulture)
                .Replace("$(alertAction)", action, StringComparison.InvariantCulture);

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, token)
                };

                return Task.WhenAll(sendTasks);
            }

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, IEnumerable<string> tags, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, tags, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, tags, token)
                };

                return Task.WhenAll(sendTasks);
            }
        }
    }
    ```

    > [!NOTE]
    > A **SendTemplateNotificationAsync** számára megadott címke kifejezés legfeljebb 20 címkét tartalmaz. A legtöbb operátor esetében legfeljebb 6 lehet, de a kifejezés ebben az esetben csak a következőt tartalmazza: Örs (| |). Ha a kérelem több mint 20 címkét használ, akkor azokat több kérelemre kell bontani. További részletekért tekintse meg az [útválasztási és címkézési kifejezések](https://msdn.microsoft.com/library/azure/Dn530749.aspx?f=255&MSPPError=-2147217396) dokumentációját.

1. A **Startup.cs**-ben frissítse a **ConfigureServices** metódust, és adja hozzá a **NotificationHubsService** -t a **INotificationService**egypéldányos implementációja.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ...

        services.AddSingleton<INotificationService, NotificationHubService>();

        services.AddOptions<NotificationHubOptions>()
            .Configure(Configuration.GetSection("NotificationHub").Bind)
            .ValidateDataAnnotations();
    }
    ```

### <a name="create-the-notifications-api"></a>Az értesítések API létrehozása

1. **Vezérlő**  +  **Kattintson** a **vezérlők** mappára, majd válassza az **új fájl...** lehetőséget a **Hozzáadás** menüből.

1. Válassza a **ASP.net Core**  >  **web API-vezérlő osztály**elemet, írja be a *NotificationsController* **nevet**, majd kattintson az **új**elemre.

1. Adja hozzá a következő névtereket a fájl elejéhez.

    ```csharp
    using System.ComponentModel.DataAnnotations;
    using System.Net;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc;
    using PushDemoApi.Models;
    using PushDemoApi.Services;
    ```

1. Frissítse a sablonban lévő vezérlőt úgy, hogy az a **ControllerBase** -ből származik, és a **ApiController** attribútummal van díszítve.

    ```cs
    [ApiController]
    [Route("api/[controller]")]
    public class NotificationsController : ControllerBase
    {
        // Templated methods here
    }
    ```

    > [!NOTE]
    > A **vezérlő** alaposztálya támogatást nyújt a nézetekhez, de ez nem szükséges ebben az esetben, így a **ControllerBase** használható.

1. Ha úgy döntött, hogy elvégzi az [ügyfelek hitelesítését az API-kulcs szakasz használatával](#authenticate-clients-using-an-api-key-optional) , akkor a **NotificationsController** az **Engedélyezés** attribútummal is együtt kell díszíteni.

    ```cs
    [Authorize]
    ```

1. Frissítse a konstruktort, hogy fogadja el a **INotificationService** regisztrált példányát argumentumként, és rendelje hozzá egy ReadOnly tag számára.

    ```cs
    readonly INotificationService _notificationService;

    public NotificationsController(INotificationService notificationService)
    {
        _notificationService = notificationService;
    }
    ```

1. A **launchSettings.json** (a **Properties (Tulajdonságok** ) mappában) módosítsa a **launchUrl** az `weatherforecast` *API-ra vagy az értesítésekre* , hogy azok megfeleljenek a **RegistrationsController** **Route** attribútumban megadott URL-címnek.

1. A hibakeresés megkezdése (**parancs**  +  **beírása**) annak ellenőrzéséhez, hogy az alkalmazás működik-e az új **NotificationsController** , és egy **401 jogosulatlan** állapotot ad vissza.

    > [!NOTE]
    > Előfordulhat, hogy a Visual Studio nem indítja el automatikusan az alkalmazást a böngészőben. Ezen a ponton a [Poster](https://www.postman.com/downloads) használatával tesztelheti az API-t.

1. Az új **[Poster](https://www.postman.com/downloads)** lapon állítsa be a **beolvasási** kérést, és adja meg az alábbi címeket.

    ```bash
    https://localhost:5001/api/notifications
    ```

    > [!NOTE]
    > A localhost címnek meg kell egyeznie a Properties **applicationUrl**launchSettings.js**on tulajdonságban** található applicationUrl értékkel  >  ** **. Az alapértelmezett értéknek `https://localhost:5001;http://localhost:5000` azonban azt kell meggyőződnie, hogy egy 404-es választ kap-e.

1. Ha úgy dönt, hogy a [hitelesítő ügyfeleket egy API-kulcs szakasz használatával](#authenticate-clients-using-an-api-key-optional) tölti be, ügyeljen arra, hogy a kérések fejléceit adja meg a **apikey** értékének.

   | Kulcs                            | Érték                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. Kattintson a **Küldés** gombra.

    > [!NOTE]
    > Néhány **JSON** -tartalommal rendelkező **200 OK** állapotot kell kapnia.
    >
    > Ha **SSL-tanúsítvány-ellenőrzési** figyelmeztetést kap, a **Beállítások**között átválthatja a kérelem SSL-tanúsítvány ellenőrzése **[Poster](https://www.postman.com/downloads)** beállítást.

1. Cserélje le a sablonba sorolt osztályok metódusait a következő kódra.

    ```csharp
    [HttpPut]
    [Route("installations")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> UpdateInstallation(
        [Required]DeviceInstallation deviceInstallation)
    {
        var success = await _notificationService
            .CreateOrUpdateInstallationAsync(deviceInstallation, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpDelete()]
    [Route("installations/{installationId}")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<ActionResult> DeleteInstallation(
        [Required][FromRoute]string installationId)
    {
        var success = await _notificationService
            .DeleteInstallationByIdAsync(installationId, CancellationToken.None);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpPost]
    [Route("requests")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> RequestPush(
        [Required]NotificationRequest notificationRequest)
    {
        if ((notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
            (!notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Text)))
            return new BadRequestResult();

        var success = await _notificationService
            .RequestNotificationAsync(notificationRequest, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }
    ```

### <a name="create-the-api-app"></a>Az API-alkalmazás létrehozása

Most létrehoz egy [API-alkalmazást](https://azure.microsoft.com/services/app-service/api/) [Azure app Service](https://docs.microsoft.com/azure/app-service/) a háttér-szolgáltatás üzemeltetéséhez.  

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Kattintson az **erőforrás létrehozása**elemre, majd keresse meg és válassza ki az **API-alkalmazást**, majd kattintson a **Létrehozás**gombra.

1. Frissítse a következő mezőket, majd kattintson a **Létrehozás**gombra.

    **Alkalmazás neve:**  
    Adja meg az **API-alkalmazás** globálisan egyedi nevét

    **Előfizetés**  
    Válassza ki ugyanazt a cél **előfizetést** , amelyhez az értesítési központot létrehozta.

    **Erőforráscsoport:**  
    Válassza ki ugyanazt az **erőforráscsoportot** , amelyben az értesítési központot létrehozta.

    **App Service terv/hely:**  
    Új **app Service terv** létrehozása  

    > [!NOTE]
    > Váltson az alapértelmezett beállításról egy olyan csomagra, amely **SSL** -támogatást tartalmaz. Ellenkező esetben meg kell tennie a megfelelő lépéseket a Mobile alkalmazás használatakor, hogy megakadályozza a **http** -kérések letiltását.

    **Application Insights:**  
    Tartsa meg a javasolt beállítást (egy új erőforrás lesz létrehozva az adott névvel), vagy válasszon ki egy meglévő erőforrást.

1. Az **API-alkalmazás** üzembe helyezése után navigáljon az adott erőforráshoz.

1. Jegyezze fel az **URL** tulajdonságot az **Áttekintés**tetején található **Essentials** összefoglalóban. Ez az URL-cím a *háttérbeli végpont* , amelyet az oktatóanyag későbbi részében fog használni.

    > [!NOTE]
    > Az URL-cím a korábban megadott API-alkalmazás nevét használja a formátummal `https://<app_name>.azurewebsites.net` .

1. Válassza a **konfiguráció** elemet a listából (a **Beállítások**alatt).  

1. Az alábbi beállítások mindegyikéhez kattintson az **új alkalmazás beállítása** elemre a **név** és az **érték**megadásához, majd kattintson **az OK**gombra.

   | Name                               | Érték                          |
   | ---------------------------------- | ------------------------------ |
   | `Authentication:ApiKey`            | <api_key_value>                |
   | `NotificationHub:Name`             | <hub_name_value>               |
   | `NotificationHub:ConnectionString` | <hub_connection_string_value>  |

   > [!NOTE]
   > Ezek ugyanazok a beállítások, amelyeket korábban a felhasználói beállításokban definiált. Ezeket át kell másolni. A **hitelesítés: ApiKey** beállítás csak akkor szükséges, ha úgy döntött, hogy elvégzi az [ügyfelek hitelesítését az API-kulcs szakasz használatával](#authenticate-clients-using-an-api-key-optional) . Éles környezetekben olyan lehetőségeket kereshet, mint például az [Azure](https://azure.microsoft.com/services/key-vault)kulcstartó. Ebben az esetben a rendszer az egyszerűség kedvéért adja hozzá ezeket az alkalmazásokat.

1. Miután az összes Alkalmazásbeállítások hozzá lett adva, kattintson a **Save (Mentés**), majd a **Continue (folytatás**) gombra.

### <a name="publish-the-backend-service"></a>A háttér-szolgáltatás közzététele

Ezután telepítse az alkalmazást az API-alkalmazásba, hogy az elérhető legyen az összes eszközről.  

1. Ha még nem tette meg, módosítsa a konfigurációt **hibakeresésről** **kiadásra** .

1. **Vezérlő**  +  **Kattintson** a **PushDemoApi** projektre, majd válassza a közzététel az Azure-ban.. **.** lehetőséget a **Közzététel** menüből.

1. Ha a rendszer kéri, kövesse az Auth flow-t. Használja azt a fiókot, amelyet az előző [API-alkalmazás létrehozása](#create-the-api-app) szakaszban használt.

1. Válassza ki a listáról korábban létrehozott **Azure app Service API-alkalmazást** közzétételi célként, majd kattintson a **Közzététel**elemre.

A varázsló befejezése után közzéteszi az alkalmazást az Azure-ban, majd megnyitja az alkalmazást. Jegyezze fel az **URL-címet** , ha még nem tette meg. Ez az URL-cím a *háttérbeli végpont* , amely az oktatóanyag későbbi részében használatos.

### <a name="validating-the-published-api"></a>A közzétett API ellenőrzése

1. Nyisson meg egy új lapot a **[Poster](https://www.postman.com/downloads)** **-ben, és adja** meg az alábbi címeket. Cserélje le a helyőrzőt az alapcímere, amelyet az előző [Közzététel a háttér-szolgáltatásban](#publish-the-backend-service) című szakaszban jegyzett.

    ```csharp
    https://<app_name>.azurewebsites.net/api/notifications/installations
    ```

    > [!NOTE]
    > Az alapcímnek formátumban kell lennie``https://<app_name>.azurewebsites.net/``

1. Ha úgy dönt, hogy a [hitelesítő ügyfeleket egy API-kulcs szakasz használatával](#authenticate-clients-using-an-api-key-optional) tölti be, ügyeljen arra, hogy a kérések fejléceit adja meg a **apikey** értékének.

   | Kulcs                            | Érték                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. Válassza ki a **törzs** **nyers** beállítását, majd a formázási beállítások listájában válassza a **JSON** lehetőséget, majd adjon meg néhány helyőrző **JSON** -tartalmat:

    ```json
    {}
    ```

1. Kattintson a **Küldés** gombra.

    > [!NOTE]
    > A szolgáltatástól **400 hibás kérési** állapotot kell kapnia.

1. Végezze el újra a 1-4 lépést, de ezúttal a kérelmek végpontjának megadásával ellenőrizze, hogy ugyanaz a 400-es **hibás kérelem** érkezik-e.

    ```bash
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

> [!NOTE]
> Az API-t az érvényes kérési adatok használatával még nem lehet tesztelni, mivel a platform-specifikus információkra lesz szüksége az ügyfél Mobile alkalmazásban.
