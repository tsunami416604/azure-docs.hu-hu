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
ms.openlocfilehash: ce55185effc67709157ce6219c405bb8e32dd5db
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768579"
---
# <a name="what-is-azure-maps"></a>Mi az Azure Maps?

Az Azure Maps gyűjteménye, térinformatikai szolgáltatások rögzítetlen által elérhető pontos földrajzi kontextusának biztosításához a webes és mobil alkalmazások a legfrissebb társítási adatok törlése. Renderelési REST API-k áll az Azure Maps **Maps** több stílusok és szatellit képanyag, **keresésével** címek, helyek és hasznos helyek szerte a világon; **Útválasztás** point-to-point, multipoint, multipoint optimalizálás, isochrone, kereskedelmi járművek, befolyásolják a forgalom és mátrix útválasztás; legjavát megtekintése a fajta forgalmat, és az incidensek; létrehozó felhasználó helye keresztül **Földrajzi hely meghatározásának**; és helyre való konvertálást **időzónák**, valamint beolvasása idő egy helyen. Az Azure Maps továbbá lehetővé teszi a szolgáltatások **Geokerítések**, térkép **adatok** helyadatokhoz, az Azure-ban; üzemeltető - tárolót és **térbeli Operations** helyére térinformatikai elemzés révén intelligencia. Azure Maps-szolgáltatások érhetők el közvetlenül a REST API-ként vagy keresztül vagy a robusztus **Web SDK** vagy **Android SDK**. Ezek az eszközök lehetővé teszik a fejlesztők gyorsan üzembe helyezheti és méretezzen olyan megoldásokat, amelyek az Azure-felhőben az Azure megoldásokba. Regisztráljon az ingyenes [Azure Maps-fiók](https://azure.microsoft.com/services/azure-maps/) ma és a kezdő fejlesztésének!

A következő videó részletesen bemutatja az Azure Mapset:

<iframe src="https://channel9.msdn.com/Shows/Azure-Friday/Azure-Location-Based-Services/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Térképvezérlők

### <a name="web-control"></a>Webes vezérlő

Az Azure Maps webes vezérlővel az interaktív térképek testreszabhatók saját tartalmak és képek hozzáadásával, amelyek megjelennek a webes vagy mobilalkalmazásaiban. Ez a vezérlő a WebGL-t használja, amely lehetővé teszi nagy adatkészletek nagy teljesítményű renderelését. A vezérlővel végzett fejlesztés JavaScript vagy TypeScript használatával történik.

![Az Azure Maps webes vezérlője](media/about-azure-maps/Introduction_WebMapControl.png)

## <a name="services-in-azure-maps"></a>Az Azure Maps szolgáltatásai

Az Azure Maps az alábbi hat szolgáltatást tartalmazza, amelyekkel földrajzi kontextust biztosíthat Azure-alkalmazásai számára.

### <a name="render-service"></a>Megjelenítő szolgáltatás

A Render Service segítségével a fejlesztők térképészeti jellegű webes és mobilalkalmazásokat fejleszthetnek. A szolgáltatás használhat nagy felbontású rasztergrafikákat, amelyek 19 nagyítási szinten érhetők el, vagy teljes körűen testreszabható, vektorformátumú térképeket.

![Azure Maps – Map.png](media/about-azure-maps/Introduction_Map.png)

A megjelenítő szolgáltatás mostantól előzetes verziójú API-kat biztosít, amelyeken keresztül a fejlesztők műholdképekkel dolgozhatnak. További részletekért olvassa el az [Azure Maps megjelenítő API-jait](https://docs.microsoft.com/rest/api/maps/render) bemutató cikket.

### <a name="route-service"></a>Útvonal szolgáltatás

Az útvonal szolgáltatás robusztus geometriai számításokat biztosít valós infrastruktúrákat és több közlekedési módot alkalmazó útvonalak számításához. A szolgáltatás lehetővé teszi a fejlesztők számára, hogy többféle közlekedési formára (autó, teherautó, kerékpár, gyalog) vonatkozóan számítsák ki az útvonalakat. Olyan tényezők figyelembe vételére is képes, mint a forgalmi viszonyok, a súlykorlátozások vagy a veszélyes anyagok szállítása.

![Azure Maps – Route.png](media/about-azure-maps/Introduction_Route.png)

Az útvonal szolgáltatás most előzetes verzióban speciális funkciókat kínál: több útvonal kötegelt feldolgozása, különböző indulási és célállomások közötti utazási idők és távolságok mátrixa, vagy a rendelkezésre álló idő vagy üzemanyag alapján megtehető útvonalak vagy távolságok keresése. Az útvonaltervezési képességekkel kapcsolatos további részletekért olvassa el az [Azure Maps útvonal API-jait](https://docs.microsoft.com/rest/api/maps/route) bemutató cikket.

### <a name="search-service"></a>Keresési szolgáltatás

A keresési szolgáltatás lehetővé teszi a fejlesztők számára, hogy címeket, helyeket és bejegyzett vállalkozásokat keressenek név, kategória vagy egyéb földrajzi adatok alapján. A keresési szolgáltatás képes [fordított geokódolással](https://en.wikipedia.org/wiki/Reverse_geocoding) meghatározni a címeket és útkereszteződéseket a hosszúsági és szélességi koordináták alapján.

![Azure Maps – Search.png](media/about-azure-maps/Introduction_Search.png)

A keresési szolgáltatás emellett speciális funkciókat is biztosít: útvonal mentén való keresés, nagyobb területen való keresés, keresési kérések csoportjának kötegelt végrehajtása, valamint nagyobb terület keresése egyetlen helypont helyett. A kötegelt és a területi keresés API-jai jelenleg előzetes verzióban érhetők el. A keresési képességekkel kapcsolatos további részletekért olvassa el az [Azure Maps keresési API-jait](https://docs.microsoft.com/rest/api/maps/search) bemutató cikket.

### <a name="time-zone-service"></a>Időzóna szolgáltatás

Az időzóna szolgáltatás lehetővé teszi a jelenlegi, korábbi és jövőbeli időzónaadatok lekérdezését hosszúsági/szélességi koordináták vagy egy [IANA-azonosító](https://www.iana.org/) alapján. Emellett a Time Zone Service használatával a Microsoft Windows időzóna-azonosítói IANA-időzónákká alakíthatók egy adott időzóna UTC-hez képest számolt eltérésének és az aktuális helyi idejének lekérésével. A Time Zone Service lekérdezéseire adott tipikus JSON-válasz a következő mintához hasonló:

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

A szolgáltatással kapcsolatos részletekért lépjen az [Azure Maps időzóna API-jainak](https://docs.microsoft.com/rest/api/maps/timezone) oldalára.

### <a name="traffic-service"></a>Forgalom szolgáltatás

A forgalmi szolgáltatás egy fejlesztők számára készült webszolgáltatás-csomag, amellyel forgalmi adatokat igénylő webes és mobilalkalmazások fejleszthetők. A szolgáltatás két adattípust biztosít:

* Forgalom áramlása – valós időben megfigyelt menetsebességek és menetidők a hálózaton belüli összes fontos útra vonatkozóan;
* Forgalmi zavarok – pontos kép az úthálózaton fennálló forgalmi dugókról és zavarokról.

![Azure Maps – Forgalom](media/about-azure-maps/Introduction_Traffic.png)

További részletekért lépjen az [Azure Maps forgalmi API-jainak](https://docs.microsoft.com/rest/api/maps/traffic) oldalára.

### <a name="ip-to-location"></a>IP-alapú helymeghatározás

Az IP – hely szolgáltatással az adott IP-címhez előre megtekinthető a kikeresett kétbetűs országkód. Ezzel a szolgáltatással az alkalmazásban a földrajzi helytől függő egyedi tartalommal javíthatja, növelheti a felhasználói élményt.

Az IP-alapú helymeghatározási szolgáltatás REST API-kkal kapcsolatos információiért lépjen az [Azure Maps Geolocation API-jainak](https://docs.microsoft.com/rest/api/maps/geolocation) oldalára.

## <a name="programming-model"></a>A programozási modell

Az Azure Maps a mobilitás jegyében készült, és különböző platformokon futó alkalmazásokat is képes kiszolgálni. Egy nyelvfüggetlen és a JSON-kimeneteket a [REST API-k](https://docs.microsoft.com/rest/api/maps/) használatával támogató programozási modellt használ.

Az Azure Maps továbbá tartalmaz egy kényelmes [JavaScript-térképvezérlőt](https://docs.microsoft.com/javascript/api/azure-maps-control), amelynek egyszerű programozási modellje lehetővé teszi a webes és mobilalkalmazások gyors és egyszerű fejlesztését.

## <a name="usage"></a>Használat

A Maps szolgáltatásainak eléréséhez csupán létre kell hozni egy Azure Maps-fiókot az [Azure Portalon](https://portal.azure.com).

Az Azure Maps kulcsalapú hitelesítési sémát használ. A fiókhoz két előre létrehozott kulcs is jár. Bármelyik kulcs használható az Azure Maps szolgáltatásnak küldött kérésben, amivel megkezdheti ezeknek a helyhez kapcsolódó képességeknek az alkalmazásba integrálását.

## <a name="supported-regions"></a>Támogatott régiók

Az Azure Maps API jelenleg az alábbiak kivételével minden országban elérhető:

* Argentína
* Kína
* India
* Marokkó
* Pakisztán
* Dél-Korea

Ellenőrizze, hogy az IP-címe szerinti hely nem a fenti nem támogatott országok valamelyikében található-e.

## <a name="next-steps"></a>További lépések

További információk az Azure Maps új szolgáltatásairól:

> [!div class="nextstepaction"]
> [Útvonalmátrix, izokrón térképek, IP-keresés és további szolgáltatások](https://azure.microsoft.com/blog/route-matrix-isochrones-ip-lookup-and-more-added-to-azure-maps/)

Lépjen tovább, és próbálja ki a szolgáltatást bemutató mintaalkalmazást:

> [!div class="nextstepaction"]
> [Bemutató interaktív kereséses térkép indítása](quick-demo-map-app.md)
