---
title: Vonal réteg hozzáadása Android-térképekhez | Microsoft Azure térképek
description: Megtudhatja, hogyan adhat hozzá vonalakat a térképekhez. A Azure Maps Android SDK-val olyan példákat talál, amelyek a vonalakat a térképekhez, valamint a szimbólumokkal és színátmenetekkel rendelkező sorok testreszabására használják.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 3e68be79a4405af103512a9009187857a0d9af39
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681585"
---
# <a name="add-a-line-layer-to-the-map-android-sdk"></a>Vonal rétegének hozzáadása a térképhez (Android SDK)

A vonal réteggel `LineString` `MultiLineString` görbékként és útvonalakként jeleníthető meg a térképen. A sorok és a szolgáltatások körvonalainak megjelenítéséhez egy sor réteg is használható `Polygon` `MultiPolygon` . Az adatforrások egy vonal réteghez csatlakoznak, így biztosítva a rendereléshez szükséges adatforrásokat.

> [!TIP]
> A sorok alapértelmezés szerint a sokszögek koordinátáit, valamint egy adatforrás sorait jelenítik meg. Ha korlátozni szeretné a réteget úgy, hogy az csak az LineString geometriai funkciókat jelenítse meg, állítsa a réteg beállítását a következőre: `filter` `eq(geometryType(), "LineString")` . Ha szeretné, hogy a MultiLineString-funkciók is szerepeljenek, állítsa a réteg beállítását a következőre: `filter` `any(eq(geometryType(), "LineString"), eq(geometryType(), "MultiLineString"))` .

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy a gyors útmutató [: Android-alkalmazás létrehozása](quick-android-map.md) dokumentum lépéseit kell végrehajtania. A cikkben szereplő kódrészletek behelyezhetők a Maps `onReady` Event handlerbe.

## <a name="add-a-line-layer"></a>Vonalréteg hozzáadása

A következő kód bemutatja, hogyan lehet sort létrehozni. Adja hozzá a sort egy adatforráshoz, majd jelenítse meg egy vonal réteggel a `LineLayer` osztály használatával.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a list of points.
List<Point> points = Arrays.asList(
    Point.fromLngLat(-73.972340, 40.743270),
    Point.fromLngLat(-74.004420, 40.756800));

//Create a LineString geometry and add it to the data source.
source.add(LineString.fromLngLats(points));

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor("blue"),
    strokeWidth(5f)
);

map.layers.add(layer);
```

Az alábbi képernyőfelvételen a fenti kód jelenik meg egy vonal rétegben.

![Térkép egy vonal réteggel megjelenített vonallal](media/android-map-add-line-layer/android-line-layer.png)

## <a name="data-drive-line-style"></a>Adatmeghajtó-vonal stílusa

A következő kód két vonalból álló funkciót hoz létre, és az egyes sorokhoz tulajdonságként adja hozzá a sebességhatár értékét. A soros réteg adatmeghajtó stílusú kifejezéssel színezi a vonalakat a sebességkorlátozás értéke alapján. Mivel a vonali adatátfedések az utak mentén történik, az alábbi kód hozzáadja a réteg alatti réteget, hogy az úthasználók továbbra is jól olvashatóak legyenek.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a line feature.
Feature feature = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.131821, 47.704033),
        Point.fromLngLat(-122.099919, 47.703678))));

//Add a property to the feature.
feature.addNumberProperty("speedLimitMph", 35);

//Add the feature to the data source.
source.add(feature);

//Create a second line feature.
Feature feature2 = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.126662, 47.708265),
        Point.fromLngLat(-122.126877, 47.703980))));

//Add a property to the second feature.
feature2.addNumberProperty("speedLimitMph", 15);

//Add the second feature to the data source.
source.add(feature2);

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor(
        interpolate(
            linear(),
            get("speedLimitMph"),
            stop(0, color(Color.GREEN)),
            stop(30, color(Color.YELLOW)),
            stop(60, color(Color.RED))
        )
    ),
    strokeWidth(5f)
);

map.layers.add(layer, "labels");
```

Az alábbi képernyőfelvételen látható, hogy a fenti kód két sort jelenít meg egy vonal rétegben, és a színük egy adatvezérelt stílusú kifejezésből származik, amely a vonal funkcióinak egyik tulajdonságán alapul.

![Térkép adatmeghajtó stílusú vonalakkal, amelyek egy sorban jelennek meg](media/android-map-add-line-layer/android-line-layer-data-drive-style.png)

## <a name="add-symbols-along-a-line"></a>Szimbólumok felvétele a vonal mentén

Ez a minta bemutatja, hogyan adhat hozzá nyíl ikonokat a Térkép egyik sorához. Ha szimbólum réteget használ, állítsa be a beállítást a következőre: `symbolPlacement` `SymbolPlacement.LINE` . Ez a beállítás megjeleníti a jeleket a vonal mentén, és elforgatja az ikonokat (0 fok = jobb).

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Load a image of an arrow into the map image sprite and call it "arrow-icon".
map.images.add("arrow-icon", R.drawable.purple-arrow-right);

//Create and add a line to the data source.
source.add(LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.18822, 47.63208),
        Point.fromLngLat(-122.18204, 47.63196),
        Point.fromLngLat(-122.17243, 47.62976),
        Point.fromLngLat(-122.16419, 47.63023),
        Point.fromLngLat(-122.15852, 47.62942),
        Point.fromLngLat(-122.15183, 47.62988),
        Point.fromLngLat(-122.14256, 47.63451),
        Point.fromLngLat(-122.13483, 47.64041),
        Point.fromLngLat(-122.13466, 47.64422),
        Point.fromLngLat(-122.13844, 47.65440),
        Point.fromLngLat(-122.13277, 47.66515),
        Point.fromLngLat(-122.12779, 47.66712),
        Point.fromLngLat(-122.11595, 47.66712),
        Point.fromLngLat(-122.11063, 47.66735),
        Point.fromLngLat(-122.10668, 47.67035),
        Point.fromLngLat(-122.10565, 47.67498))));

//Create a line layer and add it to the map.
map.layers.add(new LineLayer(source,
    strokeColor("DarkOrchid"),
    strokeWidth(5f)
));

//Create a symbol layer and add it to the map.
map.layers.add(new SymbolLayer(source,
    //Space symbols out along line.
    symbolPlacement(SymbolPlacement.LINE),

    //Spread the symbols out 100 pixels apart.
    symbolSpacing(100f),
    
    //Use the arrow icon as the symbol.
    iconImage("arrow-icon"),

    //Allow icons to overlap so that they aren't hidden if they collide with other map elements.
    iconAllowOverlap(true),

    //Center the symbol icon.
    iconAnchor(AnchorType.CENTER),

    //Scale the icon size.
    iconSize(0.8f)
));
```

Ebben a példában a következő rendszerkép be lett töltve az alkalmazás megrajzolható mappájába.

| ![Lila nyíl ikon képe](media/android-map-add-line-layer/purple-arrow-right.png)|
|:-----------------------------------------------------------------------:|
|                                                  |

Az alábbi képernyőképen a fenti kód jeleníti meg a sort, amelyen a nyíl ikon jelenik meg.

![Térkép adatmeghajtó stílusú vonalakkal egy sorban megjelenített nyilakkal](media/android-map-add-line-layer/android-symbols-along-line-path.png)

## <a name="next-steps"></a>További lépések

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Adatforrás létrehozása](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](how-to-add-shapes-to-android-map.md)
