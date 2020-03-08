---
title: Azure Storage-fiók figyelése a Azure Portalban | Microsoft Docs
description: Megtudhatja, hogyan figyelheti a Storage-fiókot az Azure-ban a Azure Portal használatával.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 43955950b2fc0544b149c67e3f8df7ca905fafed
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361035"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Storage-fiók figyelése a Azure Portal

A [Azure Storage Analytics](storage-analytics.md) metrikákat biztosít az összes tárolási szolgáltatáshoz, valamint a Blobok, várólisták és táblák naplóihoz. A [Azure Portal](https://portal.azure.com) használatával beállíthatja, hogy mely mérőszámok és naplók legyenek rögzítve a fiókjához, és hogyan konfigurálhat olyan diagramokat, amelyek vizuális ábrázolást biztosítanak a metrikák adataihoz. 

Javasoljuk, hogy tekintse át [Azure monitor a Storage szolgáltatáshoz](../../azure-monitor/insights/storage-insights-overview.md) (előzetes verzió). Ez az Azure Monitor szolgáltatása, amely átfogóan figyeli az Azure Storage-fiókokat az Azure Storage-szolgáltatások teljesítményének, kapacitásának és rendelkezésre állásának egységes áttekintésével. Nem igényli, hogy bármit engedélyezzen vagy konfiguráljan, és azonnal megtekintheti ezeket a metrikákat az előre definiált interaktív diagramokon és egyéb vizualizációkban is.

> [!NOTE]
> A Azure Portal figyelési adataival kapcsolatos költségek vannak kivizsgálva. További információ: [Storage Analytics](storage-analytics.md).
>
> Azure Files jelenleg támogatja Storage Analytics metrikákat, de még nem támogatja a naplózást.
>
> A prémium szintű teljesítmény-blokkoló blob Storage-fiókok nem támogatják a Storage analitikai metrikáit, de támogatják a naplózást. Az REST API vagy az ügyféloldali kódtár segítségével programozott módon engedélyezheti a naplózást. Ha a prémium szintű blob blob Storage-fiókokkal rendelkező mérőszámokat szeretné megtekinteni, érdemes lehet [Az Azure Storage-metrikákat használni a Azure monitorban](storage-metrics-in-azure-monitor.md).
>
> Az Azure Storage szolgáltatással kapcsolatos problémák azonosítására, diagnosztizálására és hibaelhárítására vonatkozó részletes útmutató a Storage Analytics és egyéb eszközök használatáról: [Microsoft Azure Storage figyelése, diagnosztizálása és hibaelhárítása](storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>Storage-fiók figyelésének konfigurálása

1. A [Azure Portal](https://portal.azure.com)válassza ki a **Storage-fiókok**elemet, majd a Storage-fiók nevét a fiók irányítópultjának megnyitásához.
1. Válassza a **diagnosztika** lehetőséget a menü panel **figyelés** szakaszában.

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Válassza ki **a figyelni** kívánt **szolgáltatás** metrikájának adatait, valamint az adatok **megőrzési szabályát** . A figyelést le is tilthatja, ha az **állapot** beállítás **ki**értékre van állítva.

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   Az adatmegőrzési szabály beállításához helyezze át a **megőrzés (nap)** csúszkát, vagy adja meg a megőrizni kívánt adatnapok számát 1 és 365 között. Az új Storage-fiókok alapértelmezett értéke hét nap. Ha nem szeretne adatmegőrzési szabályt beállítani, adja meg a nulla értéket. Ha nincs adatmegőrzési szabály, akkor a figyelési adatok törlése is megtörténik.

   > [!WARNING]
   > A metrikai adatok manuális törlése után számítunk fel díjat. A rendszer díjmentesen törli az elavult elemzési adatok (az adatmegőrzési szabályzatnál régebbi adatok) adatait. Azt javasoljuk, hogy állítsa be az adatmegőrzési szabályzatot azon alapul, hogy mennyi ideig szeretné megőrizni a Storage Analytics-adatait a fiókjához. További információért lásd a [Storage-metrikák számlázása](storage-analytics-metrics.md#billing-on-storage-metrics) című témakört.
   >

1. A figyelési konfiguráció befejezése után válassza a **Mentés**lehetőséget.

A metrikák alapértelmezett készlete a Storage-fiók panelen, valamint az egyes szolgáltatásokhoz tartozó (blob, üzenetsor, tábla és fájl) diagramokon jelenik meg. Miután engedélyezte a metrikákat a szolgáltatásokhoz, akár egy óráig is eltarthat, amíg az adatok megjelennek a diagramokban. Bármelyik metrikai diagramon kiválaszthatja a **Szerkesztés** lehetőséget a diagramon megjelenítendő mérőszámok konfigurálásához.

A metrikák gyűjtését és naplózását letilthatja, ha az **állapot** beállítás **ki**értékre van állítva.

> [!NOTE]
> Az Azure Storage a [Table Storage](storage-introduction.md#table-storage) használatával tárolja a Storage-fiók metrikáit, és a fiókban lévő táblákban tárolja a metrikákat. További információ: . [A metrikák tárolása](storage-analytics-metrics.md#how-metrics-are-stored)
>

## <a name="customize-metrics-charts"></a>Metrikák diagramjainak testreszabása

A következő eljárással kiválaszthatja, hogy mely tárolási metrikákat szeretné megtekinteni egy mérőszámok diagramján.

1. Először jelenítse meg a tárolási metrika diagramot a Azure Portal. Az egyes szolgáltatásokhoz (blob, üzenetsor, tábla, fájl) tartozó diagramok a **Storage-fiók** panelen és a **metrikák** panelen találhatók.

   Ebben a példában a következő, a **Storage-fiók**panelen megjelenő diagramot használja:

   ![Diagram kijelölése Azure Portal](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. A diagram szerkesztéséhez kattintson bárhová a diagramon belül.

1. Ezután válassza ki a diagramon megjelenítendő mérőszámok **időtartományát** , valamint azt a **szolgáltatást** (blob, üzenetsor, tábla, fájl), amelynek metrikáit meg szeretné megjeleníteni. Itt az előző heti mérőszámok vannak kiválasztva a blob szolgáltatás megjelenítéséhez:

   ![Időtartomány és szolgáltatás kiválasztása a diagram szerkesztése panelen](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. Válassza ki a diagramon megjeleníteni kívánt egyéni **metrikákat** , majd kattintson az **OK**gombra.

   ![Egyéni metrika kiválasztása a diagram szerkesztése panelen](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

A diagram beállításai nem érintik a tárolási fiókban lévő figyelési adatgyűjtést, összesítést vagy tárolást.

### <a name="metrics-availability-in-charts"></a>Metrikák rendelkezésre állása a diagramokon

Az elérhető mérőszámok listája azon alapul, hogy a legördülő listában kiválasztott szolgáltatás és a szerkesztett diagram egység típusa alapján változik-e meg. Kiválaszthatja például, hogy a százalékos mérőszámok, például a *percentnetworkerror értéket mutatnak* és a *percentthrottlingerror értéket mutatnak* csak akkor legyenek, ha olyan diagramot szerkeszt, amely százalékban jeleníti meg az egységeket:

![Lekérési hiba százalékos diagramja a Azure Portal](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Metrikák feloldása

A **diagnosztika** kiválasztott mérőszámai határozzák meg a fiókhoz elérhető metrikák feloldását:

* Az **összesített** figyelés olyan mérőszámokat biztosít, mint a bejövő/kimenő forgalom, a rendelkezésre állás, a késés és a sikerességi arány. Ezek a metrikák a blob-, tábla-, fájl-és üzenetsor-szolgáltatásokból vannak összesítve.
* Az API-k **egy** finomabb felbontást biztosítanak, és a szolgáltatási szint összesítései mellett egyéni tárolási műveletekhez elérhető metrikák is rendelkezésre állnak.

## <a name="configure-metrics-alerts"></a>Metrikai riasztások konfigurálása

Riasztásokat hozhat létre, amelyekkel értesítést kaphat, ha elérte a tárolási erőforrás metrikáinak küszöbértékeit.

1. A **riasztási szabályok**panel megnyitásához görgessen le a **figyelés** szakaszhoz a **menü** panelen, és válassza a **riasztások (klasszikus)** lehetőséget.
2. A **riasztási szabály hozzáadása** panel megnyitásához válassza a **metrikus riasztás hozzáadása (klasszikus) lehetőséget.**
3. Adja meg az új riasztási szabály **nevét** és **leírását** .
4. Válassza ki azt a **metrikát** , amelynek hozzá szeretne adni egy riasztást, egy riasztási **feltételt**és egy **küszöbértéket**. A küszöbérték-egység típusa a választott mérőszámtól függően változik. Például a "Count" a *ContainerCount*egység típusa, míg a *percentnetworkerror értéket mutatnak* metrika egysége százalék.
5. Válassza ki az **időszakot**. A riasztást kiváltó időszakon belüli küszöbértéket elérő vagy annál nagyobb mérőszámok.
6. Választható **E-mailek** és **webhookok** értesítéseinek konfigurálása. A webhookokkal kapcsolatos további információkért lásd: [webhook konfigurálása Azure metrikai riasztáshoz](../../azure-monitor/platform/alerts-webhooks.md). Ha nem konfigurálja az e-mail-vagy webhook-értesítéseket, a riasztások csak a Azure Portal fognak megjelenni.

!["Riasztási szabály hozzáadása" panel a Azure Portal](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Metrikai diagramok hozzáadása a portál irányítópulthoz

Bármelyik Storage-fiókhoz hozzáadhat Azure Storage metrikai diagramokat a portál irányítópultján.

1. Válassza az **irányítópult szerkesztése** lehetőséget az irányítópult megtekintésekor a [Azure Portal](https://portal.azure.com).
1. A csempe- **gyűjteményben**válassza a **csempék keresése** > **típus**alapján lehetőséget.
1. Válassza a **típus** > **Storage-fiókok**lehetőséget.
1. Az **erőforrások**területen válassza ki azt a Storage-fiókot, amelynek metrikáit hozzá szeretné adni az irányítópulthoz.
1. Válassza ki a **kategóriákat** > a **figyelés**lehetőséget.
1. Húzza a diagram csempét az Irányítópultra a megjelenő mérőszámhoz. Ismételje meg az összes olyan mérőszámot, amelyet meg szeretne jeleníteni az irányítópulton. A következő ábrán a "Blobok – összes kérelem" diagram ki van emelve példaként, de az irányítópulton az összes diagram elérhetővé válik.

   ![Csempe-gyűjtemény Azure Portal](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. Ha elkészült a diagramok hozzáadásával, válassza az irányítópult tetején található **Testreszabás kész** lehetőséget.

Miután hozzáadta a diagramokat az irányítópulthoz, tovább testreszabhatja azokat a következő témakörben leírtak szerint: metrika-diagramok testreszabása.

## <a name="configure-logging"></a>Naplózás konfigurálása

A blob-, tábla-és üzenetsor-szolgáltatásokhoz tartozó olvasási, írási és törlési kérelmek esetében az Azure Storage-ba is utasíthatja a diagnosztikai naplók mentését. A beállított adatmegőrzési szabályzat ezekre a naplókra is érvényes.

> [!NOTE]
> Azure Files jelenleg támogatja Storage Analytics metrikákat, de még nem támogatja a naplózást.
>

1. A [Azure Portal](https://portal.azure.com)válassza ki a Storage- **fiókok**elemet, majd a Storage-fiók nevét a Storage-fiók panel megnyitásához.
1. Válassza a **diagnosztikai beállítások (klasszikus)** lehetőséget a menü panel **monitorozás (klasszikus)** szakaszában.

    ![Diagnosztika menüpont a Azure Portal figyelés területén.](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Győződjön **meg**arról, hogy az **állapot** beállítás be értékre van állítva, majd válassza ki azokat a **szolgáltatásokat** , amelyeknek engedélyezni szeretné a naplózást.

    ![Konfigurálja a naplózást a Azure Portalban.](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. Kattintson a **Save** (Mentés) gombra.

A diagnosztikai naplókat a rendszer egy *$logs* nevű blob-tárolóba menti a Storage-fiókjában. A naplófájlokat a [Microsoft Storage Explorerhoz](https://storageexplorer.com)hasonló Storage Explorerrel, vagy programozott módon, a Storage ügyféloldali kódtár vagy a PowerShell használatával tekintheti meg.

További információ a $logs tároló eléréséről: [Storage Analytics naplózása](storage-analytics-logging.md).

## <a name="next-steps"></a>Következő lépések

* További információ a Storage Analytics [metrikákkal, naplózással és számlázással](storage-analytics.md) kapcsolatban.
