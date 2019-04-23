---
title: Particionálási és horizontális skálázás az Azure Cosmos DB-ben
description: Ismerje meg, hogyan particionálási működését az Azure Cosmos DB, hogyan konfigurálja a particionálási és particionálhatja a kulcsok és az alkalmazás a megfelelő partíciókulcs kiválasztása.
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.openlocfilehash: cf454d6504f0433d7ac7ca883982ae317b14f814
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797823"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Particionálási és horizontális skálázás az Azure Cosmos DB-ben

Ez a cikk ismerteti az Azure Cosmos DB fizikai és logikai partíció. Gyakorlati tanácsok a méretezés és a particionálás emellett ismerteti. 

## <a name="logical-partitions"></a>Logikai partíciók

A logikai partíció ugyanazzal a partíciókulccsal rendelkező elemek egy készlete áll. Például az egy tárolót, ha az összes elemet tartalmaz egy `City` tulajdonság, használhat `City` tároló partíciókulcsként. Elemek, amelyekhez az adott értékek csoportjait `City`, mint például `London`, `Paris`, és `NYC`, külön logikai partíciót alkotnak. Nem kell aggódnia az egy partíció törlése, ha az alapul szolgáló adatokat törölnek.

Az Azure Cosmos DB a tároló lehet méretezhetőség alapvető egysége. A tároló és az átviteli sebességet, a tárolóban kiosztott hozzáadott adatokat a program automatikusan (vízszintesen) particionált logikai partíciók egy halmazában között. Adatok és az átviteli sebesség a vannak particionálva, a partíciókulcsot, adja meg az Azure Cosmos-tárolók alapján. További információkért lásd: [hozzon létre egy Azure Cosmos-tárolóhoz](how-to-create-container.md).

Egy logikai partíciót is adatbázis-tranzakciók hatóköre határozza meg. A logikai partíció található elem használatával frissítheti a [tranzakciót a pillanatkép-elkülönítés](database-transactions-optimistic-concurrency.md). Új elemek hozzáadásakor egy tárolóba, amikor új logikai partíciók átlátható módon hozza létre a rendszer.

## <a name="physical-partitions"></a>Fizikai partíciók

Adatok és az átviteli sebesség a nagy számú logikai partíciók közötti elosztásával egy Azure Cosmos-tárolóhoz van méretezve. Belső használatra van leképezve egy vagy több logikai partíciót egy fizikai partíciónak, amely replikákat, más néven készlete áll egy [ *replikakészlethez*](global-dist-under-the-hood.md). Minden egyes replikának egy példányát az Azure Cosmos DB-adatbázismotor beállítása a gazdagépek. A replikakészlet lehetővé teszi a fizikai partíciók tartós, magas rendelkezésre állású és egységes tárolja az adatokat. Egy fizikai partíciónak támogatja a storage- és kérésegységek (RU-k) maximális számát. Minden egyes replikának, hogy a fizikai partíciók örökli a partíció tárolási kvótát. Az összes fizikai partíciók replikáit együttesen támogatja az átviteli sebességet, amely hozzá van rendelve a fizikai partíciók. 

Az alábbi képen látható, hogyan logikai partíciók globálisan elosztott fizikai partíciókra van leképezve:

![Azure Cosmos DB particionálási bemutató kép](./media/partition-data/logical-partitions.png)

Egy tároló kiosztott átviteli sebesség egyenletesen oszlik fizikai partíciók között. Az átviteli sebesség kérelmek egyenletes elosztása nem partíciós kulcs kialakítás előfordulhat, hogy "Forró" partíciót alakíthatnak ki. Forró partíciók okozhat a sebességhatárolt és a nem elég hatékony használata a kiosztott átviteli sebesség és a magasabb költségeket.

Logikai partíció ellentétben a fizikai partíciók a rendszer belső megvalósítását. A méret, elhelyezést vagy fizikai partíciók száma nem szabályozhatja, és logikai partíció és fizikai partíciók közötti nem szabályozhatja. Azonban szabályozhatja, hány logikai partíciót és adatok, a számítási feladatok és az átviteli sebessége a terjesztési [kiválasztása a megfelelő logikai partíciókulcs](partitioning-overview.md#choose-partitionkey).

## <a name="next-steps"></a>További lépések

* Ismerje meg [olyan partíciókulcsot](partitioning-overview.md#choose-partitionkey).
* Ismerje meg [kiosztott átviteli sebesség az Azure Cosmos DB](request-units.md).
* Ismerje meg [az Azure Cosmos DB globális terjesztésének](distribute-data-globally.md).
* Ismerje meg, hogyan [üzembe helyezése egy Azure Cosmos-tároló átviteli sebességet](how-to-provision-container-throughput.md).
* Ismerje meg, hogyan [üzembe helyezése egy Azure Cosmos database átviteli sebességet](how-to-provision-database-throughput.md).
