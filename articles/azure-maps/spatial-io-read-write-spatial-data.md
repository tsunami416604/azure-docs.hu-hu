---
title: Téradatok olvasása és írása | Microsoft Azure Maps
description: Ismerje meg, hogyan olvashatja és írhatja az adatokat az Azure Maps Web SDK által biztosított Térbeli I/O-modul használatával.
author: philmea
ms.author: philmea
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4c47335689401ebce98224992c74c3396821a1dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334157"
---
# <a name="read-and-write-spatial-data"></a>Téradatok olvasása és írása

Az alábbi táblázat felsorolja azokat a térbeli fájlformátumokat, amelyek a térbeli IO-modullal támogatottak az olvasási és írási műveletekhez.

| Adatformátum       | Olvasás | Írás |
|-------------------|------|-------|
| GeoJSON között           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| Gpx               | ✓  |  ✓  |
| Kml               | ✓  |  ✓  |
| Kmz               | ✓  |  ✓  |
| Térbeli CSV       | ✓  |  ✓  |
| Jól ismert szöveg   | ✓  |  ✓  |

Ezek a következő szakaszok ismertetik a térbeli adatok olvasására és írására szolgáló összes különböző eszközt a térbeli IO-modul használatával.

## <a name="read-spatial-data"></a>Térbeli adatok olvasása

A `atlas.io.read` funkció a fő funkció, amely a téradatokat rendelkező közös térbeli adatformátumok, például A KML, A GPX, a GeoRSS, a GeoJSON és a CSV-fájlok olvasására szolgál. Ez a funkció is olvasni tömörített változatai ezeket a formátumokat, mint egy zip fájlt, vagy egy KMZ fájlt. A KMZ fájlformátum a KML tömörített változata, amely olyan eszközöket is tartalmazhat, mint például a képek. Másik lehetőségként az olvasási függvény olyan URL-címet is bevehet, amely a következő formátumok bármelyikében mutat egy fájlra. Url-címeket kell üzemeltetni egy CORS-kompatibilis végponton, vagy egy proxy szolgáltatást kell biztosítani az olvasási beállításokat. A proxyszolgáltatás olyan tartományok erőforrásainak betöltésére szolgál, amelyek nem CORS-kompatibilisek. Az olvasási függvény azt az ígéretet adja vissza, hogy a képikonokat hozzáadja a térképhez, és aszinkron módon dolgozza fel az adatokat, hogy minimálisra csökkentse a felhasználói felület szálára gyakorolt hatást.

Tömörített fájl –zip vagy KMZ formátumban történő olvasásakor a program kicsomagolja és beolvassa az első érvényes fájlt. Például doc.kml vagy más érvényes kiterjesztésű fájl, például: .kml, .xml, geojson, .json, .csv, .tsv vagy .txt. Ezután a KML- és GeoRSS-fájlokban hivatkozott képek előre betöltődnek, hogy azok elérhetők legyenek. A nem elérhető képadatok betölthetnek egy másik tartalék képet, vagy törlődnek a stílusokból. A KMZ fájlokból kinyert képek adatURI-kká alakulnak át.

Az olvasási függvény eredménye `SpatialDataSet` egy objektum. Ez az objektum kiterjeszti a GeoJSON FeatureCollection osztályt. Könnyen átadható egy `DataSource` as-is teszi a funkciók a térképen. A `SpatialDataSet` nem csak szolgáltatásinformációkat tartalmaz, de tartalmazhat KML földátfedéseket, feldolgozási mutatókat és egyéb részleteket is, ahogy azt az alábbi táblázat ismerteti.

| Tulajdonság neve | Típus | Leírás | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Az adatkészlet összes adatának határolókerete. |
| `features` | `Feature[]` | A GeoJSON az adatkészleten belül található. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | KML GroundOverlay-ek tömbje. |
| `icons` | Rekordkarakterlánc,&lt;karakterlánc&gt; | Ikon URL-ek készlete. Kulcs = ikon neve, Érték = URL. |
| properties | bármely | Térbeli adatkészlet dokumentumszintjén megadott tulajdonságinformációk. |
| `stats` | `SpatialDataSetStats` | A térbeli adatkészlet tartalmára és feldolgozási idejére vonatkozó statisztika. |
| `type` | `'FeatureCollection'` | Írásvédett GeoJSON-típus értéke. |

