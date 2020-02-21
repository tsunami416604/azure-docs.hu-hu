---
title: Azure Event Grid Azure Maps esemény sémája
description: A Azure Maps eseményekhez megadott tulajdonságokat és sémát ismerteti Azure Event Grid
services: event-grid
author: femila
ms.service: event-grid
ms.topic: reference
ms.date: 02/08/2019
ms.author: femila
ms.openlocfilehash: 9acef524521e8fac6ce6f8f61e5ff3fbbb81d18d
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77486359"
---
# <a name="azure-event-grid-event-schema-for-azure-maps"></a>Azure Maps Azure Event Gridi esemény sémája

Ez a cikk a Azure Maps eseményeinek tulajdonságait és sémáját ismerteti. Az események sémáinak bemutatása: [Azure Event Grid Event Schema](https://docs.microsoft.com/azure/event-grid/event-schema).

## <a name="available-event-types"></a>Elérhető események típusai

Egy Azure Maps fiók a következő típusú eseményeket bocsátja ki:

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Akkor következik be, amikor a kapott koordináták egy adott geokerítésen kívülre kerültek a következőn belül: |
| Microsoft.Maps.GeofenceExited | Akkor következik be, amikor a kapott koordináták egy adott geokerítésen kívülre kerültek |
| Microsoft.Maps.GeofenceResult | Minden alkalommal, amikor egy geokerítések-lekérdezés eredményt ad vissza, az állapottól függetlenül |

## <a name="event-examples"></a>Példák az eseményekre

Az alábbi példa egy **GeofenceEntered** -esemény sémáját mutatja be.

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceEntered", 
   "eventTime":"2018-11-08T00:54:17.6408601Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

Az alábbi példa a **GeofenceResult** sémáját mutatja be 

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

## <a name="event-properties"></a>Esemény tulajdonságai

Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Event Grid megadja ezt az értéket. |
| subject | sztring | Közzétevő által megadott elérési út az esemény tárgya számára. |
| eventType | sztring | Az eseményforrás egyik regisztrált eseménytípus. |
| eventTime | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| id | sztring | Az esemény egyedi azonosítója. |
| data | objektum | Geokerítések. |
| dataVersion | sztring | Az adatobjektum séma-verziója. A közzétevő határozza meg a séma verzióját. |
| metadataVersion | sztring | Az esemény metaadatainak séma-verziója. Event Grid a legfelső szintű tulajdonságok sémáját határozza meg. Event Grid megadja ezt az értéket. |

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| apiCategory | sztring | Az esemény API-kategóriája. |
| apiName | sztring | Az esemény API-neve. |
| issues | objektum | A feldolgozás során felmerülő problémák felsorolása. Ha bármilyen hibát ad vissza, akkor a válaszban nem lesznek visszaadott geometriák. |
| responseCode | szám | HTTP-válasz kódja |
| geometries | objektum | Felsorolja a koordináta pozícióját tartalmazó kerítési geometriákat, vagy átfedésben van a searchBuffer a pozíció körül. |

A rendszer visszaadja a hiba objektumot, ha hiba történik a Maps API-ban. A hiba objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| error | ErrorDetails |Ezt az objektumot akkor adja vissza a rendszer, ha hiba történik a Maps API-ban  |

A ErrorDetails objektum visszaadása akkor történik meg, ha hiba lép fel a Maps API-ban. A ErrorDetails vagy objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| code | sztring | A HTTP-állapotkód. |
| message | sztring | Ha elérhető, a hiba olvasható leírása. |
| innererror | InnerError | Ha elérhető, a hibával kapcsolatos szolgáltatás-specifikus adatokat tartalmazó objektum. |

A InnerError egy olyan objektum, amely szolgáltatás-specifikus adatokat tartalmaz a hibáról. A InnerError objektum a következő tulajdonságokkal rendelkezik: 

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| code | sztring | A hibaüzenet. |

A geometriák objektum felsorolja a kérelemben a felhasználói időponthoz képest lejárt geofences geometriai azonosítóit. A geometriák objektum a következő tulajdonságokkal rendelkező geometriai elemeket tartalmaz: 

| Tulajdonság | Típus | Leírás |
|:-------- |:---- |:----------- |
| deviceid | sztring | Az eszköz azonosítója. |
| távolságskála | sztring | <p>A koordináta távolsága a geokerítésen legközelebbi szegélyéhez. A pozitív érték azt jelenti, hogy a koordináta a geokerítésen kívül esik. Ha a koordináta a geokerítésen kívül esik, de a legközelebb geokerítésen searchBuffer nagyobb a távolság, akkor az érték 999. Negatív érték azt jelenti, hogy a koordináta a geokerítésen belül van. Ha a koordináta a sokszögen belül van, de a legközelebbi geokerítések searchBuffer nem, akkor az érték-999. A 999 érték azt jelenti, hogy nagy a megbízhatóság, hogy a koordináta jól kívül van a geokerítésen. A-999 érték azt jelenti, hogy nagy a megbízhatóság, hogy a koordináta jól működik a geokerítésen belül.<p> |
| geometryid |sztring | Az egyedi azonosító azonosítja a geokerítésen geometriát. |
| nearestlat | szám | A geometria legközelebbi pontjának földrajzi szélessége |
| nearestlon | szám | A geometria legközelebbi pontjának hosszúsága |
| udId | sztring | A felhasználói feltöltési szolgáltatás által visszaadott egyedi azonosító a geokerítésen feltöltésekor. Nem fog szerepelni a geokerítések post API-ban. |

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| expiredGeofenceGeometryId | string[] | Azon geokerítésen geometria-AZONOSÍTÓinak listája, amelyek a kérelemben megadott felhasználói időhöz képest lejárnak. |
| geometries | geometriák [] |Felsorolja a koordináta pozícióját tartalmazó kerítési geometriákat, vagy átfedésben van a searchBuffer a pozíció körül. |
| invalidPeriodGeofenceGeometryId | string[]  | A kérelemben szereplő felhasználói időhöz képest érvénytelen időszakra vonatkozó geokerítésen geometriai AZONOSÍTÓjának listája. |
| isEventPublished | logikai | Igaz, ha legalább egy eseményt közzétesznek a Azure Maps esemény előfizetője számára, hamis értéket, ha egyetlen eseményt sem tesznek közzé a Azure Maps esemény előfizetője számára. |

## <a name="next-steps"></a>Következő lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).