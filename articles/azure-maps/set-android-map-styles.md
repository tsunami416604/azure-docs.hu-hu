---
title: Térképstílus beállítása az Azure Maps Android SDK használatával| Microsoft Azure Maps
description: Ebben a cikkben megismerheti az Android SDK Microsoft Azure Maps stílussal kapcsolatos funkcióit.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a4d761abf54682ed0263922d0a118debc9eccf0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334351"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Térképstílus beállítása az Azure Maps Android SDK használatával

Ez a cikk két módon állíthatja be a térképstílusokat az Azure Maps Android SDK használatával. Az Azure Maps hat különböző térképstílus közül választhat. A támogatott térképstílusokról az [Azure Maps támogatott térképstílusok című témakörében](./supported-map-styles.md)talál további információt.


## <a name="prerequisites"></a>Előfeltételek

A folyamat befejezéséhez ebben a cikkben telepítenie kell az [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) betölteni egy térképet.


## <a name="set-map-style-in-the-layout"></a>Térképstílus beállítása az elrendezésben

A tevékenységosztály elrendezésfájljában térképstílust állíthat be. Az **activity_main.xml > > szerkesztése,** így az alábbielrendezésnek tűnik:

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

A `mapcontrol_style` fenti attribútum a térképstílust **grayscale_dark**állítja. 

<center>

![stílus-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Térképstílus beállítása a tevékenységosztályban

A térképstílus beállítható a tevékenységosztályban. Másolja a következő kódrészletet az `MainActivity.java` osztály **onCreate()** metódusába. Ez a kód a térkép stílusát **satellite_road_labels**állítja.

```Java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![stílus-műholdas-road-címkék](./media/set-android-map-styles/satellite-road-labels.png)</center>