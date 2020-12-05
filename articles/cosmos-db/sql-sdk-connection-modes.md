---
title: Azure Cosmos DB SQL SDK kapcsolódási módjai
description: Ismerje meg a Azure Cosmos DB SQL SDK-k által elérhető különböző csatlakozási módokat.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: maquaran
ms.custom: devx-track-dotnet, contperfq2
ms.openlocfilehash: 7901ad4f67f6f3243a2d3b8dc25733ac68b612db
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608367"
---
# <a name="azure-cosmos-db-sql-sdk-connectivity-modes"></a>Azure Cosmos DB SQL SDK kapcsolódási módjai
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Az ügyfél Azure Cosmos DBhoz való kapcsolódásának módja fontos teljesítménybeli következményekkel jár, különösen a megfigyelt ügyféloldali késés miatt. Azure Cosmos DB egy egyszerű, nyitott, REST-alapú programozási modellt kínál a HTTPS protokollal, az átjáró mód használatával. Emellett egy hatékony TCP protokollt is biztosít, amely a kommunikációs modellben is REST-alapú, és a TLS-t használja a kezdeti hitelesítéshez és a titkosított adatforgalomhoz, melyet közvetlen üzemmódnak nevezünk.

## <a name="available-connectivity-modes"></a>Elérhető csatlakozási módok

A két elérhető csatlakozási mód a következők:

  * Átjáró üzemmód
      
    Az átjáró üzemmód minden SDK platformon támogatott. Ha az alkalmazása szigorú tűzfal-korlátozásokkal rendelkező vállalati hálózaton belül fut, az átjáró mód a legjobb választás, mivel a szabványos HTTPS-portot és egyetlen DNS-végpontot használ. A teljesítmény-kompromisszum azonban az, hogy az átjáró üzemmód egy további hálózati ugrást is magában foglal minden alkalommal, amikor az adatok beolvasása vagy írása Azure Cosmos DB. Azt is javasoljuk, hogy az átjáró kapcsolati üzemmódja csak korlátozott számú szoftvercsatorna-kapcsolattal rendelkező környezetekben futtatott alkalmazásokat futtasson.

    Ha az SDK-t Azure Functionsban használja, különösen a használati [tervben](../azure-functions/functions-scale.md#consumption-plan), vegye figyelembe a [kapcsolatok jelenlegi korlátait](../azure-functions/manage-connections.md).

  * Közvetlen mód

    A közvetlen mód támogatja a TCP protokollon keresztüli csatlakozást, és jobb teljesítményt nyújt, mivel kevesebb hálózati ugrás van. Az alkalmazás közvetlenül kapcsolódik a háttérbeli replikához. A közvetlen mód jelenleg csak a .NET-és Java SDK-platformokon támogatott.
     
:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="A Azure Cosmos DB kapcsolódási módjai" border="false":::

Ezek a csatlakozási módok lényegében feltétele az adatsík által kért útvonalnak – a dokumentumok olvasását és írását – az ügyfélszámítógépről a Azure Cosmos DB háttérbe tartozó partíciók között. A közvetlen mód az előnyben részesített lehetőség a legjobb teljesítmény érdekében – lehetővé teszi az ügyfél számára a TCP-kapcsolatok közvetlen megnyitását a Azure Cosmos DB háttérbeli partíciók számára, és a *közvetlen* ly küldési kéréseket. Ezzel szemben az átjáró módban az ügyfél által kezdeményezett kérelmeket az előtér-Azure Cosmos DB egy úgynevezett "átjáró" kiszolgálóra irányítja, amely viszont a Azure Cosmos DB háttérbeli megfelelő partíció (k) re küldi a kéréseket.

## <a name="service-port-ranges"></a>Szolgáltatási portok tartományai

Ha közvetlen módot használ, az átjáró portjain kívül meg kell győződnie arról, hogy a 10000 és a 20000 közötti porttartomány meg van nyitva, mert Azure Cosmos DB dinamikus TCP-portokat használ. Ha közvetlen módot használ [privát végpontokon](./how-to-configure-private-endpoints.md), a TCP-portok teljes tartományát (0 és 65535 között) nyitva kell lennie. Ha ezek a portok nincsenek megnyitva, és megpróbálja használni a TCP protokollt, előfordulhat, hogy a 503-es szolgáltatás nem érhető el hibaüzenetet kap.

Az alábbi táblázat a különböző API-k és az egyes API-k által használt szolgáltatási portok összefoglalását mutatja be:

|Kapcsolat módja  |Támogatott protokoll  |Támogatott SDK-k  |API/szolgáltatás portja  |
|---------|---------|---------|---------|
|Átjáró  |   HTTPS    |  Minden SDK    |   SQL (443), MongoDB (10250, 10255, 10256), tábla (443), Cassandra (10350), Graph (443) <br> Az 10250-es port a MongoDB-példány alapértelmezett Azure Cosmos DB API-ját képezi le a Geo-replikáció nélkül. Míg a 10255-es és a 10256-es port a Geo-replikációval rendelkező példányhoz rendelhető hozzá.   |
|Direct    |     TCP    |  .NET SDK Java SDK    | Nyilvános/szolgáltatási végpontok használata esetén: a 10000-as port a 20000-tartományon keresztül<br>Privát végpontok használata esetén: a 0 és 65535 közötti portok |

## <a name="next-steps"></a>További lépések

Az SDK platform adott teljesítményének optimalizálásához:

* [.NET v2 SDK – teljesítménnyel kapcsolatos tippek](performance-tips.md)

* [.NET v3 SDK – teljesítménnyel kapcsolatos tippek](performance-tips-dotnet-sdk-v3-sql.md)
 
* [A Java v4 SDK teljesítményével kapcsolatos tippek](performance-tips-java-sdk-v4-sql.md)