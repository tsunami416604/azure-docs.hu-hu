---
title: Térbeli információk olvasása és írása | Microsoft Azure térképek
description: Megtudhatja, hogyan olvashatja és írhatja az információkat a Azure Maps web SDK által biztosított térbeli IO-modul használatával.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 458b307cf1158c467100e032e3f789462e8fdcca
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370907"
---
# <a name="read-and-write-spatial-data"></a>Térbeli információk olvasása és írása

Az alábbi táblázat azokat a térbeli fájlformátumokat sorolja fel, amelyek a térbeli IO-modullal végzett műveletek olvasásához és írásához támogatottak.

| Adatformátum       | Olvasás | Írás |
|-------------------|------|-------|
| A GeoJSON           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| GPX               | ✓  |  ✓  |
| KML               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| Térbeli CSV       | ✓  |  ✓  |
| Jól ismert szöveg   | ✓  |  ✓  |

Ezek a következő fejezetek a térbeli IO modul használatával ismertetik az összes olyan eszközt, amely a térbeli információk olvasására és írására használható.

## <a name="read-spatial-data"></a>Térbeli információk olvasása

A `atlas.io.read` függvény az általános térbeli adatformátumok, például a KML-, GPX-, GeoRSS-, GeoJSON-és CSV-fájlok a térbeli adattal való olvasására szolgáló fő függvény. Ez a függvény a formátumok tömörített verzióit is beolvashatja zip-fájlként vagy KMZ-fájlként. A KMZ fájlformátum a KML tömörített verziója, amely olyan objektumokat is tartalmazhat, mint például a képek. Azt is megteheti, hogy az olvasási függvény olyan URL-címet is igénybe vehet, amely az adott formátumú fájlokra mutat. Az URL-címeket egy CORs-kompatibilis végponton kell tárolni, vagy az olvasási beállításokban proxy szolgáltatást kell megadni. A proxy szolgáltatás a nem CORs engedélyezett tartományokban lévő erőforrások betöltésére szolgál. Az olvasási függvény egy ígéretet ad vissza, amely a képikonokat hozzáadja a térképhez, és aszinkron módon dolgozza fel az adatfeldolgozást a felhasználói felületi szálra gyakorolt hatás csökkentése érdekében.

Tömörített fájl (zip vagy KMZ) beolvasásakor a rendszer kibontja és beolvassa az első érvényes fájlt. Például a doc. KML vagy más érvényes kiterjesztésű fájl, például:. kml,. XML, geojson,. JSON,. csv,. TSV vagy. txt. Ezután a KML-és GeoRSS-fájlokban hivatkozott lemezképek előre be vannak töltve, hogy azok elérhetők legyenek. A nem elérhető képadatok egy másik tartalék képet is betölthetnek, vagy eltávolíthatók a stílusokból. A KMZ-fájlokból kinyert rendszerképeket a rendszer az adaturi-ra konvertálja.

Az olvasási függvény eredménye egy `SpatialDataSet` objektum. Ez az objektum kiterjeszti a GeoJSON FeatureCollection osztályt. Egyszerűen átadható egy `DataSource`, ha a funkcióit egy térképen jeleníti meg. A `SpatialDataSet` nem csupán a szolgáltatással kapcsolatos információkat tartalmaz, de tartalmazhat a KML-fedéseket, a metrikák feldolgozási mérőszámait és az egyéb részleteket is, az alábbi táblázatban leírtak szerint.

| Tulajdonság neve | Típus | Leírás | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Az adatkészletben lévő összes adat határoló mezője. |
| `features` | `Feature[]` | Az adatkészleten belüli GeoJSON-funkciók. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | A KML-GroundOverlays tömbje. |
| `icons` | Rekord&lt;sztring, karakterlánc&gt; | Ikon URL-címek készlete Kulcs = ikon neve, érték = URL. |
| tulajdonságok | bármely | A térbeli adatkészletek dokumentum szintjén megadott tulajdonság-információk. |
| `stats` | `SpatialDataSetStats` | A térbeli adatkészletek tartalmával és feldolgozási idejével kapcsolatos statisztikák. |
| `type` | `'FeatureCollection'` | Csak olvasási GeoJSON típusú érték. |

## <a name="examples-of-reading-spatial-data"></a>Példák a térbeli információk olvasására

