---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: fc479522b3fd436ff02ff6b8985bdeddb66da90a
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85448735"
---
### <a name="create-the-xamarinforms-solution"></a>A Xamarin. Forms megoldás létrehozása

1. A **Visual Studióban**hozzon létre egy új **Xamarin. Forms** megoldást az **üres Forms alkalmazás** használatával sablonként, és adja meg a *PushDemo* a **projekt neveként**.

    > [!NOTE]
    > Az **Üres űrlapok alkalmazás konfigurálása** párbeszédpanelen ellenőrizze, hogy a **szervezet azonosítója** megegyezik-e a korábban használt értékkel, és hogy az **Android** -és **iOS** -célkitűzések is be vannak-e jelölve.

1. **Vezérlő**  +  **Kattintson** a *PushDemo* -megoldásra, majd válassza a **NuGet-csomagok frissítése**lehetőséget.
1. **Vezérlő**  +  **Kattintson** a *PushDemo* -megoldásra, majd válassza a **NuGet-csomagok kezelése..**. lehetőséget.
1. Keressen rá **aNewtonsoft.Jsre** , és ellenőrizze, hogy be van-e jelölve.
1. Kattintson a **csomagok hozzáadása**lehetőségre, majd az **elfogadás** gombra, amikor a rendszer kéri, hogy fogadja el a licencfeltételeket.
1. Az alkalmazás létrehozása és futtatása minden egyes cél platformon (a**parancs**  +  **megadása**) a sablonban lévő alkalmazás futtatásának teszteléséhez az eszközön (k).

### <a name="implement-the-cross-platform-components"></a>A platformok közötti összetevők implementálása

1. **Vezérlő**  +  **Kattintson** a **PushDemo** projektre, válassza **az új mappa** lehetőséget a **Hozzáadás** menüben, majd kattintson a **Hozzáadás** a *modellek* használatával **mappa neveként**elemre.

1. **Vezérlő**  +  **Kattintson** a **modellek** mappára, majd válassza az **új fájl...** lehetőséget a **Hozzáadás** menüből.

1. Válassza az **általános**  >  **üres osztály**lehetőséget, írja be a *DeviceInstallation.cs*, majd adja hozzá a következő implementációt.

    ```csharp
    using System.Collections.Generic;
    using Newtonsoft.Json;

    namespace PushDemo.Models
    {
        public class DeviceInstallation
        {
            [JsonProperty("installationId")]
            public string InstallationId { get; set; }

            [JsonProperty("platform")]
            public string Platform { get; set; }

            [JsonProperty("pushChannel")]
            public string PushChannel { get; set; }

            [JsonProperty("tags")]
            public List<string> Tags { get; set; } = new List<string>();
        }
    }
    ```

1. Adjon hozzá egy **üres enumerálást** a *PushDemoAction.cs* nevű **modellek** mappához a következő implementációval.

    ```csharp
    namespace PushDemo.Models
    {
        public enum PushDemoAction
        {
            ActionA,
            ActionB
        }
    }
    ```

