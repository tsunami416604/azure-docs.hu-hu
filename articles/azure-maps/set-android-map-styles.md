---
title: Képezze le az Azure Maps funkciói stílus |} A Microsoft Docs
description: További tudnivalók az Azure Maps stílus kapcsolódó funkciókat Android SDK-t.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3c8c5d4bae16d8e15c8f2c5b1cc8e00eb14e4ce3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64870972"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Az Azure Maps Android SDK térkép stílusának beállítása

Ez a cikk bemutatja az Azure Maps Android SDK-val térkép stílus beállítása két különböző módon. Az Azure Maps rendelkezik hat különböző maps stílusok közül választhat. Támogatott térkép stílusok kapcsolatos további információkért lásd: [térkép stílusok támogatott az Azure Maps](./supported-map-styles.md).


## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben a folyamat befejezéséhez, telepítenie kell [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) betölteni a térképen.


## <a name="set-map-style-in-the-layout"></a>Az elrendezés térkép stílusának beállítása

Beállíthat egy térkép stílus az elrendezés fájl a tevékenység osztályból. Szerkesztés **res > elrendezés > activity_main.xml**, így az alábbihoz hasonlóan néz ki:

```XML
<?xml version="1.0" encoding="utf-8"?>
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

A `mapcontrol_style` attribútumot a fentiek közül beállítja a térkép stílus **grayscale_dark**. 

<center>

![style-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>A tevékenység osztály térkép stílusának beállítása

Térkép stílus állítható be a tevékenység osztályból. Másolja a következő kódrészletet a **onCreate()** módszere a `MainActivity.java` osztály. A térkép stílust állítja **satellite_road_labels**.

```Java
    mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![style-satellite-road-labels](./media/set-android-map-styles/satellite-road-labels.png)</center>