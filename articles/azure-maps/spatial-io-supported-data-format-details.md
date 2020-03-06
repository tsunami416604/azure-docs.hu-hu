---
title: Támogatott adatformátum részletei | Microsoft Azure térképek
description: Ismerje meg, hogyan történik a tagolt térbeli adatainak elemzése a térbeli IO-modulban.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cbeb1ccaed6ec2c62aaa731e88fcefbe84d1df02
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370881"
---
# <a name="supported-data-format-details"></a>Támogatott adatformátum részletei

Ez a cikk az összes XML-címke és a jól ismert szöveg-geometriai típusok olvasási és írási támogatásának részletes leírását tartalmazza. Azt is részletezi, hogyan történik a tagolt térbeli adatok elemzése a térbeli IO-modulban.

## <a name="supported-xml-namespaces"></a>Támogatott XML-névterek

A térbeli IO-modul támogatja az XML-címkéket a következő névterekben.

| Névtér előtagja | Névtér URI-ja   | Megjegyzések                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | Csak olvasható támogatás a GeoRSS-fájlokban.           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | Csak olvasható támogatás a GeoRSS-fájlokban.                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | Csak olvasható támogatás a GPX-fájlokban. Elemzi és használja az DisplayColor-t. Az alakzat metaadataihoz hozzáadott összes többi tulajdonság. |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | A GPX-fájlok támogatják. A vonal színét használja. |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | Csak olvasható. GeoRSS az Atom formátum használatával.              |

## <a name="supported-xml-elements"></a>Támogatott XML-elemek

A térbeli IO-modul a következő XML-elemeket támogatja. A nem támogatott XML-címkék egy JSON-objektumba lesznek konvertálva. Ezt követően a rendszer a szülő alakzat vagy réteg `properties` mezőjében minden címkét hozzáad a tulajdonsághoz.

### <a name="kml-elements"></a>KML-elemek

A térbeli IO-modul a következő KML-elemeket támogatja.

