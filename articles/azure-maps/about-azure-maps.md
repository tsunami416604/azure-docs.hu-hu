---
title: Áttekintés | Microsoft Azure Maps
description: Ismerje meg a Microsoft Azure Maps szolgáltatásait és képességeit, és hogy miként használhatja őket az alkalmazásokban.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: fdac12350db785e6194cd3d057f4d2adfefa5969
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80335735"
---
# <a name="what-is-azure-maps"></a>Mi az Azure Maps?

Az Azure Maps olyan térinformatikai szolgáltatások gyűjteménye, amelyek friss leképezési adatokat használnak a webes és mobilalkalmazások földrajzi környezetének biztosításához. Az Azure Maps a következőket biztosítja:

* REST API-k a térképek több stílusban és műholdas képekben való renderelésére.''
* A szolgáltatások keresésével világszerte megkeresheti a címeket, helyeket és érdekes helyeket.
* Különböző útválasztási lehetőségek; például pont-pont, többpontos, többpontos optimalizálás, isochrone, haszongépjármű, forgalom által befolyásolt, és mátrix routing.
* Forgalmi nézet és incidensek nézet, a forgalmi információkat igénylő alkalmazások hoz.
* Mobilitási szolgáltatás a tömegközlekedési logisztika igényléséhez, az útvonalak valós idejű megtervezéséhez és az alternatív szállítási módokhoz való információkéréshez.
* Időzóna- és helymeghatározási szolgáltatások, valamint hely időzónákká alakítása.
* Geofencing szolgáltatás és térképészeti adattárolás, az Azure-ban tárolt helyadatokkal. 
* Helymeghatározási intelligencia térinformatikai elemzéssel. 

Emellett az Azure Maps-szolgáltatások a webes SDK-n vagy az Android SDK-n keresztül érhetők el. Ezek az eszközök segítségével a fejlesztők gyorsan fejleszthetnek és méretezhetnek olyan megoldásokat, amelyek helyadatokat integrálnak az Azure-megoldásokba. 

