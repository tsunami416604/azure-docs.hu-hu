---
title: Térkép stílusának beállítása a Azure Maps Android SDK-val
description: Ismerje meg, hogyan állíthatja be a Térkép stílusát. A stílus módosításához tekintse meg, hogyan használhatja az Microsoft Azure Maps Android SDK-t az elrendezési fájlban vagy a tevékenység osztályban.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8c7689fb87575ac6e150f793b43f35e8bf6adc83
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532479"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Térkép stílusának beállítása a Azure Maps Android SDK-val

Ez a cikk bemutatja, hogyan állíthatja be a leképezési stílusokat a Azure Maps Android SDK használatával. A Azure Maps hat különböző leképezési stílus közül választhat. A támogatott térképi stílusokkal kapcsolatos további információkért tekintse [meg a Azure Maps támogatott leképezési stílusait](./supported-map-styles.md).

## <a name="prerequisites"></a>Előfeltételek

1. [Azure Maps fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot.
3. Töltse le és telepítse a [Azure Maps Android SDK](./how-to-use-android-map-control-library.md)-t.


## <a name="set-map-style-in-the-layout"></a>Térkép stílusának beállítása az elrendezésben

A tevékenység osztályának elrendezési fájljában megadhat egy térképi stílust. Szerkessze úgy `res > layout > activity_main.xml` , hogy az alábbihoz hasonlóan néz ki:

```XML
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    >

    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/mapcontrol"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

A `mapcontrol_style` fenti attribútum a leképezési stílust **grayscale_darkre** állítja.

:::image type="content" source="./media/set-android-map-styles/grayscale-dark.png" border="true" alt-text="Azure Maps, Térkép képének ábrázolása grayscale_dark":::

## <a name="set-map-style-in-the-mainactivity-class"></a>Térkép stílusának beállítása a MainActivity osztályban

A Térkép stílusa a MainActivity osztályban is megadható. Nyissa meg a `java > com.example.myapplication > MainActivity.java` fájlt, és másolja a következő kódrészletet a **onCreate ()** metódusba. Ez a kód **satellite_road_labelsre** állítja a leképezési stílust.

>[!WARNING]
>Előfordulhat, hogy a Android Studio nem importálta a szükséges osztályokat.  Ennek eredményeképpen a kódnak néhány feloldhatatlan-hivatkozása lesz. A szükséges osztályok importálásához egyszerűen vigye az egérmutatót az egyes megoldatlan hivatkozások fölé, és nyomja meg a `Alt + Enter` (Option + Return on a Mac-t).

```Java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle((style(SATELLITE_ROAD_LABELS)));
       
});
```

:::image type="content" source="./media/set-android-map-styles/satellite-road-labels.png" border="true" alt-text="Azure Maps, Térkép képének ábrázolása satellite_road_labels":::