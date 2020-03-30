---
title: Támogatott adatformátum részletei | Microsoft Azure Maps
description: Ismerje meg, hogyan van elemezve a dekettozott térbeli adatok a térbeli IO-modulban.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3353620f1751e939a04543115fe704555fb3bc21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334083"
---
# <a name="supported-data-format-details"></a>Támogatott adatformátum részletei

Ez a cikk az összes XML-címke és jól ismert szöveggeometria-típus olvasási és írási támogatásának sajátosságait tartalmazza. Azt is részletezi, hogy a dekált térbeli adatok a térbeli IO-modulban elemző.

## <a name="supported-xml-namespaces"></a>Támogatott XML-névterek

A térbeli I/O-modul támogatja az alábbi névterekBŐL származó XML-címkéket.

| Névtér előtag | Névtér URI   | Megjegyzések                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | Csak írásvédett támogatás geoRSS fájlokban.           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | Csak írásvédett támogatás geoRSS fájlokban.                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | Csak olvasható támogatás a GPX-fájlokban. Elemzi és használja a DisplayColor programot. Az alakzat metaadataihoz hozzáadott összes többi tulajdonság. |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | Támogatott GPX fájlokat. Vonalszínt használ. |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | Csak olvasható. GeoRSS írja atom formátumban.              |

## <a name="supported-xml-elements"></a>Támogatott XML-elemek

A térbeli IO-modul a következő XML-elemeket támogatja. A nem támogatott XML-címkék JSON-objektummá alakulnak át. Ezután minden címke a szülőalakzat `properties` vagy -réteg mezőjében tulajdonságként lesz hozzáadva.

### <a name="kml-elements"></a>KML-elemek

A térbeli IO modul a következő KML-elemeket támogatja.

