---
title: Geokerítéses leküldéses értesítések az Azure Notification Hubs és a Bing térbeli adatainak használatával | Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogyan küldhet helyalapú leküldéses értesítéseket az Azure Notification Hubs és a Bing térbeli adatainak használatával.
services: notification-hubs
documentationcenter: windows
keywords: leküldéses értesítés,leküldéses értesítés
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: f41beea1-0d62-4418-9ffc-c9d70607a1b7
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 27f978fac1f8aa68aa0eb1a6ffcec4e0e81b0df5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778443"
---
# <a name="tutorial-push-location-based-notifications-with-azure-notification-hubs-and-bing-spatial-data"></a>Oktatóanyag: Helyalapú leküldéses értesítések küldése az Azure Notification Hubs és a Bing térbeli adatainak használatával
Ebben az oktatóanyagban elsajátíthatja, hogyan küldhet helyalapú leküldéses értesítéseket az Azure Notification Hubs és a Bing térbeli adatainak használatával. 

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Az adatforrás beállítása
> * Az UWP-alkalmazás beállítása
> * A háttérrendszer beállítása
> * Leküldéses értesítések tesztelése Universal Windows Platform (UWP)-alkalmazásban


## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.
- [Visual Studio 2015 1. frissítés](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) vagy újabb ([Community Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409). 
- Az [Azure SDK](https://azure.microsoft.com/downloads/) legújabb verziója. 
- [Fiók a Bing Térképek fejlesztői központjához](https://www.bingmapsportal.com/) (ingyenesen létrehozható, és hozzárendelhető a Microsoft-fiókjához). 

## <a name="set-up-the-data-source"></a>Az adatforrás beállítása

1. Jelentkezzen be a [Bing Térképek fejlesztői központjába](https://www.bingmapsportal.com/). 
2. Kattintson a felső navigációs sáv **Data Sources** (Adatforrások) elemére, és válassza a **Manage Data Sources** (Adatforrások kezelése) lehetőséget. 

    ![](./media/notification-hubs-geofence/bing-maps-manage-data.png)
3. Ha még nincs adatforrása, akkor látni fog egy hivatkozást, amellyel létrehozhat egyet. Válassza az **Upload data as a data source** (Adatok feltöltése adatforrásként) lehetőséget. Használhatja a **Data sources** (Adatforrások)  > **Upload data** (Adatok feltöltése) menüt is. 

    ![](./media/notification-hubs-geofence/bing-maps-create-data.png)

4. Hozzon létre egy **NotificationHubsGeofence.pipe** fájlt a merevlemezén az alábbi tartalommal: Ebben az oktatóanyagban a következő folyamatalapú mintát használjuk, amely a San Francisco-i partszakasz egy területét határolja:

    ```
    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))
    ```

    A folyamatfájl a következő entitást jelöli:
    
    ![](./media/notification-hubs-geofence/bing-maps-geofence.png)
5. Az **Upload data as a data source** (Adat feltöltése adatforrásként) oldalon tegye a következőket:
    1. Válassza ki a **pipe** (folyamat) elemet a **Data format** (Adatformátum) beállításnál.
    2. Válassza ki az előző lépésben létrehozott **NotificationHubGeofence.pipe** elemet. 
    3. Válassza az **Upload** (Feltöltés) gombot. 
    
    > [!NOTE]
    > Lehetséges, hogy új, a **Lekérdezési kulcstól** eltérő **Főkulcsot** kell megadnia. Csak hozzon létre egy új kulcsot az irányítópulton, és frissítse az adatforrás-feltöltési oldalt.
6. Az adatfájl feltöltése után közzé kell tennie az adatforrást. A korábbiakhoz hasonlóan válassza a **Data sources** (Adatforrások) -> **Manage Data Sources** (Adatforrások kezelése) lehetőséget. 
7. Keresse meg az adatforrást a listában, majd válassza ki a **Publish** (Közzététel) elemet az **Actions** (Műveletek) oszlopban. 

    ![](./media/notification-hubs-geofence/publish-button.png)
8. Váltson a **Published Data Sources** (Közzétett adatforrások) lapra, és győződjön meg arról az adatforrás szerepel a listán.

    ![](./media/notification-hubs-geofence/bing-maps-published-data.png)

9. Válassza a **Szerkesztés** elemet. (Egy pillantással) áttekintheti az adatokban létrehozott helyeket.

    ![](./media/notification-hubs-geofence/bing-maps-data-details.png)

    A portál ekkor még nem jeleníti meg a létrehozott geokerítés határvonalait. Ehhez csak meg kell erősítenie, hogy a megadott hely a megfelelő környéken található.
8. Most már az adatforrás minden követelménye teljesül. Az API-hívás kérési URL-jének részleteit úgy szerezheti be, ha a Bing Térképek fejlesztői központban kiválasztja a **Data Sources** (Adatforrások), majd a **Data Source Information** (Adatforrás adatai) elemet.

    ![](./media/notification-hubs-geofence/bing-maps-data-info.png)

    A **Query URL** az a végpont, ahol lekérdezéseket végezhet annak ellenőrzésére, hogy az eszköz egy adott hely határvonalain belül vagy kívül található-e. Ennek az ellenőrzésnek az elvégzéséhez egyszerűen egy GET hívást kell végrehajtania a lekérdezés URL-címén a következő paraméterek hozzáfűzésével:

    ```
    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY
    ```

    A Bing Térképek automatikusan elvégzi a számítást, így látható, hogy az eszköz a geokerítésen belül van-e. Ha böngésző (vagy a cURL) használatával hajtja végre a kérést, szabványos JSON-választ kap:

    ![](./media/notification-hubs-geofence/bing-maps-json.png)

    Ez a válasz csak akkor jelenik meg, ha a pont a kijelölt határokon belül található. Ha ez nem így van, a **results** (eredmények) gyűjtő üres lesz:

    ![](./media/notification-hubs-geofence/bing-maps-nores.png)

## <a name="set-up-the-uwp-application"></a>Az UWP-alkalmazás beállítása

1. A Visual Studióban indítson egy **Üres alkalmazás (Univerzális Windows)** típusú új projektet.

    ![](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

    A projekt létrehozása után kihasználhatja az alkalmazás lehetőségeit. Most adjuk meg a geokerítés-infrastruktúra valamennyi beállítását. Ehhez a Bing szolgáltatásait fogja használni, és a következő nyilvános REST API-végpont használatával kérdezheti le az adott helyeket:

    http://spatial.virtualearth.net/REST/v1/data/

    A következő paramétereket kell megadnia a rendszer működéséhez:

    - **Adatforrás azonosítója** és **Adatforrás neve** – a Bing Térképek API-adatforrásai számos összegyűjtött metaadatot tartalmaznak, például a helyadatokat és a nyitvatartási időt.  
    - **Entitás neve** – az értesítés hivatkozási pontjaként használni kívánt entitás. 
    - **Bing Térképek API-kulcs** – ezt a kulcsot már korábban megkapta, amikor létrehozta a fiókját a Bing fejlesztői központjában.

    Most, hogy az adatforrás rendelkezésre áll, elkezdhetünk dolgozni az UWP-alkalmazással.
2. Engedélyezze a helyszínszolgáltatásokat az alkalmazáshoz. Nyissa meg a `Package.appxmanifest` fájlt a **Megoldáskezelőben**.

    ![](./media/notification-hubs-geofence/vs-package-manifest.png)
3. A csomagtulajdonságokat tartalmazó megnyíló lapon válassza a **Capabilities** (Képességek) elemet, majd a **Location** (Hely) elemet.

    ![](./media/notification-hubs-geofence/vs-package-location.png)

4. Hozzon létre egy `Core` nevű új mappát a megoldásban, és adjon hozzá egy `LocationHelper.cs` nevű fájlt:

    ![](./media/notification-hubs-geofence/vs-location-helper.png)

    A `LocationHelper` osztály kódja a felhasználó tartózkodási helyének lekérését biztosítja a rendszer API-n keresztül:

    ```csharp
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
    ```

    A felhasználói helyadatok UWP-alkalmazásokban történő lekérdezéséről [a felhasználó helyének lekérdezését](https://msdn.microsoft.com/library/windows/apps/mt219698.aspx) ismertető dokumentumban talál további információt.

5. Nyissa meg a főoldal (`MainPage.xaml.cs`) kódoldalát annak ellenőrzéséhez, hogy a helyadatok lekérése valóban működik-e. Hozzon létre egy új eseménykezelőt a `Loaded` eseményhez a `MainPage` konstruktorban.

    ```csharp
    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }
    ```

    Az eseménykezelő megvalósítása a következő:

    ```csharp
    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }
    ```
6. Futtassa az alkalmazást, majd engedélyezze a helyadatokhoz való hozzáférést.

    ![](./media/notification-hubs-geofence/notification-hubs-location-access.png)
7. Az alkalmazás elindítása után a koordináták a **Kimenet** ablakban láthatók:

    ![](./media/notification-hubs-geofence/notification-hubs-location-output.png)

    Most, hogy már ismeri a helyadatok beszerzésének működését, igény szerint eltávolíthatja a Loaded eseménykezelőjét, mert többé nem lesz rá szükség.
8. A következő lépés a helyadatok módosításainak rögzítése. A `LocationHelper` osztályban adja hozzá a `PositionChanged` eseménykezelőjét:

    ```csharp
    geolocator.PositionChanged += Geolocator_PositionChanged;
    ```

    A hely koordinátái az **Output** (Kimenet) ablakban jelennek meg:

    ```csharp
    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }
    ```

## <a name="set-up-the-backend"></a>A háttérrendszer beállítása
1. Töltse le a [.NET-háttérrendszer mintáját a GitHubról](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers). 
2. A letöltés után nyissa meg a `NotifyUsers` mappát, majd a `NotifyUsers.sln` fájlt a Visual Studióban. 
3. Állítsa be **Indítási projektként**, majd futtassa az `AppBackend` projektet.

    ![](./media/notification-hubs-geofence/vs-startup-project.png)

    A projekt már konfigurálva van leküldéses értesítések céleszközökre történő küldésére, így csak két dolgot kell megtennie: cserélje le az értesítési központ megfelelő kapcsolati karakterláncát, illetve végezzen határvonal-azonosítást, hogy az üzenetet csak a geokerítésen belül tartózkodó felhasználók kapják meg.
4. A kapcsolati karakterlánc konfigurálásához a `Models` mappában nyissa meg a következőt: `Notifications.cs`. A `NotificationHubClient.CreateClientFromConnectionString` függvénynek tartalmaznia kell az értesítési központ adatait, amelyet az [Azure Portalon](https://portal.azure.com) szerezhet be (nyissa meg a **Beállítások** menü **Hozzáférési szabályzatok** oldalát). Mentse a frissített konfigurációs fájlt.
5. Hozzon létre egy modellt a Bing Térképek API eredményéhez. Ennek a legegyszerűbb módja, ha megnyitja a `Models` mappát, majd az **Add** (Hozzáadás) > **Class** (Osztály) elemet választja. Nevezze el a következőképpen: `GeofenceBoundary.cs`. Ezután másolja át a JSON-t az első szakaszban kapott API-válaszból. A Visual Studióban válassza az **Edit** (Szerkesztés) > **Paste Special** (Irányított beillesztés) > **Paste JSON as Classes** (JSON beillesztése osztályokként) elemet. 

    Így biztosítható, hogy az objektum deszerializálása pontosan a kívánt módon történjen. Az eredményül kapott osztály a következőhöz hasonló lesz:

    ```csharp
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
    ```
6. Ezután nyissa meg a következőt: `Controllers` > `NotificationsController.cs`. Frissítse a Post hívást a kívánt hosszúsági és szélességi értékeknek megfelelően. Ehhez egyszerűen adja hozzá a függvényaláíráshoz a következő két karakterláncot: `latitude` és `longitude`.

    ```csharp
    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)
    ```
7. Hozzon létre a projektben egy új, `ApiHelper.cs` nevű osztályt. Ezzel fog csatlakozni a Binghez, hogy ellenőrizze a pontok határvonalainak metszéspontjait. Adjon hozzá egy `IsPointWithinBounds` függvényt az alábbi kódban látható módon:

    ```csharp
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
    ```

    > [!IMPORTANT]
    > Az API-végpontot cserélje le a Bing fejlesztői központjából korábban beszerzett lekérdezési URL-címre (ugyanez vonatkozik az API-kulcsra is). 

    Ha a lekérdezés eredményes volt, akkor az adott pont a geokerítés határain belül van, így a függvény a következőt adja vissza: `true`. Ha a lekérdezés eredménytelen, a Bing ezzel azt jelzi, hogy a pont a keresési területen kívül található, ezért a függvény `false` értéket ad vissza.
8. A `NotificationsController.cs`-hez visszatérve hozzon létre egy ellenőrzést közvetlenül a switch utasítás előtt:

    ```csharp
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
    ```

## <a name="test-push-notifications-in-the-uwp-app"></a>Leküldéses értesítések tesztelése az UWP-alkalmazásban

1. Az UWP-alkalmazásban most már tudjuk tesztelni az értesítéseket. A `LocationHelper` osztályba hozzon létre egy új függvényt – `SendLocationToBackend`:

    ```csharp
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
    ```

    > [!NOTE]
    > Állítsa be a `POST_URL` elemet az üzembe helyezett webalkalmazás helyére. Most a helyi futtatás is megfelelő, de ha nyilvános verziót kíván üzembe helyezni, egy külső szolgáltatón kell üzemeltetnie.
1. Regisztrálja az UWP-alkalmazást a leküldéses értesítésekre. A Visual Studióban válassza a **Project** (Projekt) > **Store** (Áruház) > **Associate app with the store** (Alkalmazás társítása az Áruházzal) lehetőséget.

    ![](./media/notification-hubs-geofence/vs-associate-with-store.png)
3. Amikor bejelentkezik a fejlesztői fiókba, válasszon ki egy meglévő alkalmazást, vagy hozzon létre egy újat, és társítsa hozzá a csomagot. 
4. Nyissa meg a fejlesztői központot, és nyissa meg a létrehozott alkalmazást. Válassza a **Szolgáltatások** > **Leküldéses értesítések** > **Live Services webhely** lehetőséget.

    ![](./media/notification-hubs-geofence/ms-live-services.png)
5. A webhelyen jegyezze fel az **Application Secret** (Alkalmazástitok) és a **Package SID** (Csomag biztonsági azonosítója) értékeit. Az Azure Portalon mindkettőre szüksége lesz. Nyissa meg az értesítési központot, válassza ki a **Beállítások** > **Értesítési szolgáltatások** > **Windows (WNS)** elemet, majd töltse ki a kötelező mezőket.

    ![](./media/notification-hubs-geofence/notification-hubs-wns.png)
6. Válassza a **Mentés** elemet.
7. Nyissa meg a **References** (Hivatkozások) elemet a **Megoldáskezelőben**, és válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget. Adjon hozzá egy, a **Microsoft Azure Service Bus felügyelt kódtárára mutató hivatkozást**. Ehhez csak keressen a `WindowsAzure.Messaging.Managed` elemre, és adja hozzá a projekthez.

    ![](./media/notification-hubs-geofence/vs-nuget.png)

7. Tesztelési célokra újra hozza létre újra a `MainPage_Loaded` eseménykezelőt, és adja hozzá a következő kódrészletet:

    ```csharp
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }
    ```

    A kód regisztrálja az alkalmazást az értesítési központban. És már kész is van. 
8. A `LocationHelper` `Geolocator_PositionChanged` kezelőjében hozzáadhat egy tesztkódszakaszt, amely a geokerítésen belülre kényszeríti a helyet:

    ```csharp
    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");
    ```

9. Mivel nem a valós koordinátákat továbbítja (lehetséges, hogy ezek jelenleg nincsenek a határvonalakon belül), valamint előre meghatározott tesztértékeket használ, frissítéskor a következő értesítés jelenik meg:

    ![](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

## <a name="next-steps"></a>További lépések
További lépésekre is szükség lehet, hogy a megoldás éles környezetben is használható legyen.

1. Mindenekelőtt a geokerítések dinamikus viselkedését kell biztosítania. Ehhez úgy kell beállítani a Bing API-t, hogy új határvonalakat tudjon feltölteni a meglévő adatforráson belül. Erről a [Bing Spatial Data Services API-dokumentációjában](https://msdn.microsoft.com/library/ff701734.aspx) talál további információt.
2. Ezután [címkézéssel](notification-hubs-tags-segment-push-message.md) megadhat adott felhasználókat, így biztosíthatja, hogy a megfelelő személyek kapják meg az értesítéseket.

A jelen oktatóanyagban bemutatott megoldás egy olyan forgatókönyvet ismertet, amelyben sokféle célplatform lehetséges, ezért nem korlátozza rendszerspecifikus képességekkel a geokerítés-rendszert. A Univerzális Windows-platform ugyanakkor a [geokerítések azonnali észlelésére](https://msdn.microsoft.com/windows/uwp/maps-and-location/set-up-a-geofence) szolgáló képességekkel rendelkezik.


