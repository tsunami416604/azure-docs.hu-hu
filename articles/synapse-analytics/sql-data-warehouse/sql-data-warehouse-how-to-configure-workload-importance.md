---
title: Számítási feladat fontosságának konfigurálása
description: Ismerje meg, hogyan állíthatja be a kérelmek szintjének fontosságát az Azure szinapszis Analyticsben.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 0ab7b8be8780f7edb2734d99587bc7709ced9436
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633360"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Számítási feladatok fontosságának konfigurálása az Azure szinapszis Analyticsben

Az Azure Szinapszishoz készült szinapszis SQL-ben a fontosság beállítása lehetővé teszi a lekérdezések ütemezésének befolyásolását. A nagyobb jelentőségű lekérdezéseket a rendszer úgy ütemezi, hogy az alacsonyabb fontosságú lekérdezések előtt fusson. A lekérdezések fontosságának kiosztásához létre kell hoznia egy számítási feladatok besorolását.

## <a name="create-a-workload-classifier-with-importance"></a>Számítási feladatok besorolása fontossággal

Gyakran egy adattárház-forgatókönyvben olyan felhasználókkal rendelkezik, akiknek a lekérdezéseit gyorsan futtatni kell.  Előfordulhat, hogy a felhasználó a vállalat vezetőinek kell futnia, vagy ha a felhasználó egy ad hoc lekérdezést futtató elemző. Létre kell hoznia egy számítási feladatok besorolását, amely fontosságot rendel a lekérdezéshez.  Az alábbi példák az új [munkaterhelés-osztályozó](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) szintaxist használják két osztályozó létrehozásához. `Membername`egyetlen felhasználó vagy csoport lehet. Az egyes felhasználói besorolások elsőbbséget élveznek a szerepkör-besorolásokkal szemben. Meglévő adatraktár-felhasználók megkereséséhez futtassa a következőt:

```sql
Select name from sys.sysusers
```

A számítási feladatok besorolásának létrehozásához nagyobb jelentőségű felhasználó esetén a következőt kell futtatni:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  above_normal);  

```

A számítási feladatok besorolásának létrehozása az olyan felhasználók számára, akik az alkalmi lekérdezéseket alacsonyabb fontossággal futtatják:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  below_normal);  
```

## <a name="next-steps"></a>Következő lépések

- A számítási feladatok kezelésével kapcsolatos további információkért lásd: [munkaterhelés besorolása](sql-data-warehouse-workload-classification.md)
- További információ a Fontosságról: számítási [feladatok fontossága](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Ugrás a számítási feladatok fontosságának kezeléséhez és figyeléséhez](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