| Elem neve         | Olvasás    | Írás   | Megjegyzések                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | Részleges | igen     | Az objektum elemezve van, de nem használható az alakzat pozicionálásához.                                                                    |
| `AddressDetails`     | Részleges | nem      | Az objektum elemezve van, de nem használható az alakzat pozicionálásához.                                                                    |
| `atom:author`        | igen     | igen     |                                                                                                                            |
| `atom:link`          | igen     | igen     |                                                                                                                            |
| `atom:name`          | igen     | igen     |                                                                                                                            |
| `BalloonStyle`       | Részleges | Részleges | `displayMode`nem támogatott. Átalakítva `PopupTemplate`a. Az íráshoz `popupTemplate` adjon hozzá egy tulajdonságot annak a szolgáltatásnak a tulajdonságaként, amelyhez írni szeretné. |
| `begin`              | igen     | igen     |                                                                                                                            |
| `color`              | igen     | igen     | Tartalmazza `#AABBGGRR` `#BBGGRR`és . CSS színkarakterláncsá elemezve                                                           |
| `colorMode`          | igen     | nem      |                                                                                                                            |
| `coordinates`        | igen     | igen     |                                                                                                                            |
| `Data`               | igen     | igen     |                                                                                                                            |
| `description`        | igen     | igen     |                                                                                                                            |
| `displayName`        | igen     | igen     |                                                                                                                            |
| `Document`           | igen     | igen     |                                                                                                                            |
| `drawOrder`          | Részleges | nem      | Olvassa el a föld fedvények és használt rendezni őket. 
| `east`               | igen     | igen     |                                                                                                                            |
| `end`                | igen     | igen     |                                                                                                                            |
| `ExtendedData`       | igen     | igen     | Támogatja a nem `Data` `SimpleData` gépelt , vagy `Schema`a `$[dataName]`, és az entitás csere az űrlap .                      |
| `extrude`            | Részleges | Részleges | Csak sokszögek esetén támogatott. A különböző magasságú sokszögekkel rendelkező MultiGeometry egyedi jellemzőkre lesz bontva. A vonalstílusok nem támogatottak. A 0-s magasságú sokszögek lapos sokszögként jelennek meg. Olvasáskor a külső gyűrű első koordinátájának magassága a sokszög magassági tulajdonságaként lesz hozzáadva. Ezután az első koordináta magassága lesz használva a sokszög renderelésére a térképen. |
| `fill`               | igen     | igen     |                                                                                                                            |
| `Folder`             | igen     | igen     |                                                                                                                            |
| `GroundOverlay`      | igen     | igen     | `color`nem támogatott                                                                                                   |
| `heading`            | Részleges | nem      | Elemezve, de nem `SimpleDataLayer`renderelte a. Csak akkor ír, ha az alakzat tulajdonságában adatok vannak tárolva.                 |
| `hotSpot`            | igen     | Részleges | Csak akkor ír, ha az alakzat tulajdonságában adatok vannak tárolva. Az egységek kimenete csak "képpontként" lesz kiteljesítve.                         |
| `href`               | igen     | igen     |                                                                                                                            |
| `Icon`               | Részleges | Részleges | Elemezve, de nem `SimpleDataLayer`renderelte a. Csak akkor írja az alakzat ikontulajdonságát, ha URI-adatokat tartalmaz. Csak `href` támogatott. |
| `IconStyle`          | Részleges | Részleges | `icon`, `heading` `colorMode`, `hotspots` és az értékek et elemzi a függvény, de azokat nem a`SimpleDataLayer`         |
| `innerBoundaryIs`    | igen     | igen     |                                                                                                                            |
| `kml`                | igen     | igen     |                                                                                                                            |
| `LabelStyle`         | nem      | nem      |                                                                                                                            |
| `LatLonBox`          | igen     | igen     |                                                                                                                            |
| `gx:LatLonQuad`      | igen     | igen     |                                                                                                                            |
| `LinearRing`         | igen     | igen     |                                                                                                                            |
| `LineString`         | igen     | igen     |                                                                                                                            |
| `LineStyle`          | igen     | igen     | `colorMode`nem támogatott.                                                                                         |
| `Link`               | igen     | nem      | Csak `href` a tulajdonság támogatott hálózati kapcsolatok.                                                                   |
| `MultiGeometry`      | Részleges | Részleges | Olvasás kor az egyes funkciókra bontható.                                                                     |
| `name`               | igen     | igen     |                                                                                                                            |
| `NetworkLink`        | igen     | nem      | A hivatkozásoknak a dokumentummal azonos tartományban kell lenniük.                                                                  |
| `NetworkLinkControl` | nem      | nem      |                                                                                                                            |
| `north`              | igen     | igen     |                                                                                                                            |
| `open`               | igen     | igen     |                                                                                                                            |
| `outerBoundaryIs`    | igen     | igen     |                                                                                                                            |
| `outline`            | igen     | igen     |                                                                                                                            |
| `overlayXY`          | nem      | nem      |                                                                                                                            |
| `Pair`               | Részleges | nem      | Csak `normal` a stílus `StyleMap` támogatott. `highlight`nem támogatott.                                   |
| `phoneNumber`        | igen     | igen     |                                                                                                                            |
| `PhotoOverlay`       | nem      | nem      |                                                                                                                            |
| `Placemark`          | igen     | igen     |                                                                                                                            |
| `Point`              | igen     | igen     |                                                                                                                            |
| `Polygon`            | igen     | igen     |                                                                                                                            |
| `PolyStyle`          | igen     | igen     |                                                                                                                            |
| `Region`             | Részleges | Részleges | `LatLongBox`dokumentum szinten támogatott.                                                                      |
| `rotation`           | nem      | nem      |                                                                                                                            |
| `rotationXY`         | nem      | nem      |                                                                                                                            |
| `scale`              | nem      | nem      |                                                                                                                            |
| `Schema`             | igen     | igen     |                                                                                                                            |
| `SchemaData`         | igen     | igen     |                                                                                                                            |
| `schemaUrl`          | Részleges | igen     | Nem támogatja a kmz-ben nem szereplő külső dokumentumok stílusainak betöltését.                             |
| `ScreenOverlay`      | nem      | nem      |                                                                                                                            |
| `screenXY`           | nem      | nem      |                                                                                                                            |
| `SimpleData`         | igen     | igen     |                                                                                                                            |
| `SimpleField`        | igen     | igen     |                                                                                                                            |
| `size`               | nem      | nem      |                                                                                                                            |
| `Snippet`            | Részleges | Részleges | `maxLines`attribútum figyelmen kívül hagyja.                                                                                  |
| `south`              | igen     | igen     |                                                                                                                            |
| `Style`              | igen     | igen     |                                                                                                                            |
| `StyleMap`           | Részleges | nem      | Csak a normál `StyleMap` stílus támogatott.                                                                        |
| `styleUrl`           | Részleges | igen     | A külső stílusú URL-címek nem támogatottak.                                                                         |
| `text`               | igen     | igen     | `$[geDirections]` A csere nem támogatott                                                                          |
| `textColor`          | igen     | igen     |                                                                                                                            |
| `TimeSpan`           | igen     | igen     |                                                                                                                            |
| `TimeStamp`          | igen     | igen     |                                                                                                                            |
| `value`              | igen     | igen     |                                                                                                                            |
| `viewRefreshMode`    | Részleges | nem      |  Ha egy WMS-szolgáltatásra `onStop` mutat, akkor csak a földalatti átfedések támogatottak. Hozzáfűzi `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` az URL-t, és frissíti, ahogy a térkép mozog.  |
| `visibility`         | igen     | igen     |                                                                                                                            |
| `west`               | igen     | igen     |                                                                                                                            |
| `when`               | igen     | igen     |                                                                                                                            |
| `width`              | igen     | igen     |                                                                                                                            |