Regisztrálhat egy ingyenes [Azure Maps-fiókot,](https://azure.microsoft.com/services/azure-maps/) és elkezdheti a fejlesztést.

A következő videó részletesen bemutatja az Azure Mapset:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Térképvezérlők

### <a name="web-sdk"></a>Web SDK

Az Azure Maps Web SDK lehetővé teszi az interaktív térképek testreszabását saját tartalmaival és képeivel. Ezt az interaktív térképet webes vagy mobilalkalmazásokhoz is használhatja. A térképvezérlő a WebGL-et használja, így nagy adathalmazokat jeleníthet meg nagy teljesítménnyel. Dolgozzon az SDK-val JavaScript vagy TypeScript használatával.

![Példa a népességváltozás térképére](media/about-azure-maps/intro_web_map_control.png)

### <a name="android-sdk"></a>Android SDK

Az Azure Maps Android SDK segítségével hozhat létre mobil térképészeti alkalmazásokat. 

![Példák leképezése mobileszközön](media/about-azure-maps/android_sdk.png)

## <a name="services-in-azure-maps"></a>Az Azure Maps szolgáltatásai

Az Azure Maps a következő kilenc szolgáltatásból áll, amelyek földrajzi környezetet biztosíthatnak az Azure-alkalmazásokszámára.

### <a name="data-service"></a>Data szolgáltatás

Az adatok elengedhetetlenek a térképekhez. Az Adatszolgáltatás segítségével térinformatikai adatokat tölthet fel és tárolhatja térbeli műveletekhez vagy képkompozícióhoz.  Ha közelebb hozza az ügyféladatokat az Azure Maps szolgáltatáshoz, azzal csökkenti a késést, növeli a termelékenységet, és új forgatókönyveket hoz létre az alkalmazásokban. A szolgáltatással kapcsolatos részleteket a [Data Service API dokumentációjában](https://docs.microsoft.com/rest/api/maps/data)találja.

### <a name="mobility-service"></a>Mobilitási szolgáltatás

Az Azure Maps Mobility szolgáltatás valós idejű utazástervezést tesz lehetővé. A lehető legjobb útvonallehetőségeket adja vissza, és különböző utazási módokat biztosít. A metró (városi) területeken, ezek a módok közé tartozik a gyaloglás, kerékpározás, és a tömegközlekedés. Kérheti az átmenő útvonalat, a vonalgeometriát, a megállók listáját, az ütemezett érkezéseket, a valós idejű érkezéseket és a szolgáltatási riasztásokat.

A szolgáltatás lehetővé teszi a hely körüli objektumtípusok keresését is. A felhasználók egy adott hely körül kereshetnek közös kerékpárokat, robogókat vagy autókat. Használók tud kereslet a szám -ból elérhető kerékpár -ban legközelebbi dokk és kutatás elérhető jármű részére lovagol- cserépdarab. És, használók tud talál részlet szeret jövő elérhetőség -ból jármű és időszerű üzemanyag szint.

Ha többet szeretne megtudni a szolgáltatásról, olvassa el a [Mobility API dokumentációját.](https://docs.microsoft.com/rest/api/maps/mobility)

### <a name="render-service"></a>Megjelenítő szolgáltatás

A Render szolgáltatás segít a fejlesztőknek webes és mobilalkalmazásokat létrehozni a térképészeti funkciókkal. A szolgáltatás használhat nagy felbontású rasztergrafikákat, amelyek 19 nagyítási szinten érhetők el, vagy teljes körűen testreszabható, vektorformátumú térképeket.

![Példa térképre a Render szolgáltatásból](media/about-azure-maps/intro_map.png)

A megjelenítő szolgáltatás mostantól előzetes verziójú API-kat biztosít, amelyeken keresztül a fejlesztők műholdképekkel dolgozhatnak. További részletekért olvassa el a [Render API dokumentációját.](https://docs.microsoft.com/rest/api/maps/render)

### <a name="route-service"></a>Útvonal szolgáltatás

Az útvonal szolgáltatás robusztus geometriai számításokat biztosít valós infrastruktúrákat és több közlekedési módot alkalmazó útvonalak számításához. A szolgáltatás lehetővé teszi a fejlesztők számára, hogy számos utazási módban, például autóban, teherautóban, kerékpáron vagy gyaloglásban számítsanak ki irányokat. A szolgáltatás figyelembe veszi az olyan bemeneteket is, mint a forgalmi feltételek, a súlykorlátozások vagy a veszélyes anyagok szállítása.

![Példa az Útvonal szolgáltatás térképére](media/about-azure-maps/intro_route.png)

Az Útvonal szolgáltatás a speciális funkciók előnézetét kínálja, mint például: 

* Több útvonalkérelem kötegelt feldolgozása.
* Az utazási idő és a távolság mátrixai az eredeti és az úti célok között.
* Olyan útvonalak vagy távolságok keresése, amelyeket a felhasználók az idő- vagy üzemanyag-igény alapján utazhatnak. 

Az útválasztási lehetőségekről az [Route API dokumentációjában](https://docs.microsoft.com/rest/api/maps/route)olvashat részletesen.

### <a name="search-service"></a>Keresési szolgáltatás

A Keresési szolgáltatás segítségével a fejlesztők nevek, illetve kategóriák szerint kereshetnek címeket, helyeket, üzleti listákat, valamint egyéb földrajzi információkat. A keresési szolgáltatás a szélességi és hosszúsági fokok alapján [megfordíthatja](https://en.wikipedia.org/wiki/Reverse_geocoding) a geokódcímeket és az utcákat.

![Példa térképen végzett keresésre](media/about-azure-maps/intro_search.png)

A Keresési szolgáltatás olyan speciális szolgáltatásokat is kínál, mint például:

* Keresés az útvonal mentén.
* Keresés egy szélesebb területen belül.
* Keresési kérelmek csoportjának kötegelése.
* Nagyobb terület keresése helypont helyett. 

A kötegelt és a területi keresés API-jai jelenleg előzetes verzióban érhetők el. A keresési lehetőségekről további információt a [Keresési API dokumentációjában](https://docs.microsoft.com/rest/api/maps/search)talál.

### <a name="spatial-operations-service"></a>Térbeli műveletek szolgáltatás

Az Azure Maps térbeli műveletek szolgáltatás helyadatokat fogad. Gyorsan elemzi a helyadatokat, hogy segítsen tájékoztatni az ügyfeleket az időben és térben zajló eseményekről. Lehetővé teszi az események közel valós idejű elemzését és prediktív modellezését. 

A szolgáltatás lehetővé teszi az ügyfelek számára, hogy közös térinformatikai matematikai számítások könyvtárával javítsák helymeghatározási intelligenciájukat. A gyakori számítások közé tartoznak a legközelebbi pont, a nagy körtávolság és a pufferek. Ha többet szeretne megtudni a szolgáltatásról és a különböző funkciókról, olvassa el a [Spatial Operations API dokumentációját.](https://docs.microsoft.com/rest/api/maps/spatial)

### <a name="time-zone-service"></a>Időzóna szolgáltatás

Az Időzóna szolgáltatás lehetővé teszi az aktuális, a korábbi és a jövőbeli időzóna-adatok lekérdezését. Használhat szélességi és hosszúsági párokat, vagy [iana-azonosítót.](https://www.iana.org/) Az időzóna szolgáltatás a következőket is lehetővé teszi:

* Microsoft Windows időzóna-azonosítók konvertálása IANA időzónákká.
* Időzóna-eltolás beolvasása utc-re.
* Az aktuális idő beszerzése a kiválasztott időzónában. 

Az időzóna-szolgáltatás lekérdezésének tipikus JSON-válasza a következő példához hasonlóan néz ki:

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

A szolgáltatással kapcsolatos részletekért olvassa el az [Időzóna API dokumentációját.](https://docs.microsoft.com/rest/api/maps/timezone)

### <a name="traffic-service"></a>Forgalom szolgáltatás

A Traffic szolgáltatás olyan webes szolgáltatások csomagja, amelyeket a fejlesztők forgalmi adatokat igénylő webes vagy mobilalkalmazásokhoz használhatnak. A szolgáltatás két adattípust biztosít:

* Forgalom: Valós idejű megfigyelt sebesség és utazási idő a hálózat összes kulcsfontosságú útján.
* Közlekedési események: Naprakész nézet az úthálózat körüli forgalmi dugókról és incidensekről.

![Példa forgalmi információkat felkészítő térképre](media/about-azure-maps/intro_traffic.png)

További információt a [Traffic API dokumentációjában](https://docs.microsoft.com/rest/api/maps/traffic)talál.

### <a name="ip-to-location-service"></a>IP-helyszolgáltatás

Az IP-hely szolgáltatás segítségével megtekintheti egy IP-cím beolvasott kétbetűs országkódját. Ez a szolgáltatás segíthet a felhasználói élmény javításában azáltal, hogy földrajzi hely alapján személyre szabott alkalmazástartalmat biztosít.

A REST API IP-helyszolgáltatásról az Azure [Maps földrajzi helymeghatározási API dokumentációjában](https://docs.microsoft.com/rest/api/maps/geolocation)olvashat bővebben.

## <a name="programming-model"></a>A programozási modell

Az Azure Maps a mobilitásra készült, és segítséget nyújt a platformok közötti alkalmazások fejlesztésében. Olyan programozási modellt használ, amely nyelvi agnosztikus, és támogatja a JSON kimenetet [a REST API-kon](https://docs.microsoft.com/rest/api/maps/)keresztül.

Emellett az Azure Maps egy egyszerű programozási modellel kényelmes [JavaScript-térképvezérlést](https://docs.microsoft.com/javascript/api/azure-maps-control) is kínál. A fejlesztés gyors és egyszerű mind a webes, mind a mobil alkalmazások számára.

## <a name="usage"></a>Használat

Az Azure Maps-szolgáltatások elérése az [Azure Portalra](https://portal.azure.com) való belépés és az Azure Maps-fiók létrehozása.

Az Azure Maps kulcsalapú hitelesítési sémát használ. A fiókhoz két már létrehozott kulcs tartozik, bármelyik kulcsot használva. Kezdje el integrálni ezeket a helymeghatározási képességeket az alkalmazásába, és kéréseket intézhet az Azure Maps-szolgáltatásokhoz.

Megjegyzés – Az Azure Maps megosztja az ügyfél által megadott cím-/helylekérdezéseket ("Lekérdezéseket") harmadik fél tomtomjával a leképezési funkciók céljából. A lekérdezések nem kapcsolódnak egyetlen ügyfélhez vagy végfelhasználóhoz sem, ha a TomTom-mal vannak megosztva, és nem használhatók személyek azonosítására. A Microsoft jelenleg is azon dolgozik, hogy felvesse a TomTom-ot az Online Services alvállalkozói nak listájára. Ne feledje, hogy a Mobilitás és Időjárás Szolgáltatások, amelyek magukban foglalják az integráció Moovit és AccuWeather jelenleg [PREVIEW](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="supported-regions"></a>Támogatott régiók

Az Azure Maps API-k jelenleg minden országban és régióban elérhetők, kivéve ezeket:

* Kína
* Dél-Korea

Ellenőrizze, hogy az aktuális IP-cím helye támogatott országban van-e.

## <a name="next-steps"></a>További lépések

Próbáljon ki egy mintaalkalmazást, amely bemutatja az Azure Mapset:

> [!div class="nextstepaction"]
> [Rövid útmutató: Webalkalmazás létrehozása](quick-demo-map-app.md)

Legyen naprakész az Azure Mapsben: 

> [!div class="nextstepaction"]
> [Az Azure Maps blogja](https://azure.microsoft.com/blog/topics/azure-maps/)
