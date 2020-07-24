---
title: Regionális jelenlét Azure Cosmos DB
description: Ez a cikk a Azure Cosmos DB és a különböző felhőalapú környezetek regionális jelenlétét ismerteti.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.custom: seodec18
ms.openlocfilehash: e2242e8febad5d55813721266f1286250af47111
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082885"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Regionális jelenlét Azure Cosmos DB

Azure Cosmos DB egy alapszintű szolgáltatás az Azure-ban, és alapértelmezés szerint mindig minden olyan régióban elérhető, [ahol az Azure elérhető](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all).

:::image type="content" source="./media/regional-presence/regional-presence.png" alt-text="Régiók, ahol elérhető Azure Cosmos DB" lightbox="./media/regional-presence/regional-presence.png" border="false":::

A Cosmos DB az ügyfelek számára elérhető öt különböző Azure Cloud-környezetben elérhető:

* **Azure nyilvános** felhő, amely globálisan elérhető.

* Az **Azure China 21Vianet** a Microsoft és a 21Vianet közötti egyedi partneri kapcsolaton keresztül érhető el, amely az ország egyik legnagyobb internetszolgáltatója Kínában.

* Az **Azure Germany** egy adatkezelői modell keretében nyújt szolgáltatásokat, amely biztosítja, hogy az ügyféladatok Németországban maradnak a T-Systems International GmbH, a Deutsche Telekom leányvállalata, a német adatkezelő nevében.

* **Azure Government** a Egyesült Államok négy régiójában érhető el az USA kormányzati szervei és azok partnerei számára. 

* A **védelmi minisztérium (DoD) Azure Government** a Egyesült Államok két régiójában érhető el az USA védelmi Minisztériuma számára.

## <a name="regional-presence-with-global-distribution"></a>Regionális jelenlét globális terjesztéssel

Alapértelmezés szerint minden Azure-régióban elérhetők a Azure Cosmos DB (beleértve az SQL, a MongoDB, a Cassandra, a Gremlin és a Table) által közzétett API-k. Előfordulhat például, hogy a MongoDB és a Cassandra API-kat a Azure Cosmos DB nem csak az összes globális Azure-régióban, hanem olyan szuverén felhőkben is elérhetővé teszi, mint például Kína, Németország, kormányzat és védelmi minisztérium (DoD) régiói.

A Azure Cosmos DB egy [globálisan elosztott](distribute-data-globally.md) adatbázis-szolgáltatás. Tetszőleges számú Azure-régiót társíthat az Azure Cosmos-fiókjához, és az adatai automatikusan és transzparens módon replikálódnak. Bármikor hozzáadhat vagy eltávolíthat egy régiót az Azure Cosmos-fiókjához. A kulcsrakész globális elosztási képességgel és a többplatformos replikálási protokollal Azure Cosmos DB kevesebb mint 10 MS olvasási és írási késéssel rendelkezik a esetek 99% percentilis esetében, 99,999 olvasási és írási rendelkezésre állást biztosít, valamint lehetővé teszi a kiépített átviteli sebesség rugalmas méretezését az olvasás és írás során az Azure Cosmos-fiókhoz társított összes régióban. A Azure Cosmos DB öt jól meghatározott konzisztencia-modellt is kínál, és dönthet úgy is, hogy egy adott konzisztencia-modellt alkalmaz az adataira. Végezetül Azure Cosmos DB az iparág egyetlen adatbázis-szolgáltatása, amely átfogó [szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) biztosít a kiépített átviteli sebesség, a késés a esetek 99% percentilis, a magas rendelkezésre állás és a konzisztencia terén. A fenti képességek az összes Azure-felhőben elérhetők.

A globális replikációs támogatás hatóköre egy Azure-beli felhőalapú környezeten belül van. Az *Azure Public* -ban található Azure Cosmos db-fiókok például a *nyilvános* Azure-régióba replikálhatók bármely Azure-régióban, de nem replikálhatók az Azure *China 21Vianet*Azure-régióiba.

## <a name="next-steps"></a>További lépések

Most megismerheti a Azure Cosmos DB alapvető fogalmait a következő cikkekkel:

* [Globális adateloszlás](distribute-data-globally.md)
* [Azure Cosmos DB-fiók kezelése](manage-account.md)
* [Adatforgalom kiépítése az Azure Cosmos-tárolók és-adatbázisok számára](set-throughput.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
