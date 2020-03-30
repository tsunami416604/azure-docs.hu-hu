---
title: Az Azure Cosmos DB-adatok visszaállítása biztonsági másolatból
description: Ez a cikk ismerteti, hogyan állíthatja vissza az Azure Cosmos DB-adatokat egy biztonsági mentésből, hogyan léphet kapcsolatba az Azure-támogatással az adatok visszaállításához, az adatok visszaállítása utáni lépésekhez.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 19ca835ca8211202cd358ac2ec3695675183a372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70240766"
---
# <a name="restore-data-from-a-backup-in-azure-cosmos-db"></a>Adatok visszaállítása biztonsági másolatból az Azure Cosmos DB-ben 

Ha véletlenül törli az adatbázist vagy egy tárolót, [nyújthat be támogatási jegyet,]( https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) vagy [hívja az Azure-támogatást]( https://azure.microsoft.com/support/options/) az automatikus online biztonsági mentések adatainak visszaállításához. Az Azure-támogatás csak a kiválasztott csomagokhoz érhető el, például **standard,** **fejlesztő**és magasabb csomagok hoz. Az Azure-támogatás nem érhető el **az alapszintű** csomaggal. A különböző támogatási csomagokról az [Azure támogatási csomagok](https://azure.microsoft.com/support/plans/) lapján olvashat. 

A biztonsági mentés egy adott pillanatképének visszaállításához az Azure Cosmos DB megköveteli, hogy az adatok elérhetők a pillanatkép biztonsági mentési ciklusának időtartama alatt.

## <a name="request-a-restore"></a>Visszaállítás kérése

A visszaállítás kérése előtt a következő adatokat kell megadnia:

* Készítse elő az előfizetés-azonosítóját.

* Az adatok véletlen törlésének vagy módosításának módjától függően érdemes további információkra felkészülnie. Azt tanácsoljuk, hogy az információk előre, hogy minimálisra csökkentsék a oda-vissza, hogy káros lehet bizonyos időérzékeny esetekben.

* Ha a teljes Azure Cosmos DB-fiók törlődik, meg kell adnia a törölt fiók nevét. Ha egy másik fiókot hoz létre a törölt fióknevével megegyező névvel, ossza meg azt a támogatási csapattal, mert segít meghatározni a megfelelő kiválasztott fiókot. Javasoljuk, hogy minden törölt fiókhoz különböző támogatási jegyeket nyújtson be, mert minimálisra csökkenti a visszaállítás állapotának zavart.

* Ha egy vagy több adatbázis törlődik, meg kell adnia az Azure Cosmos-fiókot, valamint az Azure Cosmos-adatbázis nevét, és meg kell adnia, hogy létezik-e új adatbázis ugyanazzal a névvel.

* Ha egy vagy több tárolótörlődik, meg kell adnia az Azure Cosmos-fiók nevét, az adatbázisneveket és a tárolóneveket. És adja meg, ha egy tároló azonos nevű létezik.

* Ha véletlenül törölte vagy megrongálta az adatokat, 8 órán belül forduljon az [Azure-támogatáshoz,](https://azure.microsoft.com/support/options/) hogy az Azure Cosmos DB csapata segíthessen az adatok visszaállításában a biztonsági mentésekből.
  
  * Ha véletlenül törölte az adatbázist vagy a tárolót, nyisson meg egy Sev B vagy Sev C Azure támogatási esetet. 
  * Ha véletlenül törölt vagy megrongált néhány dokumentumot a tárolóban, nyisson meg egy Sev A támogatási esetet. 

Adatsérülés esetén, és a tárolóban lévő dokumentumokat módosítják vagy törlik, **a lehető leghamarabb törölje a tárolót.** A tároló törlésével elkerülheti, hogy az Azure Cosmos DB felülírja a biztonsági mentéseket. Ha valamilyen oknál fogva a törlés nem lehetséges, a lehető leghamarabb be kell nyújtania egy jegyet. Az Azure Cosmos-fiók neve, adatbázisnevek, tárolónevek mellett meg kell adnia azt az időpontot, amelyre az adatok visszaállíthatók. Fontos, hogy a lehető legpontosabb, hogy segítsen meghatározni a legjobb rendelkezésre álló biztonsági mentések abban az időben. Fontos az idő meghatározása UTC-ben is. 

Az alábbi képernyőkép bemutatja, hogyan hozhat létre egy támogatási kérelmet egy tároló (gyűjtemény/graph/table) adatok visszaállításához az Azure Portal használatával. Adjon meg további részleteket, például az adatok típusát, a visszaállítás célját, az adatok törlésének idejét, hogy segítsen a kérelem rangsorolásában.

![Biztonsági mentési támogatási kérelem létrehozása az Azure Portal használatával](./media/how-to-backup-and-restore/backup-support-request-portal.png)

## <a name="post-restore-actions"></a>A visszaállítás utáni műveletek

Az adatok visszaállítása után értesítést kap az új fiók nevéről (általában formátumban), `<original-name>-restored1`valamint a fiók visszaállításának időpontjáról. A visszaállított fiók ugyanazt a kiépített átviteli, indexelési szabályzatok, és ugyanabban a régióban, mint az eredeti fiók. Az előfizetés rendszergazdája vagy a társadminisztrátor oka a visszaállított fiók.

Az adatok visszaállítása után ellenőrizze és érvényesítse a visszaállított fiókban lévő adatokat, és győződjön meg arról, hogy azok a várt verziót tartalmazzák. Ha minden jól néz ki, az adatokat vissza kell telepítenie az eredeti fiókba az [Azure Cosmos DB módosítási hírcsatornájával](change-feed.md) vagy az [Azure Data Factory használatával.](../data-factory/connector-azure-cosmos-db.md)

Javasoljuk, hogy az adatok áttelepítése után azonnal törölje a tárolót vagy adatbázist. Ha nem törli a visszaállított adatbázisokat vagy tárolókat, azok a kérelemegységek, a tárolás és a kimenő forgalom költségeit fogják felhálni.

## <a name="next-steps"></a>További lépések

Ezután megtudhatja, hogyan telepítheti vissza az adatokat az eredeti fiókjába az alábbi cikkek használatával:

* Visszaállítási kérelem benyújtásához lépjen kapcsolatba az Azure-támogatással, [kérjen jegyet az Azure Portalról](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [A Cosmos DB módosítási hírcsatornájával](change-feed.md) áthelyezheti az adatokat az Azure Cosmos DB-be.

* [Az Azure Data Factory segítségével](../data-factory/connector-azure-cosmos-db.md) áthelyezheti az adatokat az Azure Cosmos DB-be.
