---
title: Event Grid használatával az Azure Maps-eseményekre reagálni |} A Microsoft Docs
description: Megtudhatja, hogyan tudunk reagálni az eseményekre az Azure Maps Event Grid használatával.
author: walsehgal
ms.author: v-musehg
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a70011b934398ac4e7f74bb67013e93bb5e86e4e
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008509"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Az Azure Maps események reagálás az Event Grid használatával 

Az Azure Maps integrálható az Azure Event Griddel, hogy más szolgáltatások és az alsóbb rétegbeli folyamatok eseményindító esemény értesítéseket küldhet. Ez a cikk célja az üzleti alkalmazások úgy, hogy megbízható, méretezhető és biztonságos módon reagálhat a kritikus eseményekre az Azure Maps-események figyelésére való konfigurálásához. Például hozhat létre olyan alkalmazás, amely több műveleteket, például egy adatbázis frissítésével, egy a jegy létrehozása és e-mail-értesítés kézbesítése minden alkalommal, amikor egy eszköz belép a geokerítésen hajtja végre.

Az Azure Event Grid egy teljes körűen felügyelt esemény-útválasztó szolgáltatás, amely egy közzétételi-feliratkozási modell. Az Azure-szolgáltatásokhoz hasonlóan beépített támogatással rendelkezik az Event Grid [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) és [Azure Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-overview), és nem Azure-szolgáltatások, webhookok segítségével közvetíti miatti riasztás. Az eseménykezelőket, amely támogatja az Event Grid teljes listáját lásd: [Azure Event Grid bemutatása](https://docs.microsoft.com/azure/event-grid/overview).


![Az Azure Event Grid működési modellje](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Az Azure Maps eseménytípusok

Event grid használ [esemény-előfizetések](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) eseményt üzenetek továbbítását-előfizetők számára. Az Azure Maps-fiók a következő esemény típusú bocsát ki: 

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Jelenik meg, ha kapott koordináták helyeztünk át belül az egy adott geokerítésen kívül |
| Microsoft.Maps.GeofenceExited | Jelenik meg, ha kapott koordináták helyeztünk át kívül adott a geokerítésen belül |
| Microsoft.Maps.GeofenceResult | Minden alkalommal, amikor a geokerítések lekérdezés visszaadja az eredményt, függetlenül az állapot kiváltása |

## <a name="event-schema"></a>Eseményséma

Az alábbi példa show-sémát a GeofenceResult

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceResult", 
   "eventTime":"2018-11-08T00:52:08.0954283Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

## <a name="tips-for-consuming-events"></a>Tippek az események felhasználásához

Az alkalmazásokat, amelyek az Azure Maps geokerítésen események kezeléséhez kövesse kell néhány ajánlott eljárást:

* Több előfizetés is beállítható úgy, hogy az azonos eseménykezelő események átirányítása. Fontos, nem azt feltételezik, hogy eseményeket adott forrásból származnak. Mindig ellenőrizze az üzenet témakörben győződjön meg arról, hogy a várt forrásból származnak.
* Üzenetek érkezésekor is, üzemen kívüli vagy késleltetéssel. Használja a `X-Correlation-id` Ha naprakész állapotban-e az adatok és objektumok megértése a válaszfejléc mezőbe.
* Amikor Get és POST Geokerítésen API hívása mode paraméter beállítása `EnterAndExit`, az Enter vagy a kilépési esemény jön létre minden egyes a geometriai, amelynek állapota módosult az előző Geokerítésen API-hívás a geokerítésen.

## <a name="next-steps"></a>További lépések

A konstrukció helyen ellenőrzési műveletek geokerítések használatával kapcsolatos további információért, lásd:

> [!div class="nextstepaction"] 
> [Az Azure Maps segítségével egy geokerítésen beállítása](tutorial-geofence.md)