1. Adjon hozzá egy új mappát a *szolgáltatások* nevű **PushDemo** projekthez, majd adjon hozzá egy **üres osztályt** a *ServiceContainer.cs* nevű mappához a következő implementációval.

     ```csharp
    using System;
    using System.Collections.Generic;

    namespace PushDemo.Services
    {
        public static class ServiceContainer
        {
            static readonly Dictionary<Type, Lazy<object>> services
                = new Dictionary<Type, Lazy<object>>();

            public static void Register<T>(Func<T> function)
                => services[typeof(T)] = new Lazy<object>(() => function());

            public static T Resolve<T>()
                => (T)Resolve(typeof(T));

            public static object Resolve(Type type)
            {
                {
                    if (services.TryGetValue(type, out var service))
                        return service.Value;

                    throw new KeyNotFoundException($"Service not found for type '{type}'");
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Ez a [ServiceContainer](https://github.com/xamcat/mobcat-library/blob/master/MobCAT/ServiceContainer.cs) osztály lerövidített verziója a [XamCAT](https://github.com/xamcat/mobcat-library) adattárból. A rendszer a NOB-ot (a vezérlő inverzióját) használja.

1. Adjon hozzá egy **üres felületet** a *IDeviceInstallationService.cs*nevű **Services** mappához, majd adja hozzá a következő kódot.

    ```csharp
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IDeviceInstallationService
        {
            string Token { get; set; }
            bool NotificationsSupported { get; }
            string GetDeviceId();
            DeviceInstallation GetDeviceInstallation(params string[] tags);
        }
    }
    ```

    > [!NOTE]
    > Ezt az interfészt a rendszer minden egyes cél bootstrapped fogja megvalósítani, és a háttérrendszer által igényelt platform-specifikus funkciókat és **DeviceInstallation** -információkat biztosítja.

1. Adjon hozzá egy másik **üres felületet** a *INotificationRegistrationService.cs*nevű **szolgáltatások** mappához, majd adja hozzá a következő kódot.  

    ```csharp
    using System.Threading.Tasks;

    namespace PushDemo.Services
    {
        public interface INotificationRegistrationService
        {
            Task DeregisterDeviceAsync();
            Task RegisterDeviceAsync(params string[] tags);
            Task RefreshRegistrationAsync();
        }
    }
    ```

    > [!NOTE]
    > Ez kezeli az ügyfél és a háttér szolgáltatás közötti interakciót.

1. Adjon hozzá egy másik **üres felületet** a *INotificationActionService.cs*nevű **szolgáltatások** mappához, majd adja hozzá a következő kódot.  

    ```csharp
    namespace PushDemo.Services
    {
        public interface INotificationActionService
        {
            void TriggerAction(string action);
        }
    }
    ```

    > [!NOTE]
    > Ezt egyszerű mechanizmusként használjuk az értesítési műveletek kezelésének központosításához.

1. Adjon hozzá egy **üres felületet** a *IPushDemoNotificationActionService.cs* nevű *INotificationActionService*, **amely a következő** implementációval származik.  

    ```csharp
    using System;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IPushDemoNotificationActionService : INotificationActionService
        {
            event EventHandler<PushDemoAction> ActionTriggered;
        }
    }
    ```

    > [!NOTE]
    > Ez a típus kifejezetten a **PushDemo** alkalmazásra vonatkozik, és a **PushDemoAction** enumerálással azonosítja az éppen aktivált műveletet.

1. Adjon hozzá egy **üres osztályt** a *NotificationRegistrationService.cs* nevű *INotificationRegistrationService* **a következő** kóddal.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    using PushDemo.Models;
    using Xamarin.Essentials;

    namespace PushDemo.Services
    {
        public class NotificationRegistrationService : INotificationRegistrationService
        {
            const string RequestUrl = "api/notifications/installations";
            const string CachedDeviceTokenKey = "cached_device_token";
            const string CachedTagsKey = "cached_tags";

            string _baseApiUrl;
            HttpClient _client;
            IDeviceInstallationService _deviceInstallationService;

            public NotificationRegistrationService(string baseApiUri, string apiKey)
            {
                _client = new HttpClient();
                _client.DefaultRequestHeaders.Add("Accept", "application/json");
                _client.DefaultRequestHeaders.Add("apikey", apiKey);

                _baseApiUrl = baseApiUri;
            }

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService = ServiceContainer.Resolve<IDeviceInstallationService>());

            public async Task DeregisterDeviceAsync()
            {
                var cachedToken = await SecureStorage.GetAsync(CachedDeviceTokenKey)
                    .ConfigureAwait(false);

                if (cachedToken == null)
                    return;

                var deviceId = DeviceInstallationService?.GetDeviceId();

                if (string.IsNullOrWhiteSpace(deviceId))
                    throw new Exception("Unable to resolve an ID for the device.");

                await SendAsync(HttpMethod.Delete, $"{RequestUrl}/{deviceId}")
                    .ConfigureAwait(false);

                SecureStorage.Remove(CachedDeviceTokenKey);
                SecureStorage.Remove(CachedTagsKey);
            }

            public async Task RegisterDeviceAsync(params string[] tags)
            {
                var deviceInstallation = DeviceInstallationService?.GetDeviceInstallation(tags);

                await SendAsync<DeviceInstallation>(HttpMethod.Put, RequestUrl, deviceInstallation)
                    .ConfigureAwait(false);

                await SecureStorage.SetAsync(CachedDeviceTokenKey, deviceInstallation.PushChannel)
                    .ConfigureAwait(false);

                await SecureStorage.SetAsync(CachedTagsKey, JsonConvert.SerializeObject(tags));
            }

            public async Task RefreshRegistrationAsync()
            {
                var cachedToken = await SecureStorage.GetAsync(CachedDeviceTokenKey)
                    .ConfigureAwait(false);

                var serializedTags = await SecureStorage.GetAsync(CachedTagsKey)
                    .ConfigureAwait(false);

                if (string.IsNullOrWhiteSpace(cachedToken) ||
                    string.IsNullOrWhiteSpace(serializedTags) ||
                    string.IsNullOrWhiteSpace(DeviceInstallationService.Token) ||
                    cachedToken == DeviceInstallationService.Token)
                    return;

                var tags = JsonConvert.DeserializeObject<string[]>(serializedTags);

                await RegisterDeviceAsync(tags);
            }

            async Task SendAsync<T>(HttpMethod requestType, string requestUri, T obj)
            {
                string serializedContent = null;

                await Task.Run(() => serializedContent = JsonConvert.SerializeObject(obj))
                    .ConfigureAwait(false);

                await SendAsync(requestType, requestUri, serializedContent);
            }

            async Task SendAsync(
                HttpMethod requestType,
                string requestUri,
                string jsonRequest = null)
            {
                var request = new HttpRequestMessage(requestType, new Uri($"{_baseApiUrl}{requestUri}"));

                if (jsonRequest != null)
                    request.Content = new StringContent(jsonRequest, Encoding.UTF8, "application/json");

                var response = await _client.SendAsync(request).ConfigureAwait(false);

                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

    > [!NOTE]
    > A **apiKey** argumentum csak akkor szükséges, ha úgy dönt, hogy a [hitelesítő ÜGYFELEKet az API-kulcs szakasz használatával](#authenticate-clients-using-an-api-key-optional) hajtja végre.

1. Adjon hozzá egy **üres osztályt** a *PushDemoNotificationActionService.cs* nevű *IPushDemoNotificationActionService* **a következő** kóddal.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public class PushDemoNotificationActionService : IPushDemoNotificationActionService
        {
            readonly Dictionary<string, PushDemoAction> _actionMappings = new Dictionary<string, PushDemoAction>
            {
                { "action_a", PushDemoAction.ActionA },
                { "action_b", PushDemoAction.ActionB }
            };

            public event EventHandler<PushDemoAction> ActionTriggered = delegate { };

            public void TriggerAction(string action)
            {
                if (!_actionMappings.TryGetValue(action, out var pushDemoAction))
                    return;

                List<Exception> exceptions = new List<Exception>();

                foreach (var handler in ActionTriggered?.GetInvocationList())
                {
                    try
                    {
                        handler.DynamicInvoke(this, pushDemoAction);
                    }
                    catch (Exception ex)
                    {
                        exceptions.Add(ex);
                    }
                }

                if (exceptions.Any())
                    throw new AggregateException(exceptions);
            }
        }
    }
    ```

