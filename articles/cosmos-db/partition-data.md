---
title: Particionálás és horizontális skálázás Azure Cosmos DB
description: Ismerje meg, hogyan működik a particionálás a Azure Cosmos DBban, hogyan konfigurálhatja a particionálási és partíciós kulcsokat, és hogyan választhatja ki az alkalmazásának megfelelő partíciós kulcsot.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: cbd171e10cc1a8b27de98d9d4d779f345ac5a3ed
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78371601"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Particionálás és horizontális skálázás Azure Cosmos DB

Ez a cikk ismerteti a fizikai és logikai particionálást az Azure Cosmos DB-ben Emellett tárgyalja a skálázáshoz és particionáláshoz kapcsolódó ajánlott eljárásokat is 

## <a name="logical-partitions"></a>Logikai partíciók

A logikai partíciók olyan elemekből állnak, amelyek ugyanazzal a partíciós kulccsal rendelkeznek. Például egy olyan tárolóban, ahol minden elem `City` tulajdonságot tartalmaz, a tároló partíciós kulcsaként használhatja a `City`. A `City`, például `London`, `Paris`és `NYC`speciális értékekkel rendelkező elemek csoportjai eltérő logikai partíciókat alkotnak. Nem kell aggódnia, hogy a rendszer törli a partíciókat az alapul szolgáló adatokat.

Azure Cosmos DB a tároló a méretezhetőség alapvető egysége. A tárolóhoz hozzáadott adatok, valamint a tárolón kiépített átviteli sebesség automatikusan (vízszintesen) particionálva van a logikai partíciók készletében. Az adatok és az átviteli sebesség az Azure Cosmos-tárolóhoz megadott partíciós kulcs alapján van particionálva. További információ: [Azure Cosmos-tároló létrehozása](how-to-create-container.md).

A logikai partíció az adatbázis-tranzakciók hatókörét is meghatározza. A logikai partíción belüli elemeket egy [Pillanatkép-elkülönítéssel rendelkező tranzakció](database-transactions-optimistic-concurrency.md)használatával frissítheti. Amikor új elemeket adnak hozzá egy tárolóhoz, a rendszer transzparens módon létrehozza az új logikai partíciókat.

## <a name="physical-partitions"></a>Fizikai partíciók

Az Azure Cosmos-tárolók méretezése az adatok és az átviteli sebesség nagy számú logikai partíción keresztüli elosztásával történik. Belsőleg egy vagy több logikai partíció egy olyan fizikai partícióra van leképezve, amely replikák halmazát, más néven [*replikakészlet*](global-dist-under-the-hood.md)-készletet tartalmaz. Mindegyik replikakészlet az Azure Cosmos adatbázismotor egy példányát tárolja. A replikakészlet a fizikai partícióban tárolt, tartós, magasan elérhető és konzisztens módon tárolja az adattárolási készletet. A fizikai partíciók a maximális tárterületet és a kérelmeket (RUs) támogatják. A fizikai partíciót alkotó replikák öröklik a partíció tárolási kvótáját. A fizikai partíció összes replikája együttesen támogatja a fizikai partícióhoz lefoglalt átviteli sebességet. 

Az alábbi képen látható, hogyan vannak leképezve a logikai partíciók a globálisan elosztott fizikai partíciókhoz:

![Azure Cosmos DB particionálást bemutató rendszerkép](./media/partition-data/logical-partitions.png)

A tárolók számára kiépített átviteli sebesség egyenletesen oszlik el a fizikai partíciók között. A partíciós kulcs olyan kialakítása, amely nem osztja el az átviteli kérelmeket, akár "forró" partíciót is létrehozhat. A gyakori partíciók mértékének korlátozását és a kiosztott átviteli sebesség nem hatékony használatát, valamint a magasabb költségeket eredményezik.

A logikai partíciókkal ellentétben a fizikai partíciók a rendszer belső megvalósítása. Nem szabályozható a fizikai partíciók mérete, elhelyezése vagy száma, és nem szabályozható a logikai és a fizikai partíciók közötti leképezés. A logikai partíciók számát és az adatok, a munkaterhelés és az átviteli sebesség elosztását azonban [a megfelelő logikai partíciós kulcs kiválasztásával](partitioning-overview.md#choose-partitionkey)szabályozhatja.

## <a name="next-steps"></a>Következő lépések

* Tudnivalók [a partíciós kulcs kiválasztásáról](partitioning-overview.md#choose-partitionkey).
* További információ [a kiépített átviteli sebességről Azure Cosmos db](request-units.md).
* Ismerkedjen meg a [Azure Cosmos db globális eloszlásával](distribute-data-globally.md).
* Útmutató az [átviteli sebesség Azure Cosmos-tárolón](how-to-provision-container-throughput.md)való kiépítéséhez.
* Útmutató az [átviteli sebesség Azure Cosmos-adatbázison](how-to-provision-database-throughput.md)való kiépítéséhez.
