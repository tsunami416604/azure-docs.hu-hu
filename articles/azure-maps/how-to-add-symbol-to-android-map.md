---
title: Szimbólum-réteg hozzáadása az Android Maps-hez | Microsoft Azure térképek
description: Megtudhatja, hogyan adhat hozzá egy jelölőt egy térképhez. Egy olyan példát láthat, amely a Azure Maps Android SDK-t használja egy adatforrásból származó, pont-alapú adatokból álló szimbólum réteg hozzáadásához.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 040fcde35707074ffaf102ed6c224b2f47a084bb
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679330"
---
# <a name="add-a-symbol-layer-android-sdk"></a>Szimbólum réteg hozzáadása (Android SDK)

Ebből a cikkből megtudhatja, hogyan jelenítheti meg a pontok adatait egy adatforrásból szimbólumként szolgáló rétegként a Azure Maps Android SDK használatával. A szimbólum-rétegek megjelenítési pontok képként és szövegként jelennek meg a térképen.

> [!TIP]
> A szimbólumok alapértelmezés szerint az adatforrásban lévő összes geometriá koordinátáit fogják megjeleníteni. Ha úgy szeretné korlátozni a réteget, hogy csak a pont geometriai funkcióit jelenítse meg, állítsa a réteg beállítását a következőre: `filter` `eq(geometryType(), "Point")` . Ha azt szeretné, hogy a multipoint-funkciók is szerepeljenek, állítsa a réteg beállítását a következőre: `filter` `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy a gyors útmutató [: Android-alkalmazás létrehozása](quick-android-map.md) dokumentum lépéseit kell végrehajtania. A cikkben szereplő kódrészletek behelyezhetők a Maps `onReady` Event handlerbe.

## <a name="add-a-symbol-layer"></a>Szimbólumréteg hozzáadása

Ahhoz, hogy hozzá lehessen adni egy szimbólum réteget a térképhez, néhány lépést végre kell hajtania. Először hozzon létre egy adatforrást, és adja hozzá a térképhez. Hozzon létre egy szimbólum réteget. Ezután továbbítsa az adatforrást a szimbólum rétegbe az adatoknak az adatforrásból való lekéréséhez. Végül vegyen fel egy adatforrást az adatforrásba, hogy valami legyen megjelenítve.

Az alábbi kód azt mutatja be, hogy mit kell hozzáadni a térképhez a betöltését követően. Ez a minta egy pontot jelenít meg a térképen egy szimbólum réteg használatával.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source);

//Add the layer to the map.
map.layers.add(layer);
```

A térképhez három különböző típusú pont-adattípust lehet hozzáadni:

- GeoJSON pont geometriája – ez az objektum csak egy pont koordinátáit tartalmazza, semmi más. A `Point.fromLngLat` statikus metódus használatával egyszerűen hozhatók létre ezek az objektumok.
- GeoJSON multipoint geometria – Ez az objektum több pont koordinátáit tartalmazza, és semmi mást nem. Adja át a pontok tömbjét az `MultiPoint` osztálynak az objektumok létrehozásához.
- GeoJSON funkció – ez az objektum bármilyen GeoJSON geometriát és olyan tulajdonságokat tartalmaz, amelyek a geometriához társított metaadatokat tartalmaznak.

További információkért lásd az [adatforrások létrehozása](create-data-source-android-sdk.md) című dokumentumot az adatok létrehozásához és a térképhez való hozzáadásához.

Az alábbi mintakód egy GeoJSON pontot hoz létre, és átadja azt a GeoJSON szolgáltatásnak, és `title` hozzáadott egy értéket a tulajdonságaihoz. A `title` tulajdonság a Térkép szimbólum ikonja fölötti szövegként jelenik meg.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(0, 0));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source, 
    //Get the title property of the feature and display it on the map.
    textField(get("title"))
);

//Add the layer to the map.
map.layers.add(layer);
```

Az alábbi képernyőfelvételen látható, hogy a fenti kód egy tépő egy ikon és egy szöveges címke használatával.

![Leképezés a ponttal megjelenített ponttal egy szimbólum-réteggel, amely egy adott pont funkció ikonját és szöveges címkéjét jeleníti meg](media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> Alapértelmezés szerint a szimbólum-rétegek az átfedésben lévő szimbólumok elrejtésével optimalizálja a szimbólumok megjelenítését. A nagyításhoz a rejtett szimbólumok láthatóvá válnak. Ha le szeretné tiltani ezt a funkciót, és az összes szimbólumot le szeretné jeleníteni, állítsa a és a beállításokat a következőre: `iconAllowOverlap` `textAllowOverlap` `true` .

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Egyéni ikon hozzáadása egy szimbólum réteghez

A szimbólumok rétegei a WebGL használatával jelennek meg. Ennek megfelelően az összes erőforrást, például ikonokat be kell tölteni a WebGL-környezetbe. Ez a minta bemutatja, hogyan adhat hozzá egyéni ikont a Térkép erőforrásaihoz. Ezt az ikont ezután a rendszer a Térkép egyéni szimbólumával jeleníti meg. A `textField` szimbólum réteg tulajdonságának meg kell adni egy kifejezést. Ebben az esetben a hőmérséklet tulajdonságot szeretnénk megjeleníteni. Mivel a hőmérséklet egy szám, azt karakterlánccá kell konvertálni. Emellett szeretnénk hozzáfűzni a "°F" kifejezést. Ehhez az összefűzéshez kifejezés használható. `concat(Expression.toString(get("temperature")), literal("°F"))`.

```java
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773));

//Add a property to the feature.
feature.addNumberProperty("temperature", 64);

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),

    //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(new Float[]{0f, -1.5f})
);
```

Ebben a példában a következő rendszerkép be lett töltve az alkalmazás megrajzolható mappájába.

| ![Rain záporok ikonjának képe](media/how-to-add-symbol-to-android-map/showers.png)|
|:-----------------------------------------------------------------------:|
| showers.png                                                  |

A következő képernyőfelvételen a fenti kód látható, amely egy egyéni ikon és egy tépő használatával formázott szöveg címkét tartalmaz.

![Térkép megjelenítése az adott pont szolgáltatás egyéni ikonját és formázott szöveges címkéjét ábrázoló szimbólum-réteggel](media/how-to-add-symbol-to-android-map/android-custom-symbol-layer.png)

> [!TIP]
> Ha csak szimbólum réteget szeretne megjeleníteni, elrejtheti a ikont az `iconImage` ikon beállításainak tulajdonságának beállításával `"none"` .

## <a name="next-steps"></a>Következő lépések

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Adatforrás létrehozása](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Buborékréteg hozzáadása](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Adatvezérelt stíluskifejezések használata](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Funkcióinformációk megjelenítése](display-feature-information-android.md)
