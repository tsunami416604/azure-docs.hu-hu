---
title: Az Azure SQL Data Warehouse számítási feladatok fontossági |} A Microsoft Docs
description: Útmutató az Azure SQL Data Warehouse számára a lekérdezések beállításához.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 0147977307ec22134777d6c3e8242a4191362ada
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66233841"
---
# <a name="azure-sql-data-warehouse-workload-importance"></a>Az Azure SQL Data Warehouse számítási feladatok fontossági

Ez a cikk bemutatja, hogyan befolyásolhatja a számítási feladatok fontossági a SQL Data Warehouse-kérelmek esetén a végrehajtás sorrendje.

## <a name="importance"></a>Fontosság

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

Üzleti igények adatraktározás számítási lennie, mint a többi fontosabb lehet szükség.  Példaként vegyünk egy forgatókönyvet, ahol alapvető fontosságú értékesítési adatok betöltése előtt a pénzügyi időszak lezárása.  Adatok betölti a más forrásokból, például az időjárási adatok szigorú SLA-k nem rendelkezik.   Nagyon fontosként megjelölve értékesítési adatok betöltése a kérés és a kevésbé fontos beállítása betölteni, hogy adatokat biztosítja, hogy az értékesítési adatok betöltése a kérelemre lekérdezi az első erőforrásokhoz való hozzáférést, és befejezi a körülményekre.

## <a name="importance-levels"></a>Fontossági szintek

Nincsenek öt fontossági szintek: alacsony, below_normal, normál, above_normal és nagy.  Kérelmek fontosság nem állít be hozzá vannak rendelve az alapértelmezett szint, a normál.  Fontosság ugyanolyan szintű kérelmek rendelkezik ütemezési viselkedést, hogy létezik még ma.

## <a name="importance-scenarios"></a>Fontosság forgatókönyvek

Az alapvető fontosságú a forgatókönyvben az értékesítési és időjárási adatokat a fent leírt túli nincsenek egyéb forgatókönyvek, ahol számítási feladatok fontossági segít az adatok feldolgozására és lekérdezési igények felel meg.

### <a name="locking"></a>Zárolás

És az írási tevékenység természetes versengés egy területéhez olvasási zárolások való hozzáférést.  Egyes tevékenységek, például [partíció közötti váltás](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition) vagy [átnevezése OBJEKTUMHOZ](/sql/t-sql/statements/rename-transact-sql) emelt szintű zárolások igényelnek.  Az SQL Data Warehouse számítási feladatok fontossági, anélkül optimalizálja a átviteli sebességet.  Optimalizálás az átviteli sebesség azt jelenti, hogy futtatásakor és várólistára került kérelmeket ugyanazon zárolási igényeinek rendelkezik, és erőforrás áll rendelkezésre, a várólistára került kérelmeket elkerülheti a korábban a kérelem-várólistában érkező kérések magasabb zárolás van szüksége.  Számítási feladatok fontossági kérések magasabb zárolás alkalmazását követően szükséges. Az alacsonyabb fontossági magasabb fontossági rendelkező kérelem futtatása előtt kérelmet.

Vegye figyelembe az alábbi példában:

V1 aktívan fut, és jelölje ki adatokat SalesFact.
2. negyedévi várólistára végrehajtásához Q1 Várakozás van állítva.  Ez el lett küldve, 9-kor, és be SalesFact partíció kapcsoló új adatokat próbál.
3. negyedévi 9-kor: 01 elküldésekor, és válassza az SalesFact adatok szeretné.

Ha v2 és a 3. negyedévi rendelkezik ugyanolyan fontosságúként kezeli, és továbbra is végrehajtja az 1., 3. negyedévi megkezdi a végrehajtása. 2. negyedévi továbbra is a SalesFact kizárólagos zárolást várja.  Ha 2. negyedévi, mint 3. negyedévi magasabb fontossági, 3. negyedévi megvárja, amíg a 2. negyedévi, végrehajtási megkezdése előtt befejeződött.

### <a name="non-uniform-requests"></a>Nem egységes kérelmek

Egy másik forgatókönyv, ahol fontos segíthet lekérdezési igények figyelembevételével készült akkor, ha a kérelmek különböző erőforrás-osztályokra való elküldése.  Amint lett korábban már említettük, alatt ugyanolyan fontosságúként kezeli, az SQL Data Warehouse optimalizálja a átviteli sebességet.  Ha vegyes mérete kérések (például smallrc vagy mediumrc) várólistára kerülnek, az SQL Data Warehouse a legkorábbi beérkező kérést, amely a rendelkezésre álló erőforrások elférjen fog választani.  Alkalmazása esetén a számítási feladatok fontossági, ezután a legmagasabb fontosság kérelem van ütemezve.
  
Vegye figyelembe az alábbi példa a DW500c:

1., 2. negyedévi, 3. negyedévi és 4. kérdés smallrc lekérdezések futnak.
5. kérdés a mediumrc erőforrásosztályhoz van elküldött 9-kor.
6. kérdés smallrc erőforrásosztályhoz van elküldött 9-kor: 01.

Mivel az 5. kérdés mediumrc, két egyidejű helyet foglalnak le van szükség.  5. kérdés várnia kell, amíg a futó lekérdezések végrehajtásához két.  Azonban a futó lekérdezések (1., 4) egyik befejeződése után 6. kérdés van ütemezve, azonnal arra az esetre, mert az erőforrások végrehajtsák a lekérdezést.  Ha 5. kérdés magasabb fontossági, mint a 6. kérdés, a 6. kérdés megvárja, amíg a 5. kérdés megkezdheti végrehajtása előtt fut-e.

## <a name="next-steps"></a>További lépések

- Besorolás létrehozásával kapcsolatos további információkért lásd: a [MUNKATERHELÉS OSZTÁLYOZÓ létrehozása (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql).  
- Az SQL Data Warehouse számítási feladatok besorolási kapcsolatos további információkért lásd: [munkaterhelés besorolási](sql-data-warehouse-workload-classification.md).  
- Tekintse meg a rövid útmutatóban [munkaterhelés-osztályozó létrehozása](quickstart-create-a-workload-classifier-tsql.md) a számítási feladatok besorolás létrehozása.
- Tekintse meg az útmutatók a [konfigurálása számítási feladatok fontossági](sql-data-warehouse-how-to-configure-workload-importance.md) és annak [kezelése és megfigyelése számítási feladatok kezeléséhez](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Lásd: [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) a lekérdezések és a hozzárendelt fontosságát.
