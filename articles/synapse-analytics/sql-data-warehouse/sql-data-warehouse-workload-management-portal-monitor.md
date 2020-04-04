---
title: A munkaterhelés-kezelési portál figyelése
description: Útmutató a számítási feladatok kezelésével foglalkozó portál figyeléséhez az Azure Synapse Analytics szolgáltatásban.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: b1f21a996f7394def4d6b1e8bde9a5ccdf703dbb
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632429"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring-preview"></a>Azure Synapse Analytics – Számítási feladatok kezelése portálfigyelés (előzetes verzió)

Ez a cikk ismerteti, hogyan figyelheti [a számítási feladatok csoport](sql-data-warehouse-workload-isolation.md#workload-groups) erőforrás-kihasználtságés és lekérdezési tevékenység.
Az Azure Metrics Explorer konfigurálásáról az [Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) első lépések című cikkében olvashat részletesen.  Tekintse meg az [Erőforrás-kihasználtság](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) szakasz az Azure Synapse Analytics monitoring dokumentációt a rendszer erőforrás-felhasználás figyeléséről.
A számítási feladatok csoport metrikáinak két különböző kategóriája van megadva a számítási feladatok kezelésének figyeléséhez: erőforrás-elosztás és lekérdezési tevékenység.  Ezek a metrikák feloszthatók és szűrhetők számítási feladatok csoportja szerint.  A metrikák feloszthatók és szűrhetők attól függően, hogy rendszerdefiniálva vannak-e (erőforrásosztály-munkaterhelés-csoportok) vagy felhasználó által definiált (a felhasználó által [létrehozott CREATE WORKLOAD GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) szintaxissal).

## <a name="workload-management-metric-definitions"></a>Számítási feladatok kezelése metrikadefiníciók

|Metrikus neve                    |Leírás  |Aggregáció típusa |
|-------------------------------|-------------|-----------------|
|Effektív erőforrás-korlát százaléka | *A tényleges erőforrás-korlát* az erőforrások számítási feladatcsoport által elérhető százalékos arányának szigorú korlátozása, figyelembe véve a más munkaterhelés-csoportok számára lefoglalt *tényleges min erőforrásszázalékot.* Az *effektív erőforrásszázalék-mérőszám* a `CAP_PERCENTAGE_RESOURCE` CREATE WORKLOAD [GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) szintaxisban található paraméter rel van konfigurálva.  A tényleges értéket itt ismerteti.<br><br>Ha például egy `DataLoads` munkaterhelési `CAP_PERCENTAGE_RESOURCE` csoport a = 100-mal jön létre, és egy másik munkaterhelés-csoport 25%-os effektív erőforrás-százalékkal jön létre, a `DataLoads` számítási feladat csoport *tényleges erőforrásszázaléka* 75%.<br><br>Az *effektív korlát erőforrásszázaléka* határozza meg a számítási feladatok csoportja által elérhető egyidejűség felső határát (és így a potenciális átviteli terhelést).  Ha további átviteli teljesítményre van szükség a *tényleges erőforrásszázalék-mérőszám* által jelenleg jelentettnél túl, vagy növelje a `CAP_PERCENTAGE_RESOURCE`, csökkentse a `MIN_PERCENTAGE_RESOURCE` többi számítási feladat csoportméretét, vagy növelje a példányt további erőforrások hozzáadásához.  A `REQUEST_MIN_RESOURCE_GRANT_PERCENT` csökkentés növelheti az egyidejűséget, de nem növelheti a teljes átviteli.| Min, Átlag, Max |
|Effektív min erőforrás százalék |*A tényleges minimális erőforrásszázaléka* a számítási feladatok csoportjához fenntartott és elkülönített erőforrások minimális százaléka, figyelembe véve a minimális szolgáltatási szintet.  Az Effektív min erőforrásszázalék metrika a `MIN_PERCENTAGE_RESOURCE` CREATE WORKLOAD [GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) szintaxisban található paraméter rel van konfigurálva.  A tényleges értéket [itt](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values)ismerteti .<br><br>Használja az Összeg összesítési típust, ha ez a metrika szűretlen és felbontatlan a rendszeren konfigurált teljes számítási feladatok elkülönítésének figyeléséhez.<br><br>A *hatékony min erőforrás százaléka* határozza meg a garantált egyidejűség (és így garantált átviteli) a számítási feladatok csoport érhető el.  Ha további garantált erőforrásokra van szükség a *tényleges min erőforrás* százalékmetria által jelentetten túl, növelje a `MIN_PERCENTAGE_RESOURCE` számítási feladatok csoportjához konfigurált paramétert.  A `REQUEST_MIN_RESOURCE_GRANT_PERCENT` csökkentés növelheti az egyidejűséget, de nem növelheti a teljes átviteli. |Min, Átlag, Max|
|Számítási feladatok csoport aktív lekérdezései  |Ez a metrika a számítási feladatcsoporton belüli aktív lekérdezéseket jelenti.  A szűretlen és felbontatlan metrika használatával megjelenik a rendszeren futó összes aktív lekérdezés.|Összeg         |
|Számítási feladatok csoportjának lefoglalása maximális erőforrásszázalék szerint |Ez a metrika az erőforrások számítási terhelési csoportonkénti *tényleges korláthoz* viszonyított százalékos allokációját jeleníti meg.  Ez a metrika a számítási feladatcsoport hatékony kihasználását biztosítja.<br><br>Fontolja meg `DataLoads` egy számítási feladatok csoport *hatékony erőforrásszázaléka* 75%, és a `REQUEST_MIN_RESOURCE_GRANT_PERCENT` konfigurált 25%.  A *Számítási feladatok csoport lefoglalása a maximális erőforrásszázalék* érték `DataLoads` szerint 33% (25% / 75%) lenne. ha egyetlen lekérdezés futott ebben a számítási feladatcsoportban.<br><br>Ezzel a metrikával azonosíthatja a számítási feladatcsoport kihasználtságát.  A 100%-ot megközelítő érték azt jelzi, hogy a munkaterhelés-csoport számára elérhető összes erőforrás használatban van.  Emellett a *számítási feladatok csoport várólistára helyezett lekérdezések metrikája* ugyanahhoz a számítási feladatcsoporthoz, amely nek értéke nagyobb, mint nulla azt jelzi, hogy a számítási feladatok csoport további erőforrásokat használna, ha levan foglalva.  Ezzel szemben, ha ez a metrika következetesen alacsony, és a *számítási feladatok csoport aktív lekérdezések* alacsony a számítási feladatok csoportja nem használja.  Ez a helyzet különösen problematikus, ha *a tényleges erőforrás-kapacitás* nagyobb, mint nulla, mivel ez azt jelentené, [kihasználatlan munkaterhelés elkülönítése.](#underutilized-workload-isolation)|Min, Átlag, Max |
|Számítási feladatok csoportjának lefoglalása rendszerszázalék szerint | Ez a metrika az erőforrások teljes rendszerhez viszonyított százalékos felosztását jeleníti meg.<br><br>Fontolja meg `DataLoads` egy `REQUEST_MIN_RESOURCE_GRANT_PERCENT` számítási feladatok csoport egy konfigurált 25%-os.  *A munkaterhelési csoport lefoglalása rendszerszázalék* érték szerint 25% (25% / 100%) `DataLoads` lesz ha egyetlen lekérdezés futott ebben a számítási feladatcsoportban.|Min, Átlag, Max |
|Számítási feladatok csoport lekérdezési időmegválaszolásai |Az időkimenő terhelést okozó munkaterhelés-csoport lekérdezései.  Ez a metrika által jelentett lekérdezési időtúllépések csak akkor, ha a lekérdezés végrehajtása megkezdődött (nem tartalmazza a zárolás vagy az erőforrás várakozási ideje miatti várakozási időt).<br><br>A lekérdezési időkorlát `QUERY_EXECUTION_TIMEOUT_SEC` a [MUNKATERHELÉSCSOPORT LÉTREHOZÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) szintaxisban lévő paraméterrel van konfigurálva.  Az érték növelése csökkentheti a lekérdezési időtúlképek számát.<br><br>Fontolja meg `REQUEST_MIN_RESOURCE_GRANT_PERCENT` a számítási feladatok csoportjának paraméterének növelését az időkihatolások csökkentéséhez és lekérdezésenként több erőforrás lefoglalásához.  Megjegyzés: `REQUEST_MIN_RESOURCE_GRANT_PERCENT` a növekvő csökkenti a számítási feladatok csoportjának egyidejűségi mennyiségét. |Összeg |
|Munkaterhelés-csoport várólistán lévő lekérdezései | A végrehajtás megkezdésére váró, jelenleg várólistára helyezett munkaterhelési csoport lekérdezései.  A lekérdezések várólistára lehetnek, mert erőforrásokra vagy zárolásra várnak.<br><br>A lekérdezések számos okból várhatnak.  Ha a rendszer túlterhelt, és az egyidejűségi igény nagyobb, mint a rendelkezésre álló, a lekérdezések várólistára kerülnek.<br><br>Fontolja meg további erőforrások hozzáadását `CAP_PERCENTAGE_RESOURCE` a munkaterhelési csoporthoz a [CREATE WORKLOAD GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) utasítás paraméterének növelésével.  Ha `CAP_PERCENTAGE_RESOURCE` nagyobb, mint a tényleges korlát erőforrás százalékmetria, a konfigurált számítási feladatok elkülönítése más számítási feladatok csoport hatással van az ehhez a számítási feladatok csoporthoz lefoglalt erőforrásokat. *Effective cap resource percent*  Fontolja meg `MIN_PERCENTAGE_RESOURCE` más számítási feladatok csoportjainak csökkentését, vagy a példány felskálázása további erőforrások hozzáadásához. |Összeg |

## <a name="monitoring-scenarios-and-actions"></a>Forgatókönyvek és műveletek figyelése

Az alábbiakban egy sor diagram konfigurációk kiemelni számítási feladatok kezelése metrika használat hibaelhárítási együtt kapcsolódó műveletek et a probléma megoldásához.

### <a name="underutilized-workload-isolation"></a>Kihasználatlan munkaterhelés elkülönítése

Vegye figyelembe a következő számítási feladatok csoport és `wgPriority` osztályozó konfiguráció, ahol a számítási `wcCEOPriority` feladat nevű csoport jön létre, és *theCEO* `membername` van leképezve, hogy a számítási feladatok osztályozó használatával.  A `wgPriority` számítási feladatok csoport 25%-os`MIN_PERCENTAGE_RESOURCE` számítási feladatok elkülönítése van konfigurálva ( = 25).  A *TheCEO* által benyújtott lekérdezések a rendszererőforrások 5%-át kapják (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5).

```sql
CREATE WORKLOAD GROUP wgPriority
WITH ( MIN_PERCENTAGE_RESOURCE = 25
      ,CAP_PERCENTAGE_RESOURCE = 50
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5);

CREATE WORKLOAD CLASSIFIER wcCEOPriority
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```

Az alábbi táblázat a következőképpen van beállítva:<br>
1. metrika: *Tényleges min erőforrás* `blue line`százalék (avg aggregáció, )<br>
2. *metrika: A számítási feladatok csoportfelosztása rendszerszázalék szerint* (avg aggregáció, `purple line`)<br>
Szűrő: [Munkaterhelés-csoport] =`wgPriority`<br>
![underutilized-wg.png](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png) A diagram azt mutatja, hogy a 25%-os munkaterhelés-elkülönítéssel átlagosan csak 10% -ot használnak.  Ebben az esetben `MIN_PERCENTAGE_RESOURCE` a paraméter értéke 10 vagy 15 közé csökkenthető, és lehetővé teszi a rendszer más számítási feladatai nak az erőforrások felhasználását.

### <a name="workload-group-bottleneck"></a>Számítási feladatok csoport szűk keresztmetszete

Vegye figyelembe a következő számítási feladatok csoport és `wgDataAnalyst` osztályozó konfiguráció, ahol a számítási feladat `wcDataAnalyst` nevű csoport jön létre, és a *DataAnalyst* `membername` van leképezve, hogy a számítási feladatok osztályozó használatával.  A `wgDataAnalyst` számítási feladatok csoport 6%-os`MIN_PERCENTAGE_RESOURCE` számítási feladatok elkülönítése van beállítva hozzá ( = 6) és az erőforrás-korlát 9% (`CAP_PERCENTAGE_RESOURCE` = 9).  A *DataAnalyst* által küldött lekérdezések a rendszererőforrások`REQUEST_MIN_RESOURCE_GRANT_PERCENT` 3%-át kapják ( = 3).

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6
      ,CAP_PERCENTAGE_RESOURCE = 9
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3);

CREATE WORKLOAD CLASSIFIER wcDataAnalyst
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```

Az alábbi táblázat a következőképpen van beállítva:<br>
1. *metrika: Tényleges felső korlát* `blue line`erőforrás százalék (Avg aggregáció, )<br>
2. *metrika: Számítási feladatok csoportszerinti lefoglalás maximális erőforrásszázalékszerint* (avg aggregáció, `purple line`)<br>
3. metrika: A számítási feladatok `turquoise line`csoport *várólistára helyezett lekérdezései* (összegösszesítés, )<br>
Szűrő: [Munkaterhelés-csoport] =`wgDataAnalyst`<br>
![palacknyakú-wg](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png) A diagram azt mutatja, hogy az erőforrások 9%-os felső határával a számítási feladatok csoportja 90%+ kihasznált (a *Számítási feladatok csoport maximális erőforrásszázalék-mutatója).*  A lekérdezések folyamatos sorban állása a *munkaterhelési csoport várólistás lekérdezések metrikája szerint.*  Ebben az esetben `CAP_PERCENTAGE_RESOURCE` a 9%-nál magasabb értékre való növelése lehetővé teszi, hogy több lekérdezés egyidejűvégrehajtása egyidejűleg.  A `CAP_PERCENTAGE_RESOURCE` feltételezi, hogy elegendő erőforrás áll rendelkezésre, és nem elkülönítve más munkaterhelés-csoportok.  Ellenőrizze a megnövelt korlátot az *Effektív korlát erőforrásszázalék-mérőszám ellenőrzésével.*  Ha nagyobb átviteli szükséges, fontolja `REQUEST_MIN_RESOURCE_GRANT_PERCENT` meg a 3-nál nagyobb értékre való növelését is.  A `REQUEST_MIN_RESOURCE_GRANT_PERCENT` lekérdezések számának növelése lehetővé teheti a lekérdezések gyorsabb futtatását.

## <a name="next-steps"></a>További lépések

- [Gyorsútmutató: A számítási feladatok elkülönítésének konfigurálása a T-SQL használatával](quickstart-configure-workload-isolation-tsql.md)<br>
- [SZÁMÍTÁSI FELADATCSOPORT LÉTREHOZÁSA (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)<br>
- [MUNKATERHELÉS-OSZTÁLYOZÓ LÉTREHOZÁSA (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)<br>
- [Erőforrás-kihasználtság figyelése](sql-data-warehouse-concept-resource-utilization-query-activity.md)
