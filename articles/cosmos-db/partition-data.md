---
title: Particionálás és horizontális skálázás az Azure Cosmos DB-ben
description: Ismerje meg, hogyan működik a particionálás a Azure Cosmos DBban, hogyan konfigurálhatja a particionálási és partíciós kulcsokat, és hogyan választhatja ki az alkalmazásának megfelelő partíciós kulcsot.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 57417a80ea83005c01b6f2a17206d46e6c049719
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85112778"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Particionálás és horizontális skálázás az Azure Cosmos DB-ben

Ez a cikk a logikai és fizikai partíciók közötti kapcsolatot ismerteti. Emellett a particionálással kapcsolatos ajánlott eljárásokat is ismerteti, és részletesen ismerteti, hogyan működik a horizontális skálázás a Azure Cosmos DBban. Ezen belső adatok megértéséhez nem szükséges, hogy [kiválassza a partíciós kulcsot](partitioning-overview.md#choose-partitionkey) , de a kezeltük, hogy egyértelmű legyen a Azure Cosmos db működése.

## <a name="logical-partitions"></a>Logikai partíciók

A logikai partíciók olyan elemekből állnak, amelyek ugyanazzal a partíciós kulccsal rendelkeznek. Például egy olyan tárolóban, amely az élelmiszer-táplálkozással kapcsolatos adatokat tartalmaz, minden elem tartalmaz egy `foodGroup` tulajdonságot. A `foodGroup` tároló partíciós kulcsaként is használható. Olyan elemek csoportjai, amelyek meghatározott értékekkel rendelkeznek `foodGroup` , például,, `Beef Products` `Baked Products` és `Sausages and Luncheon Meats` , különböző logikai partíciókat alkotnak. Nem kell aggódnia a logikai partíció törlésével kapcsolatban az alapul szolgáló adat törlésekor.

A logikai partíció az adatbázis-tranzakciók hatókörét is meghatározza. A logikai partíción belüli elemeket egy [Pillanatkép-elkülönítéssel rendelkező tranzakció](database-transactions-optimistic-concurrency.md)használatával frissítheti. Amikor új elemeket adnak hozzá egy tárolóhoz, a rendszer transzparens módon létrehozza az új logikai partíciókat.

A tárolóban található logikai partíciók száma nincs korlátozva. Minden logikai partíció akár 20 GB-ot is tárolhat. A jó partíciós kulcs választása a lehetséges értékek széles választékával rendelkezik. Például egy olyan tárolóban, ahol minden elem tartalmaz egy `foodGroup` tulajdonságot, a `Beef Products` logikai partíción belüli adatok 20 GB-ig növekednek. A lehetséges értékek széles választékával rendelkező [partíciós kulcs kiválasztásával](partitioning-overview.md#choose-partitionkey) biztosítható, hogy a tároló méretezhető legyen.

## <a name="physical-partitions"></a>Fizikai partíciók

Az Azure Cosmos-tárolók méretezése az adatok és az átviteli sebesség fizikai partíciók közötti elosztásával történik. Belsőleg egy vagy több logikai partíció egyetlen fizikai partícióra van leképezve. A legtöbb kis Cosmos-tároló sok logikai partícióval rendelkezik, de csak egyetlen fizikai partíciót igényel. A logikai partíciókkal ellentétben a fizikai partíciók a rendszer belső implementációja, és teljes mértékben a Azure Cosmos DB kezeli őket.

A Cosmos-tárolóban található fizikai partíciók száma a következőktől függ:

- Kiépített átviteli sebesség (az egyes fizikai partíciók másodpercenként legfeljebb 10 000 adatátviteli sebességet biztosíthatnak)
- Összes adattárolás (minden egyes fizikai partíció akár 50 GB is tárolhat)

A tárolóban lévő fizikai partíciók teljes száma nincs korlátozva. A kiosztott átviteli sebesség vagy az adatméret növekedésével a Azure Cosmos DB automatikusan új fizikai partíciókat hoz létre a meglévők felosztásával. A fizikai partíciók osztása nem befolyásolja az alkalmazás rendelkezésre állását. A fizikai partíció felosztása után az egyetlen logikai partíción belüli összes adattal továbbra is ugyanazon a fizikai partíción lesz tárolva. A fizikai partíciók megosztása egyszerűen létrehozza a logikai partíciók új hozzárendelését a fizikai partíciókhoz.

A tárolók számára kiépített átviteli sebesség egyenletesen oszlik el a fizikai partíciók között. A partíciós kulcs olyan kialakítása, amely nem osztja el az átviteli kérelmeket, akár "forró" partíciót is létrehozhat. A gyakori partíciók mértékének korlátozását és a kiosztott átviteli sebesség nem hatékony használatát, valamint a magasabb költségeket eredményezik.

A tároló fizikai partícióit a Azure Portal **metrika** panelének **Storage (tárolás** ) szakaszában tekintheti meg:

:::image type="content" source="./media/partition-data/view-partitions-zoomed-out.png" alt-text="Fizikai partíciók számának megtekintése" lightbox="./media/partition-data/view-partitions-zoomed-in.png" ::: 

Ebben a példában a `/foodGroup` partíciós kulcsként kiválasztott tárolóban a három téglalap egy fizikai partíciót jelöl. A rendszerképben a **partíciós kulcs tartománya** ugyanaz, mint a fizikai partíció. A kiválasztott fizikai partíció három logikai partíciót tartalmaz: `Beef Products` , `Vegetable and Vegetable Products` és `Soups, Sauces, and Gravies` .

Ha a másodpercenkénti adat18 000 átviteli kapacitást (RU/s) is kiépítjük, akkor a három fizikai partíció 1/3 a teljes kiépített átviteli sebesség alapján. A kiválasztott fizikai partíción belül a logikai partíció kulcsai `Beef Products` , `Vegetable and Vegetable Products` és `Soups, Sauces, and Gravies` együttesen a fizikai partíció 6 000 kiépített ru/s-t használhatják. Mivel a kiépített átviteli sebesség egyenletesen oszlik meg a tároló fizikai partíciói között, fontos, hogy olyan partíciós kulcsot válasszon, amely egyenletesen osztja el az átviteli sebességet [a megfelelő logikai partíciós kulcs kiválasztásával](partitioning-overview.md#choose-partitionkey). Ha olyan partíciós kulcsot választ, amely egyenletesen osztja el az átviteli sebességet a logikai partíciók között, akkor gondoskodni fog arról, hogy a fizikai partíciók átviteli sebessége egyensúlyban legyen.

## <a name="replica-sets"></a>Replikakészlet

Mindegyik fizikai partíció replikák halmazát, más néven [*replikakészlet*](global-dist-under-the-hood.md)-készletet tartalmaz. Mindegyik replikakészlet az Azure Cosmos adatbázismotor egy példányát tárolja. A replikakészlet a fizikai partícióban tárolt, tartós, magasan elérhető és konzisztens módon tárolja az adattárolási készletet. A fizikai partíciót alkotó replikák öröklik a partíció tárolási kvótáját. A fizikai partíció összes replikája együttesen támogatja a fizikai partícióhoz lefoglalt átviteli sebességet. A Azure Cosmos DB automatikusan kezeli a replikákat.

A legtöbb kisméretű Cosmos-tároló csak egyetlen fizikai partíciót igényel, de továbbra is legalább 4 replikával rendelkezik.

Az alábbi képen látható, hogyan vannak leképezve a logikai partíciók a globálisan elosztott fizikai partíciókhoz:

:::image type="content" source="./media/partition-data/logical-partitions.png" alt-text="Azure Cosmos DB particionálást bemutató rendszerkép" border="false":::

## <a name="next-steps"></a>További lépések

* Tudnivalók [a partíciós kulcs kiválasztásáról](partitioning-overview.md#choose-partitionkey).
* További információ [a kiépített átviteli sebességről Azure Cosmos db](request-units.md).
* Ismerkedjen meg a [Azure Cosmos db globális eloszlásával](distribute-data-globally.md).
* Útmutató az [átviteli sebesség Azure Cosmos-tárolón](how-to-provision-container-throughput.md)való kiépítéséhez.
* Útmutató az [átviteli sebesség Azure Cosmos-adatbázison](how-to-provision-database-throughput.md)való kiépítéséhez.
