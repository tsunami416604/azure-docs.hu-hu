---
title: Kezelhetők és figyelhetők Azure SQL Data Warehouse a számítási feladatok fontossági |} A Microsoft Docs
description: Ismerje meg, hogyan felügyelheti és figyelheti a kérelem szintű fontosságát.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: a39d71e0f8b8072cab6a3af9a2f0913973f303ee
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235926"
---
# <a name="manage-and-monitor-workload-importance-in-azure-sql-data-warehouse"></a>Kezelhetők és figyelhetők Azure SQL Data Warehouse a számítási feladatok fontossági

Felügyeleti és megfigyelési kérelem szintű fontosságát az Azure SQL Data Warehouse DMV-kkel és Rendszerkatalógus-nézetek használata.

## <a name="monitor-importance"></a>A figyelő fontosság

Az új fontosság oszlopa fontosság figyelése a [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) dinamikus kezelési nézetet.
Az alábbi lekérdezést mutat be küldési ideje és kezdő időpont lekérdezések figyelése. Tekintse át a Küldés ideje, és a kezdési idő együtt fontosság hogyan befolyásolja fontosság ütemezés megtekintéséhez.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Tekintse meg a további be, hogyan lekérdezések ütemezés folyamatban van, használja a Rendszerkatalógus-nézetek.

## <a name="manage-importance-with-catalog-views"></a>A katalógus nézetek fontosság kezelése

A sys.workload_management_workload_classifiers katalógusnézet ismerteti az Azure SQL Data Warehouse-példány osztályozót. A rendszer által definiált osztályozó hozzárendelése erőforrásosztályok kizárandó futtassa a következő kódot:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

A katalógusnézet [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest), információkat tartalmaz az osztályozó létrehozása használt paraméterek.  Az alábbi lekérdezést, amely ExecReportsClassifier lett létrehozva a a ```membername``` paramétere ExecutiveReports értékeket:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![Lekérdezés eredményei](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Hibaelhárítási téves besorolás egyszerűsítése érdekében, javasoljuk, számítási feladatok osztályozó létrehozása erőforrás osztály szerepkör-hozzárendelések eltávolítása. Az alábbi kódot az osztály szerepkörtagságai meglévő erőforrás adja vissza. Futtassa sp_droprolemember ```membername``` megfelelő erőforrásosztály adott vissza.
Alább példaként szolgál a számítási feladatok besorolás eldobása előtt meglétét:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>További lépések
- A besorolási további információkért lásd: [munkaterhelés besorolási](sql-data-warehouse-workload-classification.md).
- Fontosság további információkért lásd: [számítási feladatok fontossági](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Ugrás a számítási feladatok fontossági konfigurálása ](sql-data-warehouse-how-to-configure-workload-importance.md)
