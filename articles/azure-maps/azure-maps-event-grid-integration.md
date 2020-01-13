---
title: Reagálás az események leképezésére Event Grid használatával | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan reagálhat Microsoft Azure Maps-eseményekre Event Grid használatával.
author: walsehgal
ms.author: v-musehg
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9a946d189706c9c789ab884670d13b0b3e7fcb0c
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911813"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Azure Maps eseményekre való reagálás Event Grid használatával 

A Azure Maps a Azure Event Grid segítségével integrálható, így értesítéseket küldhet más szolgáltatásoknak, és az alsóbb rétegbeli folyamatokat is elindíthatja. Ennek a cikknek a célja, hogy segítséget nyújtson az üzleti alkalmazások konfigurálásában Azure Maps események figyelésére, hogy megbízható, méretezhető és biztonságos módon reagáljon a kritikus eseményekre. Létrehozhat például egy olyan alkalmazást, amely több műveletet hajt végre, például egy adatbázis frissítését, egy jegy létrehozását és egy e-mail-értesítés kézbesítését minden alkalommal, amikor egy eszköz bekerül egy geokerítésen.

A Azure Event Grid egy teljes körűen felügyelt esemény-útválasztási szolgáltatás, amely egy közzétételi és előfizetési modellt használ. Event Grid beépített támogatást biztosít az Azure-szolgáltatásokhoz, például a [Azure Functionshoz](https://docs.microsoft.com/azure/azure-functions/functions-overview) és a [Azure Logic Appshoz](https://docs.microsoft.com/azure/azure-functions/functions-overview), és az események riasztásait a nem Azure-szolgáltatásokhoz webhookok használatával lehet kézbesíteni. A Event Grid által támogatott eseménykezelők teljes listájáért tekintse [meg a Azure Event Grid bemutatása](https://docs.microsoft.com/azure/event-grid/overview)című témakört.


![Azure Event Grid funkcionális modell](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Azure Maps események típusai

Az Event Grid [esemény-előfizetések](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) használatával irányítja az esemény-üzeneteket az előfizetőknek. Egy Azure Maps fiók a következő típusú eseményeket bocsátja ki: 

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft. maps. GeofenceEntered | Akkor következik be, amikor a kapott koordináták egy adott geokerítésen kívülre kerültek a következőn belül: |
| Microsoft. maps. GeofenceExited | Akkor következik be, amikor a kapott koordináták egy adott geokerítésen kívülre kerültek |
| Microsoft. maps. GeofenceResult | Minden alkalommal, amikor egy geokerítések-lekérdezés eredményt ad vissza, az állapottól függetlenül |

## <a name="event-schema"></a>Eseményséma

Az alábbi példa a GeofenceResult sémáját mutatja be

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

## <a name="tips-for-consuming-events"></a>Tippek az események fogyasztásához

Az Azure Maps geokerítésen-eseményeket kezelő alkalmazásoknak néhány ajánlott gyakorlatot követniük kell:

* Több előfizetés is konfigurálható az események ugyanahhoz az eseménykezelőhöz való továbbítására. Fontos, hogy ne feltételezzük, hogy az események egy adott forrásból származnak. Mindig ellenőrizze az üzenet témakörét, és győződjön meg arról, hogy a várt forrásból származik.
* Az üzenetek megérkeznek a sorrendbe, vagy késés után is. A válasz fejlécében lévő `X-Correlation-id` mezővel megtudhatja, hogy az objektumok adatai naprakészek-e.
* Ha a Get és a POST Geokerítésen API-t úgy hívja meg, hogy a mode paraméter értéke `EnterAndExit`, akkor a rendszer egy Enter vagy Exit eseményt hoz létre a geokerítésen minden olyan geometriájában, amelynek az állapota módosult az előző Geokerítésen API-hívásból.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni arról, hogyan használhatja a geokerítések-t egy építkezési helyen lévő műveletek vezérlésére, tekintse meg a következőt:

> [!div class="nextstepaction"] 
> [Geokerítésen beállítása Azure Maps használatával](tutorial-geofence.md)