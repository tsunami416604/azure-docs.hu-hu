---
title: Particionálási és horizontális skálázás az Azure Cosmos DB-ben
description: Ismerje meg az Azure Cosmos DB, hogyan konfigurálja a particionálási és particionálhatja a kulcsok és hogy miként választható ki a megfelelő partíciókulcs az alkalmazás hogyan particionálási működését.
ms.author: mjbrown
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 667dbab6613b83d0e72762b8bbad738d0d7697d8
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034437"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Particionálási és horizontális skálázás az Azure Cosmos DB-ben

Ez a cikk ismerteti az Azure Cosmos DB és a méretezést, és a particionálás vonatkozó ajánlott eljárások a fizikai és logikai partíció. 

## <a name="logical-partitions"></a>Logikai partíciók

A logikai partíció ugyanazzal a partíciókulccsal rendelkező egy tételcsoport áll. Vegyük példaként egy tárolót, ha az összes elemet tartalmaznak egy `City` tulajdonság, majd használhatja `City` tároló partíciókulcsként. Csoportok adott értékek az elemek a `City` például a "London", "Párizs", "NYC" stb. egy külön logikai partícióba képezik.

Az Azure Cosmos DB a tároló lehet méretezhetőség alapvető egysége. A tároló és az átviteli sebességet, a tárolóban kiosztott elemhez adott adatokat a program automatikusan (vízszintesen) particionált logikai partíciók egy halmazában között. Ezek vannak particionálva, a partíciókulcs adja meg, hogy az a Cosmos-tárolók alapján. További tudnivalókért lásd: [megadása a partíciókulcs az Cosmos-tárolóhoz](how-to-create-container.md) cikk.

A logikai partíció adatbázis-tranzakciók hatóköre határozza meg. A logikai partíció található elem egy tranzakciót a pillanatkép-elkülönítés használatával frissítheti.

A tárolóba új elemek hozzáadásakor, vagy ha a tárolóban kiosztott átviteli emelkedett, új logikai partíciók átlátható módon hozza létre a rendszer.

## <a name="physical-partitions"></a>Fizikai partíciók

Az adatok és az átviteli sebességet a nagy számú logikai partíciók közötti elosztásával méretezett egy Cosmos-tárolót. Belső használatra vannak rendelve egy vagy több logikai partíciót egy **fizikai partíciók** , más néven a replikakészlethez replikák készlete áll. Minden egyes replikakészlethez üzemelteti a Cosmos-adatbázismotor egy példányához. A replikakészlet lehetővé teszi a fizikai partíciók tartós, magas rendelkezésre állású és egységes tárolja az adatokat. Egy fizikai partíciónak támogatja egy rögzített, maximális összege a tárolás és a fenntartott egységek. Minden egyes replikának, amely magában foglalja a fizikai partíciók örökli a tárhelykvótát. És a egy fizikai partíciónak az összes replika együttesen támogatja a fizikai partíciónként kiosztott átviteli. Az alábbi képen látható, hogyan logikai partíciók globálisan elosztott fizikai partíciókra van leképezve:

![Az Azure Cosmos DB particionálási](./media/partition-data/logical-partitions.png)

Egy tároló kiosztott átviteli sebesség egyenlően vannak elosztva a fizikai partíciók között. Az átviteli sebesség kérelmek egyenletes elosztása nem partíciós kulcs kialakítás ezért "Forró" partíció hozható létre. Forró partíciók eredményezhet a kiosztott átviteli sebességének korlátozása és a nem elég hatékony használatát.

Logikai partíció ellentétben a fizikai partíciók a rendszer belső megvalósítását. A méretük, elhelyezés, a count vagy logikai partíciót és a fizikai partíciók közötti leképezést nem szabályozhatja. Logikai partíciók száma és a terjesztési, adatok és az átviteli sebesség azonban szabályozhatja a megfelelő partíciókulcs kiválasztása.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megadott méretezhetők, és az Azure Cosmos DB particionálási adatparticionálás és ajánlott eljárások áttekintését. 

* Ismerje meg [kiosztott átviteli sebesség az Azure Cosmos DB-ben](request-units.md)
* Ismerje meg [az Azure Cosmos DB globális terjesztését](distribute-data-globally.md)
* Ismerje meg [olyan partíciókulcsot](partitioning-overview.md#choose-partitionkey)
* Ismerje meg, [átviteli sebességet egy Cosmos-tárolón kiépítése](how-to-provision-container-throughput.md)
* Ismerje meg, [átviteli sebességet egy Cosmos-adatbázis kiépítése](how-to-provision-database-throughput.md)
