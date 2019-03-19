---
title: Számítási feladatok fontossági |} A Microsoft Docs
description: Útmutató az Azure SQL Data Warehouse számára a lekérdezések beállításához.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: e53a6fcefb0f5370f6e24cc50fad2ad4ad4c64e3
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2019
ms.locfileid: "57864066"
---
# <a name="sql-data-warehouse-workload-importance-preview"></a>Az SQL Data Warehouse számítási feladatok fontossági (előzetes verzió)

Ez a cikk bemutatja, hogyan befolyásolhatja a számítási feladatok fontossági a SQL Data Warehouse-kérelmek esetén a végrehajtás sorrendje.

> [!Note]
> Az SQL Data Warehouse Gen2 számítási feladatok fontossági érhető el.

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

Az SQL Data Warehouse számítási feladatok besorolási kapcsolatos további információkért lásd: [SQL Warehouse számítási feladatok az Adatbesorolás](sql-data-warehouse-workload-classification.md) és [hozzon létre egy számítási feladat osztályozó](quickstart-create-a-workload-classifier-tsql.md). Lásd: [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) a lekérdezések és a hozzárendelt fontosságát.
