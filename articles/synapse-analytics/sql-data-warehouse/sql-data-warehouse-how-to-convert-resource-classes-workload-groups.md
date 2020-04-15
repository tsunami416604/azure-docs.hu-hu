---
title: Erőforrásosztály átalakítása munkaterhelési csoportgá
description: Ismerje meg, hogyan hozhat létre egy olyan számítási feladatcsoportot, amely hasonlít egy erőforrásosztályhoz az Azure SQL Data Warehouse-ban.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5d73ba8f21fe7731fb751d42a8497ff8e1ebba7d
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383629"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Erőforrásosztályok konvertálása munkaterhelés-csoportokká

A számítási feladatok csoportjai a rendszererőforrások elkülönítésére és tárolására szolgálnak.  Emellett a számítási feladatok csoportjai lehetővé teszik, hogy végrehajtási szabályokat állítson be a bennük futó kérelmekhez.  A lekérdezési időout végrehajtási szabály lehetővé teszi a futtatott lekérdezések felhasználói beavatkozás nélkül történő megszakítását.  Ez a cikk bemutatja, hogyan vehet egy meglévő erőforrásosztályt, és hozzon létre egy hasonló konfigurációjú munkaterhelési csoportot.  Emellett egy választható lekérdezési időmegadási szabály is hozzáadódik.

> [!NOTE]
> Tekintse meg az [erőforrásosztály-hozzárendelések keverése osztályozókkal](sql-data-warehouse-workload-classification.md#mixing-resource-class-assignments-with-classifiers) című szakaszát a [Számítási feladatok besorolása](sql-data-warehouse-workload-classification.md) koncepciódokumentumban a számítási feladatok csoportjainak és erőforrásosztályai egyidejű használatával kapcsolatos útmutatásnak.

## <a name="understanding-the-existing-resource-class-configuration"></a>A meglévő erőforrásosztály-konfiguráció ismertetése

A számítási feladatok `REQUEST_MIN_RESOURCE_GRANT_PERCENT` csoportjainak meg kell adniuk egy hívott paramétert, amely megadja a kérésenként lefoglalt összes rendszererőforrás százalékát.  Az erőforrás-elosztás az [erőforrásosztályokhoz](resource-classes-for-workload-management.md#what-are-resource-classes) az egyidejűségi tárolóhelyek lefoglalásával történik.  A megadott érték meghatározásához használja a sys.dm_workload_management_workload_groups_stats DMV.To determine the value to specify for, `REQUEST_MIN_RESOURCE_GRANT_PERCENT`use the sys.dm_workload_management_workload_groups_stats <link tbd> DMV.  Például az alábbi lekérdezési lekérdezés ad vissza `REQUEST_MIN_RESOURCE_GRANT_PERCENT` egy értéket, amely a paraméter a staticrc40-hez hasonló munkaterhelési csoport létrehozásához használható.

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> A munkaterhelési csoportok a teljes rendszererőforrások százalékos aránya alapján működnek.  

Mivel a munkaterhelési csoportok a teljes rendszererőforrások százalékos aránya alapján működnek, a fel- és leskálázás korlátja, a statikus erőforrásosztályokhoz rendelt erőforrások százalékos aránya a teljes rendszererőforrás-erőforrásokhoz viszonyítva megváltozik.  A Staticrc40 a DW1000c-nél például a teljes rendszererőforrás 9,6%-át foglalja le.  A DW2000c-nél 19,2%-ot osztanak ki.  Ez a modell hasonló, ha azt szeretné, hogy az egyidejűség és kérésenként több erőforrás felosztása szeretné bővíteni.

## <a name="create-workload-group"></a>Számítási feladatcsoport létrehozása

Az ismert `REQUEST_MIN_RESOURCE_GRANT_PERCENT`használatával a munkaterheléscsoport <link> létrehozása szintaxissal hozhatja létre a munkaterheléscsoportot.  A munkaterhelési csoport `MIN_PERCENTAGE_RESOURCE` erőforrásainak elkülönítéséhez tetszés szerint megadhat egy nullánál nagyobb at.  Emellett tetszés `CAP_PERCENTAGE_RESOURCE` szerint 100-nál kevesebbet is megadhat a munkaterhelés-csoport által felhasználható erőforrások mennyiségének korlátozásához.  

Az alábbi példa `MIN_PERCENTAGE_RESOURCE` úgy állítja be, hogy a rendszererőforrások 9,6%-át szentelje, `wgDataLoads` és garantálja, hogy egy lekérdezés minden alkalommal futtatható lesz.  Emellett 38,4%-ra van állítva, `CAP_PERCENTAGE_RESOURCE` és ezt a számítási feladatok csoportját négy egyidejű kérelemre korlátozza.  Ha a `QUERY_EXECUTION_TIMEOUT_SEC` paramétert 3600-ra állítja, az 1 óránál tovább futó lekérdezések automatikusan törlődnek.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Az osztályozó létrehozása

Korábban a lekérdezések erőforrásosztályokhoz való hozzárendelése [sp_addrolemember.](resource-classes-for-workload-management.md#change-a-users-resource-class)  Ha ugyanazt a funkciót szeretné elérni, és le szeretné képezni a számítási feladatok csoportjainak hozzárendelését, használja a [MUNKATERHELÉS-osztályzat létrehozása](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) szintaxist.  A sp_addrolemember használatával csak akkor lehet erőforrásokat leképezni egy kérelemhez bejelentkezés alapján.  Az osztályozó a bejelentkezésmellett további lehetőségeket is kínál, például:
    - label
    - munkamenet
    - idő Az alábbi példa olyan `AdfLogin` lekérdezéseket rendel hozzá a `factloads` bejelentkezésből, `wgDataLoads` amelyekhez az [OPTION LABEL](sql-data-warehouse-develop-label.md) is be van állítva a fent létrehozott munkaterhelési csoporthoz.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## <a name="test-with-a-sample-query"></a>Tesztelés mintalekérdezéssel

Az alábbiakban egy mintalekérdezés és egy DMV-lekérdezés biztosítja a számítási feladat csoport és osztályozó megfelelően konfigurálva.

```sql
SELECT SUSER_SNAME() --should be 'AdfLogin'

--change to a valid table AdfLogin has access to
SELECT TOP 10 *
  FROM nation
  OPTION (label='factloads')

SELECT request_id, [label], classifier_name, group_name, command
  FROM sys.dm_pdw_exec_requests
  WHERE [label] = 'factloads'
  ORDER BY submit_time DESC
```

## <a name="next-steps"></a>További lépések

- [Munkaterhelés elkülönítése](sql-data-warehouse-workload-isolation.md)
- [Hogyan hozzon létre egy számítási feladatcsoport](quickstart-configure-workload-isolation-tsql.md)
- [MUNKATERHELÉS-OSZTÁLYOZÓ LÉTREHOZÁSA (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?&view=azure-sqldw-latest)
- [SZÁMÍTÁSI FELADATCSOPORT LÉTREHOZÁSA (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