| Elem neve         | Olvasás    | Írás   | Megjegyzések                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | részleges | igen     | Az objektum elemzése megtörtént, de a pozicionálási alakzat nem használja.                                                                    |
| `AddressDetails`     | részleges | nem      | Az objektum elemzése megtörtént, de a pozicionálási alakzat nem használja.                                                                    |
| `atom:author`        | igen     | igen     |                                                                                                                            |
| `atom:link`          | igen     | igen     |                                                                                                                            |
| `atom:name`          | igen     | igen     |                                                                                                                            |
| `BalloonStyle`       | részleges | részleges | a `displayMode` nem támogatott. `PopupTemplate`ra konvertálva. Az íráshoz vegyen fel egy `popupTemplate` tulajdonságot annak a szolgáltatásnak a tulajdonságára, amelybe írni szeretné. |
| `begin`              | igen     | igen     |                                                                                                                            |
| `color`              | igen     | igen     | `#AABBGGRR` és `#BBGGRR`is tartalmaz. Elemzés egy CSS-szín sztringbe                                                           |
| `colorMode`          | igen     | nem      |                                                                                                                            |
| `coordinates`        | igen     | igen     |                                                                                                                            |
| `Data`               | igen     | igen     |                                                                                                                            |
| `description`        | igen     | igen     |                                                                                                                            |
| `displayName`        | igen     | igen     |                                                                                                                            |
| `Document`           | igen     | igen     |                                                                                                                            |
| `drawOrder`          | részleges | nem      | Olvassa el a befedéseket, és rendezze őket. 
| `east`               | igen     | igen     |                                                                                                                            |
| `end`                | igen     | igen     |                                                                                                                            |
| `ExtendedData`       | igen     | igen     | Támogatja a nem típusos `Data`, `SimpleData` vagy `Schema`, valamint az űrlap entitás-visszahelyezését `$[dataName]`.                      |
| `extrude`            | részleges | részleges | Csak a sokszögek esetében támogatott. A különböző magasságú sokszögekkel rendelkező többgeometriás elemek az egyes funkciókban lesznek kiosztva. A vonalstílus nem támogatottak. A 0 magasságú sokszögek sima sokszögként lesznek megjelenítve. Olvasáskor a rendszer a külső gyűrű első koordinátája magasságát hozzáadja a sokszög height tulajdonságához. Ezt követően az első koordináta magasságát fogja használni a rendszer a sokszög megjelenítéséhez a térképen. |
| `fill`               | igen     | igen     |                                                                                                                            |
| `Folder`             | igen     | igen     |                                                                                                                            |
| `GroundOverlay`      | igen     | igen     | a `color` nem támogatott                                                                                                   |
| `heading`            | részleges | nem      | Elemezte, de a `SimpleDataLayer`nem jeleníti meg. Csak azt írja, hogy a rendszer az alakzat tulajdonságában tárolja-e az adatot.                 |
| `hotSpot`            | igen     | részleges | Csak azt írja, hogy a rendszer az alakzat tulajdonságában tárolja-e az adatot. Az egységek csak "képpont"-ként vannak megadva.                         |
| `href`               | igen     | igen     |                                                                                                                            |
| `Icon`               | részleges | részleges | Elemezte, de a `SimpleDataLayer`nem jeleníti meg. Csak akkor írja be az alakzat Icon tulajdonságát, ha URI-adatot tartalmaz. Csak `href` támogatott. |
| `IconStyle`          | részleges | részleges | `icon`, `heading`, `colorMode`és `hotspots` értékeket elemez a rendszer, de a `SimpleDataLayer` nem jeleníti meg őket.         |
| `innerBoundaryIs`    | igen     | igen     |                                                                                                                            |
| `kml`                | igen     | igen     |                                                                                                                            |
| `LabelStyle`         | nem      | nem      |                                                                                                                            |
| `LatLonBox`          | igen     | igen     |                                                                                                                            |
| `gx:LatLonQuad`      | igen     | igen     |                                                                                                                            |
| `LinearRing`         | igen     | igen     |                                                                                                                            |
| `LineString`         | igen     | igen     |                                                                                                                            |
| `LineStyle`          | igen     | igen     | a `colorMode` nem támogatott.                                                                                         |
| `Link`               | igen     | nem      | A hálózati kapcsolatok csak a `href` tulajdonságot támogatják.                                                                   |
| `MultiGeometry`      | részleges | részleges | Az olvasáskor az egyes funkciókban is kibonthatók.                                                                     |
| `name`               | igen     | igen     |                                                                                                                            |
| `NetworkLink`        | igen     | nem      | A hivatkozásoknak a dokumentummal megegyező tartományba kell esniük.                                                                  |
| `NetworkLinkControl` | nem      | nem      |                                                                                                                            |
| `north`              | igen     | igen     |                                                                                                                            |
| `open`               | igen     | igen     |                                                                                                                            |
| `outerBoundaryIs`    | igen     | igen     |                                                                                                                            |
| `outline`            | igen     | igen     |                                                                                                                            |
| `overlayXY`          | nem      | nem      |                                                                                                                            |
| `Pair`               | részleges | nem      | Csak a `StyleMap` `normal` stílusa támogatott. a `highlight` nem támogatott.                                   |
| `phoneNumber`        | igen     | igen     |                                                                                                                            |
| `PhotoOverlay`       | nem      | nem      |                                                                                                                            |
| `Placemark`          | igen     | igen     |                                                                                                                            |
| `Point`              | igen     | igen     |                                                                                                                            |
| `Polygon`            | igen     | igen     |                                                                                                                            |
| `PolyStyle`          | igen     | igen     |                                                                                                                            |
| `Region`             | részleges | részleges | a `LatLongBox` a dokumentum szintjén támogatott.                                                                      |
| `rotation`           | nem      | nem      |                                                                                                                            |
| `rotationXY`         | nem      | nem      |                                                                                                                            |
| `scale`              | nem      | nem      |                                                                                                                            |
| `Schema`             | igen     | igen     |                                                                                                                            |
| `SchemaData`         | igen     | igen     |                                                                                                                            |
| `schemaUrl`          | részleges | igen     | Nem támogatja a KMZ-ban nem szereplő külső dokumentumok stílusának betöltését.                             |
| `ScreenOverlay`      | nem      | nem      |                                                                                                                            |
| `screenXY`           | nem      | nem      |                                                                                                                            |
| `SimpleData`         | igen     | igen     |                                                                                                                            |
| `SimpleField`        | igen     | igen     |                                                                                                                            |
| `size`               | nem      | nem      |                                                                                                                            |
| `Snippet`            | részleges | részleges | `maxLines` attribútumot a rendszer figyelmen kívül hagyja.                                                                                  |
| `south`              | igen     | igen     |                                                                                                                            |
| `Style`              | igen     | igen     |                                                                                                                            |
| `StyleMap`           | részleges | nem      | A `StyleMap` csak a normál stílus támogatott.                                                                        |
| `styleUrl`           | részleges | igen     | A külső stílusú URL-címek nem támogatottak.                                                                         |
| `text`               | igen     | igen     | `$[geDirections]` cseréje nem támogatott                                                                          |
| `textColor`          | igen     | igen     |                                                                                                                            |
| `TimeSpan`           | igen     | igen     |                                                                                                                            |
| `TimeStamp`          | igen     | igen     |                                                                                                                            |
| `value`              | igen     | igen     |                                                                                                                            |
| `viewRefreshMode`    | részleges | nem      |  Ha egy WMS szolgáltatásra mutat, a rendszer csak a `onStop` támogatja. Hozzáfűzi a `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` az URL-címhez, és a térképet helyezi át a frissítéshez.  |
| `visibility`         | igen     | igen     |                                                                                                                            |
| `west`               | igen     | igen     |                                                                                                                            |
| `when`               | igen     | igen     |                                                                                                                            |
| `width`              | igen     | igen     |                                                                                                                            |

