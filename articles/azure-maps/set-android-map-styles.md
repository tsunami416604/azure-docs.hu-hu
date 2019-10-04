---
title: Stílusbeli funkciók leképezése Azure Mapsban | Microsoft Docs
description: Ismerje meg az Android SDK Azure Maps stílussal kapcsolatos funkcióit.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 5bcc73c63e7c9d804d01df98551aa51b81d98d07
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844853"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Térkép stílusának beállítása a Azure Maps Android SDK-val

Ebből a cikkből megtudhatja, hogyan állíthatja be a leképezési stílusokat a Azure Maps Android SDK használatával. A Azure Maps hat különböző leképezési stílus közül választhat. A támogatott térképi stílusokkal kapcsolatos további információkért tekintse [meg a Azure Maps támogatott leképezési stílusait](./supported-map-styles.md).


## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő folyamat elvégzéséhez telepítenie kell [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) -t egy Térkép betöltéséhez.


## <a name="set-map-style-in-the-layout"></a>Térkép stílusának beállítása az elrendezésben

A tevékenység osztályának elrendezési fájljában megadhat egy térképi stílust. Szerkessze a **res > elrendezést > activity_main. xml fájlt**, így a következőképpen néz ki:

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

A `mapcontrol_style` fenti attribútum a Térkép stílusát **grayscale_dark**állítja be. 

<center>

![style-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Térkép stílusának beállítása a tevékenység osztályban

A Térkép stílusa beállítható a tevékenység osztályban. Másolja az alábbi kódrészletet az `MainActivity.java` osztály **onCreate ()** metódusára. Ezzel a **satellite_road_labels**fogja beállítani a leképezési stílust.

```Java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Style-Satellite-Road-labels](./media/set-android-map-styles/satellite-road-labels.png)</center>