---
title: Az Azure Cosmos DB méretezési átviteli sebesség
description: Ez a cikk bemutatja, hogyan Azure Cosmos DB méretezi az átviteli sebességet rugalmasan
services: cosmos-db
author: dharmas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: adbac964e6654e16f6c405b9a5b8669326583f3e
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244156"
---
# <a name="scaling-throughput-in-azure-cosmos-db"></a>Az Azure Cosmos DB méretezési átviteli sebesség

Az Azure Cosmos DB kiosztott átviteli sebesség jelenik meg kérelem egység/másodperc (RU/s, plural: RUs). RUs a költségét olvasása is mérjük, és írási műveleteket végeznek az Cosmos-tároló.

![Kérelemegység](./media/scale-throughput/figure1.png)

Telepíthet egy Cosmos-tároló vagy egy Cosmos-adatbázis a RUs. Egy tároló kiosztott Kérelemegységek adott tárolón végrehajtott műveletek kizárólag érhető el. Az adatbázisok kiosztott Kérelemegységek (kivéve a kizárólag kiosztott Kérelemegységek tartalmazó összes tárolót) adatbázison belüli összes tárolók között vannak megosztva.

A rugalmas méretezéssel átviteli sebesség, akkor növelheti vagy csökkentheti a kiosztott RU/s bármikor. További információkért lásd: útmutató kiépítése átviteli sebességet, és rugalmasan skálázhatja a Cosmos-tárolók és adatbázisok. Globálisan méretezéshez az átviteli sebesség, akkor is régiók hozzáadása vagy eltávolítása a Cosmos-fiókjában bármikor. További információkért lásd: Útmutató a Cosmos-fiókjába régiók hozzáadása vagy eltávolítása. Közel valós idejű eléréséhez helyzetekben fontos több régióban társít egy Cosmos-fiók és [magas rendelkezésre állású](high-availability.md) szerte a világon.

## <a name="throughput-scaling-details"></a>Átviteli sebesség méretezési részletei

Ha üzembe helyezi az R RUs Cosmos tároló (vagy adatbázis), Cosmos DB biztosítja, hogy az R fenntartott egységek érhetők el a *egyes* a Cosmos-fiókjához társított régiót. Minden alkalommal, amikor egy új régióban Cosmos-fiókjába, adja hozzá a Cosmos DB automatikusan látja a R-re növeli az újonnan hozzáadott régióban. A műveletek lettek elvégezve, mint a Cosmos-tároló garantáltan minden Cosmos-fiókjához társított régiókhoz R RUs beolvasása. Külön-külön nem rendelhető hozzá egy adott régióban kérelemegység - Cosmos-fiókjához társított minden régió esetében az, Cosmos-tároló (vagy adatbázis) számára kiosztott Kérelemegységek felhasznált.

Feltételezve, hogy egy Cosmos-tároló meg van adva az R-re növeli, és nincsenek N régiók a Cosmos-fiókjához társított, majd:

- Ha a Cosmos-fiók van konfigurálva a egyetlen írási régiót, a Kérelemegységek teljes rendelkezésre álló globálisan a tárolóra = R x n
- Ha a Cosmos-fiók van konfigurálva több írási régióval, a tárolót a globálisan elérhető teljes RUs R = x (N + 1). A további R RUs automatikusan hozzáférést kapnak folyamat frissítési ütközések és a víruskereső vysokou forgalom a régiók között elosztva.

A választott [konzisztenciájú modellt](consistency-levels.md) is hatással van az átviteli sebességet. Körülbelül 2 x olvasási teljesítménye a munkamenet, konzisztens előtag és végleges konzisztenciát korlátozott frissesség, vagy erős konzisztencia képest kérheti le.

## <a name="next-steps"></a>További lépések

Ezután megismerheti átviteli sebesség konfigurálásához a következő cikk segítségével:

* [GET, és állítsa be az átviteli sebesség tárolók és adatbázisok](set-throughput.md) 

