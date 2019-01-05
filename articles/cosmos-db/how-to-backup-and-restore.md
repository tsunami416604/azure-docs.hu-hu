---
title: Hogyan lehet Azure Cosmos DB-adatok visszaállítása biztonsági másolatból
description: Ez a cikk azt ismerteti, hogyan lehet Azure Cosmos DB-adatok visszaállítása biztonsági másolatból, az adatok visszaállítása után elvégzendő lépések elérhetőségi adatokat, állítsa vissza az Azure ügyfélszolgálatától.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: e21a3ae23ca6a856c9524e52e80f33362f53cae1
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54040846"
---
# <a name="how-to-restore-azure-cosmos-db-data-from-a-backup"></a>Hogyan lehet Azure Cosmos DB-adatok visszaállítása biztonsági másolatból

Ha véletlenül törli az adatbázis és a egy tároló, [küldjön egy támogatási jegyet]( https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) vagy [hívja meg az Azure-támogatás]( https://azure.microsoft.com/support/options/) , visszaállíthatja az adatokat automatikus online biztonsági mentést. Az Azure-támogatás érhető el a kiválasztott csomagok csak például **Standard**, ** fejlesztői, és a magasabb, mint őket. Az Azure-támogatás nem érhető el a **alapszintű** tervet. Különböző támogatási csomagok kapcsolatos további információkért tekintse meg a [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/) lapot. 

Egy adott pillanatképet a biztonsági mentés visszaállításához az Azure Cosmos DB kell lennie, hogy az adatok a biztonsági mentési ciklust, hogy a pillanatkép idejére.

## <a name="request-a-restore"></a>A visszaállítási kérelem

A visszaállítás kérelmezése előtt rendelkeznie kell a következő adatokat:

* Az előfizetés-azonosító kész rendelkezik.

* Alapján, hogy az adatok véletlenül törölték vagy módosították el kell készítenie további információkat. Javasolt, hogy a rendelkezésre álló információk előre a vissza oda-, idő-és nagybetűket esetenként hátrányos lehet minimalizálása érdekében.

* Ha a teljes Azure Cosmos DB-fiókot törölték, meg kell adja meg a törölt fiók nevét. Ha egy másik fiókot hoz létre, amelynek a neve megegyezik a törölt fiók, megoszthatja, amely a támogatási csoporthoz, mert segít megállapítani a megfelelő fiókot válassza ki. Ajánlott minden törölt fiók fájl különböző támogatási jegyeket, mert minimalizálja a visszaállítás állapotának mindebben.

* Ha egy vagy több adatbázis törölve vannak, adja meg az Azure Cosmos-fiók, valamint az Azure Cosmos-adatbázis neve, és adja meg, hogy létezik-e egy új adatbázis ugyanazzal a névvel.

* Ha egy vagy több tárolók törlődnek, az Azure Cosmos fiók nevét, adatbázis nevét, és a tároló nevének kell biztosítania. És adja meg, ha van ilyen nevű tároló.

Ha adatok sérülésével történik, és ha a dokumentumok egy tárolón belül módosított vagy törölt, **törölje a tárolót a lehető leghamarabb**. A tároló törlésével felülírják a biztonsági mentések elkerülheti a Azure Cosmos DB-hez. Ha valamilyen okból a törlés nem lehetséges, hogy kell jegyet minél hamarabb. Fiók neve az Azure Cosmos, az adatbázis nevét, a gyűjtemények nevére, valamint a pont, amelyre az adatok tudja állítani az időben kell megadnia. Fontos, hogy segítsen határozza meg a legjobban elérhető biztonsági másolatok ekkor minél pontosabb. Fontos továbbá adja meg az idő (UTC). 

Az alábbi képernyőképen azt ábrázolja, hogyan hozzon létre egy támogatási kérelmet a container(collection/graph/table) adatok visszaállítása az Azure portal használatával. Adja meg a további részleteket, például írja be az adatokat, a helyreállítás célját meg, amikor az adatok segítenie rangsorolására a kérelem törölve lett.

![Hozzon létre egy biztonsági mentési támogatási kérést az Azure portal használatával](./media/how-to-backup-and-restore/backup-support-request-portal.png)

## <a name="post-restore-actions"></a>Visszaállítás utáni műveletek

Miután visszaállította az adatokat, az új fiók nevét egy értesítést kap (általában a következő formátumban van `<original-name>-restored1`) és az időt, ha a fiók helyre lett állítva a. A visszaállított fiók lesz az azonos kiosztott átviteli sebesség, indexelési szabályzataihoz és és az eredeti fiók ugyanabban a régióban van. Egy felhasználó, aki az előfizetés-rendszergazda vagy egy társfelügyeletű láthatja a visszaállított fiókot.

Az adatok visszaállítása után kell vizsgálja meg, és ellenőrizze az adatokat a visszaállított fiókban, és ellenőrizze, hogy a benne a várt verzió. Ha mindent rendben talál, kell-e az adatok áttelepítése az eredeti fiók az [Azure Cosmos DB módosításcsatornáját](change-feed.md) vagy [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

Azt javasoljuk, hogy biztosan törölje a tároló vagy az adatbázis közvetlenül az adatok áttelepítése után. Ha nem törli a visszaállított adatbázis vagy a tárolókat, azok költséget számítunk fel kérelemegység, a storage és a kimenő forgalom.

## <a name="next-steps"></a>További lépések

Ezután megismerheti az adatok migrálása vissza az eredeti fiók használatával a következő cikkeket:

* Győződjön meg arról, a visszaállítási kérelem, az Azure ügyfélszolgálatától a [jegyet az Azure Portalról](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Cosmos DB használatát a módosítási hírcsatornáról](change-feed.md) adatok áthelyezése az Azure Cosmos DB-hez.

* [Azure Data Factory használata](../data-factory/connector-azure-cosmos-db.md) adatok áthelyezése az Azure Cosmos DB-hez.
