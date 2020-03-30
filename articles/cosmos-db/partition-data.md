---
title: Particionálás és horizontális skálázás az Azure Cosmos DB-ben
description: Megtudhatja, hogyan működik a particionálás az Azure Cosmos DB-ben, hogyan konfigurálhatja a particionálási és partíciókulcsokat, és hogyan választhatja ki a megfelelő partíciókulcsot az alkalmazáshoz.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: cbd171e10cc1a8b27de98d9d4d779f345ac5a3ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246616"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Particionálás és horizontális skálázás az Azure Cosmos DB-ben

Ez a cikk ismerteti a fizikai és logikai particionálást az Azure Cosmos DB-ben Emellett tárgyalja a skálázáshoz és particionáláshoz kapcsolódó ajánlott eljárásokat is 

## <a name="logical-partitions"></a>Logikai partíciók

A logikai partíció olyan elemekből áll, amelyek azonos partíciókulccsal rendelkeznek. Például egy tárolóban, ahol `City` az összes elem `City` tartalmaz egy tulajdonságot, használhatja a tároló partíciókulcsaként. A speciális értékekkel `City`rendelkező elemcsoportok, például `London`a , `Paris`és `NYC`a különböző logikai partíciókat alkotnak. Nem kell aggódnia a partíció törlése miatt, amikor az alapul szolgáló adatok törlődnek.

Az Azure Cosmos DB-ben a tároló a méretezhetőség alapvető egysége. A tárolóhoz hozzáadott adatok és a tárolón üzembe helyezett átviteli érték automatikusan (vízszintesen) lesz elosztva logikai partíciók között. Az adatok és az átviteli nyomás az Azure Cosmos-tárolóhoz megadott partíciókulcs alapján van particionálva. További információ: [Hozzon létre egy Azure Cosmos-tárolót.](how-to-create-container.md)

A logikai partíció határozza meg az adatbázis-tranzakciók hatókörét is. A logikai partíción belüli elemeket [pillanatkép-elkülönítéssel rendelkező tranzakció](database-transactions-optimistic-concurrency.md)használatával frissítheti. Amikor új elemeket ad hozzá egy tárolóhoz, a rendszer transzparens módon hoz létre új logikai partíciókat.

## <a name="physical-partitions"></a>Fizikai partíciók

Az Azure Cosmos-tároló méretezése az adatok és az átviteli nagyszámú logikai partíciók közötti elosztásával. Belsőleg egy vagy több logikai partíció egy olyan fizikai partícióra van leképezve, amely replikák készletéből , más néven [*kópiakészletből*](global-dist-under-the-hood.md)áll. Minden replikakészlet az Azure Cosmos adatbázis-motor egy példányát üzemelteti. A replikakészlet a fizikai partíción belül tárolt adatokat tartóssá, magas rendelkezésre állásúvá és konzisztenssé teszi. A fizikai partíció támogatja a maximális mennyiségű tárolási és kérelem egységek (RT). Minden olyan replika, amely a fizikai partíciót alkotja, örökli a partíció tárolási kvótáját. A fizikai partíció összes replikája együttesen támogatja a fizikai partícióhoz lefoglalt átviteli. 

Az alábbi képen látható, hogyan vannak leképezve a logikai partíciók globálisan elosztott fizikai partíciókra:

![Az Azure Cosmos DB particionálását szemléltető lemezkép](./media/partition-data/logical-partitions.png)

A tárolóhoz kiosztott átviteli teljesítmény egyenletesen oszlik meg a fizikai partíciók között. A partíciókulcs-kialakítás, amely nem osztja el egyenletesen az átviteli kérelmeket, "forró" partíciókat hozhat létre. A gyorselérésű partíciók sebességkorlátozást és a kiosztott átviteli sebesség nem hatékony kihasználását eredményezhetik, és magasabb költségeket eredményezhetnek.

A logikai partíciókkal ellentétben a fizikai partíciók a rendszer belső implementációi. A fizikai partíciók mérete, elhelyezése vagy száma nem szabályozható, és a logikai partíciók és a fizikai partíciók közötti leképezés nem szabályozható. A logikai partíciók számát, valamint az adatok, a munkaterhelés és az átviteli terhelés elosztását azonban [a megfelelő logikai partíciókulcs kiválasztásával](partitioning-overview.md#choose-partitionkey)szabályozhatja.

## <a name="next-steps"></a>További lépések

* További információ a [partíciókulcs kiválasztásáról.](partitioning-overview.md#choose-partitionkey)
* Ismerje meg [az Azure Cosmos DB kiépített átviteli.](request-units.md)
* Ismerje meg [az Azure Cosmos DB globális disztribúcióját.](distribute-data-globally.md)
* Ismerje meg, hogyan [építheti ki az átviteli fazonát egy Azure Cosmos-tárolóban.](how-to-provision-container-throughput.md)
* Ismerje meg, hogyan [építheti ki az átviteli mertét egy Azure Cosmos-adatbázisban.](how-to-provision-database-throughput.md)
