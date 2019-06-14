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
ms.openlocfilehash: 8092cd169f93a6815e52517d805941ac7ddcbbc0
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2019
ms.locfileid: "66807510"
---
# <a name="what-is-azure-maps"></a>Mi az Azure Maps?

Az Azure Maps gyűjteménye, térinformatikai szolgáltatások rögzítetlen által elérhető pontos földrajzi kontextusának biztosításához a webes és mobil alkalmazások a legfrissebb társítási adatok törlése. Renderelési REST API-k áll az Azure Maps **Maps** több stílusok és szatellit képanyag, **keresésével** címek, helyek és hasznos helyek szerte a világon; **Útválasztás** point-to-point, multipoint, multipoint optimalizálás, isochrone, kereskedelmi járművek, befolyásolják a forgalom és mátrix útválasztás; iparágvezető forgalmat, és események megtekintése **Mobilitási** nyilvános átvitel során, bikeshare, scooter megosztás és autó igénylő szolgáltatások információikat megtervezhetik az útvonalakat, kihasználva a szállítás és a valós idejű adatok alternatív módja; létrehozó felhasználó helye keresztül **Földrajzi hely meghatározásának**; és helyre való konvertálást **időzónák**, valamint beolvasása idő egy helyen. Az Azure Maps továbbá lehetővé teszi a szolgáltatások **Geokerítések**, térkép **adatok** helyadatokhoz, az Azure-ban; üzemeltető - tárolót és **térbeli Operations** helyére térinformatikai elemzés révén intelligencia. Azure Maps-szolgáltatások érhetők el közvetlenül a REST API-ként vagy keresztül vagy a robusztus **Web SDK** vagy **Android SDK**. Ezek az eszközök lehetővé teszik a fejlesztők gyorsan üzembe helyezheti és méretezzen olyan megoldásokat, amelyek az Azure-felhőben az Azure megoldásokba. Regisztráljon az ingyenes [Azure Maps-fiók](https://azure.microsoft.com/services/azure-maps/) ma és a kezdő fejlesztésének!

A következő videó részletesen bemutatja az Azure Mapset:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Térképvezérlők

### <a name="web-sdk"></a>Web SDK

Az Azure Maps Web SDK lehetővé teszi a saját tartalmat és a webes vagy mobilalkalmazásaiba való megjelenítéshez képanyag interaktív leképezések testreszabása. Ez a vezérlő a WebGL-t használja, amely lehetővé teszi nagy adatkészletek nagy teljesítményű renderelését. Az az SDK-ban a JavaScript- vagy TypeScript nyelven fejleszthet.

![Azure Maps Web SDK](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>Android SDK

Az Azure Maps Android SDK hatékony mobil leképezés alkalmazások létrehozását teszi lehetővé. 

![Az Azure Maps SDK Androidra](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Az Azure Maps szolgáltatásai

Az Azure Maps áll a következő kilenc földrajzi kontextusának biztosításához az Azure-alkalmazások által biztosított szolgáltatásokat.

### <a name="data-service"></a>Adatszolgáltatás

Adatok versenyképesség a térképek szolgáltatáshoz, és közelebb a vásárlói adatokat és az Azure Maps szolgáltatás fogja késés csökkentése érdekében, hatékonyság növeléséhez, majd hozzon létre világos, hatékony, új forgatókönyvek fel az alkalmazások. Data Service lehetővé teszi, hogy töltse fel és térinformatikai adatokat tároló térbeli műveletek vagy lemezkép-összeállításban csökkentheti a késéseket, a hatékonyság növeléséhez, valamint az alkalmazások új forgatókönyveket. Ez a szolgáltatás a részletekért látogasson el a [adatok Service API-k](https://docs.microsoft.com/rest/api/maps/data) lapot.

### <a name="mobility-service"></a>A mobilitási szolgáltatás

Az Azure Maps a mobilitási szolgáltatás valós idejű hely intelligencia biztosítsanak közeli nyilvános átviteli szolgáltatások, beleértve a leáll, az útvonal-információk és az utazási idő becsléseket. A szolgáltatás lehetővé teszi, hogy a keresés adott objektumtípus, például a nyilvános átvitel közben leáll, átvitel objektum objektum részletei készletét visszaadása egy megadott helyen körül kerékpárok/robogó/autók megosztott. A szolgáltatás lehetővé teszi, hogy a fejlesztők is kérelem átvitel soradatok alapvető információkat és a további részleteket, például a geometriai, az leáll, ütemezett listája és a valós idejű továbbításához beérkező kérelmek és szolgáltatási riasztások. Hány elérhető megosztott kerékpárok van hátra a legközelebbi dock által rögzített állomások információt kérő felhasználók is kérheti. A mobilitási szolgáltatás is rendelkezik a kereshetővé elérhető autó megosztás járművek, például a jövőbeli rendelkezésre állás és a jelenlegi ösztönzésének szint visszaadása.
Az Azure Maps a mobilitási szolgáltatás lehetővé teszi, hogy valós idejű trip tervezési, a legjobb útvonal lehetséges beállítások visszaadó és többféle közlekedési formára, beleértve a metro (város) területen elérhető Séta, kerékpározáshoz és nyilvános átvitel biztosít. Emellett a fejlesztők is kérhető az átvitel Útiterv részletei további információkkal ilyen geometry, az útvonal és részletes Útiterv ütemezéseket.

További információ a szolgáltatás és a különféle szolgáltatások kapcsolatban lásd: a [API-dokumentáció](https://docs.microsoft.com/rest/api/maps/mobility)

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

### <a name="spatial-operations"></a>Térműveletek

Az Azure Maps térbeli műveletei helyére vonatkozó információt is, és a-t a megalapozottabb történik időt és a helyet, a folyamatban lévő események ügyfeleink engedélyezésével közel valós idejű elemzés és prediktív modellezés az események valós időben elemezhetők. Szolgáltatás használatát teszi lehetővé az Azure Maps natív módon, ha a hely intelligencia üzembe helyezhető a közös térinformatikai matematikai számítások, beleértve a szolgáltatások, mint a legközelebbi pont, nagy kör távolság és pufferek. További információ a szolgáltatás és a különféle szolgáltatások kapcsolatban lásd: a [API-dokumentáció](https://docs.microsoft.com/rest/api/maps/spatial).

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
* Forgalom incidensek - naprakész képet forgalmi dugókról és incidensek a közúti hálózati körül.

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

Az Azure Maps API jelenleg az összes országban és régióban kivételével az alábbi régiókban érhető el:

* Argentína
* Kína
* India
* Marokkó
* Pakisztán
* Dél-Korea

Győződjön meg arról, hogy az aktuális IP-címe helye nem a nem támogatott országok/régiók fenti egyikében.

## <a name="next-steps"></a>További lépések

További információk az Azure Maps új szolgáltatásairól:

> [!div class="nextstepaction"]
> [Útvonalmátrix, izokrón térképek, IP-keresés és további szolgáltatások](https://azure.microsoft.com/blog/route-matrix-isochrones-ip-lookup-and-more-added-to-azure-maps/)

Próbálja ki az Azure Maps kipróbálunk egy mintaalkalmazást:

> [!div class="nextstepaction"]
> [Rövid útmutató: Webes alkalmazás létrehozása](quick-demo-map-app.md)
