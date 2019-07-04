---
title: Az Azure Maps áttekintése | Microsoft Docs
description: Az Azure Maps bemutatása
author: walsehgal
ms.author: v-musehg
ms.date: 02/04/2019
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 5f41263113568cf9f3771119135be8db37119181
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442941"
---
# <a name="what-is-azure-maps"></a>Mi az Azure Maps?

Az Azure Maps friss térképadatokat használó pontos földrajzi kontextusának biztosításához a webes és mobilalkalmazások térinformatikai szolgáltatások gyűjteménye. Az Azure Maps biztosítja:

* REST API-k több stílusok és szatellit képanyag a maps megjelenítése.
* Címek, helyek és a világ különböző pontjain hasznos helyek keresése.
* Útválasztási pont-pont típusú, multipoint, multipoint optimalizálás, isochrone, kereskedelmi járművek, befolyásolják a forgalom és mátrix Útválasztás; forgalom és események megtekintése.
* A mobilitási szolgáltatások nyilvános átvitel és alternatív szállítási lehetőségek (például bikeshare scooter megosztás és autó megosztás), és valós időben útvonalak megtervezése. 
* Létrehozó felhasználói hely földrajzi hely meghatározásának keresztül, és a hely az időzóna. 
* A geokerítések és az adattárolás, a helyre vonatkozó adatokat az Azure-ban üzemeltetett szolgáltatások. 
* Térinformatikai elemzés révén intelligencia helyét. 

Kívül REST API-k az Azure Maps-szolgáltatások érhetők el a Web SDK-t vagy az Android SDK-t. Ezek az eszközök segítségével a fejlesztők gyorsan fejleszthet és méretezni a megoldásokat, amelyek az Azure-megoldások. 