### <a name="georss-elements"></a>GeoRSS-elemek

A térbeli IO-modul a következő GeoRSS-elemeket támogatja.

| Elem neve             | Olvasás    | Írás | Megjegyzések                                                                                          |
|--------------------------|---------|-------|------------------------------------------------------------------------------------------------|
| `atom:author`            | igen     | igen   |                                                                                                |
| `atom:category`          | igen     | igen   |                                                                                                |
| `atom:content`           | igen     | igen   |                                                                                                |
| `atom:contributor`       | igen     | igen   |                                                                                                |
| `atom:email`             | igen     | igen   |                                                                                                |
| `atom:entry`             | igen     | igen   |                                                                                                |
| `atom:feed`              | igen     | igen   |                                                                                                |
| `atom:icon`              | igen     | igen   |                                                                                                |
| `atom:id`                | igen     | igen   |                                                                                                |
| `atom:link`              | igen     | igen   |                                                                                                |
| `atom:logo`              | igen     | igen   |                                                                                                |
| `atom:name`              | igen     | igen   |                                                                                                |
| `atom:published`         | igen     | igen   |                                                                                                |
| `atom:rights`            | igen     | igen   |                                                                                                |
| `atom:source`            | igen     | igen   |                                                                                                |
| `atom:subtitle`          | igen     | igen   |                                                                                                |
| `atom:summary`           | igen     | igen   |                                                                                                |
| `atom:title`             | igen     | igen   |                                                                                                |
| `atom:updated`           | igen     | igen   |                                                                                                |
| `atom:uri`               | igen     | igen   |                                                                                                |
| `geo:lat`                | igen     | nem    | Írt, `georss:point`mint a .                                                                   |
| `geo:lon`                | igen     | nem    | Írt, `georss:point`mint a .                                                                   |
| `geo:long`               | igen     | nem    | Írt, `georss:point`mint a .                                                                   |
| `georss:box`             | igen     | nem    | Olvassa el, mint egy `subType` sokszög, és adott egy tulajdonsága "Téglalap"                                |
| `georss:circle`          | igen     | igen   |                                                                                                |
| `georss:elev`            | igen     | igen   |                                                                                                |
| `georss:featurename`     | igen     | igen   |                                                                                                |
| `georss:featuretypetag`  | igen     | igen   |                                                                                                |
| `georss:floor`           | igen     | igen   |                                                                                                |
| `georss:line`            | igen     | igen   |                                                                                                |
| `georss:point`           | igen     | igen   |                                                                                                |
| `georss:polygon`         | igen     | igen   |                                                                                                |
| `georss:radius`          | igen     | igen   |                                                                                                |
| `georss:relationshiptag` | igen     | igen   |                                                                                                |
| `georss:where`           | igen     | igen   |                                                                                                |
| `geourl:latitude`        | igen     | nem    | Írt, `georss:point`mint a .                                                                   |
| `geourl:longitude`       | igen     | nem    | Írt, `georss:point`mint a .                                                                   |
| `position`               | igen     | nem    | Egyes XML-hírcsatornák a GML-t pozíciócímkével `georss:where` csomagolják, nem pedig címkével. Fogja olvasni ezt a címkét, de írni egy `georss:where` tag. |
| `rss`                    | igen     | nem    | GeoRSS aTOM formátumban íródott.                                                                 |
| `rss:author`             | igen     | Részleges | Írt, `atom:author`mint egy .                                                                 |
| `rss:category`           | igen     | Részleges | Írt, `atom:category`mint egy .                                                               |
| `rss:channel`            | igen     | nem    |                                                                                                |
| `rss:cloud`              | igen     | nem    |                                                                                                |
| `rss:comments`           | igen     | nem    |                                                                                                |
| `rss:copyright`          | igen     | Részleges | Ha alakzatként `atom:rights` írt, még `rights` `properties` nincs tulajdonsága.       |
| `rss:description`        | igen     | Részleges | Ha alakzatként `atom:content` írt, még `content` `properties` nincs tulajdonsága.      |
| `rss:docs`               | igen     | nem    |                                                                                                |
| `rss:enclosure`          | igen     | nem    |                                                                                                |
| `rss:generator`          | igen     | nem    |                                                                                                |
| `rss:guid`               | igen     | Részleges | Ha alakzatként `atom:id` írt, még `id` `properties` nincs tulajdonsága.         |
| `rss:image`              | igen     | Részleges | Ha alakzatként `atom:logo` írt, még `logo` `properties` nincs tulajdonsága.      |
| `rss:item`               | igen     | Részleges | Írt, `atom:entry`mint egy .                                                                  |
| `rss:language`           | igen     | nem    |                                                                                                |
| `rss:lastBuildDate`      | igen     | Részleges | Ha alakzatként `atom:updated` írt, még `updated` `properties` nincs tulajdonsága.     |
| `rss:link`               | igen     | Részleges | Írt, `atom:link`mint egy .                                                                   |
| `rss:managingEditor`     | igen     | Részleges | Írt, `atom:contributor`mint egy .                                                            |
| `rss:pubDate`            | igen     | Részleges | Ha alakzatként `atom:published` írt, még `published` `properties` nincs tulajdonsága.  |
| `rss:rating`             | igen     | nem    |                                                                                                |
| `rss:skipDays`           | igen     | nem    |                                                                                                |
| `rss:skipHours`          | igen     | nem    |                                                                                                |
| `rss:source`             | igen     | Részleges | Írt, `atom:source` mint egy `atom:link`tartalmazó .                                       |
| `rss:textInput`          | igen     | nem    |                                                                                                |
| `rss:title`              | igen     | Részleges | Írt, `atom:title`mint egy .                                                                  |
| `rss:ttl`                | igen     | nem    |                                                                                                |
| `rss:webMaster`          | igen     | nem    |                                                                                                |

