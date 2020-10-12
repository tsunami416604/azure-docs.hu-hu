---
title: A számítási feladat fontosságának kezelése és figyelése
description: Ismerje meg, hogyan kezelheti és figyelheti a kérelmek szintjének fontosságát az Azure szinapszis Analyticsben.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 43006456142728287ddf4adba1fbb9b45f5ccc89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85211969"
---
# <a name="manage-and-monitor-workload-importance-in-azure-synapse-analytics"></a>A számítási feladatok fontosságának kezelése és figyelése az Azure szinapszis Analyticsben

Az Azure Szinapszisban az DMV-és katalógus-nézetek segítségével kezelheti és figyelheti a szinapszis SQL-kérelmek szintjének fontosságát.

## <a name="monitor-importance"></a>Figyelés fontossága

Figyelje meg a fontosságot a [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) dinamikus kezelés nézet új fontossági oszlopának használatával.
Az alábbi figyelési lekérdezés a lekérdezések küldési idejét és kezdési idejét jeleníti meg. Tekintse át a beküldési időt és a kezdési időpontot, és tekintse meg a fontosságot az ütemezés fontosságának meghatározásához

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

A lekérdezések időzítésének további megkereséséhez használja a katalógus nézeteket.

## <a name="manage-importance-with-catalog-views"></a>Fontosság kezelése a katalógus nézeteivel

Az sys.workload_management_workload_classifiers Catalog nézet az osztályozók információit tartalmazza. Ha ki szeretné zárni az erőforrás-osztályokra leképezett rendszer által definiált osztályozók körét, hajtsa végre a következő kódot:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

A katalógus nézet, [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), az osztályozó létrehozásakor használt paraméterekről tartalmaz információkat.  Az alábbi lekérdezés azt mutatja, hogy a ExecReportsClassifier a paraméterben lett létrehozva a ```membername``` ExecutiveReports értékkel:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![lekérdezés eredményei](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

A hibák elhárítása érdekében javasoljuk, hogy távolítsa el az erőforrás-osztály szerepkör-hozzárendeléseket a számítási feladatok besorolásának létrehozásakor. Az alábbi kód az erőforrás-osztály meglévő szerepkör-tagságát adja vissza. Sp_droprolemember futtatása a ```membername``` megfelelő erőforrás osztályból visszaadott mindegyikhez.
Az alábbi példa a meglétét ellenőrzi a számítási feladatok besorolásának eldobása előtt:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>További lépések

- A besorolással kapcsolatos további információkért lásd: [munkaterhelés besorolása](sql-data-warehouse-workload-classification.md).
- További információ a Fontosságról: számítási [feladatok fontossága](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Ugrás a számítási feladatok fontosságának konfigurálására](sql-data-warehouse-how-to-configure-workload-importance.md)
