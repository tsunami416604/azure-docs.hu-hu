---
title: Áttekintés | Microsoft Azure térképek
description: Ismerkedjen meg Microsoft Azure Maps szolgáltatásaival és képességeivel, és hogyan használhatja őket az alkalmazásokban.
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 2909dbebc7531be0e45b321cc31b599b0f63ee4a
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76262571"
---
# <a name="what-is-azure-maps"></a>Mi az Azure Maps?

A Azure Maps térinformatikai szolgáltatások gyűjteménye, amely friss leképezési információt használ a webes és mobil alkalmazások földrajzi kontextusának biztosításához. Azure Maps a következőket biztosítja:

* REST API-k, amelyek több stílusban és műholdon ábrázolt leképezéseket mutatnak be.
* A címek, a helyek és az érdekes pontok keresése a világon.
* Különböző típusú útválasztás: pont – pont, multipoint, multipoint-optimalizálás, isochrone, kereskedelmi jármű, forgalom befolyásolta és mátrix-Útválasztás
* Forgalmi folyamat és incidensek nézet.
* Mobilitási szolgáltatások, amelyek a tömegközlekedést és az alternatív átviteli módokat (például a kerékpár-megosztást, a robogó-megosztást és a személygépkocsi-megosztást) és a tervezési útvonalakat valós időben használják. 
* Felhasználói hely létrehozása térinformatikai szolgáltatással, valamint a hely és az időzónák konvertálása. 
* Szolgáltatások geokerítések és térképes adattároláshoz, az Azure-ban tárolt helyadatok mellett. 
* Helymeghatározási intelligencia a térinformatikai elemzéseken keresztül. 

Emellett Azure Maps szolgáltatások a web SDK-n vagy az Android SDK-n keresztül is elérhetők. Ezek az eszközök segítenek a fejlesztőknek olyan megoldások gyors fejlesztésében és méretezésében, amelyek a helyadatok integrálását biztosítják az Azure-megoldásokba. 

