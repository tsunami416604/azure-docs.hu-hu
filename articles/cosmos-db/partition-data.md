---
title: Particionálás és horizontális skálázás Azure Cosmos DB
description: Ismerje meg, hogyan működik a particionálás a Azure Cosmos DBban, hogyan konfigurálhatja a particionálási és partíciós kulcsokat, és hogyan választhatja ki az alkalmazásának megfelelő partíciós kulcsot.
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 1b624270fe22004a6ae64affe87b2fc22a2e9a66
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467686"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Particionálás és horizontális skálázás Azure Cosmos DB

Ez a cikk a Azure Cosmos DB fizikai és logikai partícióit ismerteti. Emellett a méretezésre és particionálásra vonatkozó ajánlott eljárásokat is tárgyalja. 

## <a name="logical-partitions"></a>Logikai partíciók

A logikai partíciók olyan elemekből állnak, amelyek ugyanazzal a partíciós kulccsal rendelkeznek. Például egy olyan tárolóban, ahol minden elem tartalmaz egy `City` tulajdonságot, a tároló `City` partíciós kulcsaként használható. Olyan elemek csoportjai `City`, amelyek meghatározott értékekkel rendelkeznek, `London`például `Paris`,, és `NYC`, különböző logikai partíciókat alkotnak. Nem kell aggódnia, hogy a rendszer törli a partíciókat az alapul szolgáló adatokat.

Azure Cosmos DB a tároló a méretezhetőség alapvető egysége. A tárolóhoz hozzáadott adatok, valamint a tárolón kiépített átviteli sebesség automatikusan (vízszintesen) particionálva van a logikai partíciók készletében. Az adatok és az átviteli sebesség az Azure Cosmos-tárolóhoz megadott partíciós kulcs alapján van particionálva. További információ: [Azure Cosmos-tároló létrehozása](how-to-create-container.md).

A logikai partíció az adatbázis-tranzakciók hatókörét is meghatározza. A logikai partíción belüli elemeket egy pillanatkép-elkülönítéssel [rendelkező tranzakció](database-transactions-optimistic-concurrency.md)használatával frissítheti. Amikor új elemeket adnak hozzá egy tárolóhoz, a rendszer transzparens módon létrehozza az új logikai partíciókat.

## <a name="physical-partitions"></a>Fizikai partíciók

Az Azure Cosmos-tárolók méretezése az adatok és az átviteli sebesség nagy számú logikai partíción keresztüli elosztásával történik. Belsőleg egy vagy több logikai partíció egy olyan fizikai partícióra van leképezve, amely replikák halmazát, más néven [*replikakészlet*](global-dist-under-the-hood.md)-készletet tartalmaz. Mindegyik replikakészlet a Azure Cosmos DB adatbázismotor egy példányát tárolja. A replikakészlet a fizikai partícióban tárolt, tartós, magasan elérhető és konzisztens módon tárolja az adattárolási készletet. A fizikai partíciók a maximális tárterületet és a kérelmeket (RUs) támogatják. A fizikai partíciót alkotó replikák öröklik a partíció tárolási kvótáját. A fizikai partíció összes replikája együttesen támogatja a fizikai partícióhoz lefoglalt átviteli sebességet. 

Az alábbi képen látható, hogyan vannak leképezve a logikai partíciók a globálisan elosztott fizikai partíciókhoz:

![Azure Cosmos DB particionálást bemutató rendszerkép](./media/partition-data/logical-partitions.png)

A tárolók számára kiépített átviteli sebesség egyenletesen oszlik el a fizikai partíciók között. A partíciós kulcs olyan kialakítása, amely nem osztja el az átviteli kérelmeket, akár "forró" partíciót is létrehozhat. A gyakori partíciók mértékének korlátozását és a kiosztott átviteli sebesség nem hatékony használatát, valamint a magasabb költségeket eredményezik.

A logikai partíciókkal ellentétben a fizikai partíciók a rendszer belső megvalósítása. Nem szabályozható a fizikai partíciók mérete, elhelyezése vagy száma, és nem szabályozható a logikai és a fizikai partíciók közötti leképezés. A logikai partíciók számát és az adatok, a munkaterhelés és az átviteli sebesség elosztását azonban [a megfelelő logikai partíciós kulcs](partitioning-overview.md#choose-partitionkey)kiválasztásával szabályozhatja.

## <a name="next-steps"></a>További lépések

* Tudnivalók [a partíciós kulcs](partitioning-overview.md#choose-partitionkey)kiválasztásáról.
* További információ [a kiépített átviteli sebességről Azure Cosmos db](request-units.md).
* Ismerkedjen meg a [Azure Cosmos db globális eloszlásával](distribute-data-globally.md).
* Útmutató az [átviteli sebesség Azure Cosmos](how-to-provision-container-throughput.md)-tárolón való kiépítéséhez.
* Útmutató az [átviteli sebesség Azure Cosmos](how-to-provision-database-throughput.md)-adatbázison való kiépítéséhez.