### <a name="gml-elements"></a>GML-elemek

A térbeli IO modul a következő GML-elemeket támogatja. 

| Elem neve            | Olvasás | Írás | Megjegyzések                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | igen  | nem    | Írásbeli `gml:posList`.                                                              |
| `gml:curveMember`       | igen  | nem    |                                                                                        |
| `gml:curveMembers`      | igen  | nem    |                                                                                        |
| `gml:Box`               | igen  | nem    | Írásbeli `gml:Envelope`.                                                             |
| `gml:description`       | igen  | igen   |                                                                                        |
| `gml:Envelope`          | igen  | igen   |                                                                                        |
| `gml:exterior`          | igen  | igen   |                                                                                        |
| `gml:Feature`           | igen  | nem    | Alakként írva.                                                                    |
| `gml:FeatureCollection` | igen  | nem    | Geometriagyűjteményként írva.                                                      |
| `gml:featureMember`     | igen  | nem    | Geometriagyűjteményként írva.                                                      |
| `gml:geometry`          | igen  | nem    | Alakként írva.                                                                    |
| `gml:geometryMember`    | igen  | igen   |                                                                                        |
| `gml:geometryMembers`   | igen  | igen   |                                                                                        |
| `gml:identifier`        | igen  | igen   |                                                                                        |
| `gml:innerBoundaryIs`   | igen  | nem    | Írásbeli `gml.interior`használatával .                                                          |
| `gml:interior`          | igen  | igen   |                                                                                        |
| `gml:LinearRing`        | igen  | igen   |                                                                                        |
| `gml:LineString`        | igen  | igen   |                                                                                        |
| `gml:lineStringMember`  | igen  | igen   |                                                                                        |
| `gml:lineStringMembers` | igen  | nem    |                                                                                        |
| `gml:MultiCurve`        | igen  | nem    | Csak `gml:LineString` a tagokat olvassa. Írásbeli`gml.MultiLineString`                  |
| `gml:MultiGeometry`     | Részleges  | Részleges   | Csak FeatureCollection-ként olvassa el.                                              |
| `gml:MultiLineString`   | igen  | igen   |                                                                                        |
| `gml:MultiPoint`        | igen  | igen   |                                                                                        |
| `gml:MultiPolygon`      | igen  | igen   |                                                                                        |
| `gml:MultiSurface`      | igen  | nem    | Csak `gml:Polygon` a tagokat olvassa. Írásbeli`gml.MultiPolygon`                        |
| `gml:name`              | igen  | igen   |                                                                                        |
| `gml:outerBoundaryIs`   | igen  | nem    | Írásbeli `gml.exterior`használatával .                                                          |
| `gml:Point`             | igen  | igen   |                                                                                        |
| `gml:pointMember`       | igen  | igen   |                                                                                        |
| `gml:pointMembers`      | igen  | nem    |                                                                                        |
| `gml:Polygon`           | igen  | igen   |                                                                                        |
| `gml:polygonMember`     | igen  | igen   |                                                                                        |
| `gml:polygonMembers`    | igen  | nem    |                                                                                        |
| `gml:pos`               | igen  | igen   |                                                                                        |
| `gml:posList`           | igen  | igen   |                                                                                        |
| `gml:surfaceMember`     | igen  | igen   |                                                                                        |

