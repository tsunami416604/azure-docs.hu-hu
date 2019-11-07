---
title: Erőforrás osztály átalakítása munkaterhelés-csoportba
description: Megtudhatja, hogyan hozhat létre olyan munkaterhelés-csoportot, amely hasonlít a Azure SQL Data Warehouse egy erőforrás osztályához.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5708dccce5f7cdcc33fe6bfca980126cd8b5ee7f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685711"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Erőforrás-osztályok átalakítása munkaterhelés-csoportokra
A munkaterhelés-csoportok olyan mechanizmust biztosítanak, amely elkülöníti és tartalmazza a rendszererőforrásokat.  Emellett a munkaterhelés-csoportok lehetővé teszik végrehajtási szabályok megadását a bennük futó kérelmekhez.  A lekérdezés időtúllépésének végrehajtási szabálya lehetővé teszi, hogy a szökevény lekérdezések felhasználói beavatkozás nélkül megszakítva legyenek.  Ez a cikk bemutatja, hogyan hozhat létre egy meglévő erőforrás-osztályt, és hogyan hozhat létre egy hasonló konfigurációval rendelkező munkaterhelés-csoportot.  Emellett a rendszer hozzáad egy opcionális lekérdezési időtúllépési szabályt is.

## <a name="understanding-the-existing-resource-class-configuration"></a>A meglévő erőforrás-osztály konfigurációjának ismertetése
A munkaterhelési csoportokhoz egy `REQUEST_MIN_RESOURCE_GRANT_PERCENT` nevű paraméter szükséges, amely meghatározza, hogy a rendszer hány összesített rendszererőforrást rendel a kérelmekhez.  Az erőforrás-hozzárendelés erőforrás- [osztályok](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#what-are-resource-classes) számára a párhuzamossági bővítőhelyek kiosztásával történik.  A `REQUEST_MIN_RESOURCE_GRANT_PERCENT`hoz használandó érték meghatározásához használja a sys. DM _workload_management_workload_groups_stats <link tbd> DMV-t.  Az alábbi lekérdezési lekérdezés például egy értéket ad vissza, amely a `REQUEST_MIN_RESOURCE_GRANT_PERCENT` paraméterhez használható a staticrc40 hasonló munkaterhelés-csoport létrehozásához.   

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> A munkaterhelés-csoportok a teljes rendszererőforrások százalékos arányán alapulnak.  

Mivel a munkaterhelés-csoportok a teljes rendszererőforrások százalékos arányán alapulnak, a vertikális fel-és leskálázás során a rendszer a statikus erőforrás-osztályokhoz lefoglalt erőforrások százalékos arányát a rendszererőforrások teljes változásaihoz viszonyítva határozza meg.  A DW1000c staticrc40 például a teljes rendszererőforrások 9,6%-át foglalja le.  A DW2000c 19,2%-os foglalást foglal le.  Ez a modell hasonló, ha a párhuzamosságot szeretné bővíteni, és több erőforrást szeretne lefoglalni egy kérésre.   

## <a name="create-workload-group"></a>Munkaterhelés-csoport létrehozása
Az ismert `REQUEST_MIN_RESOURCE_GRANT_PERCENT`használhatja a munkaterhelés csoport létrehozása <link> szintaxist a munkaterhelés csoport létrehozásához.  A munkaterhelési csoport erőforrásainak elkülönítéséhez opcionálisan megadhat egy nullánál nagyobb `MIN_PERCENTAGE_RESOURCE` is.  Azt is megteheti, hogy a munkaterhelési csoport által felhasználható erőforrások mennyiségének korlátozásához megadhatja a 100-nél kisebb `CAP_PERCENTAGE_RESOURCE`.  

Az alábbi példa úgy állítja be a `MIN_PERCENTAGE_RESOURCE`, hogy a rendszererőforrások 9,6%-át `wgDataLoads`, és garantálja, hogy az egyik lekérdezés minden alkalommal képes lesz futni.  Emellett a `CAP_PERCENTAGE_RESOURCE` 38,4%-ra van beállítva, és a munkaterhelés-csoportot négy egyidejű kérelemre korlátozza.  Ha a `QUERY_EXECUTION_TIMEOUT_SEC` paramétert a 3600 értékre állítja, akkor a több mint 1 órát futtató lekérdezések automatikusan meg lesznek szakítva.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Az osztályozó létrehozása
Korábban az erőforrás-osztályokhoz intézett lekérdezések leképezése a [sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class)használatával történt.  Ha ugyanazokat a funkciókat és leképezési kérelmeket szeretné elérni a munkaterhelés-csoportokhoz, használja a [munkaterhelés-osztályozó létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql) szintaxist.  A sp_addrolemember használata csak akkor engedélyezett, ha az erőforrásokat egy kérelemhez rendeli hozzá egy bejelentkezési azonosító alapján.  Az osztályozó a bejelentkezés mellett további lehetőségeket is biztosít, például a következőket:
    - label
    - session
    - Az alábbi példa a `AdfLogin` bejelentkezéshez tartozó lekérdezéseket rendeli hozzá, amelyekben a [beállítás felirata](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-label) is `factloads` a fent létrehozott munkaterhelés-`wgDataLoads` csoportra.

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

Munkaterhelések elkülönítése – a hivatkozás azt ismerteti, hogyan hozható létre munkaterhelés-csoport – hivatkozás