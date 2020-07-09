---
title: Számítási feladat fontosságának konfigurálása
description: Ismerje meg, hogyan állíthatja be a kérelmek szintjének fontosságát az Azure szinapszis Analyticsben.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 83170f4090909e3edcc163312383773d088d8c57
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212122"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Számítási feladatok fontosságának konfigurálása az Azure szinapszis Analyticsben

Az Azure Szinapszishoz készült szinapszis SQL-ben a fontosság beállítása lehetővé teszi a lekérdezések ütemezésének befolyásolását. A nagyobb jelentőségű lekérdezéseket a rendszer úgy ütemezi, hogy az alacsonyabb fontosságú lekérdezések előtt fusson. A lekérdezések fontosságának kiosztásához létre kell hoznia egy számítási feladatok besorolását.

## <a name="create-a-workload-classifier-with-importance"></a>Számítási feladatok besorolása fontossággal

Gyakran egy adattárház-forgatókönyvben a felhasználók egy foglalt rendszeren vannak, akiknek gyorsan le kell futtatniuk a lekérdezéseket.  Előfordulhat, hogy a felhasználó a vállalat vezetőinek kell futnia, vagy ha a felhasználó egy ad hoc lekérdezést futtató elemző. A fontosság kiosztásához létre kell hoznia egy számítási feladatok besorolását, és a fontosságot egy lekérdezéshez rendeli hozzá.  Az alábbi példák a [munkaterhelés-osztályozó létrehozása](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) szintaxist használják két osztályozó létrehozásához. `Membername`egyetlen felhasználó vagy csoport lehet.  Meglévő adatraktár-felhasználók megkereséséhez futtassa a következőt:

```sql
Select name from sys.sysusers
```

A számítási feladatok besorolásának létrehozásához nagyobb jelentőségű felhasználó esetén a következőt kell futtatni:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = above_normal);
```

A számítási feladatok besorolásának létrehozása az olyan felhasználók számára, akik az alkalmi lekérdezéseket alacsonyabb fontossággal futtatják:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = below_normal);
```

## <a name="next-steps"></a>Következő lépések

- A számítási feladatok kezelésével kapcsolatos további információkért lásd: [munkaterhelés besorolása](sql-data-warehouse-workload-classification.md)
- További információ a Fontosságról: számítási [feladatok fontossága](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Ugrás a számítási feladatok fontosságának kezeléséhez és figyeléséhez](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