### <a name="georss-elements"></a>GeoRSS elemek

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
| `geo:lat`                | igen     | nem    | `georss:point`ként íródott.                                                                   |
| `geo:lon`                | igen     | nem    | `georss:point`ként íródott.                                                                   |
| `geo:long`               | igen     | nem    | `georss:point`ként íródott.                                                                   |
| `georss:box`             | igen     | nem    | Olvasás sokszögként, és a "téglalap" `subType` tulajdonsága                                |
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
| `geourl:latitude`        | igen     | nem    | `georss:point`ként íródott.                                                                   |
| `geourl:longitude`       | igen     | nem    | `georss:point`ként íródott.                                                                   |
| `position`               | igen     | nem    | Egyes XML-hírcsatornák betakarják a GML egy GeoRSS: where címkével. A beolvassa ezt a címkét, de a következőt fogja írni egy GeoRSS: where címke használatával. |
| `rss`                    | igen     | nem    | Az ATOM formátumú GeoRSS.                                                                 |
| `rss:author`             | igen     | részleges | `atom:author`ként íródott.                                                                 |
| `rss:category`           | igen     | részleges | `atom:category`ként íródott.                                                               |
| `rss:channel`            | igen     | nem    |                                                                                                |
| `rss:cloud`              | igen     | nem    |                                                                                                |
| `rss:comments`           | igen     | nem    |                                                                                                |
| `rss:copyright`          | igen     | részleges | `atom:rights`, ha az alakzatnak nincs `rights` `properties` tulajdonsága.       |
| `rss:description`        | igen     | részleges | `atom:content`, ha az alakzatnak nincs `content` `properties` tulajdonsága.      |
| `rss:docs`               | igen     | nem    |                                                                                                |
| `rss:enclosure`          | igen     | nem    |                                                                                                |
| `rss:generator`          | igen     | nem    |                                                                                                |
| `rss:guid`               | igen     | részleges | `atom:id`, ha az alakzat már nem rendelkezik `id` `properties` tulajdonsággal.         |
| `rss:image`              | igen     | részleges | `atom:logo`, ha az alakzatnak nincs `logo` `properties` tulajdonsága.      |
| `rss:item`               | igen     | részleges | `atom:entry`ként íródott.                                                                  |
| `rss:language`           | igen     | nem    |                                                                                                |
| `rss:lastBuildDate`      | igen     | részleges | `atom:updated`, ha az alakzat már nem rendelkezik `updated` `properties` tulajdonsággal.     |
| `rss:link`               | igen     | részleges | `atom:link`ként íródott.                                                                   |
| `rss:managingEditor`     | igen     | részleges | `atom:contributor`ként íródott.                                                            |
| `rss:pubDate`            | igen     | részleges | `atom:published`, ha az alakzatnak nincs `published` `properties` tulajdonsága.  |
| `rss:rating`             | igen     | nem    |                                                                                                |
| `rss:skipDays`           | igen     | nem    |                                                                                                |
| `rss:skipHours`          | igen     | nem    |                                                                                                |
| `rss:source`             | igen     | részleges | `atom:link`t tartalmazó `atom:source` íródott.                                       |
| `rss:textInput`          | igen     | nem    |                                                                                                |
| `rss:title`              | igen     | részleges | `atom:title`ként íródott.                                                                  |
| `rss:ttl`                | igen     | nem    |                                                                                                |
| `rss:webMaster`          | igen     | nem    |                                                                                                |

