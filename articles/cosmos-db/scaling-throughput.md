---
title: Az Azure Cosmos DB méretezési átviteli sebesség
description: Ez a cikk bemutatja, hogyan Azure Cosmos DB méretezi az átviteli sebességet rugalmasan
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: b645fe210e7eeb073380dcadefead3e1b4d7ccc0
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407436"
---
# <a name="globally-scale-provisioned-throughput"></a>Kiosztott átviteli sebesség globális skálázása 

Az Azure Cosmos DB kiosztott átviteli sebesség jelenik meg kérelem egység/másodperc (RU/mp vagy többes számú kérelemegység formájában). Mindkét olvasási költsége mérjük, illetve írási műveleteket végeznek az Cosmos-tároló, a következő képen látható módon:

![Kérelemegység](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

Telepíthet egy Cosmos-tároló vagy egy Cosmos-adatbázis a RUs. Egy tároló kiosztott Kérelemegységek érhetők el kizárólag az adott tárolón végrehajtott műveletek. Az adatbázisok kiosztott Kérelemegységek (kivéve a kizárólag kiosztott Kérelemegységek tartalmazó összes tárolót) adatbázison belüli összes tárolók között vannak megosztva.

A kiosztott átviteli sebesség rugalmasan méretezést, növelheti vagy csökkentheti a kiosztott RU/s bármikor. További információkért lásd: [útmutató kiépítése átviteli](set-throughput.md) és rugalmasan méretezhető Cosmos-tárolók és adatbázisok. Globálisan méretezéshez az átviteli sebesség, akkor is régiók hozzáadása vagy eltávolítása a Cosmos-fiókjából bármikor. További információkért lásd: [hozzáadása/eltávolítása régiók adatbázis-fiókjából](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Több régióban társít egy Cosmos-fiók fontos a számos forgatókönyv - közel valós idejű eléréséhez és [magas rendelkezésre állású](high-availability.md) szerte a világon.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Hogyan régió között oszlanak meg kiosztott átviteli sebesség

Ha kioszt *"R"* RUs Cosmos tároló (vagy adatbázis), a Cosmos DB biztosítja, hogy *"R"* fenntartott egységek érhetők el a *egyes* Cosmos-fiókjához társított régió. Minden alkalommal, amikor egy új régióban adja hozzá a fiókjához, Cosmos DB automatikusan kiosztja *"R"* RUs az újonnan hozzáadott régióban. A műveletek lettek elvégezve, mint a Cosmos-tároló beszerzése garantáltan *"R"* RUs minden régióban. Egy adott régióban külön-külön nem rendelhető hozzá RUs. Az, Cosmos-tároló (vagy adatbázis) kiosztott Kérelemegységek a Cosmos-fiókjához társított összes régióban vannak kiépítve.

Feltételezve, hogy meg van adva egy Cosmos-tárolót az *"R"* Kérelemegységet, és nincsenek *n* Cosmos-fiók, majd társított régiók:

- Ha a Cosmos-fiók van konfigurálva a egyetlen írási régiót, a Kérelemegységek teljes rendelkezésre álló globálisan a tárolóra = *R* x *N*.

- Ha a Cosmos-fiók van konfigurálva több írási régióval, a tárolót a globálisan elérhető teljes RUs = *R* x (*N*+ 1). A további *R* RUs automatikusan hozzáférést kapnak folyamat frissítési ütközések és a víruskereső vysokou forgalom a régiók között elosztva.

A választott [konzisztenciájú modellt](consistency-levels.md) is hatással van az átviteli sebességet. A több Könnyített konzisztenciaszintek körülbelül 2 x olvasás átviteli sebességét is igénybe (pl. *munkamenet*, *konzisztens előtag* és *végleges* konzisztencia) képest erősebb konzisztenciaszintek (pl. *korlátozott frissesség* vagy *erős* konzisztencia).

## <a name="next-steps"></a>További lépések

Ezután megismerheti egy tároló vagy az adatbázis átviteli sebesség konfigurálásához:

* [GET, és állítsa be az átviteli sebesség tárolók és adatbázisok](set-throughput.md) 

