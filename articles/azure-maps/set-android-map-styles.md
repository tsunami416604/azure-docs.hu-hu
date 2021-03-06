---
title: Térkép stílusának beállítása Android-térképeken | Microsoft Azure térképek
description: Ismerje meg, hogyan állíthatja be a Térkép stílusát. A stílus módosításához tekintse meg a Azure Maps Android SDK használatát az elrendezési fájlban vagy a tevékenység osztályban.
author: rbrundritt
ms.author: richbrun
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 1cce355c8ffbcd4704bd32b0e4d1739c77c2b623
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97678461"
---
# <a name="set-map-style-android-sdk"></a>Térkép stílusának beállítása (Android SDK)

Ebből a cikkből megtudhatja, hogyan állíthatja be a leképezési stílusokat a Azure Maps Android SDK használatával. A Azure Maps hat különböző leképezési stílus közül választhat. A támogatott térképi stílusokkal kapcsolatos további információkért tekintse [meg a Azure Maps támogatott leképezési stílusait](supported-map-styles.md).

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy a gyors útmutató [: Android-alkalmazás létrehozása](quick-android-map.md) dokumentum lépéseit kell végrehajtania.

## <a name="set-map-style-in-the-layout"></a>Térkép stílusának beállítása az elrendezésben

A Térkép vezérlőelem hozzáadásakor megadhat egy térképi stílust a tevékenység osztályának elrendezési fájljában. A következő kód a középpont helyét, a nagyítási szint és a Térkép stílusát állítja be.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/mapcontrol"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_centerLat="47.602806"
    app:mapcontrol_centerLng="-122.329330"
    app:mapcontrol_zoom="12"
    app:mapcontrol_style="grayscale_dark"
    />
```

Az alábbi képernyőfelvételen látható, hogy a fenti kód a szürkeárnyalatos sötét stílussal jeleníti meg a közúti térképet.

![Térkép szürkeárnyalatos sötét úthálózati stílussal](media/set-android-map-styles/android-grayscale-dark.png)

## <a name="set-map-style-in-code"></a>Térkép stílusának beállítása a kódban

A Térkép stílusa programozott módon állítható be a kódban a `setStyle` Térkép metódusának használatával. A következő kód a középpont helyét és a nagyítási szintet állítja be a Maps `setCamera` metódus és a Térkép stílusa alapján `SATELLITE_ROAD_LABELS` .

```java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-122.33, 47.64)), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE_ROAD_LABELS));
});
```

Az alábbi képernyőfelvételen a fenti kód látható, amely a műholdas útjelző feliratok stílusát jeleníti meg.

![Térkép a Satellite Road labels stílussal](media/set-android-map-styles/android-satellite-road-labels.png)

## <a name="setting-the-map-camera"></a>A Térkép kamera beállítása

A Térkép kamera azt szabályozza, hogy a Térkép milyen része jelenjen meg a térképen. A kamera lehet elrendezésben programozott módon a kódban. A kód beállításakor két fő módszer áll rendelkezésre a Térkép pozíciójának beállításához; középre és nagyításra, vagy egy határoló mezőben való továbbításra. A következő kód bemutatja, hogyan állíthatja be az összes opcionális kamera beállítást a és a használatakor `center` `zoom` .

```java
//Set the camera of the map using center and zoom.
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.64)), 

    //The zoom level. Typically a value between 0 and 22.
    zoom(14),

    //The amount of tilt in degrees the map where 0 is looking straight down.
    pitch(45),

    //Direction the top of the map is pointing in degrees. 0 = North, 90 = East, 180 = South, 270 = West
    bearing(90),

    //The minimum zoom level the map will zoom-out to when animating from one location to another on the map.
    minZoom(10),
    
    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

Gyakran érdemes a térképet egy adathalmazra összpontosítani. A metódust használó funkciókból kiszámítható egy határolókeret `MapMath.fromData` , amely átadható a `bounds` térképes kamera lehetőségével. Ha a Térkép nézetet egy határolókeret alapján állítja be, akkor gyakran hasznos megadnia azt `padding` az értéket, amelyet a rendszer buborékként vagy szimbólumként megjelenített pontok képpontban való méretének megadására használ. A következő kód azt mutatja be, hogyan állítható be az összes opcionális kamera beállítás, ha egy határolókeret használatával beállítja a kamera pozícióját.

```java
//Set the camera of the map using a bounding box.
map.setCamera(
    //The area to focus the map on.
    bounds(BoundingBox.fromLngLats(
        //West
        -122.4594,

        //South
        47.4333,
        
        //East
        -122.21866,
        
        //North
        47.75758
    )),

    //Amount of pixel buffer around the bounding box to provide extra space around the bounding box.
    padding(20),

    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

Vegye figyelembe, hogy a határolókeret oldalaránya nem egyezhet meg a Térkép méretarányával, mivel a Térkép gyakran a teljes határolókeret területét jeleníti meg, de gyakran csak függőlegesen vagy vízszintesen lesz látható.

## <a name="next-steps"></a>Következő lépések

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Buborékréteg hozzáadása](map-add-bubble-layer-android.md)
