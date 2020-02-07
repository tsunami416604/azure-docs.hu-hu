---
title: 'Oktatóanyag: webszolgáltatások migrálása a Google Maps szolgáltatásból | Microsoft Azure térképek'
description: Webszolgáltatások migrálása a Google Maps szolgáltatásból Microsoft Azure Maps-be.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 50bdc0722328f857279b2cbd9a6e4cee740b9df8
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048933"
---
# <a name="migrate-web-service-from-google-maps"></a>Webszolgáltatás migrálása a Google Mapsből

A Azure Maps és a Google Maps a REST-alapú webszolgáltatások segítségével biztosít hozzáférést a térbeli API-khoz. A két platform API-felülete hasonló funkciókat hajt végre, de mindegyik különböző elnevezési konvenciókat és válasz-objektumokat használ.

A következő táblázat a Azure Maps Service API-t mutatja be, amely hasonló funkciókat biztosít a Google Maps Service API-hoz.

| Google Maps szolgáltatás API | Azure Maps Service API                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Irányban              | [Útvonal](https://docs.microsoft.com/rest/api/maps/route)                               |
| Távolsági mátrix         | [Útvonal-mátrix](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) |
| Geokódolás               | [Search](https://docs.microsoft.com/rest/api/maps/search)                             |
| Helyek keresése           | [Search](https://docs.microsoft.com/rest/api/maps/search)                             |
| Automatikus kiegészítés      | [Search](https://docs.microsoft.com/rest/api/maps/search)                             |
| Statikus Térkép              | [Renderelési](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                 |
| Időzóna               | [Időzóna](https://docs.microsoft.com/rest/api/maps/timezone)                        |

A következő szolgáltatási API-k jelenleg nem érhetők el Azure Mapsban:

- Jogosultságszint
- Térinformatikai
- Elhelyezheti a részleteket, és elhelyezheti a fényképeket. A telefonszámok és a webhely URL-címe a Azure Maps Search API-ban érhető el.
- Térkép URL-címei
- Utakon. A sebességkorlátozás adatátviteli sebessége a Azure Maps útvonalon és fordított helymeghatározáshoz API-kon keresztül érhető el.
- Statikus utcai nézet

Azure Maps számos további REST-webszolgáltatással rendelkezik, amelyek érdekesek lehetnek:

- [Térbeli műveletek](https://docs.microsoft.com/rest/api/maps/spatial): összetett térbeli számítások és műveletek (például geokerítések) kiszervezése szolgáltatáshoz.
- [Forgalom](https://docs.microsoft.com/rest/api/maps/traffic): a valós idejű forgalom és az incidensek adatforgalmának elérése.

## <a name="geocoding-addresses"></a>Helymeghatározáshoz-címek

A helymeghatározáshoz a címek koordinátabe alakításának folyamata. Például a "1 Microsoft Way, Redmond, WA" a "hosszúság:-122,1298, szélesség: 47,64005" értékre konvertálva. Koordináták szükségesek a jelölők térképre vagy középpontba helyezéséhez.

A Azure Maps számos módszert biztosít a helymeghatározáshoz-címekhez:

- [**Szabad formátumú helymeghatározáshoz**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): egyetlen címnek megfelelő karakterláncot kell megadni, és azonnal fel kell dolgozni a kérést. Egyetlen karakterlánc-címe: "1 Microsoft Way, Redmond, WA". Ez a módszer akkor javasolt, ha gyorsan kell geocode az egyes címeket.
- [**Strukturált helymeghatározáshoz**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): egy adott címek részeit határozza meg, és a kérést közel valós időben dolgozza fel. A cím részei közé tartozik az utca neve, a város, az ország és az irányítószám. Ez a módszer két fő forgatókönyv esetén ajánlott. Az adatelemek már egyéni címekként vannak értelmezve. Vagy gyorsan kell geocode az egyes címeket.
- [**Batch-helymeghatározáshoz**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): hozzon létre egy legfeljebb 10 000 címet tartalmazó kérelmet, és dolgozza fel a kérelmet egy adott időszakban. A rendszer az összes címet párhuzamosan geokódolva a kiszolgálón. Ha a helymeghatározáshoz befejeződik, a teljes eredmény letölthető lesz. Ez a metódus nagyméretű adatkészletek helymeghatározáshoz ajánlott.
- [**Fuzzy keresés**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): ez az API ötvözi a helymeghatározáshoz a keresett ponttal. Ez az API egy szabad formátumú karakterláncot vesz igénybe, és közel valós időben dolgozza fel a kérést. A szabad formátumú karakterlánc lehet a címe, a hely, a tereptárgy, az érdeklődési pont vagy az érdeklődési pont kategóriája. Ez az API akkor ajánlott, ha ugyanazt a szövegmezőt használja a címek és a hasznos pontok lekérdezéséhez.
- [**Fuzzy batch-keresés**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): hozzon létre legfeljebb 10 000 címet tartalmazó kérelmet, és dolgozza fel a kérelmet egy adott időszakban. Megkérheti a helyeket, a tereptárgyak vagy az érdeklődési pontok megkeresését. A rendszer minden adatfeldolgozást a kiszolgálón és párhuzamosan fog feldolgozni. Ha elkészült, a teljes eredményhalmaz letölthető lesz.

A következő táblázat kereszthivatkozásokat hivatkozik a Google Maps API-paraméterekre a Azure Maps hasonló API-paramétereivel.

| Google Maps API-paraméter | Hasonló Azure Maps API-paraméter  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` és `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` – város/város<br/>`municipalitySubdivision` – szomszédság, Sub/Super City<br/>`countrySubdivision` – állam vagy megye<br/>`countrySecondarySubdivision` – megye<br/>`countryTertiarySubdivision` – kerület<br/>`countryCode` – kétbetűs országkód |
| `key`                       | `subscription-key` – tekintse meg a Azure Maps dokumentációjának [hitelesítését](azure-maps-authentication.md) is. |
| `language`                  | `language` – tekintse meg a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `region`                    | `countrySet`                       |

A Search szolgáltatás használatának példáját [itt](how-to-search-for-address.md)találja. Mindenképpen tekintse át [az ajánlott eljárásokat a kereséshez](how-to-use-best-practices-for-search.md).

> [!TIP]
> A szabad formátumú helymeghatározáshoz és a fuzzy keresési API-k automatikus kiegészítési módban is használhatók `&amp;typeahead=true` a kérelem URL-címéhez való hozzáadásával. Ez azt közli a kiszolgálóval, hogy a bemeneti szöveg valószínűleg részleges, és a keresés a prediktív módba lép.

## <a name="reverse-geocode-a-coordinate"></a>Koordináta fordított geocode

A fordított helymeghatározáshoz az a folyamat, amellyel a földrajzi koordinátákat egy hozzávetőleges címmé alakíthatja. Koordináták a "hosszúság:-122,1298, Latitude: 47,64005" értékre váltás "1 Microsoft Way, Redmond, WA" értékűre.

Azure Maps több fordított helymeghatározáshoz módszert biztosít:

- [**Fordított geocoder**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse): egyetlen földrajzi koordináta megadásával lekérheti a koordináta számára megfelelő hozzávetőleges címeket. A kérelem feldolgozása közel valós időben.
- [**Cross Street fordított geocoder**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): egyetlen földrajzi koordinátat határozhat meg, és információkat kérhet le a közeli Cross Streetről. A kérelem feldolgozása közel valós időben.
- [**Fordított geocoder**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): hozzon létre egy legfeljebb 10 000 koordinátákat tartalmazó kérelmet, és dolgozza fel a kérelmet egy adott időszakban. Az összes adattal párhuzamosan lesznek feldolgozva a kiszolgálón. Ha elkészült, a teljes eredmény letölthető lesz.

A következő táblázat kereszthivatkozásokat hivatkozik a Google Maps API-paraméterekre a Azure Maps hasonló API-paramétereivel.

| Google Maps API-paraméter   | Hasonló Azure Maps API-paraméter   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` – tekintse meg a Azure Maps dokumentációjának [hitelesítését](azure-maps-authentication.md) is. |
| `language`                  | `language` – tekintse meg a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `latlng`                    | `query`  |
| `location_type`             | *N/A*     |
| `result_type`               | `entityType`    |

Tekintse át [az ajánlott eljárásokat a kereséshez](how-to-use-best-practices-for-search.md).

A Azure Maps fordított helymeghatározáshoz API további funkciókkal rendelkezik, amelyek nem érhetők el a Google Maps szolgáltatásban. Ezek a funkciók hasznosak lehetnek az alkalmazással való integrációhoz, amikor az alkalmazást áttelepíti:

- Sebességkorlátozó-adatlekérdezés
- Közúti használati információk beolvasása: helyi út, artériás, korlátozott hozzáférés, rámpa stb.
- Az utca azon oldalának lekérése, amelyen a koordináta található

## <a name="search-for-points-of-interest"></a>Hasznos helyek keresése

A helyek keresési API-ját a Google Maps szolgáltatásban keresheti meg. Ez az API három különböző módszert biztosít a fontos helyek kereséséhez:

- **Hely keresése a következő szövegből:** Megkeresi az érdeklődési pontot a neve, címe vagy telefonszáma alapján.
- **Közeli keresés**: megkeresi azokat az érdeklődési pontokat, amelyek egy adott távolságon belül vannak.
- **Szöveges keresés:** Megkeresi a helyeket egy szabad formátumú szöveggel, amely az érdeklődési pont és a hely adatait is tartalmazza. Például: "pizza a New York-ban" vagy "éttermek a Main St közelében".

Azure Maps számos keresési API-t biztosít a hasznos helyek számára:

- [**POI-keresés**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): érdeklődési pontok keresése név alapján. Például: "Starbucks".
- [**POI-kategória keresése**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): az érdeklődési pontok kategória szerint kereshetők. Például: "étterem".
- [**Közeli keresés**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): megkeresi azokat az érdeklődési pontokat, amelyek egy adott távolságon belül vannak.
- [**Fuzzy keresés**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): ez az API ötvözi a helymeghatározáshoz a keresett ponttal. Ez az API egy szabad formátumú karakterláncot vesz igénybe. Egy lakcím, egy hely, egy mérföldkő, egy érdekes pont, egy érdekes kategória és így tovább. Ez az API közel valós időben tudja feldolgozni a kérelmet. Ezt az API-t akkor érdemes használni, ha a felhasználók azonos szövegmezővel keresik meg a címeket vagy az érdeklődési pontokat.
- [**Keresés a geometrián belül**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): a megadott geometrián (sokszög) belül található érdeklődési pontok keresése.
- [**Keresés az útvonal mentén**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): keressen olyan pontokat, amelyek egy adott útvonal útvonalán találhatók.
- [**Fuzzy batch-keresés**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): hozzon létre egy olyan kérést, amely legfeljebb 10 000 címet, helyet, tereptárgyat vagy érdeklődési pontot tartalmaz, és egy adott időszakban feldolgozható. Az összes adattal párhuzamosan lesznek feldolgozva a kiszolgálón. Ha a kérés befejezi a feldolgozást, töltse le az összes találati készletet.

Jelenleg Azure Maps nem rendelkezik hasonló API-val a Google Maps-beli szöveges keresési API-val.

> [!TIP]
> A POI Search API, a POI kategória keresési API és a fuzzy Search API-k mindegyike használható automatikus kiegészítés módban. `&amp;typeahead=true` hozzáadása a kérelem URL-címéhez. Ez azt közli a kiszolgálóval, hogy a bemeneti szöveg valószínűleg részleges, és a keresés a prediktív módban lesz végrehajtva.

Tekintse át [az ajánlott eljárásokat a kereséshez](how-to-use-best-practices-for-search.md).

### <a name="find-place-from-text"></a>Hely keresése a szövegből

Használja Azure Maps a [POI Search](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) API-t vagy a [fuzzy Search](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) API-t, hogy az érdeklődési pontokat név vagy címek alapján keressen.

A következő táblázat a Google Maps API paramétereit mutatja be a megfelelő Azure Maps API-paraméterekkel.

| Google Maps API-paraméter | Hasonló Azure Maps API-paraméter |
|---------------------------|-------------------------------------|
| `fields`                  | *N/A*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N/A*                               |
| `key`                     | `subscription-key` – tekintse meg a Azure Maps dokumentációjának [hitelesítését](azure-maps-authentication.md) is. |
| `language`                | `language` – tekintse meg a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `locationbias`            | `lat`, `lon` és `radius`<br/>`topLeft` és `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Közeli keresés

A Azure Maps a [közeli keresési](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) API-val lekéri a közeli nevezetességeket.

Az alábbi táblázat a Google Maps API-paramétereket Azure Maps API-paraméterekkel hivatkozik.

| Google Maps API-paraméter | Hasonló Azure Maps API-paraméter  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` – tekintse meg a Azure Maps dokumentációjának [hitelesítését](azure-maps-authentication.md) is. |
| `keyword`                   | `categorySet` és `brandSet`        |
| `language`                  | `language` – tekintse meg a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `location`                  | `lat` és `lon`                     |
| `maxprice`                  | *N/A*                               |
| `minprice`                  | *N/A*                               |
| `name`                      | `categorySet` és `brandSet`        |
| `opennow`                   | *N/A*                               |
| `pagetoken`                 | `ofs` és `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *N/A*                               |
| `type`                      | `categorySet –` tekintse meg a [támogatott keresési kategóriák](supported-search-categories.md) dokumentációját.   |

## <a name="calculate-routes-and-directions"></a>Útvonalak és irányok kiszámítása

Útvonalak és irányok kiszámítása Azure Maps használatával. Azure Maps a Google Maps útválasztási szolgáltatása számos különböző funkciót tartalmaz, például:

- érkezési és távozási idő.
- valós idejű és prediktív forgalmi útvonalak.
- Különböző szállítási módok. Például:, vezetés, gyaloglás, kerékpározás.

> [!NOTE]
> Azure Maps az összes Útpontot koordinálni kell. Először a címeket kell geokódolva.

A Azure Maps útválasztási szolgáltatás a következő API-kat biztosítja az útvonalak kiszámításához:

- [**Útvonal kiszámítása**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): az útvonal kiszámítása és a kérés feldolgozása közel valós időben. Ez az API a GET és a POST kérelmeket is támogatja. A POST kérések nagy számú útpontok megadására és számos útvonal-beállítás használatára ajánlottak. A POST használatával gondoskodhat arról, hogy az URL-cím kérése ne legyen túl hosszú, és problémákat okozzon.
- [**Batch Route**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): hozzon létre egy legfeljebb 1 000 útválasztási kérelmet tartalmazó kérelmet. A kérelmek folyamata egy adott időszakban történik. Az összes adatfolyamat párhuzamosan fut a kiszolgálón. Töltse le a készletet, amint a kérelem befejeződik, és az eredmények készen állnak.
- [**Mobilitási szolgáltatások**](https://docs.microsoft.com/rest/api/maps/mobility): útvonalak és irányok kiszámítása a nyilvános átvitel használatával.

A következő táblázat kereszthivatkozásokat hivatkozik a Google Maps API-paraméterekre a Azure Maps hasonló API-paramétereivel.

| Google Maps API-paraméter    | Hasonló Azure Maps API-paraméter  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` – koordináták formátuma `"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key` – tekintse meg a Azure Maps dokumentációjának [hitelesítését](azure-maps-authentication.md) is. |
| `language`                     | `language` – tekintse meg a [támogatott nyelvek](supported-languages.md) dokumentációját.   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *N/A* – ez a funkció helymeghatározáshoz kapcsolatos. Használja a *countrySet* paramétert a Azure Maps helymeghatározáshoz API használatakor.  |
| `traffic_model`               | *N/A* – csak azt adhatja meg, hogy a forgalmi adatokat a *Traffic* paraméterrel kell-e használni. |
| `transit_mode`                | Lásd: [mobilitási szolgáltatások dokumentációja](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | Lásd: [mobilitási szolgáltatások dokumentációja](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | *N/A* – Azure Maps csak a metrikus rendszer használatát használja.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Alapértelmezés szerint a Azure Maps Route API csak összegzést (távolságot és időpontokat) és az útvonal útvonalának koordinátáit adja vissza. Használja a `instructionsType` paramétert a lépésenkénti utasítások lekéréséhez. Használja a `routeRepresentation` paramétert az összefoglalás és az útvonal elérési útjának kiszűréséhez.

A Azure Maps Routing API számos további funkcióval rendelkezik, amelyek nem érhetők el a Google Maps-ben. Hasznos lehet ezen funkciók integrálása az alkalmazás migrálása során:

- Az útvonal típusának támogatása: legrövidebb, leggyorsabb, trilling és a legtöbb üzemanyag-takarékos.
- További utazási módok támogatása: busz, motorkerékpár, taxi, tehergépkocsi és Furgon.
- 150-es útpontok támogatása.
- Több utazási idő kiszámítása egyetlen kérelemben; korábbi forgalom, élő forgalom, forgalom nélkül.
- Kerülje a további utak típusát: telekocsi utak, nem burkolt utak, már használatban lévő utak.
- A kerülni kívánt egyéni területek meghatározása.
- Korlátozza a jogosultságszint-emelést, amelyet az útvonal felemel.
- Útvonal a motor előírásai alapján. A motor specifikációi alapján kiszámítja az égési vagy az elektromos járművek útvonalát, valamint a fennmaradó üzemanyagot vagy díjat.
- A kereskedelmi jármű útvonalának paramétereinek támogatása. Ilyenek például a járművek méretei, súlyozása, Axel-száma és a rakomány típusa.
- A jármű maximális sebességének meghatározása.

Ezen funkciók mellett a Azure Maps útvonal-szolgáltatása támogatja az [útválasztásos tartományok kiszámítását](https://docs.microsoft.com/rest/api/maps/route/getrouterange). Az átirányítható tartományok kiszámítása izokrón néven is ismert. A művelet egy sokszögtel rendelkező térség létrehozását vonja maga után. Ezután kiszámíthatja az utazást a forrás pont bármely irányában. A megadott időtartamot figyelembe véve, valamint az üzemanyag vagy a díj mértékét.

## <a name="retrieve-a-map-image"></a>Térkép rendszerképének beolvasása

A Azure Maps egy API-t biztosít a statikus Térkép képének megjelenítéséhez. A [Térkép képmegjelenítési](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) API-ját Azure Maps a Google Maps statikus Térkép API-ját hasonlítja össze.

> [!NOTE]
> Azure Maps a középpontot, az összes jelölőt és az elérési út helyét a "hosszúság, szélesség" formátumban kell koordinálni. Másfelől a Google Maps a "szélesség, hosszúság" formátumot használja. Először a címeket kell geokódolva.

A következő táblázat a Google Maps API paramétereit mutatja be a hasonló Azure Maps API-paraméterekkel.

| Google Maps API-paraméter | Hasonló Azure Maps API-paraméter  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` – az URL elérési útjának részeként van megadva. Jelenleg csak a PNG támogatott. |
| `key`                       | `subscription-key` – tekintse meg a Azure Maps dokumentációjának [hitelesítését](azure-maps-authentication.md) is. |
| `language`                  | `language` – tekintse meg a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `maptype`                   | `layer` és `style` – tekintse meg a [támogatott térképi stílusok](supported-map-styles.md) dokumentációját. |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *N/A* – ez egy helymeghatározáshoz-hez kapcsolódó szolgáltatás. Használja a `countrySet` paramétert a Azure Maps helymeghatározáshoz API használatakor.  |
| `scale`                     | *N/A*                              |
| `size`                      | `width` és `height` – mérete akár 8192x8192 is lehet. |
| `style`                     | *N/A*                              |
| `visible`                   | *N/A*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> A Azure Maps csemperendszer olyan csempét használ, amely a Google Maps-ben használt Térkép csempék méretének kétszerese. Mivel a nagyítási szint értéke egy nagyítási szint megközelíti a Azure Maps a Google Maps-hez képest. A nagyítási szint eggyel csökkentése a migrált kérelmekben. A nagyítási szint értékének csökkentése kompenzálja a csempe-rendszerek variációját.

További információ: [útmutató a Térkép rendszerkép-renderelési API-hoz](how-to-render-custom-data.md).

A statikus térképi rendszerképek létrehozása mellett a Azure Maps Render Service lehetővé teszi a Térkép csempék közvetlen elérését raszteres (PNG) és vektoros formátumokban:

- [**Térkép csempe**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): a RASZTER (PNG) és a vektor csempék lekérése az alaptérképekhez (utak, határok, háttér).
- [**Térkép képének csempe**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile): antenna-és műholdas képcsempék lekérése.

> [!TIP]
> Néhány évvel ezelőtt számos Google Maps-alkalmazás az interaktív térképi élménytől a statikus térképi rendszerképekig váltott át, költségmegtakarítási módszerként. Azure Maps a web SDK interaktív térkép vezérlőelemének használata gyakran sokkal költséghatékonyabb. A szolgáltatási díj az alkalmazás által betöltött Térkép csempék számán alapul. Azure Maps a csempék nagy méretűek. Gyakran csak néhány csempét kell létrehoznia, hogy újra létrehozza ugyanazt a leképezési nézetet statikus térképként. A Térkép csempéit a böngésző automatikusan gyorsítótárazza. Így az interaktív térkép vezérlőelem gyakran generál egy tranzakció egy részét, amikor egy statikus Térkép nézetet készít elő. A pásztázás és a nagyítás több csempét is betölt, azonban a Térkép vezérlőelemben lehetőség van a viselkedés letiltására. Az interaktív térkép vezérlőelem sokkal több vizualizációs lehetőséget is biztosít, mint a statikus Térkép-szolgáltatások.

### <a name="marker-url-parameter-format-comparison"></a>Jelölő URL-paraméter formátumának összehasonlítása

**Előtte: Google Maps**

A Google Maps-ben hozzáadhat jelölőket egy statikus térképi képhez az URL-cím `markers` paraméterének használatával. A `markers` paraméter egy stílust és a térképen megjeleníteni kívánt helyszínek listáját veszi figyelembe az alábbi ábrán látható módon:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

További stílusok is használhatók további `markers` paraméterek hozzáadásával az URL-címhez egy másik stílussal és hellyel.

A jelölő helyei a "szélesség, hosszúság" formátumban vannak megadva.

A Google Maps-ben a jelölő stílusait `optionName:value`formátummal adja hozzá a rendszer, és több, a cső (\|) karakterrel elválasztott stílust tartalmaz. Ehhez hasonlóan: "optionName1: érték1\|optionName2: Érték2". Megjegyzés: a beállítások nevei és értékei kettősponttal vannak elválasztva (:). A következő stílus-beállítási nevek használhatók a Google Maps-beli jelölők stílusához:

- `color` – az alapértelmezett jelölő ikonjának színe. Lehet egy 24 bites hexadecimális szín (`0xrrggbb`) vagy a következő értékek egyike: `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label` – egy nagybetűs alfanumerikus karakter, amely az ikon tetején jelenik meg.
- `size` – a jelölő mérete. `tiny`, `mid`vagy `small`lehet.

Egyéni ikonok a Google Maps-ben a következő stílus-beállítási nevek használatával adhatók hozzá:

- `anchor` – megadja, hogy az ikon képének hogyan legyen igazítva a koordinátahöz. Lehet egy képpont (x, y) érték vagy a következő értékek egyike: `top`, `bottom`, `left`, `right`, `center`, `topleft`, `topright`, `bottomleft`vagy `bottomright`.
- `icon` – az ikon képére mutató URL-cím.

Például a Google Maps-ben egy piros, közepes méretű jelölőt adhat hozzá a térképhez a Koordinátákban (hosszúság:-110, szélesség: 45) a következő URL-paraméterrel:

```
&markers=color:red|size:mid|45,-110
```

<center>

![Google Maps-jelölő](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**Utána: Azure Maps**

A Azure Mapsban adja hozzá a jelölőket egy statikus térképi képhez a `pins` paraméter megadásával az URL-címben. A Google Mapshöz hasonlóan az ebben a paraméterben szereplő helyszínek is megtalálhatók. A `pins` paramétert többször is meg kell adni a különböző stílusokkal rendelkező jelölők támogatásához.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Ha további stílusokat szeretne használni, adjon hozzá további `pins` paramétereket az URL-címhez egy másik stílussal és hellyel.

A PIN-kód helye Azure Maps megköveteli, hogy a koordináták "hosszúsági szélesség" formátumúak legyenek. A Google Maps a "szélesség, hosszúság" formátumot használja. Egy szóköz, nem pedig vessző, elkülöníti a hosszúságot és a szélességet Azure Maps formátumban.

A `iconType` a létrehozandó PIN-kód típusát adja meg. A következő értékeket veheti fel:

- `default` – az alapértelmezett PIN-kód ikon.
- `none` – egyetlen ikon sem jelenik meg, csak a feliratok lesznek megjelenítve.
- `custom` – egyéni ikont kell használni. Az ikon képére mutató URL-címet a PIN-kód helye információ után a `pins` paraméter végéhez lehet adni.
- `{udid}` – egyedi Adatazonosító (UDID) a Azure Maps adattárolási platformon tárolt ikonhoz.

A Azure Maps `optionNameValue` formátumban adja hozzá a PIN-stílusokat. A pipe (\|) karakterekkel több stílust is elkülönítheti. Például: `iconType|optionName1Value1|optionName2Value2`. A beállítások nevei és értékei nincsenek elválasztva. A következő stílusú nevek használhatók a Azure Maps stílusú jelölők számára:

- `al` – a jelölő opacitását (alfa) adja meg. 0 és 1 közötti számot adjon meg.
- `an` – a PIN-kód horgonyát adja meg. X és y képpont értékek az "x y" formátumban megadva.
- `co` – a PIN-kód színe. 24 bites hexadecimális színnek kell lennie: `000000` `FFFFFF`.
- `la` – a címke horgonyát adja meg. X és y képpont értékek az "x y" formátumban megadva.
- `lc` – a címke színe. 24 bites hexadecimális színnek kell lennie: `000000` `FFFFFF`.
- `ls` – a felirat mérete képpontban megadva. Nullánál nagyobb számot adjon meg.
- `ro` – az ikon elforgatásához szükséges fokban megadott érték. Válasszon egy-360 és 360 közötti számot.
- `sc` – a rögzítési ikon méretezési értéke. Nullánál nagyobb számot adjon meg.

Az egyes PIN-kódokhoz meg kell adni a felirat értékeit. Ez a megközelítés hatékonyabb, mint egyetlen címke értékének alkalmazása a helyszínek listáján szereplő összes jelölőre. A címke értéke több karakterből álló karakterlánc lehet. A sztringet egyetlen idézőjelek közé csomagolva biztosíthatja, hogy ne legyen helytelen a stílus vagy a hely értéke.

Azure Maps például egy piros (`FF0000`) alapértelmezett ikon hozzáadásával, amely a "Space tű" címkével rendelkezik, az alábbi pozícióban (15 50), a koordinátákat tartalmazó ikonnal (hosszúság:-122,349300, Latitude: 47,620180) pedig a következő URL-lel történik:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![Azure Maps jelölő](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

Az alábbi példában három PIN-kód szerepel az "1", a "2" és a "3" címke értékkel:

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Azure Maps több jelölő](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>Elérési út URL-paraméterének formátumának összehasonlítása

**Előtte: Google Maps**

A Google Maps szolgáltatásban a vonalak és a sokszögek hozzáadhatók egy statikus térképi képhez az URL-cím `path` paraméterének használatával. A `path` paraméter egy stílust és a térképen megjeleníteni kívánt helyszínek listáját veszi figyelembe, az alábbi ábrán látható módon:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

További stílusok is használhatók további `path` paraméterek hozzáadásával az URL-címhez egy másik stílussal és hellyel.

A Google Maps elérési utak helyei a `latitude1,longitude1|latitude2,longitude2|…`formátummal vannak megadva. Az elérési utak kódolása vagy a pontok címeinek tárolására használható.

A Google Maps-beli elérésiút-stílusok a `optionName:value`formátummal lesznek hozzáadva, több stílus pedig pipe (\|) karakterekkel elválasztva. Ehhez hasonlóan: `optionName1:value1|optionName2:value2`. Megjegyzés: a beállítások nevei és értékei kettősponttal vannak elválasztva (:). A következő stílus-beállítási nevek használhatók a Google Maps-útvonalak stílusához:

- `color` – az elérési út vagy a sokszög körvonalának színe Lehet egy 24 bites hexadecimális szín (`0xrrggbb`), egy 32 bites hexadecimális szín (`0xrrggbbbaa`) vagy a következő értékek egyike: fekete, barna, zöld, lila, sárga, kék, szürke, narancssárga, piros, fehér.
- `fillColor` – az elérési út (sokszög) kitöltésére szolgáló szín. Lehet egy 24 bites hexadecimális szín (`0xrrggbb`), egy 32 bites hexadecimális szín (`0xrrggbbbaa`) vagy a következő értékek egyike: fekete, barna, zöld, lila, sárga, kék, szürke, narancssárga, piros, fehér.
- `geodesic` – azt jelzi, hogy az elérési útnak olyan vonalnak kell-e lennie, amely követi a föld görbületét.
- `weight` – az elérési út vonalvastagsága képpontban megadva

A Google Maps-ben a piros vonal opacitása és a képpont vastagsága is hozzáadható a térképhez a koordináták között az URL paraméterben. Az alábbi példában a vonal 50%-os opacitással és négy képpont vastagsággal rendelkezik. A koordináták a következők:-110, szélesség: 45 és hosszúság:-100, szélesség: 50.

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![Google Maps-vonallánc](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**Utána: Azure Maps**

A Azure Maps-ben adja hozzá a vonalakat és a sokszögeket egy statikus térképi képhez a `path` paraméter megadásával az URL-címben. A Google Mapshöz hasonlóan az ebben a paraméterben szereplő helyszínek is megtalálhatók. A `path` paramétert többször is meghatározhatja több kör, vonal és sokszög megjelenítéséhez különböző stílusokkal.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

Az elérési utak helyeinél Azure Maps megköveteli, hogy a koordináták "hosszúsági szélesség" formátumúak legyenek. A Google Maps a "szélesség, hosszúság" formátumot használja. Egy szóköz, nem pedig vessző, elkülöníti a hosszúságot és a szélességet Azure Maps formátumban. A Azure Maps nem támogatja a pontok kódolására szolgáló elérési utakat vagy címeket. Nagyobb adatkészletek feltöltése GeoJSON betölti a Azure Maps adattárolási API-t az [itt](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage)dokumentált módon.

A Azure Maps-ben adja hozzá az elérésiút-stílusokat a `optionNameValue` formátumhoz. Több stílust is elválaszthat a cső (\|) karakterekkel, például a `optionName1Value1|optionName2Value2`. A beállítások nevei és értékei nincsenek elválasztva. A következő stílus-beállítási neveket használja a Azure Maps stílusának elérési útjaihoz:

- `fa` – a sokszögek rendereléséhez használt kitöltési szín opacitása (alpha). 0 és 1 közötti számot adjon meg.
- `fc` – a sokszög területének megjelenítéséhez használt kitöltési szín.
- `la` – a vonalak és a sokszögek körvonalának megjelenítésekor használt vonal színopacitása (alpha). 0 és 1 közötti számot adjon meg.
- `lc` – a sorok megjelenítéséhez és a sokszögek körvonalához használt vonal színe.
- `lw` – a vonal szélessége képpontban megadva.
- `ra` – a körök sugarát adja meg méterben.

A Azure Maps-ben adjon hozzá egy piros vonal opacitását és egy képpont vastagságot a koordináták között az URL paraméterben. Az alábbi példában a vonal 50%-os opacitással és négy képpont vastagsággal rendelkezik. A koordináták értékei a következők: hosszúság:-110, szélesség: 45 és hosszúság:-100, szélesség: 50.

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Azure Maps vonallánc](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>Távolsági mátrix kiszámítása

Azure Maps a távolsági mátrix API-t biztosítja. Ezzel az API-val kiszámíthatja az utazási időt és a távolságokat a különböző tárolóhelyek között, a távolsági mátrixtal együtt. A Google Maps szolgáltatásban a távolsági mátrix API-hoz hasonló.

- [**Útvonal-mátrix**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): aszinkron módon számítja ki az utazási időpontokat és a távolságokat az egyes eredetek és célhelyek esetében. A legfeljebb 700 cellát támogat (az eredetek száma megszorozva a célhelyek számával). Ennek a korlátozásnak a szem előtt tartva a lehetséges mátrix-dimenziók például a következők: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> A távolsági mátrix API-ra irányuló kérés csak POST-kéréssel hozható létre a kérelem törzsében, a forrás-és a célként megadott információval. Emellett Azure Maps az összes eredetet és célhelyet koordinálni kell. Először a címeket kell geokódolva.

Az alábbi táblázat az összehasonlítható Azure Maps API-paraméterekkel hivatkozik a Google Maps API-paraméterekre.

| Google Maps API-paraméter      | Hasonló Azure Maps API-paraméter  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` – a POST kérelem törzsében válassza a GeoJSON. |
| `key`                          | `subscription-key` – tekintse meg a Azure Maps dokumentációjának [hitelesítését](azure-maps-authentication.md) is. |
| `language`                     | `language` – tekintse meg a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` – a POST kérelem törzsében válassza a GeoJSON.  |
| `region`                       | *N/A* – ez a funkció helymeghatározáshoz kapcsolatos. Használja a `countrySet` paramétert a Azure Maps helymeghatározáshoz API használatakor. |
| `traffic_model`                | *N/A* – csak azt adhatja meg, hogy a forgalmi adatokat a `traffic` paraméterrel kell-e használni. |
| `transit_mode`                 | *N/A – az* átvitelen alapuló távolsági mátrixok jelenleg nem támogatottak.  |
| `transit_routing_preference`   | *N/A – az* átvitelen alapuló távolsági mátrixok jelenleg nem támogatottak.  |
| `units`                        | *N/A* – Azure Maps csak a metrikus rendszer használatát használja. |

> [!TIP]
> A Azure Maps útválasztási API-ban elérhető összes speciális útválasztási lehetőség támogatott a Azure Maps távolsági mátrix API-ban. A speciális útválasztási lehetőségek a következők: Ruck útválasztás, motor-specifikációk stb.

## <a name="get-a-time-zone"></a>Időzóna beolvasása

A Azure Maps egy API-t biztosít a koordináta időzónájának lekéréséhez. A Azure Maps időzóna API hasonló az időzóna API-hoz a Google Maps szolgáltatásban:

- [**Időzóna koordináta szerint**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): megadási koordináta és a koordináta időzóna-adatainak fogadása.

A következő táblázat kereszthivatkozásokat hivatkozik a Google Maps API-paraméterekre a Azure Maps hasonló API-paramétereivel.

| Google Maps API-paraméter | Hasonló Azure Maps API-paraméter   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` – tekintse meg a Azure Maps dokumentációjának [hitelesítését](azure-maps-authentication.md) is.       |
| `language`                  | `language` – tekintse meg a [támogatott nyelvek](supported-languages.md) dokumentációját.    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Ezen az API-on kívül a Azure Maps platform számos időzóna API-t biztosít. Ezek az API-k az időzóna neve vagy azonosítói alapján alakítják át az időt:

- [**Időzóna azonosító szerint**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): a megadott IANA Időzóna-azonosító aktuális, korábbi és jövőbeli időzóna-információit adja vissza.
- [**Időzóna enumerálása IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): az IANA időzóna-azonosítók teljes listáját adja vissza. Az IANA szolgáltatás frissítései egy napon belül megjelennek a rendszeren.
- [**Időzóna enumerálása Windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): a Windows időzóna-azonosítók teljes listáját adja vissza.
- [**Időzóna – IANA-verzió**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): az Azure Maps által használt aktuális IANA-verziószámot adja vissza.
- [**Időzóna-ablakok az IANA számára**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): egy megfelelő IANA-azonosítót ad vissza, amely érvényes Windows időzóna-azonosítót adott meg. Egy Windows-AZONOSÍTÓhoz több IANA-azonosító is visszaküldhető.

## <a name="client-libraries"></a>Ügyfélkódtárak

A Azure Maps a következő programozási nyelvekhez biztosít ügyféloldali kódtárakat:

- JavaScript, írógéppel, Node. js – [dokumentáció](how-to-use-services-module.md) \| [NPM csomag](https://www.npmjs.com/package/azure-maps-rest)

Nyílt forráskódú ügyféloldali kódtárak más programozási nyelvekhez:

- .NET Standard 2,0 – [GitHub-projekt](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet csomag](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>További források

Az alábbiakban néhány további dokumentációt és erőforrást talál a Azure Maps REST-szolgáltatásokhoz.

- [Ajánlott eljárások a kereséshez](how-to-use-best-practices-for-search.md)
- [Címek keresése](how-to-search-for-address.md)
- [Azure Maps REST Service API-referenciák dokumentációja](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>Következő lépések

További információ a Azure Maps REST Services szolgáltatásról.

> [!div class="nextstepaction"]
> [Ajánlott eljárások a keresési szolgáltatás használatához](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [A Services modul (web SDK) használata](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Kódminták](https://docs.microsoft.com/samples/browse/?products=azure-maps)