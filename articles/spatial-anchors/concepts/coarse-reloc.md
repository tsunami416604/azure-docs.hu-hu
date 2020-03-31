---
title: Durva relokalizáció
description: További információ a durva áttelepülés használatáról az Ön közelében lévő horgonyok keresésére.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 4c1604eaad1ebdedf6a360a647fe5b9f95c829c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844394"
---
# <a name="coarse-relocalization"></a>Durva helyzet-újrameghatározás

Durva relocalization egy olyan funkció, amely kezdeti választ a kérdésre: *Hol van a készülék most / Milyen tartalmat kell megfigyelni?* A válasz nem pontos, hanem a következő formában: Közel vagy ezekhez a *horgonyokhoz; próbálja meg megtalálni az egyiket.*

A durva újralokáció úgy működik, hogy különböző eszközön lévő érzékelőértékeket társít a horgonyok létrehozásához és lekérdezéséhez. Kültéri esetekben az érzékelő adatai általában az eszköz GPS (Global Positioning System) pozíciója. Ha a GPS nem érhető el vagy nem megbízható (például beltérben), az érzékelő adatai a WiFi hozzáférési pontokból és a hatótávolságon belül lévő Bluetooth-jelzőkből állnak. Az összes összegyűjtött érzékelőadatok hozzájárulnak egy térbeli index fenntartásához, amelyet az Azure Spatial Anchors használ az eszköz körülbelül 100 méteres körzetében lévő horgonyok gyors meghatározásához.

A durva újrakallokalizáció által engedélyezett horgonyok gyors felkelődése leegyszerűsíti az alkalmazások fejlesztését, amelyet világméretű (mondjuk több millió földrajzi lagúna) gyűjtemény támogat. A horgonykezelés összetettsége el van rejtve, így jobban összpontosíthat a félelmetes alkalmazáslogikára. Az Azure Spatial Anchors a színfalak mögött végzi el a horgonyok minden nehéz emelését.

## <a name="collected-sensor-data"></a>Összegyűjtött érzékelőadatok

A horgonyszolgáltatásnak küldhető érzékelőadatok a következők egyike:

* GPS pozíció: szélesség, hosszúság, magasság.
* A WiFi hozzáférési pontok jelerőssége hatótávolságon belül van.
* A Bluetooth-jeladók jelerőssége hatótávolságon belül.

Általánosságban elmondható, hogy az alkalmazásnak eszközspecifikus engedélyeket kell szereznie a GPS-, WiFi- vagy BLE-adatok eléréséhez. Emellett a fenti érzékelőadatok egy része nem érhető el tervezés szerint bizonyos platformokon. Az ilyen helyzetek figyelembevétele érdekében az érzékelőadatok gyűjtése nem kötelező, és alapértelmezés szerint ki van kapcsolva.

## <a name="set-up-the-sensor-data-collection"></a>Az érzékelő adatgyűjtésének beállítása

Kezdjük azzal, hogy létrehoz egy érzékelő ujjlenyomat-szolgáltatót, és tudatja a munkamenettel:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objc"></a>[ObjC között](#tab/objc)

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Create and configure the session
cloudSpatialAnchorSession = [[ASACloudSpatialAnchorSession alloc] init];

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Create and configure the session
cloudSpatialAnchorSession = ASACloudSpatialAnchorSession()

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider
```

# <a name="java"></a>[Java](#tab/java)

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Create and configure the session
cloudSpatialAnchorSession = std::make_shared<CloudSpatialAnchorSession>();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)
```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

Ezután el kell döntenie, hogy mely érzékelőket szeretné használni a durva áthelyezéshez. Ez a döntés a jelenleg fejlesztett alkalmazásra vonatkozik, de az alábbi táblázatban szereplő ajánlásoknak jó kiindulópontot kell adniuk:


|             | Beltéri | Szabadban |
|-------------|---------|----------|
| Gps         | Ki | Bekapcsolva |
| Wifi        | Bekapcsolva | Be (nem kötelező) |
| BLE jelzők | Be (nem kötelező a kikötésekkel, lásd alább) | Ki |


### <a name="enabling-gps"></a>A GPS engedélyezése

Feltéve, hogy az alkalmazás már rendelkezik engedéllyel az eszköz GPS-pozíciójának eléréséhez, beállíthatja az Azure Spatial Anchors-t a használatára:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objc"></a>[ObjC között](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

