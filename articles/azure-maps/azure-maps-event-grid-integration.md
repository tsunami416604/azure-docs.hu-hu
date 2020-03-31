---
title: Reagálás a térképeseményekre az Event Grid használatával | Microsoft Azure Maps
description: Ebben a cikkben megtudhatja, hogyan reagálhat a Microsoft Azure Maps-eseményekre az Event Grid használatával.
author: philmea
ms.author: philmea
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9c9483af191e5439af0c0b5e433187d6475c178c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335721"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reagálás az Azure Maps-eseményekre az Event Grid használatával 

Az Azure Maps integrálható az Azure Event Griddel, így a felhasználók eseményértesítéseket küldhetnek más szolgáltatásoknak, és elindíthatják az alsóbb rétegbeli folyamatokat. Ez a cikk célja, hogy segítsen beállítani az üzleti alkalmazások at az Azure Maps-események meghallgatására. Ez lehetővé teszi a felhasználók számára, hogy megbízható, skálázható és biztonságos módon reagáljanak a kritikus eseményekre. Például a felhasználók létrehozhatnak egy alkalmazást egy adatbázis frissítéséhez, létrehozhatnak egy jegyet, és e-mailértesítést adhatnak meg minden alkalommal, amikor egy eszköz belép egy geokerítésbe.

Az Azure Event Grid egy teljes körűen felügyelt esemény-útválasztási szolgáltatás, amely egy közzétételi-előfizetési modellt használ. Az Event Grid beépített támogatást nyújt az Azure-szolgáltatásokhoz, például az [Azure Functionshez](https://docs.microsoft.com/azure/azure-functions/functions-overview) és az [Azure Logic Apps ekhez.](https://docs.microsoft.com/azure/azure-functions/functions-overview) Eseményriasztásokat biztosíthat a nem Azure-szolgáltatások webhookok használatával. Az Event Grid által támogatott eseménykezelők teljes listáját az [Azure Event Grid bemutatkozása](https://docs.microsoft.com/azure/event-grid/overview)című témakörben található.


![Az Azure Event Grid funkcionális modellje](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Az Azure Maps eseménytípusai

Az eseményrács [esemény-előfizetéseket](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) használ az eseményüzenetek előfizetőkhöz való irányításához. Az Azure Maps-fiók a következő eseménytípusokat bocsátja ki: 

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Amikor a kapott koordináták egy adott geokerítésen kívülről a megadott geokerítésen belülre |
| Microsoft.Maps.GeofenceKilépett | Ha a kapott koordináták egy adott geokerítésen belülről külső |
| Microsoft.Maps.GeofenceEredmény | Minden alkalommal, amikor egy geofencing lekérdezés eredményt ad vissza, függetlenül az állapottól |

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

## <a name="tips-for-consuming-events"></a>Tippek az események használatához

Az Azure Maps geokerítés-eseményeit kezelő alkalmazásoknak néhány ajánlott gyakorlatot kell követniük:

* Konfiguráljon több előfizetést az események ugyanazon eseménykezelőhöz való továbbításához. Fontos, hogy ne feltételezze, hogy az események egy adott forrásból származnak. Mindig ellenőrizze az üzenet témakörét, és győződjön meg arról, hogy az üzenet a várt forrásból érkezett.
* A `X-Correlation-id` válaszfejléc mezője segítségével megtudhatja, hogy az objektumokkal kapcsolatos információk naprakészek-e. Az üzenetek nem sorrendben vagy késleltetés után érkezhetnek.
* Ha a Geofence API-ban get vagy post a kérelmet hív meg `EnterAndExit`a módparaméter beállítása , majd egy Enter vagy Exit esemény jön létre a geokerítés minden olyan geometriájához, amelynek állapota megváltozott az előző Geofence API-híváshoz képest.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni arról, hogyan használhatja a geofencing-t az építési műveletek ellenőrzésére, olvassa el az:

> [!div class="nextstepaction"] 
> [Geokerítés beállítása az Azure Maps használatával](tutorial-geofence.md)