### <a name="gml-elements"></a>GML elemek

A térbeli IO-modul a következő GML-elemeket támogatja. 

| Elem neve            | Olvasás | Írás | Megjegyzések                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | igen  | nem    | `gml:posList`ként íródott.                                                              |
| `gml:curveMember`       | igen  | nem    |                                                                                        |
| `gml:curveMembers`      | igen  | nem    |                                                                                        |
| `gml:Box`               | igen  | nem    | `gml:Envelope`ként íródott.                                                             |
| `gml:description`       | igen  | igen   |                                                                                        |
| `gml:Envelope`          | igen  | igen   |                                                                                        |
| `gml:exterior`          | igen  | igen   |                                                                                        |
| `gml:Feature`           | igen  | nem    | Alakzatként írva.                                                                    |
| `gml:FeatureCollection` | igen  | nem    | Geometriai gyűjteményként íródott.                                                      |
| `gml:featureMember`     | igen  | nem    | Geometriai gyűjteményként íródott.                                                      |
| `gml:geometry`          | igen  | nem    | Alakzatként írva.                                                                    |
| `gml:geometryMember`    | igen  | igen   |                                                                                        |
| `gml:geometryMembers`   | igen  | igen   |                                                                                        |
| `gml:identifier`        | igen  | igen   |                                                                                        |
| `gml:innerBoundaryIs`   | igen  | nem    | Írás a `gml.interior`használatával.                                                          |
| `gml:interior`          | igen  | igen   |                                                                                        |
| `gml:LinearRing`        | igen  | igen   |                                                                                        |
| `gml:LineString`        | igen  | igen   |                                                                                        |
| `gml:lineStringMember`  | igen  | igen   |                                                                                        |
| `gml:lineStringMembers` | igen  | nem    |                                                                                        |
| `gml:MultiCurve`        | igen  | nem    | Csak `gml:LineString` tagok olvasása. Írásbeli `gml.MultiLineString`                  |
| `gml:MultiGeometry`     | részleges  | részleges   | Csak FeatureCollection olvasása.                                              |
| `gml:MultiLineString`   | igen  | igen   |                                                                                        |
| `gml:MultiPoint`        | igen  | igen   |                                                                                        |
| `gml:MultiPolygon`      | igen  | igen   |                                                                                        |
| `gml:MultiSurface`      | igen  | nem    | Csak `gml:Polygon` tagok olvasása. Írásbeli `gml.MultiPolygon`                        |
| `gml:name`              | igen  | igen   |                                                                                        |
| `gml:outerBoundaryIs`   | igen  | nem    | Írás a `gml.exterior`használatával.                                                          |
| `gml:Point`             | igen  | igen   |                                                                                        |
| `gml:pointMember`       | igen  | igen   |                                                                                        |
| `gml:pointMembers`      | igen  | nem    |                                                                                        |
| `gml:Polygon`           | igen  | igen   |                                                                                        |
| `gml:polygonMember`     | igen  | igen   |                                                                                        |
| `gml:polygonMembers`    | igen  | nem    |                                                                                        |
| `gml:pos`               | igen  | igen   |                                                                                        |
| `gml:posList`           | igen  | igen   |                                                                                        |
| `gml:surfaceMember`     | igen  | igen   |                                                                                        |

#### <a name="additional-notes"></a>További megjegyzések