#### <a name="additional-notes"></a>további megjegyzések

- A tagelemek olyan geometriát keresnek, amely a gyermekelemekben temethető el. Erre a keresési műveletre azért van szükség, mert a GML-ből kinyúló számos XML-formátum nem helyezhet geometriát egy tagelem közvetlen gyermekeként.
- `srsName`a WGS84 koordinátái és a következő kódok esetében támogatott:[EPSG:4326](https://epsg.io/4326)), valamint web Mercator ([EPSG:3857](https://epsg.io/3857) vagy annak egyik alternatív kódja). Minden más koordináta-rendszer WGS84-ként lesz elemezve.
- Ha az XML-hírcsatorna olvasásakor nincs megadva, a tengelysorrend az XML-hírcsatornában lévő tippek alapján kerül meghatározásra. Előnyben részesíti a "szélességi, hosszúsági" tengelysorrendet.
- Ha a GML-fájlba íráskor nincs megadva egyéni GML-névtér a tulajdonságokhoz, a program nem ad hozzá további tulajdonságadatokat.

### <a name="gpx-elements"></a>GPX-elemek

A térbeli IO modul a következő GPX-elemeket támogatja.

| Elem neve             | Olvasás    | Írás   | Megjegyzések                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | igen     | igen     |                                                                                             |
| `gpx:author`             | igen     | igen     |                                                                                             |
| `gpx:bounds`             | igen     | igen     | Olvasáskor LocationRect-vé alakítják.                                                    |
| `gpx:cmt`                | igen     | igen     |                                                                                             |
| `gpx:copyright`          | igen     | igen     |                                                                                             |
| `gpx:desc`               | igen     | igen     | Másolva egy leíró tulajdonságba olvasáskor, hogy igazodjon más XML-formátumokhoz.               |
| `gpx:dgpsid`             | igen     | igen     |                                                                                             |
| `gpx:ele`                | igen     | igen     |                                                                                             |
| `gpx:extensions`         | Részleges | Részleges | Olvasáskor a stílusinformációk kibontása történik. Az összes többi bővítmény egy egyszerű JSON-objektumba lesz lapítva. Csak az alakzatstílus-információk vannak megírva. |
| `gpx:geoidheight`        | igen     | igen     |                                                                                             |
| `gpx:gpx`                | igen     | igen     |                                                                                             |
| `gpx:hdop`               | igen     | igen     |                                                                                             |
| `gpx:link`               | igen     | igen     |                                                                                             |
| `gpx:magvar`             | igen     | igen     |                                                                                             |
| `gpx:metadata`           | igen     | igen     |                                                                                             |
| `gpx:name`               | igen     | igen     |                                                                                             |
| `gpx:pdop`               | igen     | igen     |                                                                                             |
| `gpx:rte`                | igen     | igen     |                                                                                             |
| `gpx:rtept`              | igen     | igen     |                                                                                             |
| `gpx:sat`                | igen     | igen     |                                                                                             |
| `gpx:src`                | igen     | igen     |                                                                                             |
| `gpx:sym`                | igen     | igen     | Az érték rögzítésre kerül, de nem használja a nyomótű ikonjának módosítására.                               |
| `gpx:text`               | igen     | igen     |                                                                                             |
| `gpx:time`               | igen     | igen     |                                                                                             |
| `gpx:trk`                | igen     | igen     |                                                                                             |
| `gpx:trkpt`              | igen     | igen     |                                                                                             |
| `gpx:trkseg`             | igen     | igen     |                                                                                             |
| `gpx:type`               | igen     | igen     |                                                                                             |
| `gpx:vdop`               | igen     | igen     |                                                                                             |
| `gpx:wpt`                | igen     | igen     |                                                                                             |
| `gpx_style:color`        | igen     | igen     |                                                                                             |
| `gpx_style:line`         | Részleges | Részleges | `color`, `opacity` `width`, `lineCap` , támogatottak.                                           |
| `gpx_style:opacity`      | igen     | igen     |                                                                                             |
| `gpx_style:width`        | igen     | igen     |                                                                                             |
| `gpxx:DisplayColor`      | igen     | nem      | Alakzat színének megadására szolgál. Íráskor `gpx_style:line` a program a színt használja.  |
| `gpxx:RouteExtension`    | Részleges | nem      | A beolvassa `properties`az összes tulajdonságot. Csak `DisplayColor` a program használatban van.                     |
| `gpxx:TrackExtension`    | Részleges | nem      | A beolvassa `properties`az összes tulajdonságot. Csak `DisplayColor` a program használatban van.                     |
| `gpxx:WaypointExtension` | Részleges | nem      | A beolvassa `properties`az összes tulajdonságot. Csak `DisplayColor` a program használatban van.                     |
| `gpx:keywords`           | igen     | igen     |                                                                                             |
| `gpx:fix`                | igen     | igen     |                                                                                             |

#### <a name="additional-notes"></a>további megjegyzések

Írásban;

- A MultiPoints pontokat egyes útpontokra bontjuk.
- A sokszögek és a Multisokonok számokként lesznek megírva. 
  
## <a name="supported-well-known-text-geometry-types"></a>Támogatott, jól ismert szöveggeometria-típusok

| Geometria típusa | Olvasás | Írás |
|--------------|:----:|:-----:|
| Pont | x | x |
| Z. pont | x | x | 
| M. pont | x | x<sup>[2]</sup> |
| Pont ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| VONALZSINÓR | x | x |
| LINESTRING Z | x | x | 
| LINESTRING M | x | x<sup>[2]</sup> |
| VONALZSINÓR ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Sokszög | x | x |
| Poligon Z | x | x |
| Poligon M | x | x<sup>[2]</sup> |
| Poligon ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Multipont | x | x |
| MULTIPOINT Z | x | x | 
| MULTIPOINT M | x | x<sup>[2]</sup> |
| POMULTIPOINTINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| TÖBBSOROS KARAKTERLÁNC | x | x |
| TöbbSOROS HÚRZ | x | x | 
| TÖBBSOROS M | x | x<sup>[2]</sup> |
| TÖBBSOROS HÚROs ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| POLIPOLIFON | x | x |
| MULTIPOLINE Z | x | x | 
| MULTIPOLINE M | x | x<sup>[2]</sup> |
| MULTIPOLINE ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| GEOMETRIAGYŰJTEMÉNY | x | x |
| GEOMETRIACOLLECTION Z | x | x | 
| M. GEOMETRIAGYŰJTEMÉNY | x | x<sup>[2]</sup> | 
| GEOMETRIACOLLECTION ZM | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1\] A program csak z paramétert rögzít és ad hozzá harmadik értékként a Pozíció értékben.

