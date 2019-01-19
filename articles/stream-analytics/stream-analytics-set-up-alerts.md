---
title: Állítsa be a riasztásokat az Azure Stream Analytics-feladatok figyelése
description: Ez a cikk ismerteti, hogyan állítsa be a figyelést és riasztásokat az Azure Stream Analytics-feladatok az Azure portal használatával.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/19/2019
ms.custom: seodec18
ms.openlocfilehash: 4c0d32a201da5befbc8b68148f0b051e283ec289
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2019
ms.locfileid: "54412389"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Állítsa be a riasztásokat az Azure Stream Analytics-feladatok

Fontos figyelése az Azure Stream Analytics-feladat annak érdekében, hogy a feladat folyamatosan futó gond nélkül. Ez a cikk ismerteti a gyakori szituációhoz kínál olyan figyelendő riasztásokat állíthat be. 

Szabályok a metrikák a portálon keresztül is beállítható, és konfigurálhatók [programozott módon](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) műveletnaplók adatokon.

## <a name="set-up-alerts-in-the-azure-portal"></a>Az Azure Portal értesítések beállítása

A következő példa bemutatja, hogyan állítható be a riasztások, a feladat sikertelen állapotba kerül, ha. Ez a riasztás javasolt az összes feladat.

1. Az Azure Portalon nyissa meg a Stream Analytics-feladat szeretne riasztást létrehozni.

2. Az a **feladat** lapon, keresse meg a **figyelés** szakaszban.  

3. Válassza ki **metrikák**, és kattintson a **Új riasztási szabály**.

   ![Az Azure portal Stream Analytics riasztások beállítása](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. A Stream Analytics-feladat neve automatikusan meg kell jelennie a **erőforrás**. Kattintson a **feltétel hozzáadása**, és válassza ki **összes felügyeleti műveletet** alatt **jellogika konfigurálása**.

   ![Válassza ki a Stream Analytics-riasztásból jel neve](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. Alatt **jellogika konfigurálása**, módosítsa **Eseményszint** való **összes** , és módosítsa **állapota** való **sikertelen** . Hagyja **esemény kezdeményezője** üres, és kattintson a **kész**.

   ![A Stream Analytics-riasztásból jellogika konfigurálása](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. Válasszon ki egy meglévő műveletet, vagy hozzon létre egy új csoportot. Ebben a példában egy új műveletcsoportot nevű **TIDashboardGroupActions** hozták- **e-mailek** műveletet, amelyet a felhasználóknak egy e-mailt küld a **tulajdonosa** Azure Resource Szerepkör.

   ![Az Azure Stream Analytics-feladat riasztás beállítása](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. A **erőforrás**, **feltétel**, és **MŰVELETCSOPORTOK** kell minden egyes egy bejegyzést.

   ![Stream Analytics riasztási szabály létrehozása](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   Adjon hozzá egy **riasztási szabály neve**, **leírása**, és a **erőforráscsoport** , a **riasztás részletei** , és kattintson a **létrehozása riasztás a szabály** a szabály a Stream Analytics-feladat létrehozásához.

   ![Stream Analytics riasztási szabály létrehozása](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)

## <a name="scenarios-to-monitor"></a>Forgatókönyvek figyelése

Az alábbi riasztások használata akkor javasolt, a Stream Analytics-feladatok teljesítményének monitorozásához. Ezeket a metrikákat a legutóbbi 5 perces időszak alatt percenként kell kiértékelni. Ha a feladat teljesítményproblémák szenved, lekérdezés párhuzamos feldolgozás segítségével győződjön meg arról, hogy több optimális, és próbálja meg növelni a streamelési egységek számát.

|Metrika|Állapot|Idő összesítése|Küszöbérték|Javítási műveleteket|
|-|-|-|-|-|
|SU százalékos kihasználtsága|Nagyobb, mint|Maximum|80|Nincsenek számos tényező, melyek növelik a SU százalékos kihasználtság. Skálázás párhuzamos lekérdezési folyamatokkal, vagy növelje a folyamatos átviteli egységek számát. További információért lásd [az Azure Stream Analytics-lekérdezések párhozamosításának előnyeit ismertető](stream-analytics-parallelization.md) cikket.|
|A futásidejű hibák|Nagyobb, mint|Összes|0|Vizsgálja meg a tevékenység vagy a diagnosztikai naplók és a megfelelő módosításokat a bemenetek, lekérdezési vagy kimenetek.|
|Vízjel késleltetés|Nagyobb, mint|Maximum|Amikor átlagos Ez a metrika az elmúlt 15 percben értéke nagyobb, mint a késedelmes érkezési tolerancia (másodpercben). Ha nem módosította a késedelmes érkezési tolerancia, az alapértelmezett érték 5 másodperc.|Próbálja meg SUs számának növelése vagy párhuzamosan futtatni a lekérdezést. SUS-t a további információkért lásd: [ismertetése és módosítása a folyamatos átviteli egységek](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job). További információ a párhuzamosan futtatni a lekérdezést: [emelés lekérdezés párhuzamos feldolgozás, az Azure Stream Analyticsben](stream-analytics-parallelization.md).|
|A deszerializálás bemeneti hibái|Nagyobb, mint|Összes|0|Vizsgálja meg a tevékenység vagy a diagnosztikai naplók, valamint a bemeneti megfelelő módosítására. További információ a diagnosztikai naplók: [hibaelhárítása az Azure Stream Analytics használatával a diagnosztikai naplók](stream-analytics-job-diagnostic-logs.md)|

## <a name="get-help"></a>Segítségkérés

Riasztások konfigurálása az Azure Portalon a további részletekért lásd: [riasztási értesítések fogadása](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  

További segítségre van szüksége, próbálja meg [Azure Stream Analytics-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-get-started.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

