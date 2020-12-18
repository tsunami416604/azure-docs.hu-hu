---
title: Forgalmi adatok megjelenítése az Android-térképeken | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan jelenítheti meg a forgalmi adatokat egy térképen a Microsoft Azure Maps Android SDK használatával.
author: rbrundritt
ms.author: richbrun
ms.date: 12/04/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 113f39ac2976b870c9e07851cdd0919e2578940f
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680462"
---
# <a name="show-traffic-data-on-the-map-android-sdk"></a>Forgalmi adatok megjelenítése a térképen (Android SDK)

A flow-adatok és az incidensek az adatok a térképen megjeleníthető kétféle forgalmi adatok. Ez az útmutató bemutatja, hogyan jelenítheti meg mindkét típusú forgalmi adatokat. Az incidensek olyan pont-és vonal-alapú adatmennyiségből állnak, mint a konstrukciók, a közúti bezárások és a balesetek. A flow adatai az úton lévő forgalom forgalmára vonatkozó metrikákat jelenítenek meg.

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy a gyors útmutató [: Android-alkalmazás létrehozása](quick-android-map.md) dokumentum lépéseit kell végrehajtania. A cikkben szereplő kódrészletek behelyezhetők a Maps `onReady` Event handlerbe.

## <a name="show-traffic-on-the-map"></a>Forgalom megjelenítése a térképen

A Azure Mapsban kétféle forgalmi adatok érhetők el:

- Incidensek – olyan pont-és vonal-alapú adatmennyiségből állnak, mint például az építőipar, a közúti bezárások és a balesetek.
- Flow-adatok – mérőszámokat biztosít a forgalom forgalmáról az utakon. Gyakran a forgalmi forgalomra vonatkozó adatokat használják az utak színezésére. A színek attól függnek, hogy mekkora forgalom lassul le a folyamaton, a sebességkorlátozás vagy más metrika alapján. Négy érték adható át a `flow` Térkép forgalmi lehetőségéhez.

    |Folyamat értéke | Description (Leírás)|
    | :-- | :-- |
    | Forgalomáramlási. NONE | Nem jeleníti meg a forgalmi adatokat a térképen |
    | Forgalomáramlási. relatív | Az út szabad áramlási sebességéhez viszonyított forgalmi adatokat jeleníti meg |
    | TrafficFlow.RELATIVE_DELAY | A várt átlagos késésnél lassabb területeket jelenít meg. |
    | Forgalomáramlási. ABSOLUTE | Az összes jármű teljes sebességét jeleníti meg az úton |

A következő kód bemutatja, hogyan jelenítheti meg a forgalmi adatokat a térképen.

```java
//Show traffic on the map using the traffic options.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);
```

Az alábbi képernyőfelvételen a fenti kódot tépő valós idejű forgalmi információk láthatók a térképen.

![Valós idejű forgalmi adatokat megjelenítő Térkép](media/how-to-show-traffic-android/android-show-traffic.png)

## <a name="get-traffic-incident-details"></a>Forgalmi incidens részleteinek beolvasása

A forgalmi incidens részleteit az incidens a térképen való megjelenítéséhez használt funkció tulajdonságai között érheti el. A forgalmi incidenseket a rendszer a Azure Maps Traffic incidens Vector csempe szolgáltatással adja hozzá a térképhez. A vektoros csempék adatformátuma, ha [itt van dokumentálva](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles). A következő kód egy Click eseményt helyez el a térképhez, és lekéri a forgalmi incidens funkcióját, amelyre rákattintott, és egy Toast-üzenetet jelenít meg a részletek közül.

```java
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);

//Add a click event to the map.
map.events.add((OnFeatureClick) (features) -> {

    if (features != null && features.size() > 0) {
        Feature incident = features.get(0);

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {

            StringBuilder sb = new StringBuilder();
            String incidentType = incident.getStringProperty("incidentType");

            if (incidentType != null) {
                sb.append(incidentType);
            }

            if (sb.length() > 0) {
                sb.append("\n");
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed".equals(incidentType)) {
                String from = incident.getStringProperty("from");

                if (from != null) {
                    sb.append(from);
                }
            } else {
                //Get the description of the traffic incident.
                String description = incident.getStringProperty("description");

                if (description != null) {
                    sb.append(description);
                }
            }

            String message = sb.toString();

            if (message.length() > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show();
            }
        }
    }
});
```

Az alábbi képernyőfelvételen a tépő valós idejű forgalmi információi láthatók a térképen, és egy Toast üzenet jeleníti meg az incidens részleteit.

![A valós idejű forgalmi információk megjelenítése egy Toast üzenet megjelenítésével – incidens részletei](media/how-to-show-traffic-android/android-traffic-details.png)

## <a name="next-steps"></a>Következő lépések

A következő útmutatókból megtudhatja, hogyan adhat hozzá további információkat a térképhez:

> [!div class="nextstepaction"]
> [Mozaikréteg hozzáadása](how-to-add-tile-layer-android-map.md)
