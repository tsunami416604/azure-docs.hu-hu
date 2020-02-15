---
title: Reagálás az események leképezésére Event Grid használatával | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan reagálhat Microsoft Azure Maps-eseményekre Event Grid használatával.
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: f7e5be8d4aca85c9574db18ec9df8a7f30850d06
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210106"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Azure Maps eseményekre való reagálás Event Grid használatával 

A Azure Maps a Azure Event Gridsal integrálódik, így a felhasználók más szolgáltatásoknak is küldhetnek esemény-értesítéseket, és alsóbb rétegbeli folyamatokat indíthatnak. Ennek a cikknek a célja, hogy segítséget nyújtson az üzleti alkalmazások konfigurálásában Azure Maps események figyelésére. Ez lehetővé teszi a felhasználók számára, hogy megbízható, méretezhető és biztonságos módon reagáljanak a kritikus eseményekre. A felhasználók például létrehozhatnak egy alkalmazást egy adatbázis frissítéséhez, egy jegy létrehozásához és egy e-mail-értesítés kézbesítéséhez, valahányszor egy eszköz bekerül egy geokerítésen.

A Azure Event Grid egy teljes körűen felügyelt esemény-útválasztási szolgáltatás, amely egy közzétételi és előfizetési modellt használ. A Event Grid beépített támogatást nyújt az Azure-szolgáltatások, például a [Azure functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) és a [Azure Logic apps](https://docs.microsoft.com/azure/azure-functions/functions-overview)számára. A webhookok használatával az események riasztásait nem Azure-szolgáltatásokhoz is kézbesítheti. A Event Grid által támogatott eseménykezelők teljes listájáért tekintse [meg a Azure Event Grid bemutatása](https://docs.microsoft.com/azure/event-grid/overview)című témakört.


![Azure Event Grid funkcionális modell](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Azure Maps események típusai

Az Event Grid [esemény-előfizetések](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) használatával irányítja az esemény-üzeneteket az előfizetőknek. Egy Azure Maps fiók a következő típusú eseményeket bocsátja ki: 

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Akkor következik be, amikor a fogadott koordináták egy adott geokerítésen kívülre kerültek a következőn belül: |
| Microsoft.Maps.GeofenceExited | Akkor következik be, amikor a fogadott koordináták egy adott geokerítésen kívülre lettek helyezve |
| Microsoft.Maps.GeofenceResult | Minden alkalommal, amikor egy geokerítések-lekérdezés eredményt ad vissza, az állapottól függetlenül |

## <a name="event-schema"></a>Eseményséma

A következő példa a GeofenceResult sémáját mutatja be:

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

* Több előfizetést konfigurálhat az események ugyanahhoz az eseménykezelőhöz való átirányításához. Fontos, hogy ne feltételezzük, hogy az események egy adott forrásból származnak. Mindig ellenőrizze az üzenet témakörét, és győződjön meg arról, hogy az üzenet a várt forrásból származik.
* A válasz fejlécében lévő `X-Correlation-id` mezővel megtudhatja, hogy az objektumok adatai naprakészek-e. Az üzenetek megérkeznek a sorrendbe, vagy késés után is.
* Ha a Geokerítésen API-ban a GET vagy a POST kérést az `EnterAndExit`értékre állítja, akkor létrejön egy Enter vagy Exit esemény a geokerítésen minden olyan geometriájában, amelynek az állapota módosult az előző Geokerítésen API-hívásból.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni arról, hogyan használhatja a geokerítések-t egy építkezési helyen lévő műveletek vezérlésére, tekintse meg a következőt:

> [!div class="nextstepaction"] 
> [Geokerítésen beállítása Azure Maps használatával](tutorial-geofence.md)