Regisztrálhat egy ingyenes [Azure Maps fiókra](https://azure.microsoft.com/services/azure-maps/) , és megkezdheti a fejlesztést.

A következő videó részletesen bemutatja az Azure Mapset:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Térképvezérlők

### <a name="web-sdk"></a>Web SDK

A Azure Maps web SDK lehetővé teszi, hogy az interaktív térképeket saját webes vagy mobil alkalmazásaihoz saját tartalmakkal és képekkel testreszabja. Ez a vezérlő a WebGL használatát teszi lehetővé, így nagy teljesítményű nagyméretű adatkészleteket lehet megjeleníteni. Fejlessze az SDK-t JavaScript vagy írógéppel használatával.

![Példa a populáció változásának térképére](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>Android SDK

Mobil leképezési alkalmazások létrehozásához használja a Azure Maps Android SDK-t. 

![Példák megjelenítése mobileszközön](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Az Azure Maps szolgáltatásai

Azure Maps a következő kilenc szolgáltatásból áll, amelyek földrajzi kontextust biztosítanak az Azure-alkalmazásokhoz.

### <a name="data-service"></a>Adatszolgáltatás

Az adatleképezések esetében elengedhetetlen az adatkezelés. Az adatszolgáltatással a térbeli adatok és a képek összeállításával használható térinformatikai adatok tölthetők fel és tárolhatók.  Az ügyféladatok a Azure Maps szolgáltatáshoz közelebb hozása csökkenti a késést, növeli a termelékenységet, és új forgatókönyveket hoz létre az alkalmazásaiban. A szolgáltatással kapcsolatos részletekért tekintse meg az [adatszolgáltatási API dokumentációját](https://docs.microsoft.com/rest/api/maps/data).

### <a name="mobility-service"></a>Mobilitási szolgáltatás

A Azure Maps mobilitási szolgáltatás valós idejű tervezést tesz lehetővé. A lehető legjobb útvonalat adja vissza, és különböző utazási módokat biztosít. A Metro (város) területek esetében ezek a módok a gyaloglást, a kerékpározást és a nyilvános átvitelt is tartalmazhatják. A továbbítási útvonalat, a vonal geometriáját, a leállítások listáját, az ütemezett és a valós idejű érkezéseket, valamint a szolgáltatási riasztásokat is kérheti.

A szolgáltatás emellett lehetővé teszi bizonyos objektumtípusok, például a megosztott kerékpárok, robogók vagy autók keresését egy adott helyen. A felhasználók a legközelebbi Dockon igényelhetik a rendelkezésre álló kerékpárok számát, és az elérhető járműveket a Ride-Sharing szolgáltatásban kereshetik meg. Emellett a felhasználók olyan részleteket is találhatnak, mint például a jövő rendelkezésre állása és az aktuális üzemanyag-szint.

A szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [mobilitási API dokumentációját](https://docs.microsoft.com/rest/api/maps/mobility).

### <a name="render-service"></a>Megjelenítő szolgáltatás

A renderelési szolgáltatás segítségével a fejlesztők webes és mobil alkalmazásokat hozhatnak létre a leképezés köré. A szolgáltatás használhat nagy felbontású rasztergrafikákat, amelyek 19 nagyítási szinten érhetők el, vagy teljes körűen testreszabható, vektorformátumú térképeket.

![Példa a render szolgáltatásból származó térképre](media/about-azure-maps/Introduction_Map.png)

A megjelenítő szolgáltatás mostantól előzetes verziójú API-kat biztosít, amelyeken keresztül a fejlesztők műholdképekkel dolgozhatnak. További részletekért olvassa el a [Render API dokumentációját](https://docs.microsoft.com/rest/api/maps/render).

### <a name="route-service"></a>Útvonal szolgáltatás

Az útvonal szolgáltatás robusztus geometriai számításokat biztosít valós infrastruktúrákat és több közlekedési módot alkalmazó útvonalak számításához. A szolgáltatás lehetővé teszi a fejlesztők számára, hogy többféle közlekedési formára (autó, teherautó, kerékpár, gyalog) vonatkozóan számítsák ki az útvonalakat. Olyan tényezők figyelembe vételére is képes, mint a forgalmi viszonyok, a súlykorlátozások vagy a veszélyes anyagok szállítása.

![Példa egy térképre az útvonal-szolgáltatásból](media/about-azure-maps/Introduction_Route.png)

Az útvonal-szolgáltatás a speciális funkciók előzetes verzióját kínálja, például: 

* Több útválasztási kérelem kötegelt feldolgozása.
* Az utazási idő és a távolságok egy készlet és a célhely között.
* Azon útvonalak vagy távolságok megkeresése, amelyeket a felhasználók az idő-vagy üzemanyag-követelmények alapján utazhatnak. 

Az útválasztási képességekkel kapcsolatos részletekért olvassa el az [Route API dokumentációját](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Keresési szolgáltatás

A Search szolgáltatás segítségével a fejlesztők megkereshetik a címeket, a helyeket, az üzleti listákat név vagy kategória alapján, valamint egyéb földrajzi információkat. A keresési szolgáltatás a földrajzi szélesség és a hosszúságok alapján fordított geocode-címeket és-utcákat is [megfordíthatja](https://en.wikipedia.org/wiki/Reverse_geocoding) .

![Térképes keresés – példa](media/about-azure-maps/Introduction_Search.png)

A Search szolgáltatás olyan speciális funkciókat is kínál, mint például a következők:

* Keresés az útvonalon.
* Keresés egy szélesebb területeken.
* Kötegelt keresési kérelmek csoportja.
* Egy hely pont helyett nagyobb terület keresése. 

A kötegelt és a területi keresés API-jai jelenleg előzetes verzióban érhetők el. A keresési lehetőségekkel kapcsolatos további információkért olvassa el a [Search API dokumentációját](https://docs.microsoft.com/rest/api/maps/search).

### <a name="spatial-operations-service"></a>Térbeli üzemeltetési szolgáltatás

A Azure Maps térbeli üzemeltetési szolgáltatás megkeresi a hely adatait, és gyorsan elemzi, hogy a folyamatos események tájékoztassák-e az időben és a térben. Lehetővé teszi a közel valós idejű elemzést és az események prediktív modellezését. 

A szolgáltatás lehetővé teszi, hogy az ügyfelek a közös térinformatikai matematikai számítások könyvtára révén fokozzák a helyük felderítését. Gyakori számítások például a legközelebbi pont, a nagy kör távolsága és a pufferek. Ha többet szeretne megtudni a szolgáltatásról és a különböző funkciókról, olvassa el a [térbeli operatív API dokumentációját](https://docs.microsoft.com/rest/api/maps/spatial).

### <a name="time-zone-service"></a>Időzóna szolgáltatás

Az időzóna-szolgáltatás lehetővé teszi a jelenlegi, korábbi és jövőbeli időzóna-információk lekérdezését. A szélességi és a hosszúsági párokat, vagy egy [IANA-azonosítót](https://www.iana.org/)is használhat. Az időzóna szolgáltatás a következőket is lehetővé teszi:

* A Microsoft Windows időzóna-azonosítóinak átalakítása az IANA időzónára.
* Időzóna eltolásának beolvasása UTC-be.
* Az aktuális idő beolvasása egy időzónában. 

Az időzóna szolgáltatás lekérdezésére jellemző JSON-válasz az alábbi példához hasonlóan néz ki:

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

A szolgáltatással kapcsolatos részletekért olvassa el a [Time Zone API dokumentációját](https://docs.microsoft.com/rest/api/maps/timezone).

### <a name="traffic-service"></a>Forgalom szolgáltatás

A Traffic szolgáltatás olyan webszolgáltatások csomagja, amelyeket a fejlesztők a forgalmi adatokat igénylő webes vagy mobil alkalmazások számára használhatnak. A szolgáltatás két adattípust biztosít:

* Forgalmi folyamat: valós idejű megfigyelt sebesség és utazási idő a hálózaton lévő összes kulcsfontosságú útvonalon.
* Forgalmi incidensek: a forgalmi torlódások és a közúti hálózatokra vonatkozó incidensek naprakész áttekintését.

![Példa a forgalmi adatokat tartalmazó térképre](media/about-azure-maps/Introduction_Traffic.png)

További információt a [Traffic API dokumentációjában](https://docs.microsoft.com/rest/api/maps/traffic)talál.

### <a name="ip-to-location-service"></a>IP – hely szolgáltatás

Az IP-cím használatával megtekintheti az IP-címek beolvasott kétbetűs országkódot. Ez a szolgáltatás segít a felhasználói élmény fokozásában azáltal, hogy a földrajzi hely alapján testreszabott alkalmazási tartalmakat biztosít.

Ha további részleteket szeretne megtudni a REST API IP-címéről, olvassa el az [Azure Maps Térinformatikai API dokumentációját](https://docs.microsoft.com/rest/api/maps/geolocation).

## <a name="programming-model"></a>A programozási modell

A Azure Maps a mobilitáshoz készült, és több platformon futó alkalmazások fejlesztéséhez is segítséget nyújt. Olyan programozási modellt használ, amely az agnosztikus nyelvét használja, és támogatja a JSON-kimenetet a [REST API](https://docs.microsoft.com/rest/api/maps/)-kon keresztül.

A Azure Maps egy egyszerű programozási modellel is kényelmes [JavaScript-leképezési vezérlőt](https://docs.microsoft.com/javascript/api/azure-maps-control) biztosít. A fejlesztés a webes és a mobil alkalmazások esetében is gyorsan és egyszerűen elvégezhető.

## <a name="usage"></a>Használat

A Azure Maps szolgáltatásokhoz való hozzáférés a [Azure Portal](https://portal.azure.com) és egy Azure Maps fiók létrehozása.

Az Azure Maps kulcsalapú hitelesítési sémát használ. A fiókja az Ön számára már létrehozott két kulcsot tartalmaz. Kezdje el integrálni ezeket a tartózkodási helyeket az alkalmazásban, vagy használja a kulcsokat, és tegyen egy kérést a Azure Maps szolgáltatásnak.

## <a name="supported-regions"></a>Támogatott régiók

A Azure Maps API-k jelenleg minden országban és régióban elérhetők, kivéve az alábbiakat:

* Kína
* Dél-Korea

Ellenőrizze, hogy az aktuális IP-cím helye támogatott országban van-e.

## <a name="next-steps"></a>Következő lépések

Próbálja ki a Azure Mapst bemutató minta alkalmazást:

> [!div class="nextstepaction"]
> [Rövid útmutató: Webalkalmazás létrehozása](quick-demo-map-app.md)

Naprakész Azure Maps: 

> [!div class="nextstepaction"]
> [Azure Maps blog](https://azure.microsoft.com/blog/topics/azure-maps/)
