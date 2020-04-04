---
title: A számítási feladat fontosságának kezelése és figyelése
description: Ismerje meg, hogyan kezelheti és figyelheti a kérelmek szintű fontosságát az Azure Synapse Analytics szolgáltatásban.
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
ms.openlocfilehash: 3efd8a776542616a9ceefba331b06406540905a8
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633321"
---
# <a name="manage-and-monitor-workload-importance-in-azure-synapse-analytics"></a>A számítási feladatok fontosságának kezelése és figyelése az Azure Synapse Analytics szolgáltatásban

Kezelheti és figyelheti a Synapse SQL-kérelemszintű fontosságát az Azure Synapse-ban a DMV-k és a katalógusnézetek használatával.

## <a name="monitor-importance"></a>Fontosság figyelése

A [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) dinamikus felügyeleti nézetben lévő új fontosságoszlop használatával figyelheti a fontosságot.
Az alábbi figyelési lekérdezés a lekérdezések küldési idejét és kezdési idejét mutatja. Tekintse át a küldési és kezdési időt a fontossággal együtt, és tekintse meg, hogy a fontosság hogyan befolyásolta az ütemezést.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Ha tovább szeretné vizsgálni a lekérdezések ütemezésének módját, használja a katalógusnézeteket.

## <a name="manage-importance-with-catalog-views"></a>Fontosság kezelése katalógusnézetekkel

A sys.workload_management_workload_classifiers katalógusnézet az osztályozókra vonatkozó információkat tartalmaz. Az erőforrásosztályokhoz leképező rendszer által definiált osztályozók kizárásához hajtsa végre a következő kódot:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

A [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)katalógusnézet az osztályozó létrehozásához használt paraméterekre vonatkozó információkat tartalmazza.  Az alábbi lekérdezés azt mutatja, hogy az ```membername``` ExecReportsClassifier az ExecutiveReports értékekkel rendelkező értékek paraméterén jött létre:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![lekérdezés eredményei](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

A hibabesorolás hibaelhárításának egyszerűsítése érdekében javasoljuk, hogy távolítsa el az erőforrásosztály szerepkör-leképezéseit a számítási feladatok osztályozóinak létrehozásakor. Az alábbi kód visszaadja a meglévő erőforrásosztály-szerepkör-tagságokat. Futtassa ```membername``` a sp_droprolemember a megfelelő erőforrásosztályból visszaadott minden egyes visszaadott hoz.
Az alábbi példa a létezés ellenőrzésére szolgál, mielőtt eldobna egy munkaterhelés-osztályozót:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>További lépések

- A besorolásról további információt a [Számítási feladatok besorolása című témakörben talál.](sql-data-warehouse-workload-classification.md)
- A fontosságról további információt a [Munkaterhelés fontossága című témakörben talál.](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Ugrás a Számítási feladatok fontosságának konfigurálása](sql-data-warehouse-how-to-configure-workload-importance.md)
