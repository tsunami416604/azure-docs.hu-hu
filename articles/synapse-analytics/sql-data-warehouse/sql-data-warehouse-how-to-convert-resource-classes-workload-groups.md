---
title: Erőforrás osztály átalakítása munkaterhelés-csoportba
description: Megtudhatja, hogyan hozhat létre olyan munkaterhelés-csoportot, amely hasonlít a Azure SQL Data Warehouse egy erőforrás osztályához.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f0cc0cd7233d0c16cae8389fcddd50a16cf96bd2
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683641"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Erőforrás-osztályok átalakítása munkaterhelés-csoportokra

A munkaterhelés-csoportok olyan mechanizmust biztosítanak, amely elkülöníti és tartalmazza a rendszererőforrásokat.  Emellett a munkaterhelés-csoportok lehetővé teszik végrehajtási szabályok megadását a bennük futó kérelmekhez.  A lekérdezés időtúllépésének végrehajtási szabálya lehetővé teszi, hogy a szökevény lekérdezések felhasználói beavatkozás nélkül megszakítva legyenek.  Ez a cikk bemutatja, hogyan hozhat létre egy meglévő erőforrás-osztályt, és hogyan hozhat létre egy hasonló konfigurációval rendelkező munkaterhelés-csoportot.  Emellett a rendszer hozzáad egy opcionális lekérdezési időtúllépési szabályt is.

> [!NOTE]
> Tekintse meg a számítási feladatok besorolása [című dokumentumban](sql-data-warehouse-workload-classification.md#mixing-resource-class-assignments-with-classifiers) található, a munkaterhelési csoportok és az erőforrás-osztályok egyidejű használatára vonatkozó útmutatást a [munkaterhelések besorolási](sql-data-warehouse-workload-classification.md) fogalma című témakörben.

## <a name="understanding-the-existing-resource-class-configuration"></a>A meglévő erőforrás-osztály konfigurációjának ismertetése

A munkaterhelési csoportokhoz a (z) nevű paramétert kell `REQUEST_MIN_RESOURCE_GRANT_PERCENT` megadni, amely meghatározza, hogy a rendszer hány összesített rendszererőforrást igényel.  Az erőforrás-hozzárendelés erőforrás- [osztályok](resource-classes-for-workload-management.md#what-are-resource-classes) számára a párhuzamossági bővítőhelyek kiosztásával történik.  A következőhöz használandó érték meghatározásához `REQUEST_MIN_RESOURCE_GRANT_PERCENT` használja a sys. dm_workload_management_workload_groups_stats DMV-t <link tbd> .  Az alábbi lekérdezési lekérdezés például egy értéket ad vissza, amely a `REQUEST_MIN_RESOURCE_GRANT_PERCENT` paraméterhez használható a staticrc40-hoz hasonló munkaterhelési csoport létrehozásához.

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> A munkaterhelés-csoportok a teljes rendszererőforrások százalékos arányán alapulnak.  

Mivel a munkaterhelés-csoportok a teljes rendszererőforrások százalékos arányán alapulnak, a vertikális fel-és leskálázás során a rendszer a statikus erőforrás-osztályokhoz lefoglalt erőforrások százalékos arányát a rendszererőforrások teljes változásaihoz viszonyítva határozza meg.  A DW1000c staticrc40 például a teljes rendszererőforrások 19,2%-át foglalja le.  A DW2000c 9,6%-os foglalást foglal le.  Ez a modell hasonló, ha a párhuzamosságot szeretné bővíteni, és több erőforrást szeretne lefoglalni egy kérésre.

## <a name="create-workload-group"></a>Munkaterhelés-csoport létrehozása

Az ismertnél a munkaterhelés `REQUEST_MIN_RESOURCE_GRANT_PERCENT` -csoport létrehozása <link> szintaxissal hozhatja létre a munkaterhelés csoportot.  Megadhat egy `MIN_PERCENTAGE_RESOURCE` nullánál nagyobb értéket is, ha el szeretné különíteni a munkaterhelés-csoport erőforrásait.  Azt is megteheti, `CAP_PERCENTAGE_RESOURCE` hogy a munkaterhelési csoport által felhasznált erőforrások mennyiségének korlátozásához a 100-nál kisebb értéket is megadhat.  

Az alábbi példa azt állítja be, hogy a rendszer a rendszererőforrások 9,6%-át felhasználja, `MIN_PERCENTAGE_RESOURCE` `wgDataLoads` és garantálja, hogy az egyik lekérdezés minden alkalommal képes lesz futni.  Emellett a `CAP_PERCENTAGE_RESOURCE` 38,4% értékre van állítva, és a számítási feladatnak négy egyidejű kérésre van korlátozva.  `QUERY_EXECUTION_TIMEOUT_SEC`Ha a paramétert 3600-ra állítja, a rendszer minden olyan lekérdezést automatikusan megszakít, amely 1 óránál hosszabb ideig fut.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Az osztályozó létrehozása

Korábban a lekérdezéseknek az erőforrás-osztályokra való leképezése [sp_addrolemembertel](resource-classes-for-workload-management.md#change-a-users-resource-class)történt.  Ha ugyanazokat a funkciókat és leképezési kérelmeket szeretné elérni a munkaterhelés-csoportokhoz, használja a [munkaterhelés-osztályozó létrehozása](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) szintaxist.  A sp_addrolemember használata csak akkor engedélyezett, ha egy bejelentkezési azonosító alapján leképezi az erőforrásokat egy kérelemre.  Az osztályozó a bejelentkezés mellett további lehetőségeket is biztosít, például a következőket:
    - címke
    - munkamenet
    - Az alábbi példa arra az esetre, ha a bejelentkezéshez olyan lekérdezéseket rendel, `AdfLogin` amelyeken a [beállítás felirata](sql-data-warehouse-develop-label.md) is be van állítva `factloads` a fent létrehozott munkaterhelés-csoportra `wgDataLoads` .

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## <a name="test-with-a-sample-query"></a>Tesztelés minta lekérdezéssel

Az alábbiakban egy minta lekérdezés és egy DMV-lekérdezés látható, amely biztosítja, hogy a munkaterhelés-csoport és az osztályozó helyesen legyen konfigurálva.

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
- [Útmutató: munkaterhelés-csoport létrehozása](quickstart-configure-workload-isolation-tsql.md)
- [Számítási feladatok BESOROLÁSának létrehozása (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?&view=azure-sqldw-latest)
- [MUNKATERHELÉS-csoport létrehozása (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
