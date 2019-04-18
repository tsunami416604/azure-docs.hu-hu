---
title: Az Azure Cosmos DB regionális jelenlét
description: Ez a cikk ismerteti az Azure Cosmos DB és a különböző környezetekben regionális meglétét.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: 851da72a308b164b9a01b7ccfa3d541fc06810ba
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58762191"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Az Azure Cosmos DB regionális jelenlét

Az Azure Cosmos DB eligazodást szolgáltatás az Azure-ban, és, alapértelmezés szerint mindig elérhető minden régióban, ahol az Azure érhető el. Az Azure jelenleg elérhető [54 régiók](https://azure.microsoft.com/global-infrastructure/regions/) világszerte. 

[![Hol érhető el az Azure Cosmos DB régiók](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

A cosmos DB összes öt különböző Azure-felhőbeli környezetek érhetők el ügyfeleink számára érhető el:

* **Azure nyilvános** felhő, amely globálisan elérhető.

* **Az Azure China 21Vianet** a Microsoft és az egyik a legnagyobb internetszolgáltatója Kínában a 21Vianet közötti partneri együttműködésnek köszönhetően érhető el.

* **Az Azure Germany** alatt egy megbízott adatkezelői modell, amely biztosítja, hogy a vásárlói adatok marad Németországban a T-Systems International GmbH, a Deutsche távközlési, leányvállalata vezérlése alatt a német megbízott adatkezelő átjáróként szolgáltatásokat biztosít.

* **Az Azure Government** négy régióban az Egyesült Államokban, az USA kormányügynökségei és partnereik számára érhető el. 

* **Az Azure Government a védelmi Minisztérium (DoD)** két régióban, az USA védelmi Minisztériumának az Egyesült Államokban érhető el.

## <a name="regional-presence-with-global-distribution"></a>A globális terjesztés regionális jelenlét

Azure Cosmos DB (beleértve az SQL, a MongoDB, a Cassandra, a Gremlin és a tábla) által elérhetővé tett összes API-k alapértelmezés szerint az összes Azure-régiókban érhetők el. Például használhat a mongodb-hez, és a Cassandra API-k az Azure Cosmos DB nem csak a globális Azure-régiók mindegyikében, hanem a független felhőkben például Kína, Németország, kormányzati és védelmi Minisztérium által elérhetővé tett (DoD) régióban.

Azure Cosmos DB egy [globálisan elosztott](distribute-data-globally.md) adatbázis-szolgáltatásban. Az Azure Cosmos-fiók is társíthat tetszőleges számú Azure-régiók és a rendszer automatikusan és transzparens módon replikálja az adatokat. Adja hozzá, vagy egy régióban, az Azure Cosmos-fiók bármikor eltávolíthatja. A kulcsrakész globális terjesztés funkciót és a többszörös egyedalakzatokkal replikációs protokoll, az Azure Cosmos DB kínálja, kevesebb mint 10 ms és olvasása és írása az késleltetések 99 százalékon 99,999 írási és olvasási, rugalmasan méretezhető kiépítve az átviteli sebesség beolvassa és az Azure Cosmos-fiókhoz társított összes régióban. Az Azure Cosmos DB is öt jól definiált konzisztenciamodelleket kínál, és dönthet úgy, hogy egy meghatározott konzisztenciamodellt alkalmazni az adatokon. Végül az Azure Cosmos DB az az egyetlen adatbázis-szolgáltatás, amely átfogó biztosít iparág [szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) kitérve kiosztott átviteli sebesség, a késés 99 százalékon, magas rendelkezésre állást, és konzisztencia. Az összes Azure-felhők a fenti funkciók érhetők el.

## <a name="next-steps"></a>További lépések

Most alapfogalmakat az Azure Cosmos DB az alábbi cikkekben olvashat:

* [Globális terjesztés](distribute-data-globally.md)
* [Az Azure Cosmos DB-fiók kezelése](manage-account.md)
* [Az Azure Cosmos-tárolók és adatbázisok kiépítése átviteli](set-throughput.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
