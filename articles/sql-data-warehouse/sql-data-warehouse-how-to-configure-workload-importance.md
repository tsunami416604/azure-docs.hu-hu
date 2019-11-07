---
title: Számítási feladat fontosságának konfigurálása
description: Megtudhatja, hogyan állíthatja be a kérelmek szintjének fontosságát.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 59ba4b936f6098b0d0b3f5e571f107af088206e0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692693"
---
# <a name="configure-workload-importance-in-azure-sql-data-warehouse"></a>A számítási feladatok fontosságának konfigurálása Azure SQL Data Warehouse

A SQL Data Warehouse fontosságának beállítása lehetővé teszi a lekérdezések ütemezésének befolyásolását. A nagyobb jelentőségű lekérdezéseket a rendszer úgy ütemezi, hogy az alacsonyabb fontosságú lekérdezések előtt fusson. A lekérdezések fontosságának kiosztásához létre kell hoznia egy számítási feladatok besorolását.

## <a name="create-a-workload-classifier-with-importance"></a>Számítási feladatok besorolása fontossággal

Gyakran egy adattárház-forgatókönyvben olyan felhasználókkal rendelkezik, akiknek a lekérdezéseit gyorsan futtatni kell.  Előfordulhat, hogy a felhasználó a vállalat vezetőinek kell futnia, vagy ha a felhasználó egy ad hoc lekérdezést futtató elemző. Létre kell hoznia egy számítási feladatok besorolását, amely fontosságot rendel a lekérdezéshez.  Az alábbi példák az új [munkaterhelés-osztályozó](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) szintaxist használják két osztályozó létrehozásához.  A tagnév lehet egyetlen felhasználó vagy csoport is. Az egyes felhasználói besorolások elsőbbséget élveznek a szerepkör-besorolásokkal szemben. Meglévő adatraktár-felhasználók megkereséséhez futtassa a következőt:

```sql
Select name from sys.sysusers
```

A számítási feladatok besorolásának létrehozásához nagyobb jelentőségű felhasználó esetén a következőt kell futtatni:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  above_normal);  

```

A számítási feladatok besorolásának létrehozása az olyan felhasználók számára, akik az alkalmi lekérdezéseket alacsonyabb fontossággal futtatják:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  below_normal);  
```

## <a name="next-steps"></a>További lépések
- A számítási feladatok kezelésével kapcsolatos további információkért lásd: [munkaterhelés besorolása](sql-data-warehouse-workload-classification.md)
- További információ a Fontosságról: számítási [feladatok fontossága](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Ugrás a számítási feladatok fontosságának kezeléséhez és figyeléséhez](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