Iratkozzon fel az ingyenes [Azure Maps-fiók](https://azure.microsoft.com/services/azure-maps/) , és kezdje el a fejlesztést.

A következő videó részletesen bemutatja az Azure Mapset:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Térképvezérlők

### <a name="web-sdk"></a>Web SDK

Az Azure Maps Web SDK lehetővé teszi a saját tartalmat és a webes vagy mobilalkalmazásaiba való megjelenítéshez képanyag interaktív leképezések testreszabása. Ez a vezérlő felhasznál vagy a Web GL, így nagy méretű adatkészleteket, nagy teljesítményű leképezhetők. Fejlesztés SDK-val JavaScript- vagy TypeScript használatával.

![Példaleképezés sokaság módosítása](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>Android SDK

Az Azure Maps Android SDK használatával mobil leképezés alkalmazások létrehozását. 

![Térkép példák a mobileszközökön](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Az Azure Maps szolgáltatásai

Az Azure Maps áll a következő kilenc földrajzi kontextusának biztosításához az Azure-alkalmazások által biztosított szolgáltatásokat.

### <a name="data-service"></a>Adatszolgáltatás

Adatok a versenyképesség a térképek szolgáltatáshoz. Data Service használatával feltöltésére és tárolására, a földrajzi adatok térbeli műveletek vagy lemezkép-összeállítás való használatra.  Oszloptárat közelebb a vásárlói adatokat az Azure Maps szolgáltatás csökkenti a késést, növelhetik hatékonyságukat, és hozzon létre új forgatókönyvek használhatók az alkalmazásokban. Ez a szolgáltatás részletes ismertetéséért tekintse meg a [adatok szolgáltatás API-dokumentáció](https://docs.microsoft.com/rest/api/maps/data).

### <a name="mobility-service"></a>Mobilitási szolgáltatás

Az Azure Maps a mobilitási szolgáltatás lehetővé teszi, hogy a valós idejű trip megtervezése. Azt adja vissza a legjobb útvonal lehetséges beállításokat, és többféle közlekedési formára biztosít. Metro (város) területek módokban walking kerékpározáshoz és nyilvános átvitel tartalmazhatnak. A fejlesztők is kérhető az átvitel Útiterv részleteknek, például a geometriai, az leáll, ütemezett listája és a valós idejű beérkező kérelmek és szolgáltatási riasztások.

A szolgáltatás lehetővé teszi adott objektumtípus, például megosztott kerékpárok, robogó vagy autók keresések körül egy helyre. Felhasználók kérhetik hány elérhető megosztott kerékpárok van hátra a legközelebb elhelyezkedő dockhoz. Ezek elérhető autó-share járművek megkeresheti, és részletes jövőbeli rendelkezésre állást és a jelenlegi ösztönzésének szint.

A szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [mobilitási API-dokumentáció](https://docs.microsoft.com/rest/api/maps/mobility).

### <a name="render-service"></a>Megjelenítő szolgáltatás

A renderelési szolgáltatás segít a fejlesztőknek a webes és mobilalkalmazásokat fejleszthetnek leképezés létrehozása. A szolgáltatás használhat nagy felbontású rasztergrafikákat, amelyek 19 nagyítási szinten érhetők el, vagy teljes körűen testreszabható, vektorformátumú térképeket.

![Példa egy térkép a renderelési szolgáltatás](media/about-azure-maps/Introduction_Map.png)

A megjelenítő szolgáltatás mostantól előzetes verziójú API-kat biztosít, amelyeken keresztül a fejlesztők műholdképekkel dolgozhatnak. További tudnivalókért olvassa el a [Render API-dokumentáció](https://docs.microsoft.com/rest/api/maps/render).

### <a name="route-service"></a>Útvonal szolgáltatás

Az útvonal szolgáltatás robusztus geometriai számításokat biztosít valós infrastruktúrákat és több közlekedési módot alkalmazó útvonalak számításához. A szolgáltatás lehetővé teszi a fejlesztők számára, hogy többféle közlekedési formára (autó, teherautó, kerékpár, gyalog) vonatkozóan számítsák ki az útvonalakat. Olyan tényezők figyelembe vételére is képes, mint a forgalmi viszonyok, a súlykorlátozások vagy a veszélyes anyagok szállítása.

![Példa egy térkép a Route service-ből](media/about-azure-maps/Introduction_Route.png)

A Route service előzetes verziója speciális szolgáltatásokra, például kínálja: 

* Több útvonal kérelmek kötegelt feldolgozásához.
* Mátrixok az utazási időt és a távolság források és a célhelyek között.
* Útvonalak keresése vagy az, hogy milyen távolságra felhasználók is utazási idő vagy ösztönzésének követelmények alapján. 

Az útválasztási lehetőségeket részleteiért olvassa el a [útvonal API-dokumentáció](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Keresési szolgáltatás

A keresési szolgáltatás segít a fejlesztőknek a címek, helyek, cégjegyzékeket keressenek név vagy a kategória, keresése és egyéb földrajzi adatok. A Search service képes [fordított geokódolással](https://en.wikipedia.org/wiki/Reverse_geocoding) címeket és útkereszteződéseket a földrajzi szélesség és hosszúság alapján.

![Keresés a térképen – példa](media/about-azure-maps/Introduction_Search.png)

A keresési szolgáltatás többek között speciális funkciókat is biztosít:

* Keressen rá egy útvonalon.
* Keressen rá egy szélesebb körű területen belül.
* Batch-keresési kérelmek egy csoporthoz.
* Keresse meg a nagyobb területet a hely pont helyett. 

A kötegelt és a területi keresés API-jai jelenleg előzetes verzióban érhetők el. A keresési képességek a további tudnivalókért olvassa el a [keresési API-dokumentáció](https://docs.microsoft.com/rest/api/maps/search).

### <a name="spatial-operations-service"></a>Térbeli Operations szolgáltatás

Az Azure Maps térbeli Operations szolgáltatás helyére vonatkozó információ vesz igénybe, és tájékoztatja a felhasználókat a munkaidő és a hely folyamatban lévő eseményekre segítségével valós időben elemzi azokat. Ezáltal a közel valós idejű elemzés és prediktív modellezés az események. 

A szolgáltatás lehetővé teszi az ügyfelek számára, hogy növelje a hely intelligencia üzembe helyezhető a közös térinformatikai matematikai számítások, beleértve a legközelebbi pont, nagy kör távolság és pufferek. További információt a szolgáltatás és a különböző szolgáltatások, olvassa el a [térbeli műveletek API-dokumentáció](https://docs.microsoft.com/rest/api/maps/spatial).

### <a name="time-zone-service"></a>Időzóna szolgáltatás

A Time Zone service lehetővé teszi a lekérdezés jelenlegi, korábbi és jövőbeli időzónaadatok vagy szélesség/hosszúság pár használatával vagy egy [IANA-azonosító](https://www.iana.org/). A Time Zone service lehetővé teszi számára:

* A Microsoft Windows időzóna-azonosítói IANA időzónák konvertálása.
* Beolvasása egy időzóna eltolódása az UTC Időzóna.
* Az aktuális idő beolvasása az időzónát. 

Egy tipikus JSON-válasz a Time Zone service, az alábbi mintához hasonlóan néz ki:

```JSON
{
    "Version": "2017c",
    "ReferenceUtcTimestamp": "2017-11-20T23:09:48.686173Z",
    "TimeZones": [{
        "Id": "America/Los_Angeles",
        "ReferenceTime": {
            "Tag": "PST",
            "StandardOffset": "-08:00:00",
            "DaylightSavings": "00:00:00",
            "WallTime": "2017-11-20T15:09:48.686173-08:00",
            "PosixTzValidYear": 2017,
            "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
        }
    }]
}
```

Ez a szolgáltatás részletes ismertetéséért olvassa el a [időzóna API-dokumentáció](https://docs.microsoft.com/rest/api/maps/timezone).

### <a name="traffic-service"></a>Forgalom szolgáltatás

A Traffic service olyan webes szolgáltatás, amely a fejlesztők a webes és mobil alkalmazások forgalmi adatokat igénylő létrehozása. A szolgáltatás két adattípust biztosít:

* Forgalom áramlását: Valós idejű megfigyelt sebesség és a hálózat összes fő utak utazási idejét.
* Incidensek forgalmat: Naprakész képet forgalmi dugókról és incidensek a közúti hálózati körül.

![Példa a forgalomra vonatkozó információkat a térképen](media/about-azure-maps/Introduction_Traffic.png)

További információkért lásd: a [forgalom API-dokumentáció](https://docs.microsoft.com/rest/api/maps/traffic).

### <a name="ip-to-location-service"></a>Hely szolgáltatás IP-cím

Az IP-cím helye szolgáltatás használatával az IP-címet a lekért kétbetűs ország kód előnézetének megtekintéséhez. Ez a szolgáltatás segítségével testre szabni, és a felhasználói élmény fokozása azáltal, hogy testre szabott alkalmazás tartalom földrajzi helye alapján.

A helymeghatározó szolgáltatás az IP-cím a REST API-k további tudnivalókért olvassa el a [Azure Maps földrajzi hely meghatározásának API-dokumentáció](https://docs.microsoft.com/rest/api/maps/geolocation).

## <a name="programming-model"></a>A programozási modell

Az Azure Maps a mobilitás jegyében készült, és segíthet a többplatformos alkalmazásokat fejleszthet. Nyelvtől és támogatja a JSON-kimenet keresztül programozási modellt használ [REST API-k](https://docs.microsoft.com/rest/api/maps/).

Az Azure Maps továbbá tartalmaz egy kényelmes [JavaScript-térképvezérlőt](https://docs.microsoft.com/javascript/api/azure-maps-control), amelynek egyszerű programozási modellje lehetővé teszi a webes és mobilalkalmazások gyors és egyszerű fejlesztését.

## <a name="usage"></a>Használat

Az Azure Maps-szolgáltatásokhoz való hozzáférését fog kérdése a [az Azure portal](https://portal.azure.com) és a egy az Azure Maps-fiók létrehozása.

Az Azure Maps kulcsalapú hitelesítési sémát használ. A fiók már létrehozott két kulcsot tartalmaz. Bármelyik kulcs használható az Azure Maps szolgáltatásnak küldött kérésben, amivel megkezdheti ezeknek a helyhez kapcsolódó képességeknek az alkalmazásba integrálását.

## <a name="supported-regions"></a>Támogatott régiók

Az Azure Maps API-k jelenleg ezek kivételével az összes országban és régióban érhető el:

* Argentína
* Kína
* India
* Marokkó
* Pakisztán
* Dél-Korea

Győződjön meg arról, hogy az aktuális IP-címe helye nem a nem támogatott országok/régiók egyikében.

## <a name="next-steps"></a>További lépések

Próbálja meg egy mintaalkalmazást, amely bemutatja az Azure Maps:

> [!div class="nextstepaction"]
> [Rövid útmutató: Webes alkalmazás létrehozása](quick-demo-map-app.md)

Maradjon naprakész az Azure Maps szolgáltatásban: 

> [!div class="nextstepaction"]
> [Azure Maps-blog](https://azure.microsoft.com/blog/topics/azure-maps/)
