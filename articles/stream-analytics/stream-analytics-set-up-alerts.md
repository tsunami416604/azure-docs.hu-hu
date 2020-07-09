---
title: Figyelési riasztások beállítása Azure Stream Analytics feladatokhoz
description: Ez a cikk azt ismerteti, hogyan használható a Azure Portal az Azure Stream Analytics feladatok figyelésének és riasztásának beállításához.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.openlocfilehash: d701a518dfa0bd21b33aa2f13f533ad6ef49430d
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037817"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Riasztások beállítása Azure Stream Analytics feladatokhoz

Fontos, hogy figyelemmel kísérje a Azure Stream Analytics-feladatot, hogy a feladatok folyamatosan, problémák nélkül fussanak. Ez a cikk azt ismerteti, hogyan állíthat be riasztásokat a figyelni kívánt gyakori forgatókönyvekhez. 

A műveleti naplók adatai a portálon keresztül, valamint [programozott](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a)módon is meghatározhatók.

## <a name="set-up-alerts-in-the-azure-portal"></a>Riasztások beállítása a Azure Portalban
### <a name="get-alerted-when-a-job-stops-unexpectedly"></a>Riasztást kap, ha a feladatok váratlanul leállnak

Az alábbi példa bemutatja, hogyan állíthatja be a riasztásokat, amikor a feladatainak sikertelen állapotba kerülnek. Ez a riasztás minden feladathoz ajánlott.

1. A Azure Portal nyissa meg azt a Stream Analytics feladatot, amelyhez riasztást szeretne létrehozni.

2. A **feladatok** lapon navigáljon a **figyelés** szakaszhoz.  

3. Válassza a **metrikák**, majd az **új riasztási szabály**lehetőséget.

   ![Azure Portal Stream Analytics riasztások beállítása](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. A Stream Analytics feladatának neve automatikusan megjelenik az **erőforrás**területen. Kattintson a **feltétel hozzáadása**elemre, majd válassza az **összes felügyeleti műveletet** a **jel logikájának konfigurálása**területen.

   ![Adja meg a Stream Analytics riasztáshoz tartozó jel nevét](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. A **jel logikájának konfigurálása**területen az **események szintje** az **összes** értékre változik, és a változás **állapota** **sikertelen**értékre vált. Hagyja üresen **az eseményt** , és válassza a **kész**lehetőséget.

   ![Stream Analytics riasztáshoz tartozó jel logikájának konfigurálása](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. Válasszon ki egy meglévő műveleti csoportot, vagy hozzon létre egy új csoportot. Ebben a példában egy **TIDashboardGroupActions** nevű új műveleti csoport jött létre egy **e-mail** -művelettel, amely e-mailt küld a felhasználóknak a **tulajdonos** Azure Resource Manager szerepkörrel.

   ![Riasztás beállítása Azure streaming Analytics-feladatokhoz](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. Az **erőforrásnak**, a **feltételnek**és a **műveleti csoportnak** szerepelnie kell egy bejegyzésben. Vegye figyelembe, hogy ahhoz, hogy a riasztások tüzet, a meghatározott feltételeknek teljesülnie kell. Mérheti például egy metrika átlagos értékét az elmúlt 15 percben 5 percenként.

   ![Stream Analytics riasztási szabály létrehozása](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   Adja hozzá a riasztási **szabály nevét**, **leírását**és az **erőforráscsoportot** a **riasztás részleteihez** , majd kattintson a **riasztási szabály létrehozása** elemre a stream Analyticsi feladathoz tartozó szabály létrehozásához.

   ![Stream Analytics riasztási szabály létrehozása](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>Figyelési forgatókönyvek

A következő riasztások ajánlottak a Stream Analyticsi feladatok teljesítményének figyeléséhez. Ezeket a metrikákat percenként kell kiértékelni az elmúlt 5 perces időszakban.

|Metrika|Állapot|Idő összesítése|Küszöbérték|Javítási műveletek|
|-|-|-|-|-|
|SU% kihasználtsága|Nagyobb, mint|Maximum|80|Több tényező is megnövelheti a SU%-os kihasználtságot. A lekérdezési párhuzamos méretezéssel vagy a folyamatos átviteli egységek számának növelésével bővíthető. További információért lásd [az Azure Stream Analytics-lekérdezések párhozamosításának előnyeit ismertető](stream-analytics-parallelization.md) cikket.|
|Futásidejű hibák|Nagyobb, mint|Összesen|0|Vizsgálja meg a tevékenység-vagy erőforrás-naplókat, és végezze el a megfelelő módosításokat a bemeneteken, lekérdezéseken vagy kimeneteken.|
|Vízjel késleltetése|Nagyobb, mint|Maximum|Ha a metrika átlagos értéke az elmúlt 15 percben meghaladja a késői érkezési toleranciát (másodpercben). Ha nem módosította a késői érkezési toleranciát, az alapértelmezett érték 5 másodperc.|Próbálja meg növelni a lekérdezés SUs-vagy tetszés számát. További információ az SUs szolgáltatásról: a [folyamatos átviteli egységek ismertetése és módosítása](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job). A lekérdezés tetszés kapcsolatos további információkért lásd: [a lekérdezési párhuzamos kihasználása a Azure stream Analyticsban](stream-analytics-parallelization.md).|
|Bemeneti deszerializálási hibák|Nagyobb, mint|Összesen|0|Vizsgálja meg a tevékenység vagy az erőforrás naplóit, és végezze el a megfelelő módosításokat a bemeneten. Az erőforrás-naplókkal kapcsolatos további információkért lásd: [Azure stream Analytics erőforrás-naplók használatával kapcsolatos hibák megoldása](stream-analytics-job-diagnostic-logs.md)|

## <a name="get-help"></a>Segítség kérése

A Azure Portal riasztások konfigurálásával kapcsolatos további részletekért lásd: [Riasztási értesítések fogadása](../azure-monitor/platform/alerts-overview.md).  

További segítségért próbálja ki a [Microsoft Q&a Azure stream Analytics kérdéseit](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>További lépések
* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-get-started.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

