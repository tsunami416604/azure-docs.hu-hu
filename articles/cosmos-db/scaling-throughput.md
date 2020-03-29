---
title: Átviteli műveletek méretezése az Azure Cosmos DB-ben
description: Ez a cikk ismerteti, hogy az Azure Cosmos DB hogyan skálázható átviteli a különböző régiókban, ahol az Azure Cosmos-fiók ki van építve.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 440f23afcd08326261be30432ad1f0ecb16f55fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873505"
---
# <a name="globally-scale-provisioned-throughput"></a>Kiosztott átviteli sebesség globális skálázása 

Az Azure Cosmos DB-ben a kiosztott átviteli igény kérésegységek/másodperc (RU/s vagy többes számú rt-k) jelenik meg. A cikkegységben az olvasási és írási műveletek költségét mérik a Cosmos-tárolóval szemben az alábbi képen látható módon:

![Kérelemegységek](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

A Cosmos-tároló vagy a Cosmos-adatbázis rinuszai üzembe helyezhető. A tárolón kiosztott több egység kizárólag az adott tárolón végrehajtott műveletekhez érhető el. Az adatbázisban kiosztott rt-k az adatbázis összes tárolója között vannak megosztva (kivéve a kizárólag hozzárendelt RUs-okkal rendelkező tárolókat).

Rugalmasan skálázás kiosztott átviteli, bármikor növelheti vagy csökkentheti a kiosztott RU/s bármikor növelheti vagy csökkentheti. További információkért lásd: [Átviteli átviteli és](set-throughput.md) rugalmas an-átviteli műveletek hez szükséges eszközök. Globális skálázási átviteli sebességgel bármikor hozzáadhat vagy eltávolíthat régiókat a Cosmos-fiókból. További információt a [Régiók hozzáadása és eltávolítása az adatbázisfiókból című témakörben talál.](how-to-manage-database-account.md#addremove-regions-from-your-database-account) Több régió társítása egy Cosmos-fiókkal számos forgatókönyvben fontos – az alacsony késés és a [magas rendelkezésre állás](high-availability.md) elérése érdekében világszerte.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>A kiosztott átviteli szolgáltatás régiók közötti elosztása

Ha egy Cosmos-tárolón (vagy *adatbázison) "R"* r-címeket létesít, a Cosmos DB biztosítja, hogy *az "R"* r-címkezelők a Cosmos-fiókhoz társított *minden* régióban elérhetők legyenek. Minden alkalommal, amikor új régiót ad hozzá a fiókjához, a Cosmos DB automatikusan leíregy *"R"* r-r-ek az újonnan hozzáadott régióban. A Cosmos-tároló n végrehajtott műveletek garantáltan az egyes régiókban *"R"* r-r-ek lesznek. A nem megfelelő erőforrásokat nem rendelhet szelektíven egy adott régióhoz. A Cosmos-tároló (vagy adatbázis) kiépített rúnák vannak kiépítve a Cosmos-fiókhoz társított összes régióban.

Feltételezve, hogy a Cosmos-tároló *"R"* r-címegységekkel van konfigurálva, és a Cosmos-fiókhoz *"N"* régiók vannak társítva, majd:

- Ha a Cosmos-fiók egyetlen írási régióval van konfigurálva, a tárolón elérhető összes ru / *R* x *N.*

- Ha a Cosmos-fiók több írási régióval van konfigurálva, a tárolón elérhető összes ru / *R* x (*N*+1) globálisan elérhető összes rendszeregység. A *R* további RPu-k automatikusan kivannak építve a frissítési ütközések és a régiók közötti entrópia-ellenes forgalom feldolgozásához.

A [konzisztenciamodell](consistency-levels.md) kiválasztása az átviteli fokkal is befolyásolja. Az erősebb konzisztenciaszintekhez (pl. *a peremcsend* vagy az *erős* konzisztencia) képest körülbelül 2x olvasási átviteli teljesítményt kaphat a lazább konzisztenciaszintek (pl. *munkamenet*, *konzisztens előtag* és *végleges* konzisztencia) érdekében.

## <a name="next-steps"></a>További lépések

Ezután megtudhatja, hogyan konfigurálhatja az átviteli fésületet egy tárolón vagy adatbázison:

* [Tárolók és adatbázisok átviteli átaputáinak beszerezni és beállítani](set-throughput.md) 