- A rendszer megkeresi a tag elemeit egy olyan geometriában, amely a gyermek elemek között eltemethető. Erre a keresési műveletre azért van szükség, mert a GML-ből kiterjesztő számos XML-formátum esetében nem lehet a tag elem közvetlen gyermekének geometriáját elhelyezni.
- a `srsName` részlegesen támogatott a WGS84 koordinátáihoz, valamint a következő kódokhoz:[EPSG: 4326](https://epsg.io/4326)) és web Mercator ([EPSG: 3857](https://epsg.io/3857) vagy az egyik alternatív kód. Minden más koordináta-rendszer a WGS84-ként lesz értelmezve.
- Az XML-hírcsatornák olvasásakor a tengely sorrendjét az XML-hírcsatorna mutatói alapján határozzák meg. A "szélesség, hosszúság" tengely sorrendjére van megadva a beállítás.
- Ha nem ad meg egyéni GML-névteret a tulajdonságokhoz a GML-fájl írásakor, a további tulajdonságértékek nem lesznek hozzáadva.

### <a name="gpx-elements"></a>GPX elemek

A térbeli IO-modul a következő GPX-elemeket támogatja.

| Elem neve             | Olvasás    | Írás   | Megjegyzések                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | igen     | igen     |                                                                                             |
| `gpx:author`             | igen     | igen     |                                                                                             |
| `gpx:bounds`             | igen     | igen     | LocationRect konvertálva olvasáskor.                                                    |
| `gpx:cmt`                | igen     | igen     |                                                                                             |
| `gpx:copyright`          | igen     | igen     |                                                                                             |
| `gpx:desc`               | igen     | igen     | Egy Description (Leírás) tulajdonságba másolva, ha az olvasás gombra más XML-formátumokkal való igazításra               |
| `gpx:dgpsid`             | igen     | igen     |                                                                                             |
| `gpx:ele`                | igen     | igen     |                                                                                             |
| `gpx:extensions`         | részleges | részleges | Olvasáskor a rendszer kinyeri a stílussal kapcsolatos adatokat. Az összes többi bővítmény egy egyszerű JSON-objektumba lesz lelapulva. Csak az alakzat stílusa információ írása. |
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
| `gpx:sym`                | igen     | igen     | Az érték rögzítése megtörténik, de nem a gombostű ikon módosítására szolgál.                               |
| `gpx:text`               | igen     | igen     |                                                                                             |
| `gpx:time`               | igen     | igen     |                                                                                             |
| `gpx:trk`                | igen     | igen     |                                                                                             |
| `gpx:trkpt`              | igen     | igen     |                                                                                             |
| `gpx:trkseg`             | igen     | igen     |                                                                                             |
| `gpx:type`               | igen     | igen     |                                                                                             |
| `gpx:vdop`               | igen     | igen     |                                                                                             |
| `gpx:wpt`                | igen     | igen     |                                                                                             |
| `gpx_style:color`        | igen     | igen     |                                                                                             |
| `gpx_style:line`         | részleges | részleges | `color`, `opacity`, `width`, `lineCap` támogatottak.                                           |
| `gpx_style:opacity`      | igen     | igen     |                                                                                             |
| `gpx_style:width`        | igen     | igen     |                                                                                             |
| `gpxx:DisplayColor`      | igen     | nem      | Egy alakzat színének megadására szolgál. Íráskor a rendszer `gpx_style:line` színt fog használni.  |
| `gpxx:RouteExtension`    | részleges | nem      | Az összes tulajdonság beolvasva a `properties`ba. Csak `DisplayColor` van használatban.                     |
| `gpxx:TrackExtension`    | részleges | nem      | Az összes tulajdonság beolvasva a `properties`ba. Csak `DisplayColor` van használatban.                     |
| `gpxx:WaypointExtension` | részleges | nem      | Az összes tulajdonság beolvasva a `properties`ba. Csak `DisplayColor` van használatban.                     |
| `gpx:keywords`           | igen     | igen     |                                                                                             |
| `gpx:fix`                | igen     | igen     |                                                                                             |

#### <a name="additional-notes"></a>További megjegyzések

Íráskor;

- A többpontos bontás az egyes iránypontokba kerül.
- A sokszögeket és a többsokszögű fájlokat zeneszámként fogja írni. 
  
## <a name="supported-well-known-text-geometry-types"></a>Támogatott jól ismert szöveg-geometriai típusok

| Geometria típusa | Olvasás | Írás |
|--------------|:----:|:-----:|
| PONT | x | x |
| Z. PONT | x | x | 
| M PONT | x | x<sup>[2]</sup> |
| ZM-PONT | x<sup>[1]</sup><sup>[2]</sup> | | 
| LINESTRING | x | x |
| LINESTRING Z | x | x | 
| LINESTRING M | x | x<sup>[2]</sup> |
| LINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| SOKSZÖG | x | x |
| SOKSZÖG – Z | x | x |
| SOKSZÖG M | x | x<sup>[2]</sup> |
| SOKSZÖG ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| TÖBBPONTOS | x | x |
| MULTIPOINT – Z | x | x | 
| MULTIPOINT M | x | x<sup>[2]</sup> |
| POMULTIPOINTINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTILINESTRING | x | x |
| MULTILINESTRING Z | x | x | 
| MULTILINESTRING M | x | x<sup>[2]</sup> |
| MULTILINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOLYGON | x | x |
| TÖBBSOKSZÖGŰ Z | x | x | 
| TÖBBSOKSZÖGŰ M | x | x<sup>[2]</sup> |
| TÖBBSOKSZÖGES ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| GEOMETRYCOLLECTION | x | x |
| GEOMETRYCOLLECTION Z | x | x | 
| GEOMETRYCOLLECTION M | x | x<sup>[2]</sup> | 
| GEOMETRYCOLLECTION ZM | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1\] csak a Z paramétert rögzíti a rendszer, és harmadik értékként adja hozzá a pozíció értékét.

