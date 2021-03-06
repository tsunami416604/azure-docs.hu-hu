---
title: Durva újrahonosítás
description: Ismerje meg, hogyan használhat durva áthonosítást a közeli horgonyok megtalálásához.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a65b2ca4ba9f1912adeaf60df123bcd3c8833bd
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2020
ms.locfileid: "95496902"
---
# <a name="coarse-relocalization"></a>Durva helyzet-újrameghatározás

A durva újrahonosítás egy olyan szolgáltatás, amely kezdeti választ ad a kérdésre: *Hol van az eszközem?* A válasz nem pontos, hanem a következő formában van: a *horgonyokhoz közeledik; próbáljon meg az egyiket* megkeresni.

A durva újrahonosítás úgy működik, hogy a különböző eszköz-érzékelőket társítja a horgonyok létrehozásával és lekérdezésével. A kültéri forgatókönyvek esetében az érzékelő adatok általában az eszköz GPS (globális helymeghatározási rendszer) pozíciója. Ha a GPS nem érhető el vagy nem megbízható (például beltérben), az érzékelői információ a hatótávolságon belül található WiFi hozzáférési pontokból és Bluetooth-figyelőből áll. Az összes összegyűjtött érzékelői adat hozzájárul a térbeli index fenntartásához. ezt az Azure térbeli horgonyok használják az eszköz körülbelül 100 méterén belüli horgonyok gyors meghatározásához.

A durva újrahonosítást használó horgonyok gyors kinézete leegyszerűsíti az olyan alkalmazások fejlesztését, amelyeknek a globális gyűjteményei (azaz több millió földrajzilag elosztott) horgonyt támogatnak. A horgonyok kezelésének összetettsége teljesen el van rejtve, így jobban összpontosíthat a félelmetes alkalmazás-logikára. Az Azure térbeli horgonyok a színfalak mögött is megtalálhatók.

## <a name="collected-sensor-data"></a>Gyűjtött érzékelők adatok

A horgony szolgáltatásnak küldendő érzékelő-adatmennyiség az alábbiak egyike:

* GPS-pozíció: szélesség, hosszúság, magasság.
* A WiFi-hozzáférési pontok jelerőssége a tartományon belül.
* A hatótávolságon belül a Bluetooth-figyelő jelerőssége.

Általánosságban elmondható, hogy az alkalmazásnak az eszközre vonatkozó engedélyeket kell megadnia a GPS-, Wi-Fi-vagy kapcsolati adateléréshez. Emellett a fenti érzékelők közül néhány nem érhető el bizonyos platformokon. Az ilyen helyzetek kiszámításához az érzékelőre vonatkozó adatgyűjtést nem kötelező megadni, és alapértelmezés szerint ki van kapcsolva.

## <a name="set-up-the-sensor-data-collection"></a>Az érzékelő adatgyűjtésének beállítása

Első lépésként hozzon létre egy érzékelő ujjlenyomat-szolgáltatót, és tegye meg a munkamenetet:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objc"></a>[ObjC](#tab/objc)

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

Ezután el kell döntenie, hogy mely érzékelőket szeretné használni a durva újrahonosításhoz. Ez a döntés a fejleszthető alkalmazásra jellemző, de a következő táblázatban szereplő javaslatok jó kiindulási pontot adnak:

|                 | Beltéri | Szabadban |
|-----------------|---------|----------|
| **GPS**         | Ki | Be |
| **Fi**        | Be | Bekapcsolva (nem kötelező) |
| **Egyazon figyelő** | Bekapcsolva (nem kötelező a kikötésekkel kapcsolatban lásd alább) | Ki |

### <a name="enabling-gps"></a>A GPS engedélyezése

Feltételezve, hogy az alkalmazásnak már van engedélye az eszköz GPS-pozíciójának elérésére, az Azure térbeli Horgonyait a következő használatára állíthatja be:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

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

Ha a GPS-t az alkalmazásban használja, vegye figyelembe, hogy a hardver által megadott olvasások jellemzően a következők:

* aszinkron és alacsony gyakoriságú (kevesebb mint 1 Hz).
* megbízhatatlan/zajos (átlagosan 7 méteres szórás).

Általánosságban elmondható, hogy az eszköz operációs rendszere és az Azure térbeli horgonyai is kiszűrik és kikövetkeztetik a nyers GPS-jelet a problémák enyhítésére tett kísérlet során. Ez a további feldolgozás további időt igényel a konvergenciához, ezért a legjobb eredmények érdekében a következőket kell tennie:

* a lehető leghamarabb hozzon létre egy érzékelő ujjlenyomat-szolgáltatót az alkalmazásban
* az érzékelő ujjlenyomat-szolgáltatójának életben tartása több munkamenet között
* az érzékelő ujjlenyomat-szolgáltatójának megosztása több munkamenet között

