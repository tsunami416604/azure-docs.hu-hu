---
title: Átviteli sebesség méretezése Azure Cosmos DB
description: Ez a cikk azt ismerteti, hogy a Azure Cosmos DB hogyan méretezi át az átviteli sebességet a különböző régiókban, ahol az Azure Cosmos-fiókot kiosztották.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 4cc8f2c90e74b5b3ab6df3169df0524f1134b66e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85113602"
---
# <a name="globally-scale-provisioned-throughput"></a>Kiosztott átviteli sebesség globális skálázása 

Azure Cosmos DB a kiépített átviteli sebesség a kérelmek egység/másodperc (RU/s vagy a többes számú RUs) formájában jelenik meg. A RUs az olvasási és írási műveletek költségeit a Cosmos-tárolón méri, ahogy az alábbi képen is látható:

:::image type="content" source="./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png" alt-text="Kérelmek egységei" border="false":::

Egy Cosmos-tárolón vagy egy Cosmos-adatbázison is kiépítheti az RUs-t. A tárolón kiépített RUs kizárólag az adott tárolón végrehajtott műveletekhez érhető el. Az adatbázison kiépített RUs az adott adatbázisban található összes tároló között megoszlik (kivéve a kizárólag hozzárendelt RUs-mel rendelkező tárolókat).

A kiépített átviteli sebesség rugalmas skálázása érdekében bármikor növelheti vagy csökkentheti a kiépített RU/mp-t. További információ: [How-to kiépítése átviteli sebesség](set-throughput.md) és rugalmasan méretezhető Cosmos-tárolók és-adatbázisok. A globális skálázási sebességhez bármikor hozzáadhat vagy eltávolíthat régiókat a Cosmos-fiókból. További információ: [régiók hozzáadása/eltávolítása az adatbázis-fiókjából](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Több régió egy Cosmos-fiókkal való társítása számos forgatókönyv esetében fontos, így alacsony késést és [magas rendelkezésre állást](high-availability.md) érhet el világszerte.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>A kiépített átviteli sebesség elosztása a régiók között

Ha a Cosmos-tárolón (vagy adatbázison) kiépíti az *r* RUS-t, Cosmos db biztosítja, hogy az *r* RUS elérhető legyen a Cosmos-fiókhoz társított *minden* régióban. Minden alkalommal, amikor új régiót ad hozzá a fiókjához, Cosmos DB automatikusan kiépíti az *"R"* RUs-t az újonnan hozzáadott régióban. A Cosmos-tárolón végrehajtott műveletek garantáltak az *R* RUs minden régióban való lekéréséhez. Az RUs nem rendelhető hozzá egy adott régióhoz. A Cosmos-tárolón (vagy adatbázison) kiépített RUs a Cosmos-fiókhoz társított összes régióban kiépítve.

Feltételezve, hogy egy Cosmos-tároló *"R"* RUs-vel van konfigurálva, és a Cosmos-fiókhoz vannak társítva *"N"* régiók, akkor:

- Ha a Cosmos-fiók egyetlen írási régióval van konfigurálva, akkor a tárolón a teljes globálisan elérhető összes RUs = *R* x *N*.

- Ha a Cosmos-fiók több írási régióval van konfigurálva, akkor a tárolóban az összes globálisan elérhető RUs az = *R* x (*N*+ 1). A további *R* RUs-ket a rendszer automatikusan kiépíti a frissítési ütközések és az entrópia-alapú forgalom feldolgozásához a régiók között.

Az Ön által választott [konzisztencia-modell](consistency-levels.md) az átviteli sebességet is befolyásolja. Körülbelül 2x olvasási sebességet érhet el a nyugodtabb konzisztencia-szintek (például *munkamenet*, *konzisztens előtag* és *végleges* konzisztencia) tekintetében az erősebb konzisztencia-szintekhez képest (például *kötött* elavulás vagy *erős* konzisztencia).

## <a name="next-steps"></a>További lépések

Következő lépésként megtudhatja, hogyan konfigurálhatja az átviteli sebességet egy tárolón vagy adatbázisban:

* [Tárolók és adatbázisok átviteli sebességének beolvasása és beállítása](set-throughput.md) 

