---
title: Az Azure Cosmos DB regionális jelenlét
description: Ez a cikk ismerteti az Azure Cosmos DB és a különböző környezetekben regionális meglétét.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: 2062862670f5b373d3153c473e45455e10d5e996
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548680"
---
# <a name="regional-presence-of-azure-cosmos-db"></a>Az Azure Cosmos DB regionális jelenlét

Az Azure jelenleg elérhető [54 régiók](https://azure.microsoft.com/global-infrastructure/regions/) világszerte. Az Azure Cosmos DB egy alapvető szolgáltatás az Azure-ban, és minden régióban, ahol az Azure már érhető el.

[![Hol érhető el az Azure Cosmos DB régiók](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

A cosmos DB összes öt különböző Azure-felhőbeli környezetek érhetők el ügyfeleink számára érhető el:

* **Azure nyilvános** felhő, amely globálisan elérhető.

* **Az Azure China** a Microsoft és az egyik a legnagyobb internetszolgáltatója, a 21Vianet közötti partneri együttműködésnek köszönhetően érhető el.

* **Az Azure Germany** alatt egy megbízott adatkezelői modell, amely biztosítja, hogy a vásárlói adatok marad Németországban a T-Systems International GmbH, a Deutsche távközlési, leányvállalata vezérlése alatt a német megbízott adatkezelő átjáróként szolgáltatásokat biztosít.

* **Az Azure Government** négy régióban az Egyesült Államokban, az USA kormányügynökségei és partnereik számára érhető el. 

* **Az Azure Government a védelmi Minisztérium** két régióban, az USA védelmi Minisztériumának az Egyesült Államokban érhető el.

## <a name="regional-presence-with-global-distribution"></a>A globális terjesztés regionális jelenlét

Azure Cosmos DB (beleértve az SQL, a MongoDB, a Cassandra, a Gremlin és az Azure Table storage) által elérhetővé tett más API-k minden Azure-régióban érhető el. Például használhat a mongodb-hez, és a Cassandra API-k az Azure Cosmos DB nem csak a globális Azure-régiók mindegyikében, hanem Azure-régióban szuverén például Kína, Németország, kormányzati és védelmi Minisztérium által elérhetővé tett (DoD) régióban.

Azure Cosmos DB egy [globálisan elosztott](distribute-data-globally.md) adatbázis. Az Azure Cosmos-fiók is társíthat tetszőleges számú Azure-régiók és a rendszer automatikusan és transzparens módon replikálja az adatokat. Adja hozzá, vagy egy régióban, az Azure Cosmos-fiók bármikor eltávolíthatja. A kulcsrakész globális terjesztés funkciót és a többszörös egyedalakzatokkal replikációs protokoll, az Azure Cosmos DB kínálja, kevesebb mint 10 ms és olvasása és írása az késleltetések 99 százalékon 99,999 írási és olvasási, rugalmasan méretezhető kiépítve az átviteli sebesség beolvassa és az Azure Cosmos-fiókhoz társított összes régióban. Az Azure Cosmos DB is öt jól definiált konzisztenciamodelleket kínál, és dönthet úgy, hogy egy meghatározott konzisztenciamodellt alkalmazni az adatokon. Végül az Azure Cosmos DB az egyetlen adatbázis-szolgáltatás, amely egy átfogó szolgáltatói szerződést (SLA) kitérve kiosztott átviteli sebesség, a 99. percentilis, magas rendelkezésre állás és konzisztencia késéssel biztosít iparág.

## <a name="next-steps"></a>További lépések

Most már talál további információt a következő cikkek az Azure Cosmos DB különböző fogalmak:

* [Globális terjesztés](distribute-data-globally.md)
* [Az Azure Cosmos DB-fiók kezelése](manage-account.md)
* [Az Azure Cosmos-tárolók és adatbázisok kiépítése átviteli](set-throughput.md)
* [Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