Ha az érzékelő ujjlenyomat-szolgáltatóját egy rögzített munkameneten kívül szeretné használni, mindenképpen indítsa el az érzékelő becslése előtt. Például az alábbi kód gondoskodik az eszköz valós idejű pozíciójának frissítéséről a térképen:

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="objc"></a>[ObjC](#tab/objc)

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

### <a name="enabling-wifi"></a>WiFi engedélyezése

Feltételezve, hogy az alkalmazásnak már van engedélye az eszköz WiFi-állapotának elérésére, az Azure térbeli Horgonyait az alábbiak használatára állíthatja be:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

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

Ha az alkalmazásban Wi-Fi-t használ, vegye figyelembe, hogy a hardver által megadott olvasások jellemzően a következők:

* aszinkron és alacsony gyakoriságú (kevesebb mint 0,1 Hz).
* potenciálisan szabályozható az operációs rendszer szintjén.
* megbízhatatlan/zajos (átlagosan 3 dBm standard szórás).

Az Azure térbeli horgonyok megkísérlik kiépíteni a szűrt WiFi jel erősségének leképezését egy munkamenet során a problémák enyhítésére tett kísérlet során. A legjobb eredmények érdekében a következőket kell megpróbálkoznia:

* az első horgony elhelyezése előtt hozza létre a munkamenetet.
* tartsa életben a munkamenetet a lehető leghosszabbra (azaz hozzon létre minden horgonyt és lekérdezést egy munkamenetben).

### <a name="enabling-bluetooth-beacons"></a>Bluetooth-figyelő engedélyezése

Feltéve, hogy az alkalmazásnak már van engedélye az eszköz Bluetooth-állapotának elérésére, az Azure térbeli Horgonyait az alábbiak használatára állíthatja be:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

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

A figyelők általában sokoldalú eszközök, ahol minden – beleértve az UUID-ket és a MAC-címeket – is konfigurálható. Ez a rugalmasság problémás lehet az Azure térbeli horgonyok esetében, mivel a figyelőket a saját UUID-azonosítóik alapján egyedileg azonosíthatónak tekinti. Ha nem biztos benne, hogy ez az egyediség valószínűleg a térbeli féreglyuk okozza. A legjobb eredmények érdekében a következőket kell tennie:

* rendeljen egyedi UUID-ket a figyelőhöz.
* üzembe helyezheti őket – jellemzően normál mintában, például rácsban.
* adja át az egyedi Beacon UUID-azonosítók listáját az érzékelő ujjlenyomat-szolgáltatójának:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objc"></a>[ObjC](#tab/objc)

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

Az Azure térbeli horgonyok csak azokat a Bluetooth-figyelőket fogják követni, amelyek az ismert Beacon közelségi UUID-listán vannak. Azok a rosszindulatú figyelők, amelyeken engedélyezve vannak a felsorolt UUID-azonosítók, továbbra is negatívan befolyásolhatják a szolgáltatás minőségét. Ezért a figyelőket csak a kihelyezett helyeken kell használni, ahol szabályozhatja az üzembe helyezést.

## <a name="querying-with-sensor-data"></a>Az érzékelővel való lekérdezés

Miután létrehozta a kapcsolódó érzékelők adataival rendelkező horgonyokat, megkezdheti a beolvasását az eszköz által jelentett szenzorok használatával. Már nem kell megadnia a szolgáltatást a keresett ismert horgonyok listájával, ehelyett a szolgáltatás a bevezetési érzékelők által jelentett módon ismeri az eszköz helyét. Az Azure térbeli horgonyok ezután megtalálják az eszközhöz közeledő horgonyokat, és megpróbálják vizuálisan egyeztetni őket.

Ha a lekérdezésekhez az érzékelőt szeretné használni, kezdje a "közeli eszköz" feltételek létrehozásával:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objc"></a>[ObjC](#tab/objc)

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

A `DistanceInMeters` paraméter határozza meg, hogy a rendszer milyen mértékben vizsgálja meg a horgony gráfot a tartalom lekéréséhez. Tegyük fel, hogy olyan helyet töltött fel, amely a 2. számú állandó sűrűségű horgonyokkal van feltöltve. Emellett az eszközön lévő kamera egyetlen horgonyt figyel, és a szolgáltatás sikeresen megtalálta. Legvalószínűbb, hogy az éppen megfigyelt egyedi horgony helyett az összes közeli horgonyt beolvassa. Feltéve, hogy a rögzített horgonyok egy gráfban vannak csatlakoztatva, a szolgáltatás a diagram széleinek követésével lekérheti az összes közeli horgonyt. A gráf bejárási elvégzésének mennyiségét a következő szabályozza:. a rendszer az Ön által birtokolt `DistanceInMeters` összes olyan horgonyt megkapja, amely közelebb van a-hoz `DistanceInMeters` .

Ne feledje, hogy a nagy értékek `MaxResultCount` negatív hatással lehetnek a teljesítményre. Állítsa az alkalmazás ésszerű értékére.

Végezetül meg kell adnia a munkamenetet, hogy használja az érzékelőn alapuló megkeresést:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objc"></a>[ObjC](#tab/objc)

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

A fogyasztói minőségű GPS-eszközök általában eléggé pontatlanok. A [Zandenbergen és Barbeau (2011)][6] által készített tanulmány a mobil telefonok közepes pontosságát jelenti a támogatott GPS-vel (A-GPS) körülbelül 7 méterre – elég nagy értéket kell figyelmen kívül hagyni! Ezeknek a mérési hibáknak a kiszámításához a szolgáltatás a GPS-térben a valószínűségi Eloszlásként kezeli a horgonyokat. Ennek megfelelően a horgony mostantól a legnagyobb valószínűséggel (azaz a több mint 95%-os megbízhatósággal rendelkező) területnek az igaz, ismeretlen GPS-pozícióját tartalmazza.

Ugyanezt az indoklást alkalmazza a rendszer a GPS lekérdezése során. Az eszköz egy másik térbeli megbízhatósági régióként jelenik meg a valódi, ismeretlen GPS-pozíciója körül. A közeli horgonyok felderítésével a rendszer egyszerűen megkeresi a megbízhatósági *régiókban található* horgonyokat, az alábbi képen látható módon:

![Horgony jelöltek kiválasztása GPS-sel](media/coarse-reloc-gps-separation-distance.png)

A GPS-jel pontossága, a horgonyok létrehozásán és a lekérdezések során is nagy hatással van a visszaadott horgonyok készletére. Ezzel szemben a WiFi/figyelőn alapuló lekérdezések figyelembe veszik az összes olyan horgonyt, amelynek legalább egy hozzáférési pontja/Beacon-je közös a lekérdezéssel. Ebben az értelemben a Wi-Fi/figyelőn alapuló lekérdezések eredményét többnyire a hozzáférési pontok/figyelők fizikai tartománya, valamint a környezeti akadályok határozzák meg.

Az alábbi táblázat a várt keresési helyet becsüli meg az egyes érzékelők típusainál:

| Érzékelő      | Keresési terület sugara (kb.) | Részletek |
|-------------|:-------:|---------|
| GPS         | 20 m – 30 m | A többi tényező között a GPS-bizonytalanság határozza meg. A jelentett számok a-GPS-vel ellátott mobiltelefonok medián GPS-pontosságának becslése, amely 7 méter. |
| Fi        | 50 m – 100 m | A vezeték nélküli hozzáférési pontok tartománya határozza meg. A gyakoriságtól, az adó erősségtől, a fizikai akadályoktól, a beavatkozástól és így tovább függ. |
| Egyazon figyelő |  70 m | A jeladó tartománya határozza meg. A gyakoriságtól, az átviteli erősségtől, a fizikai akadályoktól, a beavatkozástól és egyebektől függ. |

## <a name="per-platform-support"></a>Platformon belüli támogatás

A következő táblázat összefoglalja az egyes támogatott platformokon összegyűjtött érzékelők adatait, valamint a platformra vonatkozó kikötéseket:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | N.A. | [LocationManager][3] API-kon keresztül támogatott (a GPS és a hálózat is) | [CLLocationManager][4] API-kon keresztül támogatott |
| **Fi**        | 3 másodpercenként körülbelül egy vizsgálattal támogatott | Támogatott. A 28-as API-szinttől kezdve a Wi-Fi vizsgálat 2 percenként 4 hívásra van Leszabályozva. Az Android 10-es verzióban a szabályozás le lehet tiltani a fejlesztői beállítások menüből. További információt az [Android dokumentációjában][5]talál. | N/A – nincs nyilvános API |
| **Egyazon figyelő** | [Eddystone][1] és [iBeacon][2] korlátozódik | [Eddystone][1] és [iBeacon][2] korlátozódik | [Eddystone][1] és [iBeacon][2] korlátozódik |

## <a name="next-steps"></a>Következő lépések

Használjon durva újrahonosítást egy alkalmazásban.

> [!div class="nextstepaction"]
> [Unity](../how-tos/set-up-coarse-reloc-unity.md)

> [!div class="nextstepaction"]
> [Objective-C](../how-tos/set-up-coarse-reloc-objc.md)

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