Amikor gps-t használ az alkalmazásban, ne feledje, hogy a hardver által szolgáltatott értékek általában a következők:

* aszinkron és alacsony frekvenciájú (kevesebb, mint 1 Hz).
* megbízhatatlan / zajos (átlagosan 7 m-es szórás).

Általában mind az eszköz operációs rendszer és az Azure Spatial Anchors némi szűrést és extrapolációt a nyers GPS-jel, hogy megpróbálja enyhíteni ezeket a problémákat. Ez az extra feldolgozás további időt igényel a konvergenciához, ezért a legjobb eredmény érdekében próbálkozzon a következőkkel:

* hozzon létre egy érzékelő ujjlenyomat-szolgáltatót a lehető leghamarabb az alkalmazásban
* az érzékelő ujjlenyomat-szolgáltatójának életben tartása több munkamenet között
* az érzékelő ujjlenyomat-szolgáltatójának megosztása több munkamenet között

Ha azt tervezi, hogy az érzékelő ujjlenyomat-szolgáltatóját egy horgonymunkameneten kívül használja, győződjön meg róla, hogy elindítja, mielőtt az érzékelő becslését kérné. Például a következő kód gondoskodik a készülék pozíciójának valós idejű frissítéséről a térképen:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(
        x: geoPose.Longitude,
        y: geoPose.Latitude,
        radius: geoPose.HorizontalError);
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

# <a name="objc"></a>[ObjC között](#tab/objc)

```objc
// Game about to start, start tracking the sensors
[sensorProvider start];

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    ASAGeoLocation *geoPose = [sensorProvider getLocationEstimate];

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError);
}

// Game ended, no need to track the sensors anymore
[sensorProvider stop];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Game about to start, start tracking the sensors
sensorProvider?.start()

// Game loop
while m_isRunning
{
    // Get the GPS estimate
    var geoPose: ASAGeoLocation?
    geoPose = sensorProvider?.getLocationEstimate()

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError)
}

// Game ended, no need to track the sensors anymore
sensorProvider?.stop()
```

# <a name="java"></a>[Java](#tab/java)

```java
// Game about to start, start tracking the sensors
sensorProvider.start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.getLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.getLongitude(), geoPose.getLatitude(), geoPose.getHorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.stop();
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Game about to start, start tracking the sensors
sensorProvider->Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    std::shared_ptr<GeoLocation> geoPose = sensorProvider->GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose->Longitude(), geoPose->Latitude(), geoPose->HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider->Stop();
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.Longitude(), geoPose.Latitude(), geoPose.HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

---

### <a name="enabling-wifi"></a>Wi-Fi engedélyezése

Feltéve, hogy az alkalmazás már rendelkezik engedéllyel az eszköz Wi-Fi-állapotának eléréséhez, konfigurálhatja az Azure Spatial Anchors-t a használatára:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objc"></a>[ObjC között](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.wifiEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.wifiEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

Amikor wi-fi-t használ az alkalmazásban, ne feledje, hogy a hardver által biztosított értékek általában a következők:

* alacsony frekvenciájú (0,1 Hz-nél kisebb).
* potenciálisan az operációs rendszer szintjén.
* megbízhatatlan / zajos (átlagosan 3-dBm szórás).

Az Azure Spatial Anchors megkísérli egy szűrt Wi-Fi jelerősség-leképezést egy munkamenet során, hogy megpróbálja enyhíteni ezeket a problémákat. A legjobb eredmény érdekében próbálja meg:

* az első horgony elhelyezése előtt hozza létre a munkamenetet.
* tartsa életben a munkamenetet, ameddig csak lehetséges (azaz hozza létre az összes horgonyt és lekérdezést egy munkamenetben).

### <a name="enabling-bluetooth-beacons"></a>Bluetooth-jeladók engedélyezése

Feltéve, hogy az alkalmazás már rendelkezik engedéllyel az eszköz Bluetooth-állapotának eléréséhez, konfigurálhatja az Azure Spatial Anchors-t a használatára:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objc"></a>[ObjC között](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.bluetoothEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.bluetoothEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

A jelzők jellemzően sokoldalú eszközök, ahol minden konfigurálható – beleértve az UUID-kat és a MAC-címeket is. Ez a rugalmasság problémás lehet az Azure Spatial Anchors számára, mivel úgy véli, hogy a jelzőket az UUID-k egyedileg azonosítják. Ennek az egyediségnek a biztosítása valószínűleg térbeli féreglyukakat okoz. A legjobb eredmény érdekében:

* egyedi UUID-kat rendelhet a jeladókhoz.
* üzembe helyezni őket - általában egy normál mintában, például egy rácsban.
* adja át az egyedi beacon UUID-k listáját az érzékelő ujjlenyomat-szolgáltatójának:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objc"></a>[ObjC között](#tab/objc)

```objc
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.knownBeaconProximityUuids = uuids;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