\[A\] 2 M paraméter nincs rögzítve.

## <a name="delimited-spatial-data-support"></a>Korlátozott térbeli adatok támogatása

A tagolt térbeli adatok, például a vesszővel tagolt értékfájlok (CSV) gyakran térbeli adatokat tartalmazó oszlopokkal rendelkeznek. Lehetnek például olyan oszlopok, amelyek szélességi és hosszúsági adatokat tartalmaznak. Jól ismert szöveg formátumban lehet egy térbeli geometriai adatokat tartalmazó oszlop.

### <a name="spatial-data-column-detection"></a>Térbeli adatoszlop észlelése

Téradatokat tartalmazó tagolt fájl olvasásakor a program elemzi a fejlécet annak meghatározásához, hogy mely oszlopok tartalmazzák a helymezőket. Ha a fejléc típusadatokat tartalmaz, a cellaértékek et a megfelelő típusra kell leadni. Ha nincs megadva fejléc, a lesz elemezve az első sor, és a fejléc létrehozásához használható. Az első sor elemzésekor a program egy ellenőrzést hajt végre, amely az oszlopneveket a következő nevekkel egyezteti, a kis- és nagybetűk et nem figyelembe vevő módon. A nevek sorrendje a prioritás, abban az esetben, ha két vagy több név van egy fájlban.

