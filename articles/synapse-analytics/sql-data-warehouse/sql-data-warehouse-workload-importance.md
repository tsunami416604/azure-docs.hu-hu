---
title: Számítási feladat fontossága
description: Útmutató a Synapse SQL-készlet lekérdezéseinek fontosságának beállításához az Azure Synapse Analytics szolgáltatásban.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 2c8617cffaa81da6423011a494b8dbc82c42d218
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632461"
---
# <a name="azure-synapse-analytics-workload-importance"></a>Az Azure Synapse Analytics munkaterhelésének fontossága

Ez a cikk bemutatja, hogyan számítási feladatok fontossága hogyan befolyásolhatja a synapse SQL-készlet kérelmek az Azure Synapse végrehajtásának sorrendjét.

## <a name="importance"></a>Fontosság

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

Az üzleti igények megkövetelhetik, hogy az adattárház-számítási feladatok fontosabbak legyenek, mint mások.  Vegyünk egy olyan forgatókönyvet, amelyben a kritikus fontosságú értékesítési adatok betöltődnek a pénzügyi időszak lezárása előtt.  Más források, például az időjárási adatok adattöltése i. Az értékesítési adatok betöltésére irányuló kérelmek nagy jelentőségének beállítása és az időjárási adatok betöltésére vonatkozó kérelmek alacsony fontossága biztosítja, hogy az értékesítési adatok betöltése elsőként hozzáférjen az erőforrásokhoz, és gyorsabban befejeződjön.

## <a name="importance-levels"></a>Fontossági szintek

Öt fontossági szint van: alacsony, below_normal, normál, above_normal és magas.  A nem fontosságot nem tartalmazó kérelmek a normál alapértelmezett szintjét kapják. Az azonos fontossági szinttel rendelkező kérelmek ütemezési viselkedése megegyezik a mai ütemezési viselkedéssel.

## <a name="importance-scenarios"></a>Fontossági forgatókönyvek

Az értékesítési és időjárási adatokkal a fent ismertetett alapvető fontosságú forgatókönyvön túl más forgatókönyvek is vannak, ahol a számítási feladatok fontossága segít az adatfeldolgozási és lekérdezési igények kielégítésében.

### <a name="locking"></a>Zárolás

Hozzáférés zárak olvasási és írási tevékenység egyik területe a természetes versengés. Az olyan tevékenységek, mint [a partícióváltás](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition) vagy [az OBJEKTUM ÁTNEVEZÉSe](/sql/t-sql/statements/rename-transact-sql?view=azure-sqldw-latest) emelt szintű zárolást igényelnek.  Számítási feladatok fontossága nélkül az Azure Synapse Synapse Synapse synapse-i SQL-készlete az átviteli terhelést optimalizálja. Az átviteli forgalom optimalizálása azt jelenti, hogy ha a futó és a várólistára helyezett kérelmek azonos zárolási igényekkel rendelkeznek, és az erőforrások rendelkezésre állnak, a várólistára helyezett kérelmek megkerülhetik a kérésvárólistába korábban megérkezett magasabb zárolási igényekkel rendelkező kérelmeket. Miután a számítási feladatok fontosságát alkalmazza a kérelmek nagyobb zárolási igényeket. A nagyobb fontosságú kéréseket a kérelem előtt alacsonyabb fontossági kérdéssel futtatja.

Tekintse meg a következő példát:

- A Q1 aktívan futtatja és kiválasztja az adatokat a SalesFact-ből.
- Q2 várólistára vár Q1 befejezéséhez.  Reggel 9-kor küldték el, és megpróbálja felosztani az új adatokat a SalesFact-be.
- A Q3 9:01-kor kerül elküldésre, és adatokat szeretne kiválasztani a SalesFact-ből.

Ha a Q2 és q3-nak ugyanaz a fontossága, és a Q1 végrehajtása még folyamatban van, a Q3 megkezdi a végrehajtást. A Q2 továbbra is megvárja a SalesFact kizárólagos zárolását.  Ha a Q2 nagyobb jelentőséggel bír, mint a Q3, a Q3 megvárja, amíg a Q2 befejeződik, mielőtt megkezdené a végrehajtást.

### <a name="non-uniform-requests"></a>Nem egységes kérelmek

Egy másik forgatókönyv, ahol fontosság segíthet az igények lekérdezése, amikor a kérelmek különböző erőforrásosztályok at küldik el.  Mint korábban említettük, ugyanilyen fontos, synapse SQL-készlet az Azure Synapse optimalizálja az átviteli. Vegyes méretű kérelmek (például smallrc vagy mediumrc) várólistára, synapse SQL-készlet választja ki a legkorábbi érkező kérelmet, amely illeszkedik a rendelkezésre álló erőforrásokat. Ha a számítási feladatok fontossága van alkalmazva, a legnagyobb fontos kérés van ütemezve a következő.
  
Tekintsük a következő példát a DW500c-en:

- Q1, Q2, Q3 és Q4 smallrc lekérdezéseket futtatnak.
- Q5 küldik a mediumrc erőforrás osztály 09:00.
- Q6 van elküldve smallrc erőforrás osztály 9:01.

Mivel a Q5 közepes, két egyidejűségi bővítőhelyet igényel. Q5 meg kell várnia, amíg két futó lekérdezés befejeződik.  Ha azonban az egyik futó lekérdezés (Q1-Q4) befejeződik, a Q6 ütemezése azonnal megtörténik, mert a lekérdezés végrehajtásához rendelkezésre álló erőforrások léteznek.  Ha a Q5 nagyobb jelentőséggel bír, mint a Q6, a Q6 megvárja, amíg az 5.

## <a name="next-steps"></a>További lépések

- Az osztályozó konszern létrehozásáról további információt a [CREATE WORKLOAD CLASSIFIER (Transact-SQL) című témakörben talál.](/sql/t-sql/statements/create-workload-classifier-transact-sql)  
- A számítási feladatok besorolásáról további információt a [Számítási feladatok besorolása című témakörben talál.](sql-data-warehouse-workload-classification.md)  
- Tekintse meg a gyorsindítás [létrehozása számítási feladatok osztályozó,](quickstart-create-a-workload-classifier-tsql.md) hogyan hozhat létre egy számítási feladatok osztályozó.
- Tekintse meg a [számítási feladatok fontosságának konfigurálása](sql-data-warehouse-how-to-configure-workload-importance.md) című útmutatócikkeket, valamint a [Munkaterhelés-kezelés kezelésének és figyelésének módját.](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
- A lekérdezések és a hozzárendelt fontosság megtekintéséhez tekintse meg a [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) című témakört.