## <a name="examples-of-reading-spatial-data"></a>Példák térbeli adatok olvasására

A következő kód bemutatja, hogyan lehet olvasni egy térbeli `SimpleDataLayer` adatkészletet, és lehetővé tenni azt a térképen az osztály használatával. A kód egy URL-cím által irányított GPX-fájlt használ.

<br/>

<iframe height='500' scrolling='no' title='Térbeli adatok betöltése egyszerű' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen Load Spatial<a href='https://codepen.io/azuremaps'>@azuremaps</a>Data <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>Simple</a> by Azure Maps ( ) című témakört a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

A következő kódbemutató bemutatja, hogyan olvashatja és töltheti be a KML-t vagy a KMZ-t a térképre. A KML tartalmazhat őrölt átfedéseket, amelyek `ImageLyaer` vagy a `OgcMapLayer`. Ezeket az átfedéseket a funkcióktól elkülönítve kell hozzáadni a térképhez. Továbbá, ha az adatkészlet egyéni ikonokkal rendelkezik, ezeket az ikonokat be kell tölteni a térképek erőforrásaiba, mielőtt a funkciók betöltődnek.

<br/>

<iframe height='500' scrolling='no' title='KML betöltése térképre' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>Tollbetöltés KML-t az</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) térképére a <a href='https://codepen.io'>CodePen-en.</a>
</iframe>

Opcionálisan proxyszolgáltatást is biztosíthat a tartományok közötti olyan eszközök eléréséhez, amelyeken a CORS nincs engedélyezve. Az olvasási függvény először a CORS használatával próbál hozzáférni egy másik tartomány fájljaihoz. Miután az első alkalommal nem fér hozzá egy másik tartomány ban lévő erőforrás cors használatával, akkor csak akkor kér további fájlokat, ha a proxy szolgáltatás biztosított. Az olvasási függvény hozzáfűzi a fájl URL-címét a megadott proxy URL-címének végéhez. Ez a kódrészlet bemutatja, hogyan lehet átadni egy proxyszolgáltatást az olvasási függvénynek:

