---
title: Számítási feladat fontosságának konfigurálása
description: Ismerje meg, hogyan állíthatja be a kérelemszint fontosságát az Azure Synapse Analytics szolgáltatásban.
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
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633360"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Számítási feladatok fontosságának konfigurálása az Azure Synapse Analytics szolgáltatásban

Az Azure Synapse synapse-i synapse-i synapse-fontosságbeállításával befolyásolhatja a lekérdezések ütemezését. A nagyobb fontosságú lekérdezések ütemezése alacsonyabb fontosságú lekérdezések előtt lesz ütemezve. A lekérdezések fontosságának hozzárendeléséhez létre kell hoznia egy munkaterhelés-osztályozót.

## <a name="create-a-workload-classifier-with-importance"></a>Fontossági feladatosztályozó létrehozása

Az adatraktári forgatókönyvben gyakran vannak olyan felhasználók, akiknek a lekérdezéseik gyors futtatásához szükségük van.  A felhasználó lehet a vállalat vezetői, akiknek jelentéseket kell futtatniuk, vagy a felhasználó lehet egy adhoc lekérdezést futtató elemző. Hozzon létre egy számítási feladatok osztályozó rendelni fontosságot egy lekérdezést.  Az alábbi példák az új [számítási feladatok létrehozása osztályozó](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) szintaxisával két osztályozót hoznak létre. `Membername`lehet egyetlen felhasználó vagy csoport. Az egyes felhasználói besorolások elsőbbséget élveznek a szerepkör-besorolásokkal szemben. Meglévő adattárház-felhasználók megkereséséhez futtassa a következőket:

```sql
Select name from sys.sysusers
```

Számítási feladatok osztályozójának létrehozásához nagyobb fontosságú felhasználók számára futtasson:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  above_normal);  

```

Számítási feladat osztályozójának létrehozása alacsonyabb fontosságú adhoc lekérdezéseket futtató felhasználók számára:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  below_normal);  
```

## <a name="next-steps"></a>Következő lépések

- A számítási feladatok kezeléséről további információt a [Számítási feladatok besorolása című](sql-data-warehouse-workload-classification.md) témakörben talál.
- A fontosságról további információt a [Munkaterhelés fontossága című témakörben talál.](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Nyissa meg a Számítási feladatok fontosságának kezelése és figyelése](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
