---
title: Webszolgáltatások migrálása a Google Maps szolgáltatásból | Microsoft Docs
description: A webszolgáltatások Google Maps szolgáltatásból Microsoft Azure Maps-be történő áttelepítésével kapcsolatos oktatóanyag.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 8fc3270d15f77576ff353a110d8a3a9a17c7144d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480020"
---
# <a name="migrate-web-service-from-google-maps"></a>Webszolgáltatás migrálása a Google Mapsből

Az Azure és a Google Maps egyaránt hozzáférést biztosít a térbeli API-khoz a REST-alapú webszolgáltatásokon keresztül. A platformok API-felületei hasonló funkciókat hajtanak végre, de eltérő elnevezési konvenciókat és válasz-objektumokat használnak.

Az alábbi táblázat tartalmazza a Azure Maps Service API-kat, amelyek hasonló funkciókat biztosítanak a Google Maps szolgáltatás API-jai számára.

| Google Maps szolgáltatás API | Azure Maps Service API                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Útvonal tervezése              | [Útvonal](https://docs.microsoft.com/rest/api/maps/route)                               |
| Távolsági mátrix         | [Útvonal-mátrix](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) |
| Geokódolás               | [Keresés](https://docs.microsoft.com/rest/api/maps/search)                             |
| Helyek keresése           | [Keresés](https://docs.microsoft.com/rest/api/maps/search)                             |
| Automatikus kiegészítés      | [Keresés](https://docs.microsoft.com/rest/api/maps/search)                             |
| Statikus Térkép              | [Megjelenítés](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                 |
| Időzóna               | [Időzóna](https://docs.microsoft.com/rest/api/maps/timezone)                        |

A következő szolgáltatási API-k jelenleg nem érhetők el Azure Mapsban:

- Jogosultságszint
- Földrajzi hely
- Részletek és fényképek elhelyezése. A Azure Maps Search API-ban elérhető telefonszámok és webhely URL-címe.
- Térkép URL-címei
- Utak – a sebességkorlátozás-adatforgalom a Azure Maps útvonalon és fordított helymeghatározáshoz API-kon keresztül érhető el.
- Statikus utcai nézet

Azure Maps számos további REST-webszolgáltatással rendelkezik, amelyek érdekesek lehetnek:

- [Térbeli műveletek](https://docs.microsoft.com/rest/api/maps/spatial): összetett térbeli számítások és műveletek (például geokerítések) kiszervezése szolgáltatáshoz.
- [Forgalom](https://docs.microsoft.com/rest/api/maps/traffic): a valós idejű forgalom és az incidensek adatforgalmának elérése.

## <a name="geocoding-addresses"></a>Helymeghatározáshoz-címek

A helymeghatározáshoz a címek (például az "1 Microsoft Way, Redmond, WA") egy koordináta (például a következő hosszúság:-122,1298, szélesség: 47,64005) átalakításának folyamata. A koordinátákat a rendszer gyakran használja a jelölők térképre vagy középpontba helyezésére.

A Azure Maps számos módszert biztosít a helymeghatározáshoz-címekhez:

- [**Szabad formátumú helymeghatározáshoz**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): egyetlen címzési karakterláncot (például "1 Microsoft Way, Redmond, WA") határozhat meg, és azonnal feldolgozza a kérést. Ez akkor ajánlott, ha gyorsan kell geocode az egyes címeket.
- [**Strukturált helymeghatározáshoz**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): egyetlen cím részeit adja meg, például az utca nevét, a várost, az országot és az irányítószámot, és azonnal feldolgozza a kérést. Ez akkor ajánlott, ha gyorsan kell geocode az egyes címeket, és az adatai már az egyes címekre is bekerülnek.
- [**Batch-helymeghatározáshoz**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): hozzon létre egy legfeljebb 10 000 címet tartalmazó kérelmet, és egy adott időszakban dolgozza fel azokat. A rendszer az összes címet párhuzamosan geokódolva a kiszolgálón, és amikor elkészült, a teljes eredményhalmazt letöltheti. Ez a nagyméretű adatkészletek helymeghatározáshoz ajánlott.
- [**Fuzzy keresés**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): ez az API ötvözi a helymeghatározáshoz a keresett ponttal. Ez az API egy szabad formátumú karakterláncot vesz igénybe, amely lehet egy lakcím, egy hely, egy mérföldkő, a hasznos hely vagy a kamat kategóriája, és azonnal feldolgozhatja a kérést. Ez az API olyan alkalmazások esetében ajánlott, amelyekben a felhasználók az azonos szövegmezőből származó címekre vagy érdekes pontokra kereshetnek.
- [**Fuzzy batch-keresés**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): hozzon létre egy olyan kérést, amely legfeljebb 10 000 címet, helyet, tereptárgyat vagy érdeklődési pontot tartalmaz, és egy adott időszakban feldolgozható. Az összes adat párhuzamosan lesz feldolgozva a kiszolgálón, és ha elkészült, a teljes eredményhalmaz letölthető.

A következő táblázat kereszthivatkozásokat hivatkozik a Google Maps API-paraméterekre a Azure Maps hasonló API-paramétereivel.

| Google Maps API-paraméter | Hasonló Azure Maps API-paraméter  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` és `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` – város/város<br/>`municipalitySubdivision` – szomszédság, Sub/Super City<br/>`countrySubdivision` – állam vagy megye<br/>`countrySecondarySubdivision` – megye<br/>`countryTertiarySubdivision` – kerület<br/>`countryCode` – kétbetűs országkód |
| `key`                       | `subscription-key` – tekintse meg a Azure Maps dokumentációjának [hitelesítését](azure-maps-authentication.md) is. |
| `language`                  | `language` – tekintse meg a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `region`                    | `countrySet`                       |

A Search szolgáltatás használatának példáját [itt](how-to-search-for-address.md)találja. Mindenképpen tekintse át az [ajánlott eljárásokat a keresési](how-to-use-best-practices-for-search.md) dokumentációhoz.

> [!TIP]
> A szabad formátumú helymeghatározáshoz és a fuzzy keresési API-k automatikus kiegészítési módban is használhatók `&amp;typeahead=true` a kérelem URL-címéhez való hozzáadásával. Ez a művelet közli a kiszolgálóval, hogy a bemeneti szöveg valószínűleg részleges, és a prediktív módba lép.

## <a name="reverse-geocode-a-coordinate"></a>Koordináta fordított geocode

A fordított helymeghatározáshoz az a folyamat, amelynek során a rendszer a földrajzi koordinátákat (például a hosszúság:-122,1298, a Latitude: 47,64005) a hozzávetőleges címmé (például "1 Microsoft Way, Redmond, WA") alakítja át.

Azure Maps több fordított helymeghatározáshoz módszert biztosít:

- [**Fordított geocoder**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse): egyetlen földrajzi koordináta megadásával megtekintheti a hozzávetőleges címeit, és azonnal feldolgozhatja a kérést.
- [**Cross Street fordított geocoder**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): egyetlen földrajzi koordinátat határozhat meg a közeli Cross Street-információk eléréséhez (például 1 & Main), és azonnal feldolgozza a kérést.
- [**Fordított geocoder**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): hozzon létre egy legfeljebb 10 000 koordinátákat tartalmazó kérelmet, amely egy adott időszakban feldolgozható. Az összes adat párhuzamosan lesz feldolgozva a kiszolgálón, és ha elkészült, a teljes eredményhalmaz letölthető.

A következő táblázat kereszthivatkozásokat hivatkozik a Google Maps API-paraméterekre a Azure Maps hasonló API-paramétereivel.

| Google Maps API-paraméter   | Hasonló Azure Maps API-paraméter   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` – tekintse meg a Azure Maps dokumentációjának [hitelesítését](azure-maps-authentication.md) is. |
| `language`                  | `language` – tekintse meg a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `latlng`                    | `query`  |
| `location_type`             | *N/A*     |
| `result_type`               | `entityType`    |

Mindenképpen tekintse át az [ajánlott eljárásokat a keresési](how-to-use-best-practices-for-search.md) dokumentációhoz.

A Azure Maps fordított helymeghatározáshoz API további funkciói nem érhetők el a Google Maps-ben, amelyek hasznosak lehetnek az alkalmazás áttelepítésekor:

- Sebességkorlátozás beolvasása.
- Közúti használati információk beolvasása: helyi út, artériás, korlátozott hozzáférés, rámpa stb.
- A koordináta azon oldala, ahová a koordináta tartozik.

## <a name="search-for-points-of-interest"></a>Hasznos helyek keresése

A helyek keresési API-ját a Google Maps szolgáltatásban keresheti meg. Ez az API három különböző módszert biztosít a fontos helyek kereséséhez:

- **Hely keresése a következő szövegből:** Megkeresi az érdeklődési pontot a neve, címe vagy telefonszáma alapján.
- **Közeli keresés**: megkeresi azokat az érdeklődési pontokat, amelyek egy adott távolságon belül vannak.
- **Szöveges keresés:** Megkeresi a helyeket egy szabad formátumú szöveggel, amely az érdeklődési pont és a hely adatait tartalmazza. Például: "pizza a New York-ban" vagy "éttermek a Main St közelében".

Azure Maps számos keresési API-t biztosít a hasznos helyek számára:

- [**POI-keresés**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): érdeklődési pontok keresése név alapján. Például: "Starbucks".
- [**POI-kategória keresése**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): az érdeklődési pontok kategória szerint kereshetők. Például: "étterem".
- [**Közeli keresés**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): megkeresi azokat az érdeklődési pontokat, amelyek egy adott távolságon belül vannak.
- [**Fuzzy keresés**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): ez az API ötvözi a helymeghatározáshoz a keresett ponttal. Ez az API egy szabad formátumú karakterláncot vesz igénybe, amely lehet egy lakcím, egy hely, egy mérföldkő, a hasznos hely vagy a kamat kategóriája, és azonnal feldolgozhatja a kérést. Ez az API olyan alkalmazások esetében ajánlott, amelyekben a felhasználók az azonos szövegmezőből származó címekre vagy érdekes pontokra kereshetnek.
- [**Keresés a geometrián belül**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): a megadott geometrián (sokszög) belül található érdeklődési pontok keresése.
- [**Keresés az útvonal mentén**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): keressen olyan pontokat, amelyek egy adott útvonal útvonalán találhatók.
- [**Fuzzy batch-keresés**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): hozzon létre egy olyan kérést, amely legfeljebb 10 000 címet, helyet, tereptárgyat vagy érdeklődési pontot tartalmaz, és egy adott időszakban feldolgozható. Az összes adat párhuzamosan lesz feldolgozva a kiszolgálón, és ha elkészült, a teljes eredményhalmaz letölthető.

Jelenleg Azure Maps nem rendelkezik hasonló API-val a Google Maps szöveges keresési API-val.

> [!TIP]
> A POI Search, a POI kategóriájú keresési és a fuzzy Search API-k az automatikus kiegészítési módban használhatók `&amp;typeahead=true` a kérelem URL-címéhez való hozzáadásával. Ez a művelet közli a kiszolgálóval, hogy a bemeneti szöveg valószínűleg részleges, és a prediktív módba lép.

Mindenképpen tekintse át az [ajánlott eljárásokat a keresési](how-to-use-best-practices-for-search.md) dokumentációhoz.

### <a name="find-place-from-text"></a>Hely keresése a szövegből

Az érdeklődési pontok név vagy címe alapján történő kereséséhez használhatja a Azure Maps [POI Search](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) és a [fuzzy Search](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) API-kat.

A következő táblázat kereszthivatkozásokat hivatkozik a Google Maps API-paraméterekre a Azure Maps hasonló API-paramétereivel.

| Google Maps API-paraméter | Hasonló Azure Maps API-paraméter |
|---------------------------|-------------------------------------|
| `fields`                  | *N/A*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N/A*                               |
| `key`                     | `subscription-key` – tekintse meg a Azure Maps dokumentációjának [hitelesítését](azure-maps-authentication.md) is. |
| `language`                | `language` – tekintse meg a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `locationbias`            | `lat`, `lon` és `radius`<br/>`topLeft` és `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Közeli keresés

A közeli [keresési](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) API-k az Azure Mapsban kérhetők le.

A következő táblázat kereszthivatkozásokat hivatkozik a Google Maps API-paraméterekre a Azure Maps hasonló API-paramétereivel.

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

Az útvonalak és irányok kiszámításához Azure Maps használható. Azure Maps a Google Maps útválasztási szolgáltatása számos különböző funkciót tartalmaz, például:

- érkezési és távozási idő.
- valós idejű és prediktív forgalmi útvonalak.
- különböző szállítási módok; vezetés, gyaloglás, kerékpározás.

> [!NOTE]
> Azure Maps az összes Útpontot koordinálni kell. Először a címeket kell geokódolva.

A Azure Maps útválasztási szolgáltatás a következő API-kat biztosítja az útvonalak kiszámításához:

- [**Útvonal kiszámítása**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): kiszámítja az útvonalat, és azonnal feldolgozza a kérést. Ez az API a GET és a POST kérelmeket is támogatja. A POST kérések használata nagy számú útpont megadása esetén ajánlott, vagy ha sok útvonal-beállítást használ, hogy az URL-cím kérése ne legyen túl hosszú, és problémákat okozzon.
- [**Batch Route**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): hozzon létre egy legfeljebb 1 000 útválasztási kérelmet tartalmazó kérelmet, és egy adott időszakban dolgozza fel azokat. Az összes adat párhuzamosan lesz feldolgozva a kiszolgálón, és ha elkészült, a teljes eredményhalmaz letölthető.
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
| `region`                       | *N/A* – ez egy helymeghatározáshoz-hez kapcsolódó szolgáltatás. Használja a *countrySet* paramétert a Azure Maps helymeghatározáshoz API használatakor.  |
| `traffic_model`               | *N/A* – csak azt adhatja meg, hogy a forgalmi adatokat a *Traffic* paraméterrel kell-e használni. |
| `transit_mode`                | Lásd: [mobilitási szolgáltatások dokumentációja](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | Lásd: [mobilitási szolgáltatások dokumentációja](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | *N/A* – Azure Maps csak a metrikus rendszer használatát használja.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Alapértelmezés szerint a Azure Maps Route API csak összegzést (távolságot és időpontokat) és az útvonal útvonalának koordinátáit adja vissza. Használja a `instructionsType` paramétert a lépésenkénti utasítások lekéréséhez. A `routeRepresentation` paraméterrel kiszűrhetők az összefoglalás és az útvonal útvonala.

A Azure Maps Routing API számos további funkcióval nem érhető el a Google Maps-ben, amelyek hasznosak lehetnek az alkalmazás áttelepítéséhez:

- Az útvonal típusának támogatása: legrövidebb, leggyorsabb, trilling és a legtöbb üzemanyag-takarékos.
- További utazási módok támogatása: busz, motorkerékpár, taxi, tehergépkocsi és Furgon.
- 150-es útpontok támogatása.
- Több utazási idő kiszámítása egyetlen kérelemben; korábbi forgalom, élő forgalom, forgalom nélkül.
- Kerülje a további utak típusát: telekocsi utak, nem burkolt utak, már használatban lévő utak.
- A kerülni kívánt egyéni területek meghatározása.
- Korlátozza a jogosultságszint-emelést, hogy az útvonal felemelkedni tud.
- A motor specifikáció-alapú útválasztása. Kiszámítja az égési vagy az elektromos járművek útvonalait a fennmaradó tüzelőanyag/díj és a motor előírásai alapján.
- Kereskedelmi jármű útvonal-paraméterének támogatása; a jármű méretei, súlyozása, Axel-száma és a rakomány típusa.
- A jármű maximális sebességének meghatározása.

Ezen kívül a Azure Maps Route szolgáltatás is támogatja az átirányítható tartományok (más néven izokrón) [kiszámítását](https://docs.microsoft.com/rest/api/maps/route/getrouterange)is, amely egy olyan területhez tartozó sokszöget állít elő, amely egy adott időtartamon belül, illetve az üzemanyag/díj alapján bármilyen irányban átutazható.

## <a name="retrieve-a-map-image"></a>Térkép rendszerképének beolvasása

A Azure Maps egy API-t biztosít a statikus Térkép képének megjelenítéséhez. Az Azure Maps [map képmegjelenítési](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) API a Google Maps-ben a statikus Térkép API-hoz hasonló.

> [!NOTE]
> Azure Maps a középpontot, az összes jelölőt és elérési utat a "hosszúság, szélesség" formátumban kell koordinálni, míg a Google Maps a "szélesség, hosszúság" formátumot használja. Először a címeket kell geokódolva.

A következő táblázat kereszthivatkozásokat hivatkozik a Google Maps API-paraméterekre a Azure Maps hasonló API-paramétereivel.

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
> A Azure Maps csemperendszer olyan csempét használ, amely a Google Maps-ben használt Térkép csempék méretének kétszerese. Mivel a nagyítási szint értéke Azure Maps egy nagyítási szint jelenik meg Azure Maps a Google Maps-hez képest. Csökkentse az áttelepíteni kívánt kérelmek nagyítási szintjét, hogy kompenzálja ezt.

További információ: [útmutató a Térkép rendszerkép-renderelési API-hoz](how-to-render-custom-data.md).

Amellett, hogy képes létrehozni egy statikus térképi képet, a Azure Maps Render szolgáltatás lehetővé teszi a Térkép csempék közvetlen elérését raszteres (PNG) és vektoros formátum esetén is:

- [**Térkép csempe**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): a RASZTER (PNG) és a vektor csempék lekérése az alaptérképekhez (utak, határok, háttér).
- [**Térkép képének csempe**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile): antenna-és műholdas képcsempék lekérése.

> [!TIP]
> Számos Google Maps-alkalmazás, ahol az interaktív térképi funkciókról a statikus térképi rendszerképekre való áttérés néhány évvel ezelőtt, a költségmegtakarítás módszere. A Azure Maps általában sokkal költséghatékonyabb, hogy a webes SDK interaktív térkép vezérlőelemét használja, mint a Térkép csempék terhelése alapján. A Azure Maps a csempék nagy méretűek, és gyakran csak néhányat vesz igénybe, hogy ugyanazt a leképezési nézetet hozza létre, mint egy statikus térképet, és a Térkép csempéket a böngésző automatikusan gyorsítótárazza. Ennek megfelelően az interaktív térkép vezérlőelem gyakran csak egy tranzakció töredékét állítja elő statikus térképes nézet létrehozásakor. A pásztázás és a nagyítás több csempét is betölt, de a Térkép vezérlőelemben lehetőség van arra, hogy letiltsa ezt a viselkedést, ha szükséges. Az interaktív térkép vezérlőelem sokkal több vizualizációs lehetőséget is biztosít, mint a statikus leképezési szolgáltatások.

### <a name="marker-url-parameter-format-comparison"></a>Jelölő URL-paraméter formátumának összehasonlítása

**Előtte: Google Maps**

A Google Maps-jelölők hozzáadhatók egy statikus térképi képhez az URL-cím `markers` paraméterének használatával. A `markers` paraméter egy stílust és a térképen megjeleníteni kívánt helyszínek listáját veszi figyelembe az alábbi ábrán látható módon:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

További stílusok is használhatók további `markers` paraméterek hozzáadásával az URL-címhez egy másik stílussal és hellyel.

A jelölő helyei a "szélesség, hosszúság" formátumban vannak megadva.

A Google Maps-ben a jelölő stílusait `optionName:value`formátummal adja hozzá a rendszer, több stílust pedig a cső (\|) karakterekkel elválasztva, például "optionName1: érték1\|optionName2: Érték2". Megjegyzés: a beállítások nevei és értékei kettősponttal vannak elválasztva (:). A következő stílus-beállítási nevek használhatók a Google Maps-beli jelölők stílusához:

- `color` – az alapértelmezett jelölő ikonjának színe. Lehet egy 24 bites hexadecimális szín (`0xrrggbb`) vagy a következő értékek egyike: `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label` – egy nagybetűs alfanumerikus karakter, amely az ikon tetején jelenik meg.
- `size` – a jelölő mérete. `tiny`, `mid`vagy `small`lehet.

Az egyéni ikonokat a Google Maps szolgáltatásban is használhatja a következő stílusú beállítások neveivel:

- `anchor` – megadja, hogy az ikon képének hogyan legyen igazítva a koordinátahöz. Lehet egy képpont (x, y) érték vagy a következő értékek egyike: `top`, `bottom`, `left`, `right`, `center`, `topleft`, `topright`, `bottomleft`vagy `bottomright`.
- `icon` – az ikon képére mutató URL-cím.

Például a Google Maps-ben egy piros, közepes méretű jelölőt adhat hozzá a térképhez a Koordinátákban (hosszúság:-110, szélesség: 45) a következő URL-paraméterrel:

```
&markers=color:red|size:mid|45,-110
```

<center>

![Google Maps-jelölő](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**Utána: Azure Maps**

Azure Maps jelölők is hozzáadhatók statikus térképi képhez a `pins` paraméter megadásával az URL-címben. A Google Maps-hez hasonlóan a stílus és a helyszínek listája is megadható ebben a paraméterben, és a `pins` paraméter többször is megadható a különböző stílusú jelölők támogatásához.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

További stílusok is használhatók további `pins` paraméterek hozzáadásával az URL-címhez egy másik stílussal és hellyel.

Ha a rendszer rögzíti a helyet, Azure Maps megköveteli, hogy a koordináták "hosszúsági szélesség" formátumban legyenek, míg a Google Maps a "szélesség, hosszúság" formátumot használja. Azt is vegye figyelembe, hogy van egy szóköz, nem pedig egy vesszővel elválasztott hosszúság és szélesség a Azure Mapsban.

A `iconType` érték határozza meg a létrehozandó PIN-kód típusát, és a következő értékeket veheti fel:

- `default` – az alapértelmezett PIN-kód ikon.
- `none` – egyetlen ikon sem jelenik meg, csak a feliratok lesznek megjelenítve.
- `custom` – egyéni ikont kell használni. Az ikon képére mutató URL-címet a PIN-kód helye információ után a `pins` paraméter végéhez lehet adni.
- `{udid}` – egyedi Adatazonosító (UDID) a Azure Maps adattárolási platformon tárolt ikonhoz.

A Azure Mapsban szereplő stílusokat a rendszer a `optionNameValue`formátumban adja hozzá, és több stílust is elválaszthat a cső (\|) karakterekkel, mint ez a `iconType|optionName1Value1|optionName2Value2`. Megjegyzés: a beállítások nevei és értékei nincsenek elválasztva. A következő stílus-beállítási nevek használhatók a Azure Mapsban található jelölők stílusához:

- `al` – a jelölő opacitását (alfa) adja meg. 0 és 1 közötti szám lehet.
- `an` – a PIN-kód horgonyát adja meg. X és y képpont értékek az "x y" formátumban megadva.
- `co` – a PIN-kód színe. 24 bites hexadecimális színnek kell lennie: `000000` `FFFFFF`.
- `la` – a címke horgonyát adja meg. X és y képpont értékek az "x y" formátumban megadva.
- `lc` – a címke színe. 24 – de hexadecimális színnek kell lennie: `000000` `FFFFFF`.
- `ls` – a felirat mérete képpontban megadva. 0-nál nagyobb szám lehet.
- `ro` – az ikon elforgatásához szükséges fokban megadott érték. A-360 és a 360 közötti szám lehet.
- `sc` – a rögzítési ikon méretezési értéke. 0-nál nagyobb szám lehet.

Az egyes PIN-kódokhoz meg kell adni a Label értékeket, nem pedig egyetlen feliratot, amely a helyek listáján szereplő összes jelölőre vonatkozik. A címke értéke lehet több karakterből álló karakterlánc is, és az idézőjelek közé helyezhető, így biztosítható, hogy ne legyen helytelen a stílus vagy a hely értéke.

A Azure Maps például egy piros (`FF0000`) alapértelmezett ikon hozzáadásával, amely a "Space tű" címkével rendelkezik (15 50), a Koordinátákban (hosszúság:-122,349300, szélesség: 47,620180) található ikon a következő URL-lel hajtható végre:

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

A Google Maps-vonalakban és-sokszögekben a `path` paraméterrel adhatók hozzá a statikus térképi képekhez. A `path` paraméter egy stílust és a térképen megjeleníteni kívánt helyszínek listáját veszi figyelembe az alábbi ábrán látható módon:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

További stílusok is használhatók további `path` paraméterek hozzáadásával az URL-címhez egy másik stílussal és hellyel.

A Google Maps elérési utak helyei a `latitude1,longitude1|latitude2,longitude2|…`formátummal vannak megadva. Az elérési utak kódolása vagy a pontok címeinek tárolására használható.

A Google Maps-beli elérésiút-stílusok a `optionName:value`formátummal lesznek hozzáadva, és több stílust is elválasztanak a cső (\|) karakterekkel, például ez a `optionName1:value1|optionName2:value2`. Megjegyzés: a beállítások nevei és értékei kettősponttal vannak elválasztva (:). A következő stílus-beállítási nevek használhatók a Google Maps-útvonalak stílusához:

- `color` – az elérési út vagy a sokszög körvonalának színe Lehet egy 24 bites hexadecimális szín (`0xrrggbb`), egy 32 bites hexadecimális szín (`0xrrggbbbaa`) vagy a következő értékek egyike: fekete, barna, zöld, lila, sárga, kék, szürke, narancssárga, piros, fehér.
- `fillColor` – az elérési út (sokszög) kitöltésére szolgáló szín. Lehet egy 24 bites hexadecimális szín (`0xrrggbb`), egy 32 bites hexadecimális szín (`0xrrggbbbaa`) vagy a következő értékek egyike: fekete, barna, zöld, lila, sárga, kék, szürke, narancssárga, piros, fehér.
- `geodesic` – azt jelzi, hogy az elérési útnak olyan vonalnak kell-e lennie, amely követi a föld görbületét.
- `weight` – az elérési út vonalvastagsága képpontban megadva

Például a Google Maps-ben egy 50%-os opacitású és 4 képpont vastagságú piros vonal adható hozzá a térképhez a koordináták között (hosszúság:-110, szélesség: 45 és hosszúság:-100, Latitude: 50), a következő URL-paraméterrel:

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![Google Maps-vonallánc](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**Utána: Azure Maps**

Azure Maps vonalakban és sokszögekben is hozzáadhatók a statikus térképi képekhez az URL-cím `path` paraméterének megadásával. A Google Maps-hez hasonlóan a stílus és a helyszínek listája is megadható ebben a paraméterben, és a `path` paraméter többször is megadható több kör, vonal és sokszög megjelenítéséhez különböző stílusokkal.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

Az elérési utak helyeihez Azure Maps megköveteli, hogy a koordináták "hosszúsági szélesség" formátumban legyenek, míg a Google Maps a "szélesség, hosszúság" formátumot használja. Azt is vegye figyelembe, hogy van egy szóköz, nem pedig egy vesszővel elválasztott hosszúság és szélesség a Azure Mapsban. A Azure Maps nem támogatja a pontok kódolására szolgáló elérési utakat vagy címeket. Nagyobb adatkészletek tölthetők fel, mivel a GeoJSON betöltik a Azure Maps adattárolási API-t az [itt](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage)dokumentált módon.

A Azure Mapsban lévő elérésiút-stílusok a következő formátummal lesznek felvéve: `optionNameValue`, több stílus pedig pipe (\|) karakterekkel elválasztva, mint ez a `optionName1Value1|optionName2Value2`. Megjegyzés: a beállítások nevei és értékei nincsenek elválasztva. A következő stílus-beállítási nevek használhatók a Azure Mapsi elérési utak stílusához:

- `fa` – a sokszögek rendereléséhez használt kitöltési szín opacitása (alpha). 0 és 1 közötti szám lehet.
- `fc` – a sokszög területének megjelenítéséhez használt kitöltési szín.
- `la` – a vonalak és a sokszögek körvonalának megjelenítésekor használt vonal színopacitása (alpha). 0 és 1 közötti szám lehet.
- `lc` – a sorok megjelenítéséhez és a sokszögek körvonalához használt vonal színe.
- `lw` – a vonal szélessége képpontban megadva.
- `ra` – a körök sugarát adja meg méterben.

Azure Maps például egy 50%-os opacitású piros vonal és egy 4 képpont vastagsága is hozzáadható a térképhez a koordináták között (hosszúság:-110, szélesség: 45 és hosszúság:-100, Latitude: 50) a következő URL paraméterrel:

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Azure Maps vonallánc](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>Távolsági mátrix kiszámítása

A Azure Maps API-t biztosít az utazási idő kiszámításához, valamint a különböző helyei közötti távolságot a távolsági mátrixként. A Azure Maps távolsági mátrix API a Google Maps-ben a távolsági mátrix API-hoz hasonló.

- [**Útvonal-mátrix**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): aszinkron módon számítja ki az utazási időpontokat és a távolságokat az egyes eredetek és célhelyek esetében. A rendszer legfeljebb 700 cellát támogat (az eredetek száma megszorozva a célhelyek számával). Ennek a korlátozásnak a szem előtt tartva a lehetséges mátrix-dimenziók például a következők: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> A távolsági mátrix API-ra irányuló kérés csak POST-kéréssel hozható létre a kérelem törzsében, a forrás-és a célként megadott információval. Emellett Azure Maps az összes eredetet és célhelyet koordinálni kell. Először a címeket kell geokódolva.

A következő táblázat kereszthivatkozásokat hivatkozik a Google Maps API-paraméterekre a Azure Maps hasonló API-paramétereivel.

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
| `region`                       | *N/A* – ez egy helymeghatározáshoz-hez kapcsolódó szolgáltatás. Használja a `countrySet` paramétert a Azure Maps helymeghatározáshoz API használatakor. |
| `traffic_model`                | *N/A* – csak azt adhatja meg, hogy a forgalmi adatokat a `traffic` paraméterrel kell-e használni. |
| `transit_mode`                 | *N/A – az* átvitelen alapuló távolsági mátrixok jelenleg nem támogatottak.  |
| `transit_routing_preference`   | *N/A – az* átvitelen alapuló távolsági mátrixok jelenleg nem támogatottak.  |
| `units`                        | *N/A* – Azure Maps csak a metrikus rendszer használatát használja. |

> [!TIP]
> A Azure Maps útválasztási API-ban elérhető összes speciális útválasztási beállítás (tehergépkocsi-útválasztás, motor-specifikáció, elkerülés...) támogatja a Azure Maps távolsági mátrix API-t.

## <a name="get-a-time-zone"></a>Időzóna beolvasása

A Azure Maps API-t biztosít a koordináta időzónájának lekéréséhez. A Azure Maps időzóna API hasonló az időzóna API-hoz a Google Maps szolgáltatásban:

- [**Időzóna a koordináta alapján**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): megadásával megtekintheti a koordinátákat, és lekérheti az adott időzóna részleteit.

A következő táblázat kereszthivatkozásokat hivatkozik a Google Maps API-paraméterekre a Azure Maps hasonló API-paramétereivel.

| Google Maps API-paraméter | Hasonló Azure Maps API-paraméter   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` – tekintse meg a Azure Maps dokumentációjának [hitelesítését](azure-maps-authentication.md) is.       |
| `language`                  | `language` – tekintse meg a [támogatott nyelvek](supported-languages.md) dokumentációját.    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

A Azure Maps platformon kívül számos további időzóna API is rendelkezésre áll, amelyek segítenek az időzóna-nevekkel és-azonosítókkal való átalakításban:

- [**Időzóna azonosító szerint**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): a megadott IANA Időzóna-azonosító aktuális, korábbi és jövőbeli időzóna-információit adja vissza.
- [**Időzóna enumerálása IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): az IANA időzóna-azonosítók teljes listáját adja vissza. Az IANA szolgáltatás frissítései egy napon belül megjelennek a rendszeren.
- [**Időzóna enumerálása Windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): a Windows időzóna-azonosítók teljes listáját adja vissza.
- [**Időzóna – IANA-verzió**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): az Azure Maps által használt aktuális IANA-verziószámot adja vissza.
- [**Időzóna-ablakok az IANA számára**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): egy megfelelő IANA-azonosítót ad vissza, amely érvényes Windows időzóna-azonosítót adott meg. Egy Windows-AZONOSÍTÓhoz több IANA-azonosító is visszaküldhető.

## <a name="client-libraries"></a>Klienskódtárak

A Azure Maps a következő programozási nyelvekhez biztosít ügyféloldali kódtárakat:

- JavaScript, írógéppel, Node. js – [dokumentáció](how-to-use-services-module.md) \| [NPM csomag](https://www.npmjs.com/package/azure-maps-rest)

Nyílt forráskódú ügyféloldali kódtárak más programozási nyelvekhez:

- .NET Standard 2,0 – [GitHub-projekt](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet csomag](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>További források

Az alábbiakban néhány további dokumentációt és erőforrást talál a Azure Maps REST-szolgáltatásokkal kapcsolatban.

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