A következő kód bemutatja, hogyan olvashat egy egyszerű térbeli adatkészletet, és hogyan jelenítheti meg a térképen a `SimpleDataLayer` osztály használatával. A kód egy GPX-fájlt használ, amelyet egy URL-cím mutat.

<br/>

<iframe height='500' scrolling='no' title='Térbeli adatkészletek egyszerű betöltése' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Az <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) segítségével egyszerűen megtekintheti a toll- <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>betöltési térbeli adathalmazt</a> .
</iframe>

A következő kód bemutatja, hogyan olvashatja és töltheti be a KML-t és a KMZ-t a térképre. A KML tartalmazhat olyan talajközeli átfedéseket, amelyek egy `ImageLyaer` vagy `OgcMapLayer`formájában lesznek láthatók. Ezeket a átfedéseket a szolgáltatásokból külön kell felvenni a térképen. Emellett, ha az adatkészlethez egyéni ikonok tartoznak, ezeket az ikonokat a szolgáltatások betöltése előtt be kell tölteni a Maps-erőforrásokba.

<br/>

<iframe height='500' scrolling='no' title='A KML betöltése a térképen' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Az <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával MEGtekintheti a KML-fájl <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>betöltését a térképre</a> .
</iframe>

Opcionálisan megadhat egy proxy szolgáltatást a tartományok közötti olyan adategységek eléréséhez, amelyekhez nem engedélyezett a CORs. Ez a kódrészlet egy proxy szolgáltatás megadását mutatja be:

```javascript

//Set the location of your proxyServiceUrl file 
var proxyServiceUrl = window.location.origin + '/CorsEnabledProxyService.ashx?url=';

//Read a KML file from a URL or pass in a raw KML string.
atlas.io.read('https://s3-us-west-2.amazonaws.com/s.cdpn.io/1717245/2007SanDiegoCountyFires.kml', {
    //Provide a proxy service
    proxyService: proxyServiceUrl
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

Az alábbi bemutató bemutatja, hogyan olvashatja el a tagolt fájlokat, és hogyan jelenítheti meg a térképen. Ebben az esetben a kód olyan CSV-fájlt használ, amely térbeli adatoszlopokat tartalmaz.

<br/>

<iframe height='500' scrolling='no' title='Tagolt fájl hozzáadása' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>a tollat</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="write-spatial-data"></a>Térbeli adatainak írása

A térbeli IO modulban két fő írási függvény található. A `atlas.io.write` függvény létrehoz egy karakterláncot, míg a `atlas.io.writeCompressed` függvény tömörített zip-fájlt hoz létre. A tömörített ZIP-fájl egy szöveges fájlt tartalmaz, amelyben a térbeli érték szerepel. Mindkét függvény egy ígéretet ad vissza az adatok fájlba való felvételéhez. És mindkét esetben a következő adatok bármelyikét írhatják: `SpatialDataSet`, `DataSource`, `ImageLayer`, `OgcMapLayer`, szolgáltatás-gyűjtemény, szolgáltatás, geometria vagy az adattípusok bármely kombinációjának tömbje. Ha függvények használatával ír, megadhatja a kívánt fájlformátumot. Ha nincs megadva a fájl formátuma, a rendszer a KML-ként írja be az adatfájlokat.

Az alábbi eszköz bemutatja a `atlas.io.write` függvénnyel használható írási lehetőségek többségét.

<br/>

<iframe height='700' scrolling='no' title='Térbeli adatírási beállítások' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>térbeli adatainak írási beállításait</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="example-of-writing-spatial-data"></a>Példa térbeli adatainak írására

A következő minta lehetővé teszi a térbeli fájlok húzását, majd a térképen való betöltését. A térképről exportálhatja a GeoJSON-adatait, és a támogatott térbeli adatformátumok egyikében megírhatja karakterláncként vagy tömörített fájlként is.

<br/>

<iframe height='700' scrolling='no' title='Térbeli fájlok húzása a térképre' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>térbeli fájlok rajzsablonról</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Opcionálisan megadhat egy proxy szolgáltatást a tartományok közötti olyan adategységek eléréséhez, amelyekhez nem engedélyezett a CORs. Ez a kódrészlet egy proxy szolgáltatás beépítését mutatja be:

```javascript

