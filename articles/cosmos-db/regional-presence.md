---
title: Az Azure Cosmos DB regionális jelenlét
description: Ez a cikk ismerteti az Azure Cosmos DB és a különböző környezetekben regionális meglétét.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: 775ca8c57a9a444df2f7d0a4df4224b7eba9d677
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384039"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Az Azure Cosmos DB regionális jelenlét

Azure Cosmos DB az Azure alapszintű szolgáltatása, és alapértelmezés szerint minden régióban elérhető, ahol az Azure elérhető. Az Azure jelenleg elérhető [54 régiók](https://azure.microsoft.com/global-infrastructure/regions/) világszerte. 

[![Régiók, ahol elérhető Azure Cosmos DB](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

A cosmos DB összes öt különböző Azure-felhőbeli környezetek érhetők el ügyfeleink számára érhető el:

* **Azure nyilvános** felhő, amely globálisan elérhető.

* Az **Azure China 21Vianet** a Microsoft és a 21Vianet közötti egyedi partneri kapcsolaton keresztül érhető el, amely az ország egyik legnagyobb internetszolgáltatója Kínában.

* **Az Azure Germany** alatt egy megbízott adatkezelői modell, amely biztosítja, hogy a vásárlói adatok marad Németországban a T-Systems International GmbH, a Deutsche távközlési, leányvállalata vezérlése alatt a német megbízott adatkezelő átjáróként szolgáltatásokat biztosít.

* **Az Azure Government** négy régióban az Egyesült Államokban, az USA kormányügynökségei és partnereik számára érhető el. 

* A **védelmi minisztérium (DoD) Azure Government** a Egyesült Államok két régiójában érhető el az USA védelmi Minisztériuma számára.

## <a name="regional-presence-with-global-distribution"></a>A globális terjesztés regionális jelenlét

Alapértelmezés szerint minden Azure-régióban elérhetők a Azure Cosmos DB (beleértve az SQL, a MongoDB, a Cassandra, a Gremlin és a Table) által közzétett API-k. Előfordulhat például, hogy a MongoDB és a Cassandra API-kat a Azure Cosmos DB nem csak az összes globális Azure-régióban, hanem olyan szuverén felhőkben is elérhetővé teszi, mint például Kína, Németország, kormányzat és védelmi minisztérium (DoD) régiói.

A Azure Cosmos DB egy [globálisan elosztott](distribute-data-globally.md) adatbázis-szolgáltatás. Az Azure Cosmos-fiók is társíthat tetszőleges számú Azure-régiók és a rendszer automatikusan és transzparens módon replikálja az adatokat. Adja hozzá, vagy egy régióban, az Azure Cosmos-fiók bármikor eltávolíthatja. A kulcsrakész globális terjesztés funkciót és a többszörös egyedalakzatokkal replikációs protokoll, az Azure Cosmos DB kínálja, kevesebb mint 10 ms és olvasása és írása az késleltetések 99 százalékon 99,999 írási és olvasási, rugalmasan méretezhető kiépítve az átviteli sebesség beolvassa és az Azure Cosmos-fiókhoz társított összes régióban. Az Azure Cosmos DB is öt jól definiált konzisztenciamodelleket kínál, és dönthet úgy, hogy egy meghatározott konzisztenciamodellt alkalmazni az adatokon. Végezetül Azure Cosmos DB az iparág egyetlen adatbázis-szolgáltatása, amely átfogó [szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) biztosít a kiépített átviteli sebesség, a késés a esetek 99% percentilis, a magas rendelkezésre állás és a konzisztencia terén. A fenti képességek az összes Azure-felhőben elérhetők.

## <a name="next-steps"></a>További lépések

Most megismerheti a Azure Cosmos DB alapvető fogalmait a következő cikkekkel:

* [Globális terjesztés](distribute-data-globally.md)
* [Az Azure Cosmos DB-fiók kezelése](manage-account.md)
* [Az Azure Cosmos-tárolók és adatbázisok kiépítése átviteli](set-throughput.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
