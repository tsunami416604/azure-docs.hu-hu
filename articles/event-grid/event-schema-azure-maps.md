---
title: Az Azure Event Grid Azure Maps eseménysémája
description: Ismerteti a tulajdonságok és az Azure Maps-események az Azure Event Griddel megadott séma
services: event-grid
author: walsehgal
ms.service: event-grid
ms.topic: reference
ms.date: 02/08/2019
ms.author: v-musehg
ms.openlocfilehash: 74a3674e632f8dc3f0755bc2ad48376708c7966f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008383"
---
# <a name="azure-event-grid-event-schema-for-azure-maps"></a>Az Azure Event Grid-esemény séma az Azure Maps

Ez a cikk a séma és az Azure Maps-események. Eseménysémák szeretné megismerni, lásd: [Azure Event Grid-esemény séma](https://docs.microsoft.com/azure/event-grid/event-schema).

## <a name="available-event-types"></a>Rendelkezésre álló események típusai

Az Azure Maps-fiók a következő esemény típusú bocsát ki:

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Jelenik meg, ha kapott koordináták helyeztünk át belül az egy adott geokerítésen kívül |
| Microsoft.Maps.GeofenceExited | Jelenik meg, ha kapott koordináták helyeztünk át kívül adott a geokerítésen belül |
| Microsoft.Maps.GeofenceResult | Minden alkalommal, amikor a geokerítések lekérdezés visszaadja az eredményt, függetlenül az állapot kiváltása |

## <a name="event-examples"></a>Esemény-példák

Az alábbi példa bemutatja a sémája egy **GeofenceEntered** esemény

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

Az alábbi példa show-sémát a **GeofenceResult** 

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

Egy esemény a következő legfelső szintű adatokat tartalmaz:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | A forrás teljes erőforrás elérési útja. Ez a mező nem írható. Event Grid biztosítja ezt az értéket. |
| tárgy | sztring | Az esemény tárgya közzétevő által megadott elérési útja. |
| eventType | sztring | Ehhez eseményre adatforráshoz regisztrált esemény típusok egyikét. |
| eventTime | sztring | Az esemény akkor jön létre az idő alapján a szolgáltató UTC idő. |
| id | sztring | Az esemény egyedi azonosítója. |
| adat | objektum | A Geokerítések eseményadatokat. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A közzétevő a sémaverziót határozza meg. |
| metadataVersion | sztring | Az esemény-metaadatok sémaverziója. Event Grid sémáját, a legfelső szintű tulajdonságait határozza meg. Event Grid biztosítja ezt az értéket. |

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| apiCategory | sztring | Az esemény kategóriája API-t. |
| ApiName | sztring | Az esemény API neve. |
| Problémák | objektum | Megtekintheti a feldolgozás során észlelt problémákat. Ha problémákat ad vissza, majd lesz nem adott vissza a visszajelzéshez geometriája. |
| responseCode | szám | HTTP-válaszkód |
| geometriája | objektum | Az időkorlát geometriája a koordináta tartalmazó listák helyezze, vagy átfedésben vannak a searchBuffer pozíciója körül. |

Ha hiba lép fel, a Maps API-ban a hibaobjektum adja vissza. A hiba objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| error | ErrorDetails |Ezt az objektumot ad vissza, ha hiba lép fel, a Maps API-ban  |

Hiba esetén a Maps API-ban a ErrorDetails objektumot adja vissza. A ErrorDetails vagy az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| Kód | sztring | A HTTP-állapotkódot. |
| message | sztring | Ha elérhető, a hiba az emberi olvasható leírása. |
| innererror | InnerError | Ha elérhető, szolgáltatásspecifikus a hibával kapcsolatos információkat tartalmazó objektumot. |

A InnerError szolgáltatásspecifikus a hibával kapcsolatos információkat tartalmazó objektumot. A InnerError objektum a következő tulajdonságokkal rendelkezik: 

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| Kód | sztring | A hibaüzenet. |

A geometriája objektum geometriai a geokerítések lejárt a kérelemben szereplő felhasználó atáridő azonosítóit sorolja fel. A geometriája objektum geometriai elemek a következő tulajdonságokkal rendelkezik: 

| Tulajdonság | Típus | Leírás |
|:-------- |:---- |:----------- |
| az eszközazonosító | sztring | Eszköz azonosítója. |
| távolságskála | sztring | <p>A geokerítésen legközelebbi szegélyének és a koordinátán közötti távolság. Pozitív azt jelenti, hogy a koordináta kívül a geokerítésen. Ha a koordináta kívül esik a geokerítésen, de több mint searchBuffer, erről a legközelebbi geokerítésen szegély értékét, majd értéke 999 közötti. Negatív azt jelenti, hogy a koordináta a geokerítésen belül. Ha a koordináta belül a sokszög, de több mint searchBuffer, erről a legközelebbi geokerítések szegély értékét, majd értéke-999. Egy érték 999 azt jelenti, hogy nincs-e nagyszerű magabiztosan a koordináta is a geokerítésen kívül esik. Egy érték-999 azt jelenti, hogy nincs-e nagyszerű magabiztosan a koordináta is a geokerítésen belül van.<p> |
| geometryid |sztring | Egyedi azonosítója a geokerítésen geometriai azonosítja. |
| nearestlat | szám | A legközelebbi pont a geometriai, földrajzi szélessége |
| nearestlon | szám | A legközelebbi pont a geometriai, földrajzi hosszúság. |
| udId | sztring | A geokerítésen feltöltése közben, a felhasználó feltöltési szolgáltatás által visszaadott tábla egyedi azonosítója. Nem szerepelni fog a geokerítések küldése API. |

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| expiredGeofenceGeometryId | String] | A kérelemben szereplő felhasználó atáridő lejárt a geokerítésen geometriai azonosítója listája. |
| geometriája | geometriája] |Az időkorlát geometriája a koordináta tartalmazó listák helyezze, vagy átfedésben vannak a searchBuffer pozíciója körül. |
| invalidPeriodGeofenceGeometryId | String]  | Geometriai azonosítója, amely szerepel a kérelemben szereplő felhasználó atáridő időtartam érvénytelen. a geokerítésen listája. |
| isEventPublished | logikai | Igaz értéket legalább egy esemény közzétételekor az Azure Maps esemény előfizető, false (hamis), ha nincs esemény közzé van téve az Azure Maps esemény előfizető. |

## <a name="next-steps"></a>További lépések

* Azure Event Grid bemutatása, lásd: [Mi az Event Grid?](overview.md)
* Az Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid-előfizetés séma](subscription-creation-schema.md).