//Set the location of your proxyServiceUrl file 
var proxyServiceUrl = window.location.origin + '/CorsEnabledProxyService.ashx?url=';

function readData(data, fileName) {
    loadingIcon.style.display = '';
    fileCount++;
    //Attempt to parse the file and add the shapes to the map.
    atlas.io.read(data, {
        //Provide a proxy service
        proxyService: proxyServiceUrl
    }).then(
        //Success
        function(r) {
            //some code goes here ...
        }
    );
}
```

## <a name="read-and-write-well-known-text-wkt"></a>Jól ismert szöveg (WKT) olvasása és írása

A [jól ismert Text](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (WKT) egy nyílt TÉRINFORMATIKAI konzorcium (OGC) szabvány, amely a térbeli geometriák szövegként való ábrázolására szolgál. Számos térinformatikai rendszer támogatja a WKT, például az Azure SQL és az Azure PostgreSQL használatát a PostGIS beépülő modullal. A legtöbb OGC-szabványhoz hasonlóan a koordináták "hosszúsági szélesség" formátumban vannak formázva az "x y" konvencióval való igazításhoz. Például a 110-es és a szélességi 45-as pont a WKT formátum használatával írható `POINT(-110 45)`.

A jól ismert szöveg olvasható a `atlas.io.ogc.WKT.read` függvény használatával, és a `atlas.io.ogc.WKT.write` függvénnyel írt.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Példák a jól ismert szöveg olvasására és írására (WKT)

A következő kód bemutatja, hogyan olvashatja el a jól ismert szöveges karakterláncot `POINT(-122.34009 47.60995)` és hogyan jelenítheti meg a térképen a buborék réteg használatával.

<br/>

<iframe height='500' scrolling='no' title='Jól ismert szöveg olvasása' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>olvasott, jól ismert szöveget</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A következő kód a jól ismert szövegek olvasását és írását mutatja be előre és vissza.

<br/>

<iframe height='700' scrolling='no' title='Jól ismert szöveg olvasása és írása' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>olvasás és írás jól ismert szövegét</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="read-and-write-gml"></a>GML olvasása és írása

A GML egy térbeli XML-fájl specifikációja, amelyet gyakran használnak más XML-specifikációk kiterjesztésére. A GeoJSON-alapú adat a `atlas.io.core.GmlWriter.write` függvény használatával XML-ként és GML-címkékkel is írható. A GML tartalmazó XML-fájl olvasható a `atlas.io.core.GmlReader.read` függvény használatával. Az olvasási függvénynek két lehetősége van:

- Az `isAxisOrderLonLat` lehetőség – a koordináták "szélesség, hosszúság" vagy "hosszúsági fok" koordinátáinak tengelyes sorrendje eltérő lehet az adathalmazok között, és nem mindig megfelelően van definiálva. Alapértelmezés szerint a GML-olvasó a "szélesség, hosszúság" értékként beolvassa a koordináta-adatokat, de a beállítás True értékre állítása "hosszúsági fok".
- A `propertyTypes` lehetőség – ez a beállítás egy kulcs értékű keresési táblázat, ahol a kulcs az adathalmazban található tulajdonság neve. Az érték az az Objektumtípus, amelybe a rendszer az elemzés során átadja az értéket. A támogatott típusú értékek a következők: `string`, `number`, `boolean`és `date`. Ha egy tulajdonság nincs a keresés táblában, vagy a típus nincs definiálva, a tulajdonság karakterláncként lesz értelmezve.

A `atlas.io.read` függvény alapértelmezés szerint a `atlas.io.core.GmlReader.read` függvényt fogja észlelni, ha azt észleli, hogy a bemeneti adatok XML-alapúak, de az adatok nem a többi támogatás térbeli XML-formátumának egyike.

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [atlas.io statikus függvények](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

> [!div class="nextstepaction"]
> [SpatialDataSet](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

> [!div class="nextstepaction"]
> [SpatialDataSetStats](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

> [!div class="nextstepaction"]
> [GmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [GmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas. IO. OGC. WKT függvények](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [OGC-Térkép réteg hozzáadása](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Kapcsolódás WFS szolgáltatáshoz](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Alapvető műveletek kihasználása](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Támogatott adatformátum részletei](spatial-io-supported-data-format-details.md)
