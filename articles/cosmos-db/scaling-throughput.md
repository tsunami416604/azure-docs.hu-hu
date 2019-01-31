---
title: Az Azure Cosmos DB méretezési átviteli sebesség
description: Ez a cikk bemutatja, hogyan Azure Cosmos DB méretezi az átviteli sebességet rugalmasan
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: dd17b08a16dedf474b2a1eca8fa8034672610c1f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55454436"
---
# <a name="globally-scale-provisioned-throughput"></a>Globális skálázása a kiosztott átviteli sebesség 

Az Azure Cosmos DB kiosztott átviteli sebesség jelenik meg kérelem egység/másodperc (RU/s, plural: RU-k). Mindkét olvasási költsége mérjük, illetve írási műveleteket végeznek az Cosmos-tároló, a következő képen látható módon:

![Kérelemegység](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

Telepíthet egy Cosmos-tároló vagy egy Cosmos-adatbázis a RUs. Egy tároló kiosztott Kérelemegységek adott tárolón végrehajtott műveletek kizárólag érhető el. Az adatbázisok kiosztott Kérelemegységek (kivéve a kizárólag kiosztott Kérelemegységek tartalmazó összes tárolót) adatbázison belüli összes tárolók között vannak megosztva.

Az átviteli sebesség rugalmasan méretezést, növelheti vagy csökkentheti a kiosztott RU/s bármikor. További információkért lásd: [útmutató kiépítése átviteli](set-throughput.md) és rugalmasan skálázható Cosmos-tárolók és adatbázisok. Globálisan méretezéshez az átviteli sebesség, akkor is régiók hozzáadása vagy eltávolítása a Cosmos-fiókjában bármikor. További információkért lásd: [hozzáadása/eltávolítása régiók adatbázis-fiókjából](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Közel valós idejű eléréséhez helyzetekben fontos több régióban társít egy Cosmos-fiók és [magas rendelkezésre állású](high-availability.md) szerte a világon.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Hogyan régió között oszlanak meg kiosztott átviteli sebesség

Ha üzembe helyezi az "R" RUs Cosmos tároló (vagy adatbázis), a Cosmos DB biztosítja, hogy "R" fenntartott egységek érhetők el a *egyes* Cosmos-fiókjához társított régió. Minden alkalommal, amikor egy új régióban adja hozzá a fiókjához, a Cosmos DB automatikusan látja a "R" RUs az újonnan hozzáadott régióban. A műveletek lettek elvégezve, mint a Cosmos-tároló garantáltan "R" RUs lekérése az egyes régiókban. Egy adott régióban külön-külön nem rendelhető hozzá RUs. Az, Cosmos-tároló (vagy adatbázis) számára kiosztott Kérelemegységek a Cosmos-fiókjához társított összes régióban vannak kiépítve.

Feltételezve, hogy egy Cosmos-tároló meg van adva, az "R" fenntartott egységek, és nincsenek "n" régiók a Cosmos-fiókjához társított, majd:

- Ha a Cosmos-fiók van konfigurálva a egyetlen írási régiót, a Kérelemegységek teljes rendelkezésre álló globálisan a tárolóra = R x n

- Ha a Cosmos-fiók van konfigurálva több írási régióval, a tárolót a globálisan elérhető teljes RUs R = x (N + 1). A további R RUs automatikusan hozzáférést kapnak folyamat frissítési ütközések és a víruskereső vysokou forgalom a régiók között elosztva.

A választott [konzisztenciájú modellt](consistency-levels.md) is hatással van az átviteli sebességet. Körülbelül 2 x olvasási teljesítménye a munkamenet, konzisztens előtag és végleges konzisztenciát korlátozott frissesség, vagy erős konzisztencia képest kérheti le.

## <a name="next-steps"></a>További lépések

Ezután megismerheti átviteli sebesség konfigurálásához a következő cikk segítségével:

* [GET, és állítsa be az átviteli sebesség tárolók és adatbázisok](set-throughput.md) 

