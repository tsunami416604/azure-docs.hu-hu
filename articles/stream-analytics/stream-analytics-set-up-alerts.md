---
title: Figyelési riasztások beállítása az Azure Stream Analytics-feladatokhoz
description: Ez a cikk bemutatja, hogyan használhatja az Azure Portalon az Azure Stream Analytics-feladatok figyelésének és riasztásának beállításához.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 836b7a489e3c73d745b128cbbc0c3566220ac409
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458736"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Riasztások beállítása az Azure Stream Analytics-feladatokhoz

Fontos, hogy figyelje az Azure Stream Analytics-feladatot, hogy a feladat folyamatosan fusson gond nélkül. Ez a cikk ismerteti, hogyan állíthat be riasztásokat a gyakori forgatókönyvek, amelyeket figyelni kell. 

A portálon keresztül, valamint [programozott módon](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a)határozhatja meg a metrikákra vonatkozó szabályokat a műveleti naplók adataiból.

## <a name="set-up-alerts-in-the-azure-portal"></a>Riasztások beállítása az Azure Portalon
### <a name="get-alerted-when-a-job-stops-unexpectedly"></a>Figyelmeztetés, ha egy feladat váratlanul leáll

A következő példa bemutatja, hogyan állíthat be riasztásokat, amikor a feladat sikertelen állapotba kerül. Ez a riasztás minden feladathoz ajánlott.

1. Az Azure Portalon nyissa meg azt a Stream Analytics-feladatot, amelyhez riasztást szeretne létrehozni.

2. A **Feladat** lapon keresse meg a **Figyelés** szakaszt.  

3. Válassza **a Metrikák**lehetőséget, majd **az Új riasztási szabályt.**

   ![Az Azure Portal Stream Analytics riasztáseinek beállítása](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. A Stream Analytics-feladat nevének automatikusan meg kell jelennie a **RESOURCE**alatt. Kattintson **a Feltétel hozzáadása gombra,** és a **Jellogika konfigurálása**csoportban válassza a **Minden felügyeleti művelet** lehetőséget.

   ![A Stream Analytics-riasztás jelnevének kiválasztása](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. A **Jellogika konfigurálása**csoportban módosítsa **az Eseményszintet** **Mind-re,** és módosítsa **az Állapot** beállítást **Sikertelen**állapotra. Hagyja üresen **az Esemény által kezdeményezett et,** és válassza a Kész **gombot.**

   ![A Stream Analytics riasztás jellogikájának konfigurálása](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. Jelöljön ki egy meglévő műveletcsoportot, vagy hozzon létre egy új csoportot. Ebben a példában egy új műveletcsoport nevű **TIDashboardGroupActions** jött létre egy **e-mail** művelet, amely e-mailt küld a felhasználóknak a **tulajdonos** I. Erőforrás-kezelő szerepkörrel.

   ![Riasztás beállítása egy Azure Streaming Analytics-feladathoz](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. Az **ERŐFORRÁS**, **FELTÉTEL**és **MŰVELETCSOPORTOK** mindegyikének rendelkeznie kell egy bejegyzéssel. Ne feledje, hogy ahhoz, hogy a riasztások tűz, a meghatározott feltételeknek kell teljesülniük. Mérheti például egy metrika átlagos értékét az elmúlt 15 percben 5 percenként.

   ![Stream Analytics riasztási szabály létrehozása](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   Adja hozzá **a riasztási szabály nevét**, **leírás**és az **erőforráscsoport** az **ALERT details** és kattintson a **Riasztási szabály létrehozása** a szabályt a Stream Analytics feladathoz.

   ![Stream Analytics riasztási szabály létrehozása](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>A figyelni tévő forgatókönyvek

A következő riasztások ajánlott a Stream Analytics-feladat teljesítményének figyeléséhez. Ezeket a mutatókat minden percben ki kell értékelni az elmúlt 5 perces időszakban.

|Metrika|Állapot|Idő összesítése|Küszöbérték|Korrekciós intézkedések|
|-|-|-|-|-|
|SU%-os kihasználtság|Nagyobb, mint|Maximum|80|Több tényező, amely növeli a SU%kihasználtság. A lekérdezés párhuzamosításával skálázható, vagy növelheti a streamelési egységek számát. További információért lásd [az Azure Stream Analytics-lekérdezések párhozamosításának előnyeit ismertető](stream-analytics-parallelization.md) cikket.|
|Futásidejű hibák|Nagyobb, mint|Összesen|0|Vizsgálja meg a tevékenység- vagy diagnosztikai naplókat, és hajtsa végre a bemenetek, lekérdezések vagy kimenetek megfelelő módosításait.|
|Vízjel késleltetése|Nagyobb, mint|Maximum|Ha ennek a metrikának az átlagos értéke az elmúlt 15 percben nagyobb, mint a késői érkezési tolerancia (másodpercben). Ha nem módosította a késői érkezési tűréshatárt, az alapértelmezett érték 5 másodpercre van állítva.|Próbálja meg növelni az SUs-ok számát, vagy párhuzamosítsa a lekérdezést. A SUs-okkal kapcsolatos további információkért [lásd: A streamelési egységek megértése és módosítása.](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job) A lekérdezés párhuzamosításáról az [Azure Stream Analytics lekérdezés-párhuzamosításának kihasználása](stream-analytics-parallelization.md)című témakörben talál további információt.|
|Bemeneti deszerializálási hibák|Nagyobb, mint|Összesen|0|Vizsgálja meg a tevékenység- vagy diagnosztikai naplókat, és hajtsa végre a megfelelő módosításokat a bemeneten. A diagnosztikai naplókról az [Azure Stream Analytics hibaelhárítása diagnosztikai naplók használatával című témakörben](stream-analytics-job-diagnostic-logs.md) talál további információt.|

## <a name="get-help"></a>Segítségkérés

A riasztások Azure Portalon történő konfigurálásával kapcsolatos további részletekért olvassa el a [Riasztási értesítések fogadása című témakört.](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)  

További segítségért próbálja ki [az Azure Stream Analytics fórumunkat.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-get-started.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

