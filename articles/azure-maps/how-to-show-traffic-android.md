---
title: Forgalmi adatok megjelenítése az Android térképen | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan jelenítheti meg a forgalmi adatokat egy térképen a Microsoft Azure Maps Android SDK használatával.
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 37de55d671bb19cfcd9fd494c2e76f658fc7db21
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78249494"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>Forgalmi adatok megjelenítése a térképen Azure Maps Android SDK használatával

A flow-adatok és az incidensek az adatok a térképen megjeleníthető kétféle forgalmi adatok. Ez az útmutató bemutatja, hogyan jelenítheti meg mindkét típusú forgalmi adatokat. Az incidensek olyan pont-és vonal-alapú adatmennyiségből állnak, mint a konstrukciók, a közúti bezárások és a balesetek. A flow adatai az úton lévő forgalom forgalmára vonatkozó metrikákat jelenítenek meg.

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy megjelenjen a forgalom a térképen, [Azure-fiókot kell létrehoznia](quick-demo-map-app.md#create-an-account-with-azure-maps), és [be kell szereznie egy előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account). Ezután telepítenie kell a [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) -t, és be kell töltenie egy térképet.

## <a name="incidents-traffic-data"></a>Incidensek forgalmi adatok 

A következő könyvtárakat kell importálnia `setTraffic` és `incidents`meghívásához:

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 A következő kódrészlet bemutatja, hogyan jelenítheti meg a forgalmi adatokat a térképen. Egy logikai értéket adunk át a `incidents` metódusnak, és ezt a `setTraffic` metódusnak adja át. 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>Flow forgalmi adatok

Először importálnia kell a következő könyvtárakat a `setTraffic` és `flow`meghívásához:

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

Az alábbi kódrészlettel állíthatja be a forgalmi folyamatra vonatkozó adatokat. Az előző szakaszban szereplő kódhoz hasonlóan a `flow` metódus visszatérési értékét is átadja a `setTraffic` metódusnak. Négy érték adható át `flow`nek, és minden érték kiváltja `flow` a megfelelő érték visszaadása érdekében. A `flow` visszatérési értéke ezután a `setTraffic`argumentumként lesz átadva. A következő négy értéknél tekintse meg az alábbi táblázatot:

| | |
| :-- | :-- |
| Forgalomáramlási. NONE | Nem jeleníti meg a forgalmi adatokat a térképen |
| Forgalomáramlási. relatív | Az út szabad áramlási sebességéhez viszonyított forgalmi adatokat jeleníti meg |
| Forgalomáramlási. RELATIVE_DELAY | A várt átlagos késésnél lassabb területeket jelenít meg. |
| Forgalomáramlási. ABSOLUTE | Az összes jármű teljes sebességét jeleníti meg az úton |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>Incidens forgalmi adatok megjelenítése egy szolgáltatásra kattintva

Egy adott szolgáltatás incidensének beszerzéséhez használhatja az alábbi kódot. Ha egy szolgáltatásra kattint, a kód logikája ellenőrzi az incidenseket, és létrehoz egy üzenetet az incidensről. Egy üzenet jelenik meg a képernyő alján a részletekkel.

1. Először szerkesztenie kell a **res > elrendezést > activity_main. XML fájlból**, hogy az alábbihoz hasonlóan néz ki. A kívánt értékekkel helyettesítheti a `mapcontrol_centerLat`, `mapcontrol_centerLng`és `mapcontrol_zoom`. Visszahívás: a nagyítási szint 0 és 22 közötti érték. A 0. nagyítási szinten a teljes világ egyetlen csempére illeszkedik.

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
           app:mapcontrol_centerLat="47.6050"
           app:mapcontrol_centerLng="-122.3344"
           app:mapcontrol_zoom="12"
           />

   </FrameLayout>
   ```

2. Adja hozzá a következő kódot a **MainActivity. Java** fájlhoz. Alapértelmezés szerint a csomag szerepel, ezért ügyeljen arra, hogy a csomag felül legyen.

   ```java
   package <yourpackagename>;
   import androidx.appcompat.app.AppCompatActivity;

   import android.os.Bundle;
   import android.widget.Toast;

   import com.microsoft.azure.maps.mapcontrol.AzureMaps;
   import com.microsoft.azure.maps.mapcontrol.MapControl;
   import com.mapbox.geojson.Feature;
   import com.microsoft.azure.maps.mapcontrol.events.OnFeatureClick;

   import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.incidents;

   public class MainActivity extends AppCompatActivity {

       static {
           AzureMaps.setSubscriptionKey("Your Azure Maps Subscription Key");
       }

       MapControl mapControl;

       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);

           mapControl = findViewById(R.id.mapcontrol);

           mapControl.onCreate(savedInstanceState);

           //Wait until the map resources are ready.
           mapControl.getMapAsync(map -> {

               map.setTraffic(flow(TrafficFlow.RELATIVE));
               map.setTraffic(incidents(true));

               map.events.add((OnFeatureClick) (features) -> {

                   if (features != null && features.size() > 0) {
                       Feature incident = features.get(0);
                       if (incident.properties() != null) {


                           StringBuilder sb = new StringBuilder();
                           String incidentType = incident.getStringProperty("incidentType");
                           if (incidentType != null) {
                               sb.append(incidentType);
                           }
                           if (sb.length() > 0) sb.append("\n");
                           if ("Road Closed".equals(incidentType)) {
                               sb.append(incident.getStringProperty("from"));
                           } else {
                               String description = incident.getStringProperty("description");
                               if (description != null) {
                                   for (String word : description.split(" ")) {
                                       if (word.length() > 0) {
                                           sb.append(word.substring(0, 1).toUpperCase());
                                           if (word.length() > 1) {
                                               sb.append(word.substring(1));
                                           }
                                           sb.append(" ");
                                       }
                                   }
                               }
                           }
                           String message = sb.toString();

                           if (message.length() > 0) {
                               Toast.makeText(this,message,Toast.LENGTH_LONG).show();
                           }
                       }
                   }
               });
           });
       }

       @Override
       public void onResume() {
           super.onResume();
           mapControl.onResume();
       }

       @Override
       protected void onStart(){
           super.onStart();
           mapControl.onStart();
       }

       @Override
       public void onPause() {
           super.onPause();
           mapControl.onPause();
       }

       @Override
       public void onStop() {
           super.onStop();
           mapControl.onStop();
       }

       @Override
       public void onLowMemory() {
           super.onLowMemory();
           mapControl.onLowMemory();
       }

       @Override
       protected void onDestroy() {
           super.onDestroy();
           mapControl.onDestroy();
       }

       @Override
       protected void onSaveInstanceState(Bundle outState) {
           super.onSaveInstanceState(outState);
           mapControl.onSaveInstanceState(outState);
       }
   }
   ```

3. Miután beépíti a fenti kódot az alkalmazásba, rákattinthat egy szolgáltatásra, és megtekintheti a forgalmi incidensek részleteit. A szélesség, a hosszúság és a **activity_main. XML** fájlban használt nagyítási szint értékeitől függően az alábbi képhez hasonló eredmények jelennek meg:

   <center>

   ![Incidens-Traffic-on-the-Map](./media/how-to-show-traffic-android/android-traffic.png)

   </center>

## <a name="next-steps"></a>Következő lépések

A következő útmutatókból megtudhatja, hogyan adhat hozzá további információkat a térképhez:

> [!div class="nextstepaction"]
> [Szimbólum réteg hozzáadása](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Csempe rétegének hozzáadása](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [Alakzatok hozzáadása az Android térképhez](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Szolgáltatás adatainak megjelenítése](display-feature-information-android.md)
