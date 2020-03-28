---
title: 'Oktatóanyag: Webszolgáltatások áttelepítése a Google Térképről | Microsoft Azure Maps'
description: Webes szolgáltatások áttelepítése a Google Térképről a Microsoft Azure Maps szolgáltatásba.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: d2f25f2b786686b8af9bad4ea8ce3c8aea9b589f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371465"
---
# <a name="migrate-web-service-from-google-maps"></a>Webszolgáltatás áttelepítése a Google Térképről

Mind az Azure, mind a Google Térkép hozzáférést biztosít a térbeli API-khoz a REST webszolgáltatásokon keresztül. Ezeknek a platformoknak az API-felületei hasonló funkciókat látnak el. De mindegyik különböző elnevezési konvenciókat és válaszobjektumokat használ.

A táblázat az Azure Maps szolgáltatás API-k, amelyek hasonló funkciókkal rendelkeznek, mint a felsorolt Google Maps szolgáltatás API-k.

| Google Maps szolgáltatás API | Azure Maps szolgáltatás API-ja                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Irányban              | [Útválasztás](https://docs.microsoft.com/rest/api/maps/route)                                     |
| Távolságmátrix         | [Útvonalmátrix](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)       |
| Geokódolás               | [Keresés](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Helyek keresése           | [Keresés](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Automatikus kiegészítés helye      | [Keresés](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Illesztés az úthoz            | Lásd: [Útvonalak és útvonaltervek számítása](#calculate-routes-and-directions) szakasz.            |
| Sebességhatárok            | Lásd: [Koordináta-szakasz geokódjának visszakódolása.](#reverse-geocode-a-coordinate)                  |
| Statikus térkép              | [Megjelenítés](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                       |
| Időzóna               | [Időzóna](https://docs.microsoft.com/rest/api/maps/timezone)                              |

A következő szolgáltatás API-k jelenleg nem érhetők el az Azure Mapsben:

- Magasság
- Földrajzi hely
- Helyek részletei és fényképei – Telefonszámok és webhely URL-címe elérhető az Azure Maps keresési API-ban.
- Térkép URL-címei
- Legközelebbi utak – Ez a web SDK használatával érhető el, ahogy [az itt](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic
)látható, de jelenleg nem érhető el szolgáltatásként.
- Statikus utcakép

Az Azure Maps számos további REST-webszolgáltatást is kínál, amelyek érdekesek lehetnek:

- [Térbeli műveletek](https://docs.microsoft.com/rest/api/maps/spatial): Összetett térbeli számítások és műveletek, például geofencing kiszervezését egy szolgáltatásba.
- [Forgalom:](https://docs.microsoft.com/rest/api/maps/traffic)Valós idejű forgalom- és incidensadatok elérése.

## <a name="geocoding-addresses"></a>Geokódolási címek

A geokódolás a cím koordinátává alakításának folyamata. Például az "1 Microsoft way, Redmond, WA" hosszúságra konvertálódik: -122.1298, szélesség: 47.64005. Ezután a koordináták különböző célokra használhatók, például egy jelölő középre helyezése a térképen.

Az Azure Maps számos módszert kínál a geokódolási címekhez:

- [**Szabadformátumú cím geokódolás:**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)Adjon meg egy címkarakterláncot, és azonnal dolgozza fel a kérelmet. Az "1 Microsoft way, Redmond, WA" egy példa egyetlen címkarakterláncra. Ez az API ajánlott, ha az egyes címek gyors geokódolására van szükség.
- [**Strukturált cím geokódolás:**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured)Adja meg egyetlen cím részeit, például az utcanevet, a várost, az országot és az irányítószámot, és azonnal feldolgozza a kérelmet. Ez az API akkor ajánlott, ha gyorsan geokódolnia kell az egyes címeket, és az adatok már az egyes címrészekbe vannak elemezve.
- [**Kötegelt cím geokódolása**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): Hozzon létre egy legfeljebb 10 000 címet tartalmazó kérelmet, és dolgozza fel őket egy adott időszakban. Az összes cím lesz geocoded párhuzamosan a szerveren, és ha befejeződött a teljes eredményhalmaz letölthető. Ez nagy adatkészletek geokódolásához ajánlott.
- [**Fuzzy search**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): Ez az API egyesíti a cím geokódolását az érdeklődési pont keresésével. Ez az API egy szabad formátumú karakterláncot vesz fel. Ez a karakterlánc lehet cím, hely, tájékozódási pont, érdekes pont vagy érdekes pont kategória. Ez az API közel valós időben dolgozza fel a kérelmet. Ez az API olyan alkalmazásokhoz ajánlott, ahol a felhasználók ugyanabban a szövegdobozban keresnek címeket vagy érdekes helyeket.
- [**Fuzzy batch search**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Hozzon létre egy kérelmet, amely legfeljebb 10.000 címek, helyek, tereptárgyak, vagy érdekes pont, és azokat feldolgozni egy ideig. Az összes adat feldolgozása párhuzamosan történik a szerveren, és amikor elkészült a teljes eredményhalmaz letölthető.

Az alábbi táblázat összeveti a Google Maps API-paramétereket az Azure Maps összehasonlítható API-paramétereivel.

| Google Maps API paraméter | Összehasonlítható Azure Maps API-paraméter  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` és `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality`- város / város<br/>`municipalitySubdivision`– szomszédság, sub / szuper város<br/>`countrySubdivision`- állam vagy tartomány<br/>`countrySecondarySubdivision`- megye<br/>`countryTertiarySubdivision`- kerület<br/>`countryCode`- kétbetűs országkód |
| `key`                       | `subscription-key`– Lásd még: az [Azure Maps-alapú hitelesítés](azure-maps-authentication.md) dokumentációja. |
| `language`                  | `language`– Lásd a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `region`                    | `countrySet`                       |

A keresési szolgáltatás használatára vonatkozó példát [itt](how-to-search-for-address.md)dokumentálja . Ügyeljen arra, hogy tekintse át [a kereséssel kapcsolatos gyakorlati tanácsokat.](how-to-use-best-practices-for-search.md)

> [!TIP]
> A szabad formátumú cím geokódolásés fuzzy keresési API-k `&typeahead=true` automatikus kiegészítési módban is használhatók a kérelem URL-címének hozzáadásával. Ez megmondja a kiszolgálónak, hogy a bemeneti szöveg valószínűleg részleges, és a keresés prediktív módba lép.

## <a name="reverse-geocode-a-coordinate"></a>Koordináta-kód sztornírozása

A fordított geokódolás a földrajzi koordináták hozzávetőleges címké alakításának folyamata. Koordinátái a "hosszúság: -122.1298, szélesség: 47.64005" konvertálni "1 Microsoft módon, Redmond, WA".

Az Azure Maps számos fordított geokódolási módszert kínál:

- [**Címfordított geokódoló**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse): Adjon meg egyetlen földrajzi koordinátát a koordinátának megfelelő hozzávetőleges cím leérkezéséhez. A kérést közel valós időben dolgozza fel.
- [**Keresztutca fordított geokódoló**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): Adjon meg egyetlen földrajzi koordinátát a közeli utcaközi információk lekéréséhez és a kérés azonnali feldolgozásához. Például a következő keresztutcákat kaphatja az 1st Ave és a Main St.
- [**Kötegcím-sztornírozó geokódoló:**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview)Hozzon létre egy kérelmet, amely legfeljebb 10 000 koordinátákat tartalmaz, és azokat egy adott időszakban feldolgozza. Minden adat feldolgozása párhuzamosan történik a kiszolgálón. Amikor a kérelem befejeződik, letöltheti az eredmények teljes készletét.

Ez a táblázat összeveti a Google Maps API-paramétereket az Azure Maps összehasonlítható API-paramétereivel.

| Google Maps API paraméter   | Összehasonlítható Azure Maps API-paraméter   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key`– Lásd még: az [Azure Maps-alapú hitelesítés](azure-maps-authentication.md) dokumentációja. |
| `language`                  | `language`– Lásd a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `latlng`                    | `query`  |
| `location_type`             | *N/a*     |
| `result_type`               | `entityType`    |

Tekintse át [a kereséssel kapcsolatos gyakorlati tanácsokat.](how-to-use-best-practices-for-search.md)

Az Azure Maps fordított geokódolási API rendelkezik néhány további funkcióval, amelyek nem érhetők el a Google Térképen. Ezek a funkciók hasznosak lehetnek az alkalmazásba való integráláshoz az alkalmazás áttelepítésekekénél:

- Sebességkorlátozási adatok beolvasása
- Az úthasználati információk letöltése: helyi út, artériás, korlátozott hozzáférés, rámpa és így tovább
- Az utca azon oldalának beolvasása, ahol a koordináta található

## <a name="search-for-points-of-interest"></a>Hasznos helyek keresése

Az érdekes helyek adatai a Helyek keresés API-val kereshetők a Google Térképen. Ez az API három különböző módot kínál az érdekes helyek keresésére:

- **Hely keresése szövegből:** A neve, címe vagy telefonszáma alapján keres egy érdekes pontot.
- **Közeli keresés:** Olyan érdeklődési pontokat keres, amelyek egy adott helyen belül vannak.
- **Szöveges keresés:** Helyek keresése szabad formátumú szöveg használatával, amely tartalmazza az érdekes pontot és a helyadatokat. Például a "pizza New Yorkban" vagy "éttermek a fő utca közelében".

Az Azure Maps számos keresési API-t biztosít az érdekes helyekhez:

- [**ÉP keresés**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): Érdeklődési pontok keresése név szerint. Például "Starbucks".
- [**ÉP kategória keresés**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): Érdeklődési pontok keresése kategóriák szerint. Például "étterem".
- [**Közeli keresés**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): Olyan érdeklődési pontokat keres, amelyek egy adott helyen belül vannak.
- [**Fuzzy search**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): Ez az API egyesíti a cím geokódolását az érdeklődési pont keresésével. Ez az API egy szabad formátumú karakterláncot vesz fel, amely lehet cím, hely, tájékozódási pont, érdekes pont vagy érdekes pont kategória. A kérést közel valós időben dolgozza fel. Ez az API olyan alkalmazásokhoz ajánlott, ahol a felhasználók ugyanabban a szövegdobozban keresnek címeket vagy érdekes helyeket.
- [**Keresés a geometrián belül:**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)Olyan érdeklődési pontok keresése, amelyek egy adott geometrián belül vannak. Például keressen egy érdekes pontot egy sokszögen belül.
- [**Keresés az útvonal mentén:**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute)Érdeklődési pontok keresése, amelyek egy adott útvonalútvonal mentén vannak.
- [**Fuzzy batch search**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Akár 10 000 címet, helyet, tereptárgyat vagy érdeklődési pontot tartalmazó kérelem létrehozása. A kérelem feldolgozása egy adott időszakban. Minden adat feldolgozása párhuzamosan történik a kiszolgálón. Amikor a kérelem feldolgozása befejeződik, letöltheti a teljes eredményhalmazt.

Jelenleg az Azure Maps nem rendelkezik hasonló API-val a Google Térkép Szöveges keresési API-jával.

> [!TIP]
> Az ÉP-keresés, az ÉP-kategória keresés és az intelligens keresési API-k automatikus kiegészítési módban használhatók a kérelem URL-címéhez való hozzáadással. `&typeahead=true` Ezzel megmondja a kiszolgálónak, hogy a bemeneti szöveg valószínűleg részleges. Az API prediktív módban végzi a keresést.

Tekintse át a [keresési dokumentációval kapcsolatos gyakorlati tanácsokat.](how-to-use-best-practices-for-search.md)

### <a name="find-place-from-text"></a>Hely keresése szövegből

Az Azure Maps [POI-keresés](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) és a [Fuzzy keresés](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) segítségével név vagy cím alapján kereshet érdeklődési pontokat.

A tábla összeveti a Google Maps API-paramétereket a hasonló Azure Maps API-paraméterekkel.

| Google Maps API paraméter | Összehasonlítható Azure Maps API-paraméter |
|---------------------------|-------------------------------------|
| `fields`                  | *N/a*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N/a*                               |
| `key`                     | `subscription-key`– Lásd még: az [Azure Maps-alapú hitelesítés](azure-maps-authentication.md) dokumentációja. |
| `language`                | `language`– Lásd a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `locationbias`            | `lat`, `lon` és`radius`<br/>`topLeft` és `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Közeli keresés

A [Közeli keresési](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) API-val lekérheti a közeli érdeklődési pontokat az Azure Mapsben.

A táblázat a Google Maps API-paramétereket mutatja a hasonló Azure Maps API-paraméterekkel.

| Google Maps API paraméter | Összehasonlítható Azure Maps API-paraméter  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key`– Lásd még: az [Azure Maps-alapú hitelesítés](azure-maps-authentication.md) dokumentációja. |
| `keyword`                   | `categorySet` és `brandSet`        |
| `language`                  | `language`– Lásd a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `location`                  | `lat` és `lon`                     |
| `maxprice`                  | *N/a*                               |
| `minprice`                  | *N/a*                               |
| `name`                      | `categorySet` és `brandSet`        |
| `opennow`                   | *N/a*                               |
| `pagetoken`                 | `ofs` és `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *N/a*                               |
| `type`                      | `categorySet –`Tekintse meg a [támogatott keresési kategóriák dokumentációját.](supported-search-categories.md)   |

## <a name="calculate-routes-and-directions"></a>Útvonalak és útvonaltervek számítása

Útvonalak és útvonaltervek kiszámítása az Azure Maps használatával. Az Azure Maps számos olyan funkcióval rendelkezik, mint a Google Térkép útválasztási szolgáltatása, például:

- Érkezési és indulási időpontok.
- Valós idejű és prediktív alapú közlekedési útvonalak.
- Különböző közlekedési módok. Mint például, vezetés, gyaloglás, kerékpározás.

> [!NOTE]
> Az Azure Maps megköveteli, hogy minden útpont koordinátákat. A címeket először geokódolni kell.

Az Azure Maps útválasztási szolgáltatás a következő API-kat biztosítja az útvonalak kiszámításához:

- [**Útvonal kiszámítása**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): Útvonal kiszámítása, és a kérelem feldolgozása azonnal. Ez az API támogatja a GET és a POST kérelmeket is. Post kérelmek ajánlott megadásakor nagy számú útpontok, vagy ha sok az útvonal-beállítások annak biztosítására, hogy az URL-kérelem nem lesz túl hosszú, és problémákat okozhat. Az Azure Maps post route direction egy lehetőség, hogy több ezer [támogató pontokat,](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints) és használja őket, hogy újra egy logikai útvonal útvonal közöttük (snap az úthoz). 
- [**Kötegelt útvonal:**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview)Hozzon létre egy kérelmet, amely legfeljebb 1000 útvonalkérelmet tartalmaz, és dolgozza fel őket egy adott időszakban. Az összes adat feldolgozása párhuzamosan történik a szerveren, és amikor elkészült a teljes eredményhalmaz letölthető.
- [**Mobilitási szolgáltatások**](https://docs.microsoft.com/rest/api/maps/mobility): Az útvonalak és irányok kiszámítása tömegközlekedéssel.

A tábla összeveti a Google Maps API-paramétereket az Azure Maps összehasonlítható API-paramétereivel.

| Google Maps API paraméter    | Összehasonlítható Azure Maps API-paraméter  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query`– koordináták formátumban`"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key`– Lásd még: az [Azure Maps-alapú hitelesítés](azure-maps-authentication.md) dokumentációja. |
| `language`                     | `language`– Lásd a [támogatott nyelvek](supported-languages.md) dokumentációját.   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *N/A* – Ez a funkció geokódolással kapcsolatos. Használja a *countrySet* paramétert az Azure Maps geokódolási API használatakor.  |
| `traffic_model`               | *N/A* – Csak azt adhatja meg, hogy a forgalmi adatokat kell-e használni a *forgalmi* paraméterrel. |
| `transit_mode`                | Lásd: [mobilitási szolgáltatások dokumentációja](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | Lásd: [mobilitási szolgáltatások dokumentációja](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | *N/A* – Az Azure Maps csak a metrikarendszert használja.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Alapértelmezés szerint az Azure Maps útvonal API csak egy összefoglalót ad vissza. Visszaadja az útvonal távolságát és idejét, valamint koordinátáit. A `instructionsType` paraméter segítségével részletes utasításokat kérhet be. És használja `routeRepresentation` a paramétert az összefoglaló és az útvonal útvonalának kiszűréséhez.

Az Azure Maps útválasztási API további funkciókkal rendelkezik, amelyek nem érhetők el a Google Térképen. Az alkalmazás áttelepítésekor érdemes lehet használni ezeket a funkciókat, és hasznosnak találhatja őket.

- Az útvonaltípus támogatása: legrövidebb, leggyorsabb, trilling és leginkább üzemanyag-hatékony.
- További utazási módok támogatása: busz, motorkerékpár, taxi, teherautó és kisteherautó.
- 150 útpont támogatása.
- Egyetlen kérelemben több utazási időt is kiszámít; történelmi forgalom, élő forgalom, nincs forgalom.
- Kerülje a további úttípusok: telekocsi utak, burkolatlan utak, már használt utak.
- Adja meg az elkerülendő egyéni területeket.
- Korlátozza a magasságot, amelyet az útvonal felemelkedhet.
- A motor specifikációin alapuló útvonal. Számítsa ki az égési vagy elektromos járművek útvonalait a motor specifikációi, valamint a fennmaradó üzemanyag vagy töltés alapján.
- A haszongépjármű útvonalának paramétereinek támogatása. Mint például a jármű méretei, súlya, a fejsze száma és a rakomány típusa.
- Adja meg a jármű maximális sebességét.

Ezenkívül az Azure Maps útvonalszolgáltatása támogatja a [irányítható tartományok kiszámítását.](https://docs.microsoft.com/rest/api/maps/route/getrouterange) A irányítható tartományok számítása iochronoknak is ismert. Ez azt jelenti, hogy egy olyan területet lefedő sokszöget hozlétre, amely egy kiindulási ponttól bármely irányba eljárható. Minden egy meghatározott ideig vagy mennyiségű üzemanyag vagy töltés.

Tekintse át az [útválasztási dokumentációval kapcsolatos gyakorlati tanácsokat.](how-to-use-best-practices-for-routing.md)

## <a name="retrieve-a-map-image"></a>Térképkép lekérése

Az Azure Maps api-t biztosít a statikus térképképek adatátratra vonatkozó rendereléséhez. Az Azure Maps [térképes leképezési](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) API-ja összehasonlítható a Google Térkép statikus térképAPI-jével.

> [!NOTE]
> Az Azure Maps megköveteli a központ, az összes marker, és az elérési út helyét, hogy koordinátákat a "hosszúsági, szélességi" formátumban. Míg a Google Térkép a "szélességi, hosszúsági" formátumot használja. A címeket először geokódolni kell.

A tábla összeveti a Google Maps API-paramétereket az Azure Maps összehasonlítható API-paramétereivel.

| Google Maps API paraméter | Összehasonlítható Azure Maps API-paraméter  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format`– az URL elérési útjának részeként adható meg. Jelenleg csak a PNG támogatott. |
| `key`                       | `subscription-key`– Lásd még: az [Azure Maps-alapú hitelesítés](azure-maps-authentication.md) dokumentációja. |
| `language`                  | `language`– Lásd a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `maptype`                   | `layer`és `style` – Lásd: [Támogatott térképstílusok](supported-map-styles.md) dokumentációja. |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *N/A* – Ez egy geokódolással kapcsolatos funkció. Használja `countrySet` a paramétert az Azure Maps geokódolási API használatakor.  |
| `scale`                     | *N/a*                              |
| `size`                      | `width`és `height` – lehet akár 8192x8192 méretű. |
| `style`                     | *N/a*                              |
| `visible`                   | *N/a*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> Az Azure Maps csemperendszerben a csempék kétszer akkorák, mint a Google Térképben használt térképcsempék. Így az Azure Maps nagyítási szintértéke egy-egy nagyítási szinttel közelebb fog megjelenni az Azure Mapsben a Google Térképhez képest. A különbség kompenzálása érdekében a nagyítási szint csökkenése az áttelepíti a kérelmeket.

További információt az [Útmutató útmutató a térképkép-leképezési API-n](how-to-render-custom-data.md)talál.

Az Azure Maps renderelési szolgáltatása amellett, hogy statikus térképképet hozhat létre, lehetővé teszi a térképcsempék közvetlen elérését raszteres (PNG) és vektor formátumban:

- [**Térképcsempe**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): Raszter (PNG) és vektormozaikok lekérése az alaptérképekhez (utak, határok, háttér).
- [**Térkép képcsempe:**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)Lekérés légi és műholdas képmozaikok.

> [!TIP]
> Sok Google Maps alkalmazás, ahol néhány évvel ezelőtt az interaktív térképélményről statikus térképképekre váltott. Ez költségmegtakarítási módszerként történt. Az Azure Mapsben általában költséghatékonyabb az interaktív térképvezérlő használata a webes SDK-ban. Az interaktív térképvezérlő díjak a csempebetöltések száma alapján. Az Azure Maps térképcsempéi nagyok. Gyakran csak néhány csempe kell ahhoz, hogy ugyanazt a térképnézetet hozza létre újra, mint egy statikus térkép. A térképcsempéket a böngésző automatikusan gyorsítótárazza. Így az interaktív térképvezérlő gyakran a tranzakció töredékét hozza létre statikus térképnézet reprodukálásakor. Pásztázás és nagyítás betölti több csempe; a térképvezérlőben azonban vannak lehetőségek a viselkedés letiltására. Az interaktív térképvezérlő sokkal több vizualizációs lehetőséget is biztosít, mint a statikus térképszolgáltatások.

### <a name="marker-url-parameter-format-comparison"></a>Jelölő URL-paraméterformátum-összehasonlítása

**Előtte: Google Térkép**

Jelölők hozzáadása `markers` az URL-címben lévő paraméter használatával. A `markers` paraméter egy stílust és egy listát vesz fel a térképen renderelendő helyekről, az adott stílussal az alábbi módon:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

További stílusok hozzáadásához `markers` használja a paramétereket az URL-címhez más stílussal és helykészlettel.

Adja meg a jelölőhelyeket a "szélesség,hosszúság" formátummal.

Jelölőstílusok hozzáadása `optionName:value` a formátumhoz, több stílust elválasztva cső (\|)\|karakterekkel, mint ez az "optionName1:value1 optionName2:value2". Vegye figyelembe, hogy a beállításnevek és értékek kettősponttal (:) vannak elválasztva. A Google Térképen a stílusbeállítások alábbi neveivel jelölőket használhat:

- `color`– Az alapértelmezett jelölőikon színe. Lehet 24 bites hexaxi`0xrrggbb`szín ( ) vagy az alábbi értékek egyike; `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label`– Egyetlen nagyalfanumerikus karakter, amely az ikon tetején jelenik meg.
- `size`- A jelölő mérete. Lehet `tiny`, `mid`vagy `small`.

Használja az egyéni ikonok alábbi stílusbeállításainak nevét a Google Térképen:

- `anchor`– Megadja, hogyan kell az ikonképet a koordinátához igazítani. Lehet képpont (x,y) érték vagy az alábbi értékek egyike; `top`, `bottom` `left`, `right` `center`, `topleft` `topright`, `bottomleft`, `bottomright`, , , vagy .
- `icon`– Az ikonképre mutató URL.– Az ikonképre mutató URL.

Például adjunk hozzá egy piros, közepes méretű jelölőt a térképhez a hosszúsági szinten: -110, szélesség: 45:

```
&markers=color:red|size:mid|45,-110
```

<center>

![Google Térkép jelölő](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**Utána: Azure Maps**

Jelölők hozzáadása statikus térképképhez az `pins` URL-ben lévő paraméter megadásával. A Google Térképhez hasonlóan adjon meg egy stílust és egy helylistát a paraméterben. A `pins` paraméter többször is megadható a különböző stílusú jelölők támogatásához.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

További stílusok használatához adjon `pins` hozzá további paramétereket az URL-címhez más stílussal és helykészlettel.

Az Azure Mapsben a pin helyét a "hosszúsági szélesség" formátumban kell lennie. A Google Térkép "szélességi, hosszúsági" formátumot használ. A szóköz, nem vessző, elválasztja a hosszúsági és szélességi az Azure Maps formátumban.

A `iconType` megadott típusú pin létrehozni. A következő értékeket veheti fel:

- `default`– Az alapértelmezett pin ikon.
- `none`– Nem jelenik meg ikon, csak a címkék jelennek meg.
- `custom`– Egyéni ikont kell használni. Az ikonképre mutató URL-cím a `pins` pin helyadatai nak lejárta után adható hozzá a paraméter végéhez.
- `{udid}`– Egyedi adatazonosító (UDID) az Azure Maps Data Storage platformon tárolt ikonhoz.

Pin-stílusok hozzáadása `optionNameValue` a formátumhoz. Több stílus tág\|( ) karakterekkel való elkülönítése. Például: `iconType|optionName1Value1|optionName2Value2`. A beállításnevek és értékek nincsenek elválasztva. A stílusjelölők stílusneveinek használata a következő stílusbeállításokhoz:

- `al`– Megadja a jelölő opacitását (alfa). Válasszon egy 0 és 1 közötti számot.
- `an`– Megadja a pin horgonyt. Adja meg az X és y képpontértékeket "x y" formátumban.
- `co`– A tű színe. Adjon meg egy 24 bites `000000` `FFFFFF`hexaxi színt: a.
- `la`– Megadja a felirathorgonyt. Adja meg az X és y képpontértékeket "x y" formátumban.
- `lc`– A címke színe. Adjon meg egy 24 bites `000000` `FFFFFF`hexaxi színt: a.
- `ls`– A címke mérete képpontban. Válasszon 0-nál nagyobb számot.
- `ro`– Az ikon elforgatásához fokban lévő érték. Válasszon egy számot -360 és 360 között.
- `sc`– A pin ikon léptékértéke. Válasszon 0-nál nagyobb számot.

Adja meg a címkeértékeket az egyes pinhelyeken. Ez a megközelítés hatékonyabb, mint egyetlen címkeérték alkalmazása a helyek listájában szereplő összes jelölőre. A címkeértéke több karakterből álló karakterlánc is lehet. A karakterláncot egyszeres idézőjelekkel tekerje körbe, hogy ne legyen összetéveszthető stílus- vagy helyértékként.

Adjunk hozzá egy piros`FF0000`( ) alapértelmezett ikont, a "Space Needle" felirattal, az alábbi (15 50) felirattal. Az ikon hosszúsági fokon áll: -122.349300, szélesség: 47.620180:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![Az Azure Maps jelölője](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

Adjon hozzá három gombostűt az "1", "2" és "3" felirattal:

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Az Azure Maps több jelölője](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>Elérési út URL-paraméterformátumának összehasonlítása

**Előtte: Google Térkép**

Adjon vonalakat és sokszöget egy `path` statikus térképképhez az URL-cím paraméterének használatával. A `path` paraméter egy stílust és egy listát vesz fel a térképen renderelendő helyekről, az alábbiak szerint:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

További stílusokat úgy `path` használhat, hogy további paramétereket ad az URL-címhez más stílussal és helykészlettel.

Az elérési út helyei a `latitude1,longitude1|latitude2,longitude2|…` formátummal vannak megadva. Az elérési utak kódolhatók vagy pontok címeit tartalmazhatják.

Görbestílusok hozzáadása `optionName:value` a formátumhoz, több stílus\|elválasztása a cső ( ) karakterekkel. És válassza el a beállításneveket és értékeket kettősponttal (:). Így: `optionName1:value1|optionName2:value2`. A Google Térképen a következő stílusbeállítások nevei használhatók a görbék stílusára:

- `color`– A görbe vagy a sokszög körvonalának színe. Lehet 24 bites hexaxi`0xrrggbb`szín ( ), 32`0xrrggbbbaa`bites hexaxi szín ( ) vagy az alábbi értékek egyike: fekete, barna, zöld, lila, sárga, kék, szürke, narancs, piros, fehér.
- `fillColor`– Az a szín, amely a görbeterületet (sokszög) tölti ki. Lehet 24 bites hexaxi`0xrrggbb`szín ( ), 32`0xrrggbbbaa`bites hexaxi szín ( ) vagy az alábbi értékek egyike: fekete, barna, zöld, lila, sárga, kék, szürke, narancs, piros, fehér.
- `geodesic`– Azt jelzi, hogy az útvonalnak a föld görbületét követő vonalnak kell-e lennie.
- `weight`– A görbevonal vastagsága képpontban.

Adjon hozzá egy piros vonalopacitást és képpontvastagságot a koordináták közötti térképhez az URL-paraméterben. Az alábbi példában a vonal 50%-os opacitású és vastagsága négy képpont. A koordináták hosszúsági fok: -110, szélesség: 45 és hosszúság: -100, szélesség: 50.

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![Google Térkép vonallánc](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**Utána: Azure Maps**

Vonalak és sokszögek hozzáadása statikus térképképhez `path` az URL-ben lévő paraméter megadásával. A Google Térképhez hasonlóan adjon meg egy stílust és egy helylistát ebben a paraméterben. Adja `path` meg többször a paramétert több kör, vonal és sokszög megjelenítéséhez különböző stílusokkal.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

Az elérési út helyeinek használata esetén az Azure Maps megköveteli, hogy a koordináták "hosszúsági szélesség" formátumban legyenek. A Google Térkép "szélességi, hosszúsági" formátumot használ. A szóköz, nem vessző, elválasztja a hosszúsági és szélességi az Azure Maps formátumban. Az Azure Maps nem támogatja a kódolt elérési utak at vagy a pontok címét. Töltsön fel nagyobb adatkészleteket GeoJSON-fájlként az Itt [dokumentált](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage)Azure Maps adattárolási API-ba.

Görbestílusok hozzáadása `optionNameValue` a formátumhoz. Több stílus elválasztása cső\|( `optionName1Value1|optionName2Value2`) karakterekkel, így . A beállításnevek és értékek nincsenek elválasztva. Az Azure Maps alábbi stílusbeállítási neveivel stíluselérési utakat használhat:

- `fa`- A kitöltés színe opacitás (alfa) használt renderelés sokszögek. Válasszon egy 0 és 1 közötti számot.
- `fc`- A sokszög területének renderelésére használt kitöltési szín.
- `la`– A vonalak renderelésekor használt vonalszín-opacitás (alfa) és a sokszögek körvonala. Válasszon egy 0 és 1 közötti számot.
- `lc`– A vonalak rendereléséhez használt vonalszín és a sokszögek körvonala.
- `lw`– A vonal szélessége képpontban.
- `ra`– Körök sugarát ad meg méterben.

Adjon hozzá egy piros vonal opacitást és képpontvastagságot a koordináták között az URL-paraméterben. Az alábbi példában a vonal 50%-os opacitású és vastagsága négy képpont. A koordináták a következő értékeket tartalmazják: -110, szélesség: 45 és hosszúság: -100, szélesség: 50.

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Az Azure Maps vonallánc](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>Távolságmátrix kiszámítása

Az Azure Maps biztosítja a távolságmátrix API-t. Ezzel az API-val kiszámíthatja az utazási időket és a távolságokat egy sor hely között, egy távolságmátrixsegítségével. Ez hasonló a távolság mátrix API-t a Google Maps.

- [**Útvonalmátrix**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): Aszinkron módon kiszámítja az utazási időket és távolságokat az eredeti és úti célok egy készletére. Kérésenként legfeljebb 700 cellát támogat. Ez az eredeti helyek számának és a célállomások számának szorzata. Ezt a megkötést szem előtt tartva példák a lehetséges mátrixdimenziókra: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> A távolságmátrix API-ra vonatkozó kérés csak postai kérelemmel érhető el a kérelem törzsében lévő kiindulási és céladatokkal. Emellett az Azure Maps megköveteli, hogy minden származási és célkoordináták. A címeket először geokódolni kell.

Ez a táblázat összeveti a Google Maps API-paramétereket a hasonló Azure Maps API-paraméterekkel.

| Google Maps API paraméter      | Összehasonlítható Azure Maps API-paraméter  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination`– adja meg a POST kérelem törzsében GeoJSON. |
| `key`                          | `subscription-key`– Lásd még: az [Azure Maps-alapú hitelesítés](azure-maps-authentication.md) dokumentációja. |
| `language`                     | `language`– Lásd a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins`– adja meg a POST kérelem törzsében GeoJSON.  |
| `region`                       | *N/A* – Ez a funkció geokódolással kapcsolatos. Használja `countrySet` a paramétert az Azure Maps geokódolási API használatakor. |
| `traffic_model`                | *N/A* – Csak azt adhatja meg, `traffic` hogy a forgalmi adatokat kell-e használni a paraméterrel. |
| `transit_mode`                 | *N/A* – A tranzitalapú távolságmátrixok jelenleg nem támogatottak.  |
| `transit_routing_preference`   | *N/A* – A tranzitalapú távolságmátrixok jelenleg nem támogatottak.  |
| `units`                        | *N/A* – Az Azure Maps csak a metrikarendszert használja. |

> [!TIP]
> Az Azure Maps útválasztási API-ban elérhető összes speciális útválasztási lehetőség támogatott az Azure Maps távolságmátrix API-ban. A speciális útválasztási lehetőségek a következők: teherautó-útválasztás, motorspecifikációk és így tovább.

Tekintse át az [útválasztási dokumentációval kapcsolatos gyakorlati tanácsokat.](how-to-use-best-practices-for-routing.md)

## <a name="get-a-time-zone"></a>Időzóna betöltése

Az Azure Maps api-t biztosít a koordináta időzónájának beolvasására. Az Azure Maps időzóna API-ja összehasonlítható a Google Térkép időzóna API-jével:

- [**Időzóna koordináta szerint**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): Adjon meg egy koordinátát, és fogadja meg a koordináta időzónájának részleteit.

Ez a táblázat összeveti a Google Maps API-paramétereket az Azure Maps összehasonlítható API-paramétereivel.

| Google Maps API paraméter | Összehasonlítható Azure Maps API-paraméter   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key`– Lásd még: az [Azure Maps-alapú hitelesítés](azure-maps-authentication.md) dokumentációja.       |
| `language`                  | `language`– Lásd a [támogatott nyelvek](supported-languages.md) dokumentációját.    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Az API mellett az Azure Maps számos időzóna API-t biztosít. Ezek az API-k az időidőket az időzóna neve vagy azonosítói alapján alakítják át:

- [**Időzóna azonosító szerint**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): A megadott IANA időzóna-azonosító aktuális, előzmény- és jövőbeli időzóna-adatait adja eredményül.
- [**Időzóna Enum IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): Az IANA időzóna-azonosítók teljes listáját adja eredményül. Az IANA szolgáltatás frissítései egy napon belül megjelennek a rendszerben.
- [**Időzóna Enum Windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): A Windows időzóna-azonosítók teljes listáját adja vissza.
- [**Időzóna IANA-verzió**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): Az Azure Maps által használt aktuális IANA-verziószámot adja eredményül.
- [**Időzóna A Windows rendszernek az IANA-nak**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): Egy megfelelő IANA-azonosítót ad vissza, amely érvényes Windows időzóna-azonosítót kap. Egyetlen Windows-azonosítóhoz több IANA-azonosító is visszaadható.

## <a name="client-libraries"></a>Ügyfélkódtárak

Az Azure Maps ügyfélkönyvtárakat biztosít a következő programozási nyelvekhez:

- JavaScript, TypeScript, Node.js – [dokumentációS](how-to-use-services-module.md) \| [NPM csomag](https://www.npmjs.com/package/azure-maps-rest)

Ezek a nyílt forráskódú ügyfélkódtárak más programozási nyelvekhez tartoznak:

- .NET Standard 2.0 – [GitHub projekt](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet csomag](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>További források

Az alábbiakban az Azure Maps REST-szolgáltatások további dokumentációját és erőforrásait tartalmazza.

- [Ajánlott keresési eljárások](how-to-use-best-practices-for-search.md)
- [Cím keresése](how-to-search-for-address.md)
- [Gyakorlati tanácsok az útválasztáshoz](how-to-use-best-practices-for-routing.md)
- [Az Azure Maps REST Service API referenciadokumentációja](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>További lépések

További információ az Azure Maps REST-szolgáltatásairól.

> [!div class="nextstepaction"]
> [Gyakorlati tanácsok a keresési szolgáltatás használatához](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Gyakorlati tanácsok az útválasztási szolgáltatás használatához](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [A szolgáltatási modul használata (Web SDK)](how-to-use-best-practices-for-routing.md)

> [!div class="nextstepaction"]
> [Kódminták](https://docs.microsoft.com/samples/browse/?products=azure-maps)
