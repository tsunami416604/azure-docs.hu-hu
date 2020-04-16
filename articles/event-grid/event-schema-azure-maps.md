---
title: Az Azure Maps eseményrács forrásaként
description: Az Azure Maps-események és az Azure Event Grid szolgáltatásban megadott tulajdonságok és séma ismertetése
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: e879ec3442f2e7912acb450a97079d80d7d95a01
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393404"
---
# <a name="azure-maps-as-an-event-grid-source"></a>Az Azure Maps eseményrács-forrásként

Ez a cikk az Azure Maps-események tulajdonságait és sémáját tartalmazza. Az eseménysémák bemutatása az [Azure Event Grid eseménysémájában.](https://docs.microsoft.com/azure/event-grid/event-schema) Emellett az Azure Maps eseményforrásként való használatához az Azure Maps használatával használható gyorsindítások és oktatóanyagok listáját is tartalmazza.

## <a name="event-grid-event-schema"></a>Eseményrács eseménysémája

### <a name="available-event-types"></a>Elérhető eseménytípusok

Az Azure Maps-fiók a következő eseménytípusokat bocsátja ki:

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Amikor a kapott koordináták egy adott geokerítésen kívülről a megadott geokerítésen belülre |
| Microsoft.Maps.GeofenceKilépett | Akkor emelkedik, ha a kapott koordináták egy adott geokerítésen belülről külső |
| Microsoft.Maps.GeofenceEredmény | Minden alkalommal, amikor egy geofencing lekérdezés eredményt ad vissza, függetlenül az állapottól |

### <a name="event-examples"></a>Példák eseményre

A következő példa egy **GeofenceEntered** esemény sémáját mutatja be.

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

A következő példa a **GeofenceResult** sémáját mutatja be. 

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

### <a name="event-properties"></a>Esemény tulajdonságai

Egy esemény legfelső szintű adatokat rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | Az eseményforrás teljes erőforráselérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| Tárgy | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| eventType | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| eventTime | sztring | Az esemény létrehozásának időpontja a szolgáltató UTC-ideje alapján. |
| id | sztring | Az esemény egyedi azonosítója |
| data | objektum | Geofencing esemény adatok. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| metadataVersion | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| apiKategória | sztring | Az esemény API-kategóriája. |
| apiName (apiName) | sztring | Az esemény API-neve. |
| problémák | objektum | A feldolgozás során észlelt problémák listája. Ha bármilyen problémát ad vissza, akkor nem lesznek geometriák a válaszmal. |
| responseCode | szám | HTTP-válaszkód |
| Geometriák | objektum | Felsorolja azokat a kerítésgeometriákat, amelyek tartalmazzák a koordináta-pozíciót, vagy átfedik a keresőpuffert a pozíció körül. |

A hibaobjektum akkor jelenik meg, ha hiba történik a Térképek API-ban. A hibaobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| error | ErrorDetails |Ez az objektum akkor kerül visszaadásra, ha hiba történik a Térképek API-ban  |

Az ErrorDetails objektum akkor jelenik meg, ha hiba történik a Térképek API-ban. Az ErrorDetails vagy az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| code | sztring | A HTTP állapotkód. |
| message | sztring | Ha rendelkezésre áll, a hiba emberileg olvasható leírása. |
| belső hiba | Belső hiba | Ha rendelkezésre áll, egy objektum, amely szolgáltatásspecifikus információkat tartalmaz a hibáról. |

Az InnerError egy olyan objektum, amely szolgáltatásspecifikus információkat tartalmaz a hibáról. Az InnerError objektum a következő tulajdonságokkal rendelkezik: 

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| code | sztring | A hibaüzenet. |

A geometriaobjektum felsorolja a kérelemben szereplő felhasználói időhöz viszonyított lejárt geokerítések geometriaazonosítóit. A geometriaobjektum geometriaelemei nek a következő tulajdonságokkal rendelkező geometriaelemei vannak: 

| Tulajdonság | Típus | Leírás |
|:-------- |:---- |:----------- |
| Deviceid | sztring | Az eszköz azonosítója. |
| Távolság | sztring | <p>Távolság a koordinátától a geokerítés legközelebbi határához. A pozitív azt jelenti, hogy a koordináta a geokerítésen kívül van. Ha a koordináta kívül esik a geokerítésen, de több, mint a searchBuffer értéke a legközelebbi geokerítés határától, akkor az érték 999. A negatív azt jelenti, hogy a koordináta a geokerítésen belül van. Ha a koordináta a sokszögön belül van, de több, mint a searchBuffer értéke a legközelebbi geofencing határtól, akkor az érték -999. A 999-es érték azt jelenti, hogy nagy a bizalom, hogy a koordináta jóval a geokerítésen kívül van. A -999 érték azt jelenti, hogy nagy a megbízhatóság, hogy a koordináta jóval a geokerítésen belül van.<p> |
| geometriai azonosító |sztring | Az egyedi azonosító azonosítja a geokerítés geometriáját. |
| legközelebbi | szám | A geometria legközelebbi pontjának szélessége. |
| legközelebbi | szám | A geometria legközelebbi pontjának hosszúsága. |
| udId | sztring | A geokerítés feltöltésekor a felhasználó feltöltési szolgáltatásából visszaadott egyedi azonosító. Nem fog szerepelni a geofencing post API.Will not be included in geofencing post API. |

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| lejártGeofenceGeometryId | karakterlánc[] | A geokerítés geometriai azonosítójának listája, amely a kérelemben szereplő felhasználói időhöz viszonyítva lejárt. |
| Geometriák | geometriák[] |Felsorolja azokat a kerítésgeometriákat, amelyek tartalmazzák a koordináta-pozíciót, vagy átfedik a keresőpuffert a pozíció körül. |
| invalidPeriodGeofenceGeometryId | karakterlánc[]  | A geokerítés geometriaazonosítójának listája, amely a kérelemben szereplő felhasználói időhöz képest érvénytelen. |
| isEventPublished | logikai | Igaz, ha legalább egy eseményt közzé tesznek az Azure Maps esemény-előfizető, hamis, ha nincs esemény közzé téve az Azure Maps esemény-előfizető. |

## <a name="tutorials-and-how-tos"></a>Oktatóanyagok és útmutatók
|Cím  |Leírás  |
|---------|---------|
| [Reagálás az Azure Maps-eseményekre az Event Grid használatával](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Az Azure Maps és az Event Grid integrálásának áttekintése. |
| [Oktatóanyag: Geokerítés beállítása](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ez az oktatóanyag végigvezeti a geokerítés azure Maps használatával történő beállításának alaplépéseit. Az Azure Event Grid segítségével streamelheti a geokerítés eredményeit, és a geokerítés-eredmények alapján értesítést állíthat be. |

## <a name="next-steps"></a>További lépések

* Az Azure Event Grid bemutatása a [Mi az eseményrács?](overview.md)
* Az Azure Event Grid-előfizetés ek létrehozásáról az [Event Grid-előfizetésséma](subscription-creation-schema.md)című témakörben talál további információt.