#### <a name="latitude"></a>Szélesség

- `latitude`
- `lat`
- `latdd`
- `lat_dd`
- `latitude83`
- `latdecdeg`
- `y`
- `ycenter`
- `point-y`

#### <a name="longitude"></a>Hosszúság

- `longitude`
- `lon`
- `lng`
- `long`
- `longdd`
- `long_dd`
- `longitude83`
- `longdecdeg`
- `x`
- `xcenter`
- `point-x`

#### <a name="elevation"></a>Magasság

- `elevation`
- `elv`
- `altitude`
- `alt`
- `z`

#### <a name="geography"></a>Földrajzi hely

A program az adatok első sorát jól ismert szöveg formátumú karakterláncokra vizsgálja. 

### <a name="delimited-data-column-types"></a>Tagolt adatoszlop-típusok

A fejlécsor beolvasásakor a program kivonja az oszlopnévben szereplő szövegadatokat, és az adott oszlop celláinak leadására használja fel. Íme egy példa egy oszlopnévre, amelynek típusértéke: "ColumnName (typeName)". A következő, kis- és nagybetűket nem megkülönböztető típusnevek támogatottak:

#### <a name="numbers"></a>Számok

- edm.int64
- int
- long
- edm.double
- lebegőpontos
- double
- szám

#### <a name="booleans"></a>Logikai változók

- edm.boolean
- Bool
- logikai

#### <a name="dates"></a>Dátumok

- edm.datetime
- dátum
- dátum/idő

#### <a name="geography"></a>Földrajzi hely

- edm.földrajz
- Földrajz

#### <a name="strings"></a>Sztringek

- edm.string
- varchar
- szöveg
- sztring

Ha a fejlécből nem lehet szövegadatokat kinyerni, és a dinamikus gépelési beállítás engedélyezve van olvasáskor, akkor minden cella külön-külön lesz elemezve, hogy meghatározza, milyen adattípusra alkalmas a legalkalmasabb az öntéshez.

## <a name="next-steps"></a>További lépések

További kódmintákat a térképekhez további kódmintákért tekintse meg:

> [!div class="nextstepaction"]
> [Téradatok olvasása és írása](spatial-io-read-write-spatial-data.md)
