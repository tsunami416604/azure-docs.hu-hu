---
title: Leképezési események kezelése Android-térképeken | Microsoft Azure térképek
description: Megtudhatja, hogy mely események legyenek kirúgva, amikor a felhasználók a Maps szolgáltatással működnek Az összes támogatott térképi esemény listájának megtekintése. Tekintse meg, hogyan kezelheti az eseményeket az Azure Maps Android SDK használatával.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 818d33947fa079a130c3009a34dcb9b72ad52f91
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681536"
---
# <a name="interact-with-the-map-android-sdk"></a>Együttműködés a térképpel (Android SDK)

Ez a cikk bemutatja, hogyan használhatja a Maps Events Managert.

## <a name="interact-with-the-map"></a>A térképpel való interakció

A Térkép az összes eseményt a `events` tulajdonságán keresztül kezeli. A következő táblázat felsorolja az összes támogatott Térkép-eseményt.

| Esemény                  | Eseménykezelő formátuma | Description (Leírás) |
|------------------------|----------------------|-------------|
| `OnCameraIdle`         | `()`                 | <p>Az utolsó olyan keret után következik be, amely után a Térkép "tétlen" állapotba kerül:<ul><li>Nincs folyamatban kamera-átmenet.</li><li>A jelenleg kért csempék betöltődik.</li><li>Minden áttűnési/átmenetes animáció befejeződött.</li></ul></p> |
| `OnCameraMove`         | `()`                 | Az egyik nézetről a másikra történő animált áttérés során többször is elindítható a felhasználói interakció vagy módszerek eredményeképpen. |
| `OnCameraMoveCanceled` | `()`                 | A kamera felé irányuló továbbítási kérelem megszakítva. |
| `OnCameraMoveStarted`  | `(int reason)`       | Közvetlenül a Térkép megkezdése előtt az egyik nézetről a másikra való áttérést a felhasználói interakció vagy módszerek eredményeképpen lehet megkezdeni. Az `reason` esemény-figyelő argumentuma egész értéket ad vissza, amely részletesen ismerteti a kamera mozgásának módját. Az alábbi lista a lehetséges okokat sorolja fel:<ul><li>1: kézmozdulat</li><li>2: fejlesztői animáció</li><li>3: API-animáció</li></ul>   |
| `OnClick`              | `(double lat, double lon)` | A Térkép megnyomásakor és a térképen való kikapcsolásakor a rendszer kilőtte a térképet. |
| `OnFeatureClick`       | `(List<Feature>)`    | A rendszer a Térkép megnyomásakor és a Szolgáltatáson megjelenő pontokon való kiadása után felgyújtotta.  |
| `OnLongClick`          | `(double lat, double lon)` | A Térkép lenyomásakor, egy pillanatra, majd a térképen megjelenő ponton jelenik meg. |
| `OnLongFeatureClick `  | `(List<Feature>)`    | A rendszer a Térkép lenyomásakor, egy pillanatra, majd a szolgáltatás ugyanazon pontján jelenik meg. |
| `OnReady`              | `(AzureMap map)`     | Akkor következik be, amikor a Térkép betöltődik, vagy ha az alkalmazás tájolásának változása megváltozik, és a minimálisan szükséges leképezési erőforrások betöltődik, és a Térkép készen áll a programozással való együttműködésre. |

A következő kód bemutatja, hogyan adhatja hozzá a `OnClick` , `OnFeatureClick` és az `OnCameraMove` eseményeket a térképhez.

```java
map.events.add((OnClick) (lat, lon) -> {
    //Map clicked.
});

map.events.add((OnFeatureClick) (features) -> {
    //Feature clicked.
});

map.events.add((OnCameraMove) () -> {
    //Map camera moved.
});
```

További információ: [Navigálás a Térkép](how-to-use-android-map-control-library.md#navigating-the-map) dokumentációjában, hogyan lehet kommunikálni a Térkép és a trigger események használatával.

## <a name="scope-feature-events-to-layer"></a>Hatókör-szolgáltatási események rétegbe

Amikor hozzáadja a `OnFeatureClick` vagy az `OnLongFeatureClick` eseményeket a térképhez, a rendszer egy második paraméterként is átadhatja a réteg azonosítóját. Ha a rendszer átadja a réteg AZONOSÍTÓját, az esemény csak akkor fog tüzet, ha az esemény az adott rétegen következik be. A rétegekre hatókörrel rendelkező eseményeket a szimbólum, a buborék, a vonal és a sokszög rétegek támogatják.

```java
//Create a data source.
DataSource source = new DataSource();
map.sources.add(source);

//Add data to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a layer and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnFeatureClick) (features) -> {
    //One or more features clicked.
}, layer.getId());

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.
}, layer.getId());
```

## <a name="next-steps"></a>További lépések

A következő cikkekben talál részletes példákat:

> [!div class="nextstepaction"]
> [Navigálás a térképen](how-to-use-android-map-control-library.md#navigating-the-map)

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Buborékréteg hozzáadása](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Vonalréteg hozzáadása](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](how-to-add-shapes-to-android-map.md)
