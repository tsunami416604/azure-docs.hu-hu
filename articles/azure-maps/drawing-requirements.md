---
title: A csomagra vonatkozó követelmények a Azure Maps Creatorban
description: A Azure Maps átalakítási szolgáltatással megtudhatja, hogyan alakíthatja át a létesítmény fájljait az adatleképezésre a rajzi csomag követelményeivel
author: anastasia-ms
ms.author: v-stharr
ms.date: 6/09/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: cb34cb386939fc1160ee5a7db0007cfbf500ccb8
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84660616"
---
# <a name="drawing-package-requirements"></a>Rajzolási csomag követelményei

A [Azure Maps konverziós szolgáltatás](https://docs.microsoft.com/rest/api/maps/conversion) lehetővé teszi a feltöltött rajzok leképezési adatként való átalakítását. Ez a cikk az átalakítási API-hoz tartozó rajzi csomagok követelményeit ismerteti. Egy minta csomag megtekintéséhez letöltheti a minta [rajzolási csomagot](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="prerequisites"></a>Előfeltételek

A rajzfájl DWG formátumban mentett rajzokat tartalmaz, amely az Autodesk AutoCAD® szoftver natív fájlformátuma, az [Autodesk Inc. védjegye](https://www.autodesk.com/company/legal-notices-trademarks/trademarks/guidelines-for-use#section12).

Bármelyik CAD-szoftvert kiválaszthatja a rajzok létrehozásához a rajzolási csomagban.  

A [Azure Maps átalakítási szolgáltatás](https://docs.microsoft.com/rest/api/maps/conversion) átalakítja a rajzolási csomagot a leképezési adatként.  Az átalakítási szolgáltatás az AutoCAD DWG fájlformátum használatával lett kifejlesztve és tesztelve. `AC1032`a DWG-fájlok belső formátumú verziója. Javasoljuk, hogy válassza `AC1032` a belső DWG fájlformátum verzióját.  

A dokumentumban használt kifejezések glosszáriuma.

| Kifejezés  | Definíció |
|:-------|:------------|
| Réteg | Egy AutoCAD DWG-réteg.|
| Szint | Egy épület területe egy beállított jogosultságszint-emeléssel. Például egy épület padlója. |
| Xref  |Egy olyan fájl, amely az elsődleges rajzhoz külső hivatkozásként csatolt AutoCAD DWG-fájlformátumban (. DWG) van csatolva.  |
| Funkció | Egy olyan objektum, amely a geometriát további metaadat-információkkal ötvözi. |
| Szolgáltatási osztályok | A funkciók közös tervrajza. Egy egység például egy szolgáltatás osztály, az Office pedig egy szolgáltatás. |

## <a name="drawing-package-structure"></a>Rajzolási csomag szerkezete

A rajzfájl egy. zip-archívum, amely a következő fájlokat tartalmazza:

* DWG-fájlok az AutoCAD DWG-fájlformátumban.
* Egyetlen létesítmény _manifest.jsa_ fájlon.

A DWG-fájlok bármilyen módon rendezhetők a mappában belül, de a jegyzékfájlnak a mappa gyökérkönyvtárában kell futnia. A mappának egy. zip kiterjesztésű, egyetlen archív fájlban kell lennie. A következő részekben részletesen ismertetjük a DWG-fájlokra, a jegyzékfájlokra és a fájlok tartalmára vonatkozó követelményeket.  

## <a name="dwg-files-requirements"></a>DWG-fájlok követelményei

A létesítmény egyes szintjeihez egyetlen DWG-fájl szükséges. A szintnek csak egyetlen DWG-fájlban kell szerepelnie. A külső hivatkozásokat (_xrefeket_) a fölérendelt rajzhoz kell kötni. Továbbá minden DWG-fájl:

* Meg kell határoznia a _külső_ és az _egység_ rétegeit. A következő választható rétegek opcionálisan meghatározhatók: _Wall_, _Door_, _UnitLabel_, _Zone_és _ZoneLabel_.
* A nem tartalmazhat több szintből származó szolgáltatásokat.
* A nem tartalmazhat több létesítményből származó szolgáltatásokat.

A [Azure Maps konverziós szolgáltatás](https://docs.microsoft.com/rest/api/maps/conversion) a következő szolgáltatási osztályokat tudja kibontani egy DWG-fájlból:

* Szintek
* Egység
* Zóna
* Nyílások
* Falvastagságát
* Vertikális behatolások

Az összes konverziós feladat az alapértelmezett kategóriák minimális készletét eredményezi: Room, Structure. Wall, Opening. Door, Zone és Facility. Az objektumok által hivatkozott egyes kategóriákhoz további kategóriák tartoznak.  

A DWG-rétegeknek egyetlen osztály funkcióit kell tartalmazniuk. Az osztályok nem oszthatnak meg réteget. Az egységek és a falak például nem oszthatnak meg réteget.

A DWG-rétegeknek a következő feltételeket is követniük kell:

* Az összes DWG-fájlhoz tartozó rajzok eredetét ugyanahhoz a szélességhez és hosszúsághoz kell igazítani.
* Minden szintnek ugyanabban a tájolásban kell lennie, mint a többi szintnek.
* Az önmetsző sokszögek automatikusan kijavításra kerülnek, és a [Azure Maps konverziós szolgáltatás](https://docs.microsoft.com/rest/api/maps/conversion) figyelmeztetést küld. Ajánlott manuálisan megvizsgálni a kijavított eredményeket, mivel azok nem egyeznek a várt eredményekkel.

Minden rétegbeli entitásnak a következő típusok egyikének kell lennie: vonal, vonallánc, sokszög, körkörös ív, kör, szöveg (egyetlen sor). A többi entitás típusa figyelmen kívül lesz hagyva.

Az alábbi táblázat az egyes rétegekhez tartozó támogatott entitások típusait és támogatott funkcióit ismerteti. Ha egy réteg nem támogatott entitás-típusokat tartalmaz, akkor a [Azure Maps konverziós szolgáltatás](https://docs.microsoft.com/rest/api/maps/conversion) figyelmen kívül hagyja ezeket az entitásokat.  

| Réteg | Entitástípusok | Szolgáltatások |
| :----- | :-------------------| :-------
| [Külső](#exterior-layer) | Sokszög, vonallánc (lezárt), kör | Szintek
| [Egység](#unit-layer) |  Sokszög, vonallánc (lezárt), kör | Vertikális behatolások, egységek
| [Fal](#wall-layer)  | Sokszög, vonallánc (lezárt), kör | Nem alkalmazható. További információért lásd a [fal réteget](#wall-layer).
| [Ajtó](#door-layer) | Sokszög, vonallánc, vonal, CircularArc, kör | Nyílások
| [Zóna](#zone-layer) | Sokszög, vonallánc (lezárt), kör | Zóna
| [UnitLabel](#unitlabel-layer) | Szöveg (egyetlen sor) | Nem alkalmazható. Ez a réteg csak tulajdonságokat adhat hozzá az egység-funkciókhoz az egység rétegből. További információ: [UnitLabel réteg](#unitlabel-layer).
| [ZoneLabel](#zonelabel-layer) | Szöveg (egyetlen sor) | Nem alkalmazható. Ez a réteg csak a ZonesLayer származó tulajdonságokat adhat hozzá a zónák funkcióihoz. További információ: [ZoneLabel réteg](#zonelabel-layer)

A következő részek részletezik az egyes rétegekre vonatkozó követelményeket.

### <a name="exterior-layer"></a>Külső réteg

Az egyes szintekhez tartozó DWG-fájlnak tartalmaznia kell egy réteget, amely meghatározza a szint kerületét. Ezt a réteget külső rétegnek nevezzük. Ha például egy létesítmény két szintet tartalmaz, akkor két DWG-fájllal kell rendelkeznie, amelyek mindegyike külső réteggel rendelkezik.

Most számít, hogy a külső rétegben hány entitás található, az [eredményül kapott eszköz adatkészlete](tutorial-creator-indoor-maps.md#create-a-feature-stateset) csak **egyetlen szintű szolgáltatást** fog tartalmazni az egyes DWG-fájlokhoz. Továbbá:

* A külsőket sokszög, vonallánc (lezárt), kör alakúra kell rajzolni.

* A külsők átfedésben lehetnek, de egy geometriában lesznek feloldva.

Ha a réteg több átfedésben lévő vonalláncot tartalmaz, akkor a vonalláncok egyetlen szintű szolgáltatásba lesznek feloldva. Ha a réteg több non_overlapping vonalláncot tartalmaz, akkor az eredményül kapott szint funkció több sokszögű ábrázolással fog rendelkezni.

A külső réteg példája a [minta rajzolási csomagban](https://github.com/Azure-Samples/am-creator-indoor-data-examples)lévő tagolási rétegként is látható.

### <a name="unit-layer"></a>Egység réteg

Az egyes szintekhez tartozó DWG-fájlnak egységeket tartalmazó réteget kell meghatároznia.  Az egységek az épületben navigálható szóközök, például irodák, folyosók, lépcsők és felvonók. Az egység rétegnek az alábbi követelményeknek kell megfelelnie:

* Az egységeket sokszögként, vonalláncként (lezárt), körként kell megrajzolni.
* Az egységeknek a létesítmény külső kerületének határain belül kell esniük.
* Az egységek nem lehetnek részben átfedésben.
* Az egységek nem tartalmazhatnak önmetsző geometriát.

 Az egység elnevezéséhez hozzon létre egy Text objektumot a _unitLabel_ rétegben, majd helyezze el az objektumot az egység határain belül. További információ: [UnitLabel réteg](#unitlabel-layer).

Az egység rétegre példa lehet a [minta rajzolási CSOMAGBAN](https://github.com/Azure-Samples/am-creator-indoor-data-examples)egység rétegként.

### <a name="wall-layer"></a>Fal réteg

Az egyes szintek DWG-fájlja tartalmazhat olyan réteget, amely meghatározza a falak, oszlopok és egyéb építési struktúra fizikai egységeit.

* A falakat sokszögként, vonalláncként (lezárva), kört kell rajzolni.
* A fal réteg (ek) csak olyan geometriát tartalmazhat, amelyet felépítési struktúraként kell értelmezni.

A falak rétegének példája a [minta rajzolási csomagban](https://github.com/Azure-Samples/am-creator-indoor-data-examples)található falak rétegként látható.

### <a name="door-layer"></a>Ajtó rétege

Tartalmazhat egy ajtókat tartalmazó DWG réteget is. Mindegyik ajtónak átfedésben kell lennie egy egység szélével az egység rétegben.

Az ajtók megnyitása egy Azure Maps adatkészletben egysoros szegmensként jelennek meg, amely átfedésben van több egység határán. Az alábbi lépéseket követve alakíthatja át a geometriát az ajtó rétegében egy adatkészlet funkcióinak megnyitásához.

![A megnyitások létrehozásának lépései](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>Zóna réteg

Az egyes szintek DWG-fájlja tartalmazhat olyan zóna réteget, amely meghatározza a zónák fizikai egységeit. Egy zóna lehet egy beltéri üres terület vagy egy hátsó udvar.

* A zónákat sokszögként, vonalláncként (lezárt), körként kell kirajzolni.
* A zónák átfedésben lehetnek.
* A zónák a létesítmény külső peremén vagy kívül esnek.

A zóna elnevezéséhez hozzon létre egy Text objektumot a _zoneLabel_ rétegben, és helyezze a szöveg objektumot a zóna határain belülre. További információ: [ZoneLabel réteg](#zonelabel-layer).

A zónák rétegre példaként a [minta rajzolási csomagban](https://github.com/Azure-Samples/am-creator-indoor-data-examples)található zónák réteg látható.

### <a name="unitlabel-layer"></a>UnitLabel réteg

Az egyes szintek DWG-fájlja tartalmazhatja az egység feliratának rétegét. Az egység felirat rétege hozzáadja a Name (név) tulajdonságot az egység rétegből kinyert egységekhez. A name tulajdonsággal rendelkező egységek további részleteket adhatnak meg a jegyzékfájlban.

* Az egység feliratának egysoros szöveges entitásnak kell lennie.
* Az egység címkéjének az egység határain belül kell esnie.
* Az egységek nem tartalmazhatnak több szöveges entitást a Unit labels rétegben.

A UnitLabel réteg példája az UNITLABELS rétegként látható a [minta rajzolási csomagban](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="zonelabel-layer"></a>ZoneLabel réteg

Az egyes szintek DWG-fájlja tartalmazhatja a zóna feliratának rétegét. Ez a réteg hozzáadja a Name (név) tulajdonságot a zóna rétegből kinyert zónákhoz. A name tulajdonsággal rendelkező zónák további részleteket adhatnak meg a jegyzékfájlban.

* A zónák címkéjének egysoros szöveges entitásnak kell lennie.
* A zónák címkéjének a zóna határain belül kell esnie.
* A zónák nem tartalmazhatnak több szöveges entitást a zóna feliratai rétegben.

A Zonelabel réteg példája az ZONELABELS rétegként látható a [minta rajzolási csomagban](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="manifest-file-requirements"></a>Manifest-fájlra vonatkozó követelmények

A zip-mappának tartalmaznia kell egy jegyzékfájlt a könyvtár legfelső szintjén, és a fájlnak **manifest.js**nevűnek kell lennie. Leírja azokat a DWG-fájlokat, amelyek lehetővé teszik a [Azure Maps átalakítási szolgáltatás](https://docs.microsoft.com/rest/api/maps/conversion) számára a tartalom elemzését. Csak a jegyzékfájlban azonosított fájlok lesznek betöltve. A rendszer figyelmen kívül hagyja a zip mappában található, de a jegyzékfájlban nem szereplő fájlokat.

A fájl elérési útjai a jegyzékfájl **buildingLevels** objektumában a zip-mappa gyökeréhez viszonyítva kell, hogy legyenek. A DWG-fájl nevének pontosan egyeznie kell a létesítmény szintjének nevével. Például a "alagsor" szintjén található DWG-fájl "alagsor. DWG" lenne. A 2. szintű DWG-fájl neve "level_2. DWG" lesz. Ha a szint neve szóközt tartalmaz, használjon aláhúzást. 

Bár a jegyzékfájl-objektumok használatakor követelmények vannak, nem minden objektumra van szükség. Az alábbi táblázat a [Azure Maps átalakítási szolgáltatás](https://docs.microsoft.com/rest/api/maps/conversion)1,1-es verziójának kötelező és választható objektumait mutatja be.

| Objektum | Kötelező | Leírás |
| :----- | :------- | :------- |
| directoryInfo | igaz | Felvázolja a létesítmény földrajzi és kapcsolattartási adatait. Az utas földrajzi és kapcsolattartási adatainak tagolására is használható. |
| buildingLevels | igaz | Meghatározza az épületek szintjét és a szintek kialakítását tartalmazó fájlokat. |
| Georeference | igaz | Numerikus földrajzi adatokat tartalmaz a létesítmény rajzolásához. |
| dwgLayers | igaz | Felsorolja a rétegek nevét, és mindegyik réteg felsorolja a saját szolgáltatásainak nevét. |
| unitProperties | hamis | További metaadatok beszúrására használható az egység szolgáltatásaihoz. |
| zoneProperties | hamis | További metaadatok beszúrására használható a zóna szolgáltatásaihoz. |

A következő részek részletezik az egyes objektumok követelményeit.

### <a name="directoryinfo"></a>directoryInfo

| Tulajdonság  | típus | Kötelező | Leírás |
|-----------|------|----------|-------------|
| name      | sztring | igaz   |  Az építési név. |
| streetAddress|    sztring |    hamis    | Az építési címe. |
|egység     | sztring    |  hamis    |  Egység az épületben. |
| településen |    sztring |    hamis |    Terület, környék vagy régió neve. Például: "túltó" vagy "központi körzet". A helység nem része a levelezési címnek. |
| adminDivisions |    Karakterláncok JSON-tömbje |    hamis     | A címek megjelölését (ország, állam, város) vagy (ország, prefektúra, város, város) tartalmazó tömb. Az ISO 3166 országkódok és az ISO 3166-2 állapot/terület kódok használata. |
| Irányítószám |    sztring    | hamis    | A levél rendezési kódja. |
| hoursOfOperation |    sztring |     hamis | Betartja az [OSM nyitvatartási idejét](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification) . |
| telefon    | sztring |    hamis |    A létrehozáshoz társított telefonszám. Tartalmaznia kell az országkódot. |
| Honlap    | sztring |    hamis    | Az épületben társított webhely. M kezdés http-vagy https-kapcsolattal. |
| nonPublic |    logikai    | hamis | Jelölő, amely megadja, hogy az épület nyitva van-e a nyilvánosság számára. |
| anchorLatitude | numerikus |    hamis | A létesítményi horgony (gombostű) földrajzi szélessége |
| anchorLongitude | numerikus |    hamis | A létesítményi horgony (gombostű) földrajzi hosszúsága. |
| anchorHeightAboveSeaLevel  | numerikus | hamis | A létesítmény talajszintének magassága a tengerszint felett, méterben. |
| defaultLevelVerticalExtent | numerikus | hamis | A létesítmény szintjének alapértelmezett magassága (vastagsága), ha egy szint `verticalExtent` nincs meghatározva. |

### <a name="buildinglevels"></a>buildingLevels

Az `buildingLevels` objektum az épületek szintjeinek JSON-tömbjét tartalmazza.

| Tulajdonság  | Típus | Kötelező | Leírás |
|-----------|------|----------|-------------|
|Szint    |sztring    |igaz |    Leíró szint neve. Például: Floor 1, lobby, Blue parkoló, alagsor stb.|
|sorszámok | egész szám |    igaz | A szintek függőleges sorrendjének meghatározásához a sorszámot kell használni. Minden létesítménynek 0 sorszámú szinten kell lennie. |
|heightAboveFacilityAnchor | numerikus |    hamis |    A magasság szintje a talajszint felett méterben. |
| verticalExtent | numerikus | hamis | A szint és a felső határ közötti magasság (vastagság) mértékegysége (méter). |
|fájlnév |    sztring |    igaz |    A CAD-rajz fájlrendszerbeli elérési útja egy építési szinthez. Az értéknek az épületben található zip-fájl gyökeréhez viszonyítva kell lennie. |

### <a name="georeference"></a>Georeference

| Tulajdonság  | Típus | Kötelező | Leírás |
|-----------|------|----------|-------------|
|Lat    | numerikus |    igaz |    A mértékek szélességének decimális ábrázolása a létesítmény-rajz forrásaként. A forrás koordinátáinak a WGS84 web Mercator () értékkel kell rendelkezniük `EPSG:3857` .|
|Lon    |numerikus|    igaz|    A fok hosszúságának decimális ábrázolása a létesítmény-rajz forrásaként. A forrás koordinátáinak a WGS84 web Mercator () értékkel kell rendelkezniük `EPSG:3857` . |
|angle|    numerikus|    igaz|   Az óramutató és a rajz függőleges (Y) tengelye közötti szögben, fokban megadva.   |

### <a name="dwglayers"></a>dwgLayers

| Tulajdonság  | Típus | Kötelező | Leírás |
|-----------|------|----------|-------------|
|külső    |Karakterláncok tömbje|    igaz|    A külső felépítési profilt meghatározó réteg (ek) neve.|
|egység|    Karakterláncok tömbje|    igaz|    Az egységeket meghatározó réteg (ek) neve.|
|fal|    Karakterláncok tömbje    |hamis|    A falakat meghatározó réteg (ek) neve.|
|ajtajának    |Karakterláncok tömbje|    hamis   | Az ajtókat meghatározó réteg (ek) neve.|
|unitLabel    |Karakterláncok tömbje|    hamis    |Az egységek nevét meghatározó réteg (ek) neve.|
|zóna | Karakterláncok tömbje    | hamis    | A zónákat meghatározó réteg (ek) neve.|
|zoneLabel | Karakterláncok tömbje |     hamis |    A zónák nevét meghatározó réteg (ek) neve.|

### <a name="unitproperties"></a>unitProperties

Az `unitProperties` objektum az egység tulajdonságainak JSON-tömbjét tartalmazza.

| Tulajdonság  | Típus | Kötelező | Leírás |
|-----------|------|----------|-------------|
|unitName    |sztring    |igaz    |A rekorddal társítandó egység neve `unitProperty` . Ez a rekord csak akkor érvényes, ha a `unitName` réteg (ek) ban címkével egyező címke található `unitLabel` . |
|categoryName|    sztring|    hamis    |Kategória neve A kategóriák teljes listájáért tekintse meg a [kategóriákat](https://aka.ms/pa-indoor-spacecategories). |
|navigableBy| Karakterláncok tömbje |    hamis    |Az egységet áthaladó navigáló ügynökök típusát jelöli. Például: "sétáló". Ez a tulajdonság tájékoztatja a wayfinding képességeit.  A megengedett értékek:,,,,,,,,,, `pedestrian` `wheelchair` `machine` `bicycle` `automobile` `hiredAuto` `bus` `railcar` `emergency` `ferry` `boat` és `disallowed` .|
|routeThroughBehavior|    sztring|    hamis    |Az egység viselkedési útvonala. A megengedett értékek: `disallowed` , `allowed` és `preferred` . Az alapértelmezett érték: `allowed` .|
|utasok    |DirectoryInfo objektumok tömbje |hamis    |Az egység utasainak listája. |
|nameAlt|    sztring|    hamis|    Az egység alternatív neve. |
|nameSubtitle|    sztring    |hamis|    Az egység alcíme. |
|addressRoomNumber|    sztring|    hamis|    Az egységhez tartozó szoba/egység/apartman/csomag száma.|
|verticalPenetrationCategory|    sztring|    hamis| Ha ez a tulajdonság meg van adva, az eredményül kapott funkció egy egység helyett függőlegesen (VRT) lesz. A VRTs a fenti vagy alatti szintek más VRT szolgáltatásaihoz is felhasználhatók. A függőleges behatolás a [Kategória](https://aka.ms/pa-indoor-spacecategories) neve. Ha ez a tulajdonság meg van adva, a categoryName tulajdonság felülbírálva van a verticalPenetrationCategory. |
|verticalPenetrationDirection|    sztring|    hamis    |Ha `verticalPenetrationCategory` meg van adva, nem kötelezően megadhatja az utazás érvényes irányát. A megengedett értékek:,, `lowToHigh` `highToLow` `both` és `closed` . Az alapértelmezett érték: `both` .|
| nonPublic | logikai | hamis | Azt jelzi, hogy az egység nyitva van-e a nyilvános számára. |
| isRoutable | logikai | hamis | Ha a értékre van állítva `false` , az egység nem navigálható a következőre:. Az alapértelmezett érték: `true` . |
| isOpenArea | logikai | hamis | Lehetővé teszi az ügynök számára az egység megadását anélkül, hogy az egységhez csatlakoztatott megnyitásra van szükség. Alapértelmezés szerint ez az érték csak akkor van beállítva, `true` Ha az egység megnyitása megkezdődött. |

### <a name="the-zoneproperties-object"></a>Az zoneProperties objektum

Az `zoneProperties` objektum a zóna tulajdonságainak JSON-tömbjét tartalmazza.

| Tulajdonság  | Típus | Kötelező | Leírás |
|-----------|------|----------|-------------|
|zoneName        |sztring    |igaz    |A rekordhoz társítandó zóna neve `zoneProperty` . Ez a rekord csak akkor érvényes, ha a `zoneName` zóna rétegében szerepel a címke egyezése `zoneLabel` .  |
|categoryName|    sztring|    hamis    |Kategória neve A kategóriák teljes listájáért tekintse meg a [kategóriákat](https://aka.ms/pa-indoor-spacecategories). |
|zoneNameAlt|    sztring|    hamis    |A zóna alternatív neve.  |
|zoneNameSubtitle|    sztring |    hamis    |A zóna alcíme. |

### <a name="sample-drawing-package-manifest"></a>Minta rajzolási csomag jegyzékfájlja

Alább látható a minta rajzolási csomaghoz tartozó jegyzékfájl-fájl. A teljes csomag letöltéséhez kattintson a [minta rajzolási csomag](https://github.com/Azure-Samples/am-creator-indoor-data-examples)lehetőségre.

#### <a name="manifest-file"></a>Jegyzékfájl

```JSON
{
    "version": "1.1", 
    "directoryInfo": { 
        "name": "Contoso Building", 
        "streetAddresss": "Contoso Way", 
        "unit": "1", 
        "locality": "Contoso eastside", 
        "postalCode": "98052", 
        "adminDivisions": [ 
            "Contoso city", 
            "Contoso state", 
            "Contoso country" 
        ], 
        "hoursOfOperation": "Mo-Fr 08:00-17:00 open", 
        "phone": "1 (425) 555-1234", 
        "website": "www.contoso.com", 
        "nonPublic": false, 
        "anchorLatitude": 47.636152, 
        "anchorLongitude": -122.132600, 
        "anchorHeightAboveSeaLevel": 1000, 
        "defaultLevelVerticalExtent": 3  
    }, 
    "buildingLevels": { 
        "levels": [ 
            { 
                "levelName": "Basement", 
                "ordinal": -1, 
                "filename": "./Basement.dwg" 
            }, { 
                "levelName": "Ground", 
                "ordinal": 0, 
                "verticalExtent": 5, 
                "filename": "./Ground.dwg" 
            }, { 
                "levelName": "Level 2", 
                "ordinal": 1, 
                "heightAboveFacilityAnchor": 3.5, 
                "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
    "georeference": { 
        "lat": 47.636152, 
        "lon": -122.132600, 
        "angle": 0 
    }, 
    "dwgLayers": { 
        "exterior": [ 
            "OUTLINE", "WINDOWS" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    }, 
    "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "navigableBy": ["pedestrian", "wheelchair", "machine"], 
            "routeThroughBehavior": "disallowed", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonWheelchairAccessible": false, 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
        { 
            "unitName": "B02" 
        }, 
        { 
            "unitName": "B05", 
            "categoryName": "room.office" 
        }, 
        { 
            "unitName": "STRB01", 
            "verticalPenetrationCategory": "verticalPenetration.stairs", 
            "verticalPenetrationDirection": "both" 
        }, 
        { 
            "unitName": "ELVB01", 
            "verticalPenetrationCategory": "verticalPenetration.elevator", 
            "verticalPenetrationDirection": "high_to_low" 
        } 
    ], 
    "zoneProperties": 
    [ 
        { 
            "zoneName": "WifiB01", 
            "categoryName": "Zone", 
            "zoneNameAlt": "MyZone", 
            "zoneNameSubtitle": "Wifi", 
            "zoneSetId": "1234" 
        }, 
        { 
            "zoneName": "Wifi101",
            "categoryName": "Zone",
            "zoneNameAlt": "MyZone",
            "zoneNameSubtitle": "Wifi",
            "zoneSetId": "1234"
        }
    ]
}
```

## <a name="next-steps"></a>Következő lépések

Ha a rajzolási csomag megfelel a követelményeknek, a [Azure Maps konverziós szolgáltatással](https://docs.microsoft.com/rest/api/maps/conversion) átalakíthatja a csomagot térképi adatkészletbe. Ezt követően használhatja az adatkészletet egy beltéri Térkép létrehozásához a beltéri térképek modul használatával. A beltéri térképek modul használatáról a következő cikkekből tájékozódhat:

> [!div class="nextstepaction"]
>[A beltéri térképek létrehozója](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: létrehozói beltéri Térkép létrehozása](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Beltéri térképek dinamikus stílusa](indoor-map-dynamic-styling.md)