let sensors = locationProvider?.sensors
sensors.knownBeaconProximityUuids = uuids
```

# <a name="java"></a>[Java](#tab/java)

```java
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setKnownBeaconProximityUuids(uuids);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->KnownBeaconProximityUuids(uuids);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

Az Azure Spatial Anchors csak azokat a Bluetooth-jeladókat követi nyomon, amelyek szerepelnek az ismert azonosítók közelségi UUID-k listáján. Rosszindulatú jelzők programozott, hogy az engedélyezett felsorolt UUID-k továbbra is negatívan befolyásolhatja a szolgáltatás minőségét mégis. Ezért csak olyan kurátori helyeken használjon jelzőket, ahol szabályozhatja a telepítésüket.

## <a name="querying-with-sensor-data"></a>Lekérdezés érzékelőadatokkal

Miután létrehozott horgonyokat a kapcsolódó érzékelőadatokkal, megkezdheti azok beolvasását az eszköz által jelentett érzékelőleolvasások segítségével. Már nem kell megadnia a szolgáltatásnak az ismert horgonyok listáját, amelyeket várhatóan megtalál - ehelyett csak tudatja a szolgáltatással az eszköz helyét a beépített érzékelők jelentése szerint. Az Azure Spatial Anchors ezután kitalálja az eszköz közelében lévő horgonyok készletét, és megpróbálja vizuálisan egyeztetni őket.

Ha azt szeretné, hogy a lekérdezések az érzékelő adatait használják, először hozza létre a "közeli eszköz" feltételeket:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objc"></a>[ObjC között](#tab/objc)

```objc
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0f;

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25;

ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let nearDeviceCriteria = ASANearDeviceCriteria()!

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25

let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
```

# <a name="java"></a>[Java](#tab/java)

```java
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.setDistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.setMaxResultCount(25);

AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria->DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria->MaxResultCount(25);

auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
NearDeviceCriteria nearDeviceCriteria = NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = AnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
```

---

A `DistanceInMeters` paraméter határozza meg, hogy milyen messzire fogjuk vizsgálni a horgony grafikon lekérni tartalmat. Tegyük fel például, hogy egy kis helyet töltött fel horgonyokkal, amelyek állandó sűrűsége 2 minden méter. Továbbá, a kamera a készüléken figyeli egyetlen horgonyt, és a szolgáltatás sikeresen megtalálta azt. Valószínűleg az érdekli, hogy visszaszerezzék az összes horgonyt, amit a közelben helyeztek el, nem pedig az egyetlen horgony, amit éppen megfigyelsz. Feltételezve, hogy az elhelyezett horgonyok egy grafikonon vannak csatlakoztatva, a szolgáltatás a diagram széleit követve lekérheti az összes közeli horgonyt. A megtett grafikon-bejárás mértékét `DistanceInMeters`a; az összes olyan horgonyt megkapja, amely a találthoz csatlakozik, `DistanceInMeters`és amely közelebb van, mint a.

Ne feledje, hogy `MaxResultCount` a nagy értékek negatívan befolyásolhatják a teljesítményt. Állítsa be, hogy egy értelmes értéket az alkalmazás.

Végül meg kell mondania a munkamenetnek, hogy használja az érzékelőalapú feltekintést:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objc"></a>[ObjC között](#tab/objc)

