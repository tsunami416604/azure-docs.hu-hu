---
title: "Geokerítéses leküldéses értesítések az Azure Notification Hubs és a Bing térbeli adatainak használatával | Microsoft Docs"
description: "Ebben az oktatóanyagban elsajátíthatja a helyalapú leküldéses értesítések kézbesítését az Azure Notification Hubs és a Bing térbeli adatainak használatával."
services: notification-hubs
documentationcenter: windows
keywords: "leküldéses értesítés,leküldéses értesítés"
author: dend
manager: yuaxu
editor: dend
ms.assetid: f41beea1-0d62-4418-9ffc-c9d70607a1b7
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/13/2017
ms.author: dendeli
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 8db82ae9f37a89b6b7049208133949a7f49e9d92
ms.contentlocale: hu-hu
ms.lasthandoff: 09/14/2017

---
# <a name="geo-fenced-push-notifications-with-azure-notification-hubs-and-bing-spatial-data"></a>Geokerítéses leküldéses értesítések az Azure Notification Hubs és a Bing térbeli adatainak használatával
> [!NOTE]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02).
> 
> 

Ebben az oktatóanyagban elsajátíthatja, hogyan kézbesíthet helyalapú leküldéses értesítéseket egy Univerzális Windows Platform-alkalmazásból az Azure Notification Hubs és a Bing térbeli adatainak használatával.

## <a name="prerequisites"></a>Előfeltételek
Először is győződjön meg arról, hogy rendelkezik az összes szoftveres és szolgáltatási előfeltétellel:

* [Visual Studio 2015 Update 1](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) vagy újabb (a [Community Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409) is megfelelő). 
* Az [Azure SDK](https://azure.microsoft.com/downloads/) legújabb verziója. 
* [Fiók a Bing Térképek fejlesztői központjához](https://www.bingmapsportal.com/) (ingyenesen létrehozható, és hozzárendelhető a Microsoft-fiókjához). 

## <a name="getting-started"></a>Első lépések
Kezdjük a projekt létrehozásával. A Visual Studióban indítson egy **Üres alkalmazás (Univerzális Windows)** típusú új projektet.

![](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

A projekt létrehozása után kihasználhatja az alkalmazás lehetőségeit. Most adjuk meg a geokerítés-infrastruktúra valamennyi beállítását. Ehhez a Bing szolgáltatásait fogjuk használni, és a következő nyilvános REST API-végpont használatával kérdezhetjük le az adott helyeket:

    http://spatial.virtualearth.net/REST/v1/data/

A következő paramétereket kell megadnia a rendszer működéséhez:

* **Adatforrás azonosítója** és **Adatforrás neve** – a Bing Térképek API-adatforrásai számos összegyűjtött metaadatot tartalmaznak, például a helyadatokat és a nyitvatartási időt. További információt itt talál. 
* **Entitás neve** – az értesítés hivatkozási pontjaként használni kívánt entitás. 
* **Bing Térképek API-kulcs** – ezt a kulcsot már korábban megkapta, amikor létrehozta a fiókját a Bing fejlesztői központjában.

Most tekintsük át részletesen a fenti elemek beállításait.

## <a name="setting-up-the-data-source"></a>Az adatforrás beállítása
Az adatforrást a Bing Térképek fejlesztői központjában állíthatja be. A felső navigációs sávban válassza a **Data sources** (Adatforrások) > **Manage Data Sources** (Adatforrások kezelése) lehetőséget.

![](./media/notification-hubs-geofence/bing-maps-manage-data.png)

Ha még nem használta korábban a Bing Térképek API-t, valószínűleg nincs elérhető adatforrás. A **Data sources** (Adatforrások) > **Upload data** (Adatok feltöltése) elemet kiválasztva létrehozhat egy újat. Győződjön meg arról, hogy az összes kötelező mezőt kitöltötte:

![](./media/notification-hubs-geofence/bing-maps-create-data.png)

Talán nem teljesen biztos benne, hogy mi az adatfájl, és mit kell feltöltenie. Ebben a tesztben a következő mintát használjuk, amely San Francisco-i partszakasz egy területét jelöli:

    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))

A fentiek a következő entitást képviselik:

![](./media/notification-hubs-geofence/bing-maps-geofence.png)

Másolja és illessze be a fenti karakterláncot egy új fájlba, mentse a fájt **NotificationHubsGeofence.pipe** néven, majd töltse fel a Bing fejlesztői központba.

> [!NOTE]
> Lehetséges, hogy új, a **Lekérdezési kulcstól** eltérő **Főkulcsot** kell megadnia. Csak hozzon létre egy új kulcsot az irányítópulton, és frissítse az adatforrás-feltöltési oldalt.
> 
> 

Az adatfájl feltöltése után közzé kell tennie az adatforrást. 

A fentiekhez hasonló módon lépjen a **Manage Data Sources** (Adatforrások kezelése) oldalra, keresse meg az adatforrást a listában, majd válassza ki a **Publish** (Közzététel) elemet az **Actions** (Műveletek) oszlopban. Az adatforrás rövid időn belül megjelenik a **Published Data Sources** (Közzétett adatforrások) lapon:

![](./media/notification-hubs-geofence/bing-maps-published-data.png)

Az **Edit** (Szerkesztés) gombra kattintva (egy pillantással) áttekintheti a létrehozott helyeket:

![](./media/notification-hubs-geofence/bing-maps-data-details.png)

A portál ekkor még nem jeleníti meg a létrehozott geokerítés határvonalait. Ehhez csak meg kell erősítenie, hogy a megadott hely a megfelelő környéken található.

Most már az adatforrás minden követelménye teljesül. Az API-hívás kérési URL-jének részleteit úgy szerezheti be, ha a Bing Térképek fejlesztői központban kiválasztja a **Data Sources** (Adatforrások), majd a **Data Source Information** (Adatforrás adatai) elemet.

![](./media/notification-hubs-geofence/bing-maps-data-info.png)

Most a **Query URL** (Lekérdezési URL) az, amit keresünk. Ez az a végpont, ahol lekérdezéseket végezhetünk annak ellenőrzésére, hogy az eszköz egy adott hely határvonalain belül vagy kívül található-e. Ennek az ellenőrzésnek az elvégzéséhez egyszerűen egy GET hívást kell végrehajtanunk a lekérdezés URL-címén a következő paraméterek hozzáfűzésével:

    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY

Így az eszközről beszerzett célpontot ad meg, és a Bing Térképek automatikusan kiszámítja, hogy az a geokerítésen belül van-e. Ha böngésző (vagy a cURL) használatával hajtja végre a kérést, szabványos JSON-választ kap:

![](./media/notification-hubs-geofence/bing-maps-json.png)

Ez a válasz csak akkor jelenik meg, ha a pont a kijelölt határokon belül található. Ha ez nem így van, az **Eredmények** gyűjtő üres lesz:

![](./media/notification-hubs-geofence/bing-maps-nores.png)

## <a name="setting-up-the-uwp-application"></a>Az UWP-alkalmazás beállítása
Most, hogy az adatforrás rendelkezésre áll, elkezdhetünk dolgozni a korábban elindított UWP-alkalmazással.

Mindenekelőtt engedélyezni kell a helyalapú szolgáltatásokat az alkalmazásban. Ehhez nyissa meg a `Package.appxmanifest` fájlt a **Megoldáskezelőben**.

![](./media/notification-hubs-geofence/vs-package-manifest.png)

A csomagtulajdonságokat tartalmazó megnyíló lapon válassza a **Capabilities** (Képességek) elemet, majd a **Location** (Hely) elemet:

![](./media/notification-hubs-geofence/vs-package-location.png)

Mivel a helymeghatározási képesség deklarálva van, hozzon létre egy `Core` nevű új mappát a megoldásban, és adjon hozzá egy `LocationHelper.cs` nevű fájlt:

![](./media/notification-hubs-geofence/vs-location-helper.png)

A `LocationHelper` osztály itt meglehetősen egyszerű, mindössze a felhasználó tartózkodási helyének lekérését biztosítja a rendszer API-n keresztül:

    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }

A felhasználói helyadatok UWP-alkalmazásokban történő lekérdezéséről a hivatalos [MSDN-dokumentumban](https://msdn.microsoft.com/library/windows/apps/mt219698.aspx) talál további információkat.

Nyissa meg a főoldal (`MainPage.xaml.cs`) kódoldalát annak ellenőrzéséhez, hogy a helyadatok lekérése valóban működik-e. Hozzon létre egy új eseménykezelőt a `Loaded` eseményhez a `MainPage` konstruktorban:

    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }

Az eseménykezelő megvalósítása a következő:

    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }

Vegye figyelembe, hogy a kezelőt aszinkron módon deklaráltuk, mivel a `GetCurrentLocation` várható, ezért aszinkron környezetben kell végrehajtani. Továbbá mivel bizonyos körülmények között null helyadatot kaphatunk (például ha a helymeghatározási szolgáltatás le van tiltva, vagy az alkalmazásnak nincs engedélye a helyadatok eléréséhez), meg kell győződnünk a megfelelő kezeléséről egy null ellenőrzéssel.

Futtassa az alkalmazást. Engedélyezze a helyadatokhoz való hozzáférést:

![](./media/notification-hubs-geofence/notification-hubs-location-access.png)

Az alkalmazás elindítása után a koordináták a **Kimenet** ablakban láthatók:

![](./media/notification-hubs-geofence/notification-hubs-location-output.png)

Most, hogy már ismeri a helyadatok beszerzésének működését, eltávolíthatja a Loaded teszt-eseménykezelőjét, mert többé nem lesz rá szükség.

A következő lépés a helyadatok módosításainak rögzítése. Ehhez lépjen vissza a `LocationHelper` osztályhoz, és adja hozzá a `PositionChanged` eseménykezelőjét:

    geolocator.PositionChanged += Geolocator_PositionChanged;

A hely koordinátái a **Kimenet** ablakban jelennek meg:

    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }

## <a name="setting-up-the-backend"></a>A háttérrendszer beállítása
Töltse le a [.NET-háttérrendszer mintáját a GitHubról](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers). A letöltés után nyissa meg a `NotifyUsers` mappát, majd a `NotifyUsers.sln` fájlt.

Állítsa be **Indítási projektként**, majd futtassa az `AppBackend` projektet.

![](./media/notification-hubs-geofence/vs-startup-project.png)

A projekt már konfigurálva van leküldéses értesítések céleszközökre történő küldésére, így csak két dolgot kell tennünk: lecseréljük az értesítési központ megfelelő kapcsolati karakterláncát, illetve határvonal-azonosítást végzünk, hogy az üzenetet csak a geokerítésen belül tartózkodó felhasználók kapják meg.

A kapcsolati karakterlánc konfigurálásához a `Models` mappában nyissa meg a következőt: `Notifications.cs`. A `NotificationHubClient.CreateClientFromConnectionString`függvénynek tartalmaznia kell az értesítési központ adatait, amelyet az [Azure Portalon](https://portal.azure.com) szerezhet be (nyissa meg a **Beállítások** menü **Hozzáférési házirendek** paneljét). Mentse a frissített konfigurációs fájlt.

Most létre kell hoznunk egy modellt a Bing Térképek API-eredményéhez. Ennek a legegyszerűbb módja, ha megnyitja a `Models` mappát, majd az **Add** (Hozzáadás) > **Class** (Osztály) elemet választja. Nevezze el a következőképpen: `GeofenceBoundary.cs`. Ezután másolja át a JSON-t az első szakaszban tárgyalt API-válaszból, majd a Visual Studióban válassza a következőt: **Szerkesztés** > **Irányított beillesztés** > **Paste JSON as Classes**(JSON beillesztése osztályokként). 

Így biztosítható, hogy az objektum deszerializálása pontosan a kívánt módon történjen. Az eredményül kapott osztály a következőhöz hasonló lesz:

    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }

Ezután nyissa meg a következőt: `Controllers` > `NotificationsController.cs`. Kisebb módosításokat kell végeznünk a Post híváson a kívánt hosszúsági és szélességi értékek figyelembe vételéhez. Ehhez egyszerűen adja hozzá a függvényaláíráshoz a következő két karakterláncot: `latitude` és `longitude`.

    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)

Hozzon létre a projektben egy új, `ApiHelper.cs` nevű osztályt. Ezzel fogunk csatlakozni a Binghez, hogy ellenőrizzük a pontok határvonalainak metszéspontjait. Adjon hozzá egy `IsPointWithinBounds` függvényt, amely a következőre hasonlít:

    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }

> [!NOTE]
> Az API-végpontot cserélje le a Bing fejlesztői központjából korábban beszerzett lekérdezési URL-címre (ugyanez vonatkozik az API-kulcsra is). 
> 
> 

Ha a lekérdezés eredményes volt, akkor az adott pont a geokerítés határain belül van, így a rendszerünk a következőt adja vissza: `true`. Ha a lekérdezés eredménytelen, a Bing ezzel azt jelzi, hogy a pont a keresési területen kívül található, ezért `false` értéket adunk vissza.

A `NotificationsController.cs`-hez visszatérve hozzon létre egy ellenőrzést közvetlenül a switch utasítás előtt:

    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }

Az értesítés így csak akkor lesz elküldve, ha a pont a határvonalakon belül található.

## <a name="testing-push-notifications-in-the-uwp-app"></a>Leküldéses értesítések tesztelése UWP-alkalmazásban
Az UWP-alkalmazásban most már tudjuk tesztelni az értesítéseket. A `LocationHelper` osztályba hozzon létre egy új függvényt – `SendLocationToBackend`:

    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }

> [!NOTE]
> Cserélje le a `POST_URL` elemet az előző szakaszban létrehozott üzembe helyezett webalkalmazás helyére. Most a helyi futtatás is megfelelő, de ha nyilvános verziót kíván üzembe helyezni, egy külső szolgáltatón kell üzemeltetnie.
> 
> 

Most regisztráljuk az UWP-alkalmazást a leküldéses értesítésekre. A Visual Studióban válassza a **Project** (Projekt) > **Store** (Áruház) > **Associate app with the store** (Alkalmazás társítása az Áruházzal) lehetőséget.

![](./media/notification-hubs-geofence/vs-associate-with-store.png)

Amikor bejelentkezik a fejlesztői fiókba, válasszon ki egy meglévő alkalmazást, vagy hozzon létre egy újat, és társítsa hozzá a csomagot. 

Nyissa meg a fejlesztői központot, és nyissa meg a most létrehozott alkalmazást. Válassza a **Szolgáltatások** > **Leküldéses értesítések** > **Live Services webhely** lehetőséget.

![](./media/notification-hubs-geofence/ms-live-services.png)

A webhelyen jegyezze fel az **Application Secret** (Alkalmazástitok) és a **Package SID** (Csomag biztonsági azonosítója) értékeit. Az Azure Portalon mindkettőre szüksége lesz. Nyissa meg az értesítési központot, válassza ki a **Beállítások** > **Értesítési szolgáltatások** > **Windows (WNS)** elemet, majd töltse ki a kötelező mezőket.

![](./media/notification-hubs-geofence/notification-hubs-wns.png)

Válassza a **Mentés** elemet.

Nyissa meg a **References** (Hivatkozások) elemet a **Megoldáskezelőben**, és válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget. Fel kell vennünk egy, a **Microsoft Azure Service Bus felügyelt kódtárára mutató hivatkozást**. Ehhez csak keressen a `WindowsAzure.Messaging.Managed` elemre, és adja hozzá a projekthez.

![](./media/notification-hubs-geofence/vs-nuget.png)

Tesztelési célokra újra létrehozhatjuk a `MainPage_Loaded` eseménykezelőt, és hozzáadhatjuk a következő kódrészletet:

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }

A fenti művelet regisztrálja az alkalmazást az értesítési központban. És már kész is van. 

A `LocationHelper` osztály `Geolocator_PositionChanged` kezelőjében hozzáadhat egy tesztkódszakaszt, amely a geokerítésen belülre kényszeríti a helyet:

    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");

Mivel nem a valós koordinátákat továbbítjuk (lehetséges, hogy ezek jelenleg nincsenek a határvonalakon belül), valamint előre meghatározott tesztértékeket használunk, frissítéskor a következő értesítés jelenik meg:

![](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

## <a name="whats-next"></a>Hogyan tovább?
A fentiek mellett további lépésekre is szükség lehet, hogy a megoldás éles környezetben is használható legyen.

Mindenekelőtt a geokerítések dinamikus viselkedését kell biztosítania. Ehhez úgy kell beállítani a Bing API-t, hogy új határvonalakat tudjunk feltölteni a meglévő adatforráson belül. Erről további információkat a [Bing térbeliadat-szolgáltatási API-dokumentációjában](https://msdn.microsoft.com/library/ff701734.aspx) talál.

Ezután [címkézéssel](notification-hubs-tags-segment-push-message.md) megadhat adott felhasználókat, így biztosíthatja, hogy a megfelelő személyek kapják meg az értesítéseket.

A fenti megoldás egy olyan forgatókönyvet ismertet, amelyben sokféle célplatform lehetséges, ezért a geokerítést nem korlátoztuk rendszerspecifikus képességekkel. A Univerzális Windows-platform ugyanakkor a [geokerítések azonnali észlelésére](https://msdn.microsoft.com/windows/uwp/maps-and-location/set-up-a-geofence) szolgáló képességekkel rendelkezik.

A Notification Hubs képességeiről a [dokumentációs portálon](https://azure.microsoft.com/documentation/services/notification-hubs/) talál további részleteket.