\[2\] M paraméter nincs rögzítve.

## <a name="delimited-spatial-data-support"></a>Tagolt térbeli adattámogatás

A tagolt térbeli adatok, például a vesszővel tagolt fájlok (CSV), gyakran tartalmaznak térbeli adatokkal rendelkező oszlopokat. Lehetnek például olyan oszlopok, amelyek szélességi és hosszúsági adatokat tartalmaznak. A jól ismert szöveges formátumban lehet olyan oszlop, amely térbeli geometriai adatokból álló oszlopot tartalmaz.

### <a name="spatial-data-column-detection"></a>Térbeli adatoszlopok észlelése

Térbeli értéket tartalmazó tagolt fájl olvasása esetén a rendszer elemzi a fejlécet annak meghatározására, hogy mely oszlopok tartalmaznak hely mezőket. Ha a fejléc típusú adatokat tartalmaz, a rendszer a cella értékeit a megfelelő típusra fogja használni. Ha nincs megadva fejléc, az első sor elemzése megtörténik, és a rendszer a fejléc létrehozásához használja. Az első sor elemzésekor a rendszer egy olyan ellenőrzési műveletet hajt végre, amely kis-és nagybetűket nem megkülönböztető módon egyezteti az oszlopnevek nevét. A nevek sorrendje a prioritás, abban az esetben, ha két vagy több név létezik egy fájlban.

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

#### <a name="elevation"></a>Jogosultságszint

- `elevation`
- `elv`
- `altitude`
- `alt`
- `z`

#### <a name="geography"></a>Földrajz

Az első sor a jól ismert szöveges formátumú karakterláncok esetében lesz megvizsgálva. 

### <a name="delimited-data-column-types"></a>Tagolt adatoszlopok típusai

A fejlécsor vizsgálatakor a rendszer az oszlop nevében található bármely típusú információt kinyeri és felhasználja a celláknak az adott oszlopban való elküldéséhez. Az alábbi példa egy olyan oszlopnevet mutat be, amely egy Type értékkel rendelkezik: "ColumnName (typeName)". A következő kis-és nagybetűket megkülönböztető típusú nevek támogatottak:

**Számok**

    - EDM. Int64
    - int
    - hosszú
    - EDM. Double
    - lebegőpontos
    - double
    - szám

**Értékei**

    - EDM. Boolean
    - bool
    - logikai

**Dátumokra**

    - EDM. datetime
    - dátum
    - dátum/idő

**Régiócsoport**

    - EDM. földrajz
    - Földrajz

**Karakterláncok**

    - EDM. String
    - varchar
    - szöveg
    - Case sztring

Ha a fejlécből nem lehet kinyerni a típus adatait, és a dinamikus gépelési beállítás be van kapcsolva az olvasáskor, akkor minden egyes cella külön lesz elemezve annak meghatározásához, hogy melyik adattípust érdemes a legjobban átadni.

## <a name="next-steps"></a>Következő lépések

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Térbeli információk olvasása és írása](spatial-io-read-write-spatial-data.md)