```objc
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

# <a name="java"></a>[Java](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>Várt eredmények

Fogyasztói minőségű GPS eszközök általában meglehetősen pontatlan. A tanulmány [zandenbergen és Barbeau (2011)][6] jelentések a medián pontossága mobiltelefonok támogatott GPS (A-GPS), hogy körülbelül 7 méter - elég nagy értéket kell figyelmen kívül hagyni! Ezeknek a mérési hibáknak a figyelembevétele érdekében a szolgáltatás a horgonyokat valószínűségi eloszlásként kezeli a GPS-térben. Mint ilyen, egy horgony van most a terület -ból hely amit a leg--bb valószínű ( vagyis, -val több mint 95% bizalom) tartalmaz -a igaz, ismeretlen GPS helyzet.

Ugyanez az érvelés vonatkozik a GPS lekérdezésekor is. A készülék egy másik térbeli megbízhatósági régióként jelenik meg a valódi, ismeretlen GPS-pozíciója körül. A közeli horgonyok felfedezése azt jelenti, hogy egyszerűen csak olyan magabiztos anamzárdal találjuk meg a horgonyokat, amelyek *elég közel vannak* az eszköz megbízhatósági régiójához, amint azt az alábbi képen látható:

![Kiválasztása horgony jelöltek GPS](media/coarse-reloc-gps-separation-distance.png)

A GPS-jel pontossága, mind a horgony létrehozása, mind a lekérdezések során, nagy hatással van a visszaadott horgonyok készletére. Ezzel szemben a WiFi / jelzőkön alapuló lekérdezések figyelembe veszik az összes olyan horgonyt, amely legalább egy hozzáférési ponttal / jeladóval rendelkezik a lekérdezéssel. Ebben az értelemben a WiFi / beacons-en alapuló lekérdezés eredményét leginkább a hozzáférési pontok / jelzők fizikai tartománya és a környezeti akadályok határozzák meg.

Az alábbi táblázat megbecsüli az egyes érzékelőtípusok várható keresési területét:

| Érzékelő      | Keresési terület sugara (kb.) | Részletek |
|-------------|:-------:|---------|
| Gps         | 20 m - 30 m | A GPS-bizonytalanság határozza meg, többek között. A jelentett számok becsült medián GPS pontossága mobiltelefonok A-GPS, azaz 7 méter. |
| Wifi        | 50 m - 100 m | A vezeték nélküli hozzáférési pontok hatótávolsága határozza meg. A frekvenciától, az adó erősségététől, fizikai elzáródásától, interferenciájától és így tovább. |
| BLE jelzők |  70 m | A jeladó hatótávolsága határozza meg. A frekvenciától, az átviteli erőtől, a fizikai akadályoktól, az interferenciától és így tovább. |

## <a name="per-platform-support"></a>Platformonkénti támogatás

Az alábbi táblázat összefoglalja az egyes támogatott platformokon gyűjtött érzékelőadatokat, valamint a platformspecifikus kikötéseket:


|             | HoloLens | Android | iOS |
|-------------|----------|---------|-----|
| Gps         | N/A | [LocationManager][3] API-kon keresztül támogatott (GPS és NETWORK egyaránt) | [CLLocationManager][4] API-kon keresztül támogatott |
| Wifi        | 3 másodpercenként körülbelül egy beszkés sebességgel támogatott | Támogatott. A 28-as API-szinttől kezdve a Wi-Fi-letapogatások 2 percenként 4 hívásra vannak szabályozva. Az Android 10-ből a szabályozás letiltható a Fejlesztői beállítások menüből. További információt az [Android dokumentációjában][5]talál. | N/A - nincs nyilvános API |
| BLE jelzők | Az [Eddystone][1] és az [iBeacon][2] | Az [Eddystone][1] és az [iBeacon][2] | Az [Eddystone][1] és az [iBeacon][2] |

## <a name="next-steps"></a>További lépések

Használjon durva áthelyezést egy alkalmazásban.

> [!div class="nextstepaction"]
> [Unity](../how-tos/set-up-coarse-reloc-unity.md)

> [!div class="nextstepaction"]
> [Célkitűzés-C](../how-tos/set-up-coarse-reloc-objc.md)

> [!div class="nextstepaction"]
> [Swift](../how-tos/set-up-coarse-reloc-swift.md)

> [!div class="nextstepaction"]
> [Java](../how-tos/set-up-coarse-reloc-java.md)

> [!div class="nextstepaction"]
> [C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)

> [!div class="nextstepaction"]
> [C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