1. Adjon hozzá egy **üres osztályt** a *config.cs* nevű **PushDemo** projekthez a következő implementációval.  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            public static string ApiKey = "API_KEY";
            public static string BackendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
        }
    }
    ```

    > [!NOTE]
    > Ezt egyszerű módszerként használjuk a források felügyeletének megtartására. Ezeket az értékeket lecserélheti egy automatizált összeállítás részeként, vagy felülbírálhatja őket egy helyi részleges osztály használatával. Ezt a következő lépésben fogja megtenni.
    >
    > A **ApiKey** mező csak akkor szükséges, ha úgy dönt, hogy elvégzi az [ügyfelek HITELESÍTÉSét az API-kulcs szakasz használatával](#authenticate-clients-using-an-api-key-optional) .

1. Adjon hozzá egy másik **üres osztályt** a **PushDemo** projekthez, ezúttal a *config. local_secrets. cs* néven a következő implementációval.  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            static Config()
            {
                ApiKey = "<your_api_key>";
                BackendServiceEndpoint = "<your_api_app_url>";
            }
        }
    }
    ```

    > [!NOTE]
    > Cserélje le a helyőrző értékeket saját értékre. Ezeket a háttér-szolgáltatás létrehozásakor jegyezze fel. Az **API-alkalmazás** URL-címének kell lennie ``https://<api_app_name>.azurewebsites.net/`` . Ne felejtse el hozzáadni a ``*.local_secrets.*`` gitignore-fájlhoz, hogy elkerülje a fájl véglegesítését.
    >
    > A **ApiKey** mező csak akkor szükséges, ha úgy dönt, hogy elvégzi az [ügyfelek HITELESÍTÉSét az API-kulcs szakasz használatával](#authenticate-clients-using-an-api-key-optional) .

1. Adjon hozzá egy **üres osztályt** a *bootstrap.cs* nevű **PushDemo** projekthez a következő implementációval.  

    ```csharp
    using System;
    using PushDemo.Services;

    namespace PushDemo
    {
        public static class Bootstrap
        {
            public static void Begin(Func<IDeviceInstallationService> deviceInstallationService)
            {
                ServiceContainer.Register(deviceInstallationService);

                ServiceContainer.Register<IPushDemoNotificationActionService>(()
                    => new PushDemoNotificationActionService());

                ServiceContainer.Register<INotificationRegistrationService>(()
                    => new NotificationRegistrationService(
                        Config.BackendServiceEndpoint,
                        Config.ApiKey));
            }
        }
    }
    ```

    > [!NOTE]
    > A **BEGIN** metódust az egyes platformok fogják meghívni, amikor az alkalmazás elindul a **IDeviceInstallationService**platform-specifikus implementációjában.
    >
    > A **NotificationRegistrationService** **apiKey** konstruktor argumentum csak akkor szükséges, ha úgy dönt, hogy elvégzi az [ügyfelek hitelesítését egy API-kulcs szakasz használatával](#authenticate-clients-using-an-api-key-optional) .

### <a name="implement-the-cross-platform-ui"></a>A platformfüggetlen felhasználói felület implementálása

1. A **PushDemo** projektben nyissa meg a **Főoldal. XAML** , és cserélje le a **StackLayout** vezérlőt a következőre.

    ```xml
    <StackLayout VerticalOptions="EndAndExpand"  
                 HorizontalOptions="FillAndExpand"
                 Padding="20,40">
        <Button x:Name="RegisterButton"
                Text="Register"
                Clicked="RegisterButtonClicked" />
        <Button x:Name="DeregisterButton"
                Text="Deregister"
                Clicked="DeregisterButtonClicked" />
    </StackLayout>
    ```

1. A **MainPage.XAML.cs**-ben most adjon hozzá egy **írásvédett** háttérbeli mezőt a **INotificationRegistrationService** megvalósítására mutató hivatkozás tárolásához.

    ```csharp
    readonly INotificationRegistrationService _notificationRegistrationService;
    ```

1. A **Főoldal** konstruktorában oldja meg a **INotificationRegistrationService** implementációját a **ServiceContainer** használatával, és rendelje hozzá a *_notificationRegistrationService_* -háttér mezőhöz.

    ```csharp
    public MainPage()
    {
        InitializeComponent();

        _notificationRegistrationService =
            ServiceContainer.Resolve<INotificationRegistrationService>();
    }
    ```

1. Implementálja a **RegisterButton** és a **DeregisterButton** gombok eseménykezelőit, és **rákattintott** a megfelelő **regisztrálási regisztrációt**elvégező / **Deregister** metódusokat hívó események elemre.

    ```csharp
    void RegisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.RegisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device registered"); });

    void DeregisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.DeregisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device deregistered"); });

    void ShowAlert(string message)
        => MainThread.BeginInvokeOnMainThread(()
            => DisplayAlert("PushDemo", message, "OK").ContinueWith((task)
                => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. A **app.XAML.cs**most ellenőrizze, hogy a következő névterek hivatkoznak-e.

    ```csharp
    using PushDemo.Models;
    using PushDemo.Services;
    using Xamarin.Essentials;
    using Xamarin.Forms;
    ```

1. Implementálja az eseménykezelőt a **IPushDemoNotificationActionService** **ActionTriggered** eseményéhez.

    ```csharp
    void NotificationActionTriggered(object sender, PushDemoAction e)
        => ShowActionAlert(e);

    void ShowActionAlert(PushDemoAction action)
        => MainThread.BeginInvokeOnMainThread(()
            => MainPage?.DisplayAlert("PushDemo", $"{action} action received", "OK")
                .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. Az **alkalmazás** konstruktorában oldja meg a **IPushNotificationActionService** implementációját a **ServiceContainer** használatával, és fizessen elő a **IPushDemoNotificationActionService** **ActionTriggered** eseményre.

    ```csharp
    public App()
    {
        InitializeComponent();

        ServiceContainer.Resolve<IPushDemoNotificationActionService>()
            .ActionTriggered += NotificationActionTriggered;

        MainPage = new MainPage();
    }
    ```

    > [!NOTE]
    > Ez egyszerűen a leküldéses értesítési műveletek fogadásának és propagálásának bemutatására szolgál. Ezeket általában csendesen kezeljük, például egy adott nézetre navigálva, vagy egy bizonyos adatfrissítéshez **, nem** pedig a **főoldalon**keresztüli riasztást.