```javascript
//Read a file from a URL or pass in a raw data as a string.
atlas.io.read('https://nonCorsDomain.example.com/mySuperCoolData.xml', {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

Az alábbi bemutató bemutatja, hogyan kell olvasni egy tagolt fájlt, és hogyan lehet azt a térképen megjeleníteni. Ebben az esetben a kód egy térbeli adatoszlopokat tartalmazó CSV-fájlt használ.

<br/>

<iframe height='500' scrolling='no' title='Tagolt fájl hozzáadása' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a Toll hozzáadása az Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps által <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>deszelt fájlhoz</a> ( ) a <a href='https://codepen.io'>CodePen-en</a>.
</iframe>

## <a name="write-spatial-data"></a>Térbeli adatok írása

A térbeli IO modulban két fő írási funkció található. A `atlas.io.write` függvény karakterláncot hoz `atlas.io.writeCompressed` létre, míg a függvény tömörített zip fájlt hoz létre. A tömörített zip fájl szövegalapú fájlt tartalmaz, amelyben a térbeli adatok szerepelnek. Mindkét függvény egy ígéretet ad az adatok fájlhoz való hozzáadására. És mindkettő a következő adatok bármelyikét `SpatialDataSet` `DataSource`írhatja: , , `ImageLayer`, `OgcMapLayer`jellemzőgyűjtemény, szolgáltatás, geometria vagy ezen adattípusok bármely kombinációjának tömbje. Ha mindkét funkcióval ír, megadhatja a kívánt fájlformátumot. Ha a fájlformátum nincs megadva, akkor az adatok KML-ként lesznek megírva.

Az alábbi eszköz bemutatja a `atlas.io.write` funkcióval használható írási beállítások többségét.

<br/>

<iframe height='700' scrolling='no' title='Térbeli adatok írási beállításai' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg az Azure Maps pen<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>térbeli adatírási beállításait</a> ( ) a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="example-of-writing-spatial-data"></a>Példa térbeli adatok írására

A következő minta lehetővé teszi a húzást, majd a térbeli fájlok betöltését a térképre. A GeoJSON-adatokat exportálhatja a térképről, és a támogatott térbeli adatformátumok egyikében karakterláncként vagy tömörített fájlként írhatja be.

<br/>

<iframe height='700' scrolling='no' title='Térbeli fájlok húzása a térképre' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Toll <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>áthúzással és húzással a térbeli fájlokat az</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) térképére a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

Opcionálisan proxyszolgáltatást is biztosíthat a tartományok közötti olyan eszközök eléréséhez, amelyeken a CORS nincs engedélyezve. Ez a kódrészlet azt mutatja, hogy proxyszolgáltatást építhet be:

```javascript
atlas.io.read(data, {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(
    //Success
    function(r) {
        //some code goes here ...
    }
);
```

## <a name="read-and-write-well-known-text-wkt"></a>Jól ismert szöveg olvasása és írása (WKT)

[A jól ismert szöveg](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (WKT) egy nyílt térinformatikai konzorcium (OGC) szabvány, amely a térbeli geometriákat szövegként jelöli. Számos térinformatikai rendszer támogatja a WKT-t, például az Azure SQL-t és az Azure PostgreSQL-t a PostGIS beépülő modul használatával. A legtöbb OGC szabványhoz hasonlóan a koordináták at "hosszúsági szélességként" formázzák, hogy igazodjanak az "x y" konvencióhoz. Például a -110 hosszúsági és 45 szélességi pont `POINT(-110 45)` wkt formátumban írható.

A jól ismert szöveg olvasható `atlas.io.ogc.WKT.read` a funkció használatával, és a `atlas.io.ogc.WKT.write` függvény használatával írható.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Példák a jól ismert szöveg olvasására és írására (WKT)

A következő kód bemutatja, hogyan lehet `POINT(-122.34009 47.60995)` olvasni a jól ismert szöveges karakterláncot, és hogyan lehet azt a térképen egy buborékréteg segítségével megjeleníteni.

<br/>

<iframe height='500' scrolling='no' title='Jól ismert szöveg olvasása' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Toll <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>olvasása jól ismert szöveget</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A következő kód jól ismert szöveg olvasását és írását mutatja be oda-vissza.

<br/>

<iframe height='700' scrolling='no' title='Jól ismert szöveg olvasása és írása' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Toll <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>olvasását és írását az</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) szerint a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="read-and-write-gml"></a>GML olvasása és írása

A GML egy térbeli XML-fájlspecifikáció, amelyet gyakran használnak más XML-specifikációk kiterjesztéseként. A GeoJSON-adatok xml formátumban írhatók `atlas.io.core.GmlWriter.write` GML-címkékkel a függvény használatával. A GML-t tartalmazó XML `atlas.io.core.GmlReader.read` a függvény használatával olvasható. Az olvasási funkciónak két lehetősége van:

- A `isAxisOrderLonLat` beállítás – A koordináták tengelysorrendje a "szélesség, hosszúság" vagy a "hosszúság, szélesség" adathalmazok között változhat, és nem mindig jól van definiálva. Alapértelmezés szerint a GML-olvasó a koordinátaadatokat "szélesség, hosszúság" ként olvassa be, de ha ezt a beállítást igazra állítja, az "hosszúság, szélesség" lesz.
- A `propertyTypes` beállítás – Ez a beállítás egy kulcsérték-kereshető tábla, ahol a kulcs az adatkészlet egyik tulajdonságának a neve. Az érték az az objektumtípus, amelyre az értéket az elemzés során ki kell vetni. A támogatott típusértékek `string` `number`a `boolean`következők: , , , és `date`. Ha egy tulajdonság nincs a keresti táblában, vagy a típus nincs definiálva, a tulajdonság karakterláncként lesz elemezve.

A `atlas.io.read` függvény alapértelmezés `atlas.io.core.GmlReader.read` szerint a függvény, ha azt észleli, hogy a bemeneti adatok XML, de az adatok nem egy a többi támogatott térbeli XML-formátumok.

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [statikus függvények atlas.io](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

> [!div class="nextstepaction"]
> [SpatialDataSet](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

> [!div class="nextstepaction"]
> [SpatialDataSetStats](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

> [!div class="nextstepaction"]
> [GmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [GmlWriter író](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [atlas.io.ogc.WKT funkciók](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

További kódmintákat a térképekhez további kódmintákért tekintse meg:

> [!div class="nextstepaction"]
> [OGC-térképréteg hozzáadása](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Csatlakozás WFS-szolgáltatáshoz](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Az alapvető műveletek kihasználása](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Támogatott adatformátum részletei](spatial-io-supported-data-format-details.md)
