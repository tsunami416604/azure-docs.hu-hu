---
title: Munkaterhelés-kezelési portál figyelése
description: Útmutató a munkaterhelés-kezelési portál monitorozásához az Azure szinapszis Analyticsben.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 01/13/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: f3baaab59031c4cfad036a7181318502d1969715
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942424"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring-preview"></a>Azure szinapszis Analytics – munkaterhelés felügyeleti portál monitorozás (előzetes verzió)
Ez a cikk azt ismerteti, hogyan lehet figyelni a [munkaterhelés-csoport](sql-data-warehouse-workload-isolation.md#workload-groups) erőforrásainak kihasználtságát és lekérdezési tevékenységeit. Az Azure-Metrikaböngésző konfigurálásával kapcsolatos további információkért tekintse meg az [azure Metrikaböngésző első lépései](../azure-monitor/platform/metrics-getting-started.md) című cikket.  A rendszererőforrás-használat figyelésével kapcsolatos részletekért tekintse meg a Azure SQL Data Warehouse figyelési dokumentációjának [erőforrás-felhasználás](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) szakaszát.
A munkaterhelés-kezelés figyeléséhez két különböző kategóriájú munkaterhelés-csoport metrikája van megadva: erőforrás-elosztási és lekérdezési tevékenység.  Ezek a metrikák a munkaterhelés csoport alapján bonthatók és szűrhetők.  A metrikák az alapján bonthatók és szűrhetők, ha a rendszer definiálva van (erőforrás-osztály munkaterhelési csoportok) vagy felhasználó által definiált (a [munkaterhelés-csoport létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) szintaxissal létrehozott felhasználó által létrehozva).

## <a name="workload-management-metric-definitions"></a>Munkaterhelés-kezelési metrika definíciói

|Metrika neve                    |Leírás  |Aggregáció típusa |
|-------------------------------|-------------|-----------------|
|Érvényes Cap-erőforrás százaléka | A *tényleges Cap-erőforrás százalékos* értéke a munkaterhelés-csoport által elérhető erőforrások százalékos arányának korlátozása, amely az egyéb munkaterhelés-csoportok számára kiosztott *tényleges minimális erőforrás-százalékos arányt* veszi figyelembe. A *tényleges Cap-erőforrás százalékos* mérőszáma a `CAP_PERCENTAGE_RESOURCE` paraméterrel van KONFIGURÁLVA a [munkaterhelés-csoport létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) szintaxisban.  A hatályos érték itt van leírva.<br><br>Ha például egy munkaterhelési csoport `DataLoads` jön létre a `CAP_PERCENTAGE_RESOURCE` = 100 és egy másik munkaterhelési csoport a 25%-os effektív minimális erőforrás-százalékos aránysal jön létre, a `DataLoads` munkaterhelés-csoport *tényleges maximális erőforrás-százalékos* értéke 75%.<br><br>A *tényleges Cap-erőforrás százaléka* határozza meg a munkaterhelés (és így lehetséges átviteli sebesség) felső határát.  Ha további átviteli sebességre van szükség a jelenlegi *Cap-erőforrás százalékos* mérőszáma által jelenleg jelentettnél, növelje a `CAP_PERCENTAGE_RESOURCE`, csökkentse az egyéb munkaterhelés-csoportok `MIN_PERCENTAGE_RESOURCE`ét, vagy bővítse a példányt több erőforrás hozzáadásával.  A `REQUEST_MIN_RESOURCE_GRANT_PERCENT` csökkentése növelheti a párhuzamosságot, de nem növelheti a teljes átviteli sebességet.| Minimum, átlag, maximum |
|Effektív minimális erőforrás százaléka |A *tényleges minimális erőforrás-százalékos* érték a munkaterhelés csoport számára fenntartott és elkülönített erőforrások minimális százalékaránya, amely a szolgáltatási szint minimumát veszi figyelembe.  A tényleges minimális erőforrás-százalékos metrika a [munkaterhelés-csoport létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) szintaxisban a `MIN_PERCENTAGE_RESOURCE` paraméterrel van konfigurálva.  A hatályos érték [itt](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values)van leírva.<br><br>A Sum aggregációs típust akkor használja, ha ez a metrika nem szűrt, és nincs szétosztva a rendszeren konfigurált teljes munkaterhelés-elkülönítés figyeléséhez.<br><br>A *tényleges minimális erőforrás-százalékos* érték határozza meg, hogy a munkaterhelés-csoport a garantált Egyidejűség (és így garantált átviteli sebesség) alsó határát is elérheti.  Ha további garantált erőforrásokra van szükség a *tényleges minimális erőforrás-százalékos* metrika által aktuálisan jelentettnél, növelje a munkaterhelés-csoporthoz konfigurált `MIN_PERCENTAGE_RESOURCE` paramétert.  A `REQUEST_MIN_RESOURCE_GRANT_PERCENT` csökkentése növelheti a párhuzamosságot, de nem növelheti a teljes átviteli sebességet. |Minimum, átlag, maximum|
|Munkaterhelési csoport aktív lekérdezései  |Ez a metrika a munkaterhelés csoporton belüli aktív lekérdezéseket jelenti.  Ha ezt a metrikát nem szűri, és a felosztatlan érték jelenik meg, akkor a rendszeren futó összes aktív lekérdezés megjelenik.|Összeg         |
|Munkaterhelési csoport lefoglalása az erőforrás maximális százaléka alapján |Ez a metrika az erőforrások százalékos kiosztását jeleníti meg a munkaterhelési csoport *tényleges maximális erőforrásához* viszonyítva.  Ez a mérőszám a munkaterhelés-csoport hatékony kihasználtságát biztosítja.<br><br>Vegyünk egy munkaterhelési csoportot a 75%-os effektív határérték- *erőforrással* `DataLoads` és egy `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 25%-os konfigurálva.  A *munkaterhelési csoport kiosztása maximális erőforrás-százalékos* értékkel, `DataLoads` 33% (25%/75%) Ha egyetlen lekérdezés futott ebben a munkaterhelés-csoportban.<br><br>Ezzel a metrikával azonosíthatók a munkaterhelés-csoportok kihasználtsága.  A 100%-ra közelítő érték azt jelzi, hogy a munkaterhelés-csoport számára elérhető összes erőforrás használatban van.  Emellett a *munkaterhelés-csoport várólistán lévő lekérdezési metrikája* ugyanarra a munkaterhelés-csoportra vonatkozóan, amely a nullánál nagyobb értéket mutat, azt jelzi, hogy a munkaterhelés-csoport további erőforrásokat fog használni, ha lefoglalt.  Ezzel szemben, ha ez a metrika konzisztensen alacsony, és a *munkaterhelési csoport aktív lekérdezései* alacsonyak, a munkaterhelés-csoport nincs használatban.  Ez a helyzet különösen akkor hasznos, ha az effektív határérték- *erőforrás százaléka* meghaladja a nullánál nagyobb értéket, mivel ez a számítási [feladatok elkülönítését](#underutilized-workload-isolation)jelzi.|Minimum, átlag, maximum |
|Munkaterhelés-csoport kiosztása rendszerszázalékkal | Ez a metrika az erőforrások százalékos kiosztását mutatja a teljes rendszerrel viszonyítva.<br><br>Vegye fontolóra egy munkaterhelés-csoport `DataLoads` egy 25%-ra konfigurált `REQUEST_MIN_RESOURCE_GRANT_PERCENT`.  A *munkaterhelési csoport kiosztása* a rendszerértékek szerint szűrve `DataLoads` 25% lesz (25%/100%) Ha egyetlen lekérdezés futott ebben a munkaterhelés-csoportban.|Minimum, átlag, maximum |
|Munkaterhelés-csoport lekérdezési időtúllépései |Túllépte az időkorlátot tartalmazó munkaterhelési csoport lekérdezéseit.  A metrika által jelentett lekérdezési időtúllépések csak a lekérdezés végrehajtásának megkezdése után (nem tartalmazza a várakozási időt a zárolás vagy az erőforrás-várakozások miatt).<br><br>A lekérdezés időkorlátja a `QUERY_EXECUTION_TIMEOUT_SEC` paraméterrel van konfigurálva a [munkaterhelés-csoport létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) szintaxisban.  Az érték növelésével csökkentheti a lekérdezési időtúllépések számát.<br><br>Vegye fontolóra a számítási feladatok csoport `REQUEST_MIN_RESOURCE_GRANT_PERCENT` paraméterének növelését, hogy csökkentse az időtúllépések mennyiségét, és több erőforrást foglaljon le lekérdezésként.  Vegye figyelembe, hogy a növekvő `REQUEST_MIN_RESOURCE_GRANT_PERCENT` csökkenti a munkaterhelés-csoport egyidejűségének mennyiségét. |Összeg |
|Munkaterhelési csoport várólistán lévő lekérdezések | Azon munkaterhelés-csoport lekérdezései, amelyek várólistára várnak a végrehajtás megkezdésére.  A lekérdezések várólistára helyezhetők, mert az erőforrásokra vagy zárolásokra várnak.<br><br>A lekérdezések több okból is várnak.  Ha a rendszer túl van terhelve, és az egyidejűségi igény nagyobb, mint ami elérhető, a lekérdezések várólistára kerülnek.<br><br>Ha további erőforrásokat szeretne hozzáadni a munkaterhelés-csoporthoz, növelje a `CAP_PERCENTAGE_RESOURCE` paramétert a [munkaterhelés-csoport létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) utasításban.  Ha `CAP_PERCENTAGE_RESOURCE` nagyobb, mint a *tényleges Cap-erőforrás százalékának* mérőszáma, akkor a számítási feladatok elkülönítése más munkaterhelési csoport számára is hatással van a munkaterhelési csoport számára lefoglalt erőforrásokra.  Vegye fontolóra a más munkaterhelés-csoportok `MIN_PERCENTAGE_RESOURCE`ének csökkentését vagy a példány vertikális felskálázását további erőforrások hozzáadásához. |Összeg |

## <a name="monitoring-scenarios-and-actions"></a>Figyelési forgatókönyvek és műveletek
Az alábbiakban számos diagram-konfigurációt talál, amelyekkel kiemelheti a számítási feladatok felügyeleti metrikájának használatát a hibaelhárításhoz és a probléma megoldásához szükséges kapcsolódó műveletekhez.

### <a name="underutilized-workload-isolation"></a>Kihasználatlan számítási feladatok elkülönítése
Vegye figyelembe a következő munkaterhelési csoportot és az osztályozó konfigurációt, ahol létrejön egy `wgPriority` nevű munkaterhelési csoport, és a *TheCEO* `membername` a `wcCEOPriority` munkaterhelés-osztályozó használatával van leképezve.  Az `wgPriority` munkaterhelési csoport számára 25%-os munkaterhelés-elkülönítés van konfigurálva (`MIN_PERCENTAGE_RESOURCE` = 25).  A *TheCEO* által küldött összes lekérdezés a rendszererőforrások 5%-át (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5) adja meg.
```sql
CREATE WORKLOAD GROUP wgPriority 
WITH ( MIN_PERCENTAGE_RESOURCE = 25   
      ,CAP_PERCENTAGE_RESOURCE = 50 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5); 

CREATE WORKLOAD CLASSIFIER wcCEOPriority 
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```
Az alábbi diagram a következőképpen van konfigurálva: metrika 1: *effektív minimális erőforrás-százalék* (AVG összesítés, `blue line`) metrika 2: *munkaterhelési csoport lefoglalása rendszerszázalékkal* (AVG összesítés, `purple line`) szűrő: [munkaterhelés-csoport] = `wgPriority`
![underutilized-WG. png](media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png) a diagram azt mutatja, hogy 25%-os munkaterhelés elkülönítése esetén a rendszer csak 10%-ot használ átlagosan  Ebben az esetben a `MIN_PERCENTAGE_RESOURCE` paraméter értéke 10 vagy 15 közötti értékre csökkenthető, és lehetővé teszi, hogy a rendszer más számítási feladatait használja az erőforrások felhasználásához.

### <a name="workload-group-bottleneck"></a>Munkaterhelés-csoport szűk keresztmetszete
Vegye figyelembe a következő munkaterhelési csoportot és az osztályozó konfigurációt, ahol létrejön egy `wgDataAnalyst` nevű munkaterhelési csoport, és a *DataAnalyst* `membername` az `wcDataAnalyst` munkaterhelés-osztályozó használatával van leképezve.  Az `wgDataAnalyst` munkaterhelés-csoport 6%-os munkaterhelés-elkülönítést (`MIN_PERCENTAGE_RESOURCE` = 6) és egy 9%-os erőforrás-korlátot (`CAP_PERCENTAGE_RESOURCE` = 9) tartalmaz.  A *DataAnalyst* által küldött összes lekérdezés a rendszererőforrások 3%-át (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3) adja meg.

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6   
      ,CAP_PERCENTAGE_RESOURCE = 9 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3); 

CREATE WORKLOAD CLASSIFIER wcDataAnalyst 
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```
Az alábbi diagram a következőképpen van konfigurálva: metrika 1: *effektív korlát erőforrásának százalékos aránya* (AVG összesítés, `blue line`) metrika 2: *munkaterhelési csoport lefoglalása maximális erőforrás-százalékos* értékkel (AVG összesítés, `purple line`) metrika 3: *munkaterhelési csoport várólistán lévő lekérdezések* (Sum összesítés, `turquoise line`) szűrő: [munkaterhelés-csoport] = `wgDataAnalyst`
![palack-nyakú-WG](media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png) a diagram azt mutatja, hogy az erőforrásokra vonatkozó 9%-os korláttal a munkaterhelés csoport 90% + kihasználtsága * erőforrás százalékának mérőszáma*).  A lekérdezések állandó sorba állítása a *munkaterhelés-csoport várólistán lévő lekérdezések metrikájában*látható.  Ebben az esetben a `CAP_PERCENTAGE_RESOURCE` 9%-nál magasabb értékre való növelése lehetővé teszi, hogy a több lekérdezés egyidejűleg legyen végrehajtva.  A `CAP_PERCENTAGE_RESOURCE` növelése feltételezi, hogy elegendő erőforrás áll rendelkezésre, és más munkaterhelés-csoportok nem elkülönítettek.  Győződjön meg róla, hogy a sapka megnőtt a *tényleges Cap-erőforrás százalékos metrikájának*ellenőrzésével.  Ha további átviteli sebességre van szükség, vegye fontolóra a `REQUEST_MIN_RESOURCE_GRANT_PERCENT` növelését 3-nál nagyobb értékre.  A `REQUEST_MIN_RESOURCE_GRANT_PERCENT` növelése lehetővé teheti a lekérdezések gyorsabb futtatását.

## <a name="next-steps"></a>Következő lépések
[Rövid útmutató: a munkaterhelés elkülönítésének konfigurálása T-SQL használatával](quickstart-configure-workload-isolation-tsql.md)<br>
[MUNKATERHELÉS-csoport létrehozása (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)<br>
[Számítási feladatok BESOROLÁSának létrehozása (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)<br>
[Erőforrás-használat figyelése](sql-data-warehouse-concept-resource-utilization-query-activity.md)

