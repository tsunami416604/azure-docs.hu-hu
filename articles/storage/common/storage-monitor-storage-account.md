---
title: Azure Storage-fiók figyelése az Azure Portalon | Microsoft dokumentumok
description: Megtudhatja, hogyan figyelheti a tárfiókot az Azure-portálhasználatával.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 43955950b2fc0544b149c67e3f8df7ca905fafed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268287"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Tárfiók monitorozása az Azure Portalon

[Az Azure Storage Analytics](storage-analytics.md) metrikákat biztosít az összes tárolási szolgáltatáshoz, valamint a blobok, várólisták és táblák naplóihoz. Az Azure [Portal](https://portal.azure.com) segítségével konfigurálhatja, hogy mely metrikák és naplók kerülnek rögzítésre a fiókjához, és konfigurálhatja a diagramokat, amelyek vizuális ábrázolása a metrikák adatait. 

Azt javasoljuk, hogy tekintse át [az Azure Monitor tárhely (előzetes](../../azure-monitor/insights/storage-insights-overview.md) verzió). Az Azure Monitor szolgáltatása az Azure Storage-fiókok átfogó figyelését kínálja az Azure Storage-szolgáltatások teljesítményének, kapacitásának és rendelkezésre állásának egységes nézetével. Nem igényel semmit engedélyezni vagy konfigurálni, és ezeket a mutatókat azonnal megtekintheti az előre definiált interaktív diagramokból és más képi megjelenítésekből.

> [!NOTE]
> Az Azure Portalon a figyelési adatok vizsgálata költségekkel jár. További információ: [Storage Analytics](storage-analytics.md).
>
> Az Azure Files jelenleg támogatja a Storage Analytics metrikák, de még nem támogatja a naplózást.
>
> Prémium szintű teljesítményblokk blob storage-fiókok nem támogatják a storage analitikus metrikák, de ők támogatják a naplózást. A naplózást programozott módon engedélyezheti a REST API-n vagy az ügyfélkódtáron keresztül. Ha prémium szintű teljesítményblob-blobstorage-fiókokkal rendelkező metrikákat szeretne megtekinteni, fontolja meg [az Azure Storage-metrikák használatát az Azure Monitorban.](storage-metrics-in-azure-monitor.md)
>
> A Storage Analytics és más eszközök használatával az Azure Storage szolgáltatással kapcsolatos problémák azonosítására, diagnosztizálására és elhárítására vonatkozó részletes útmutatót a [Microsoft Azure Storage figyelése, diagnosztizálása és hibaelhárítása](storage-monitoring-diagnosing-troubleshooting.md)című témakörben talál.
>

## <a name="configure-monitoring-for-a-storage-account"></a>Tárfiók figyelésének konfigurálása

1. Az [Azure Portalon](https://portal.azure.com)válassza **a Tárfiókok**lehetőséget, majd a tárfiók nevét a fiók irányítópultjának megnyitásához.
1. Válassza **a Diagnosztika** lehetőséget a menüpanel **FIGYELVE** szakaszában.

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Válassza ki **a** metrikák adatait az egyes **szolgáltatás,** amelyet figyelni szeretne, és az **adatmegőrzési szabályzat.** A figyelést úgy is letilthatja, hogy **az Állapot** beállítást **Ki**állásra állítja.

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   Az adatmegőrzési szabály beállításához helyezze át az **Adatmegőrzési (nap)** csúszkát, vagy adja meg a megőrzési napok számát 1-ről 365-re. Az új tárfiókok alapértelmezett beállítása hét nap. Ha nem szeretne adatmegőrzési szabályt beállítani, írja be a nullát. Ha nincs adatmegőrzési szabály, a figyelési adatok törlése.

   > [!WARNING]
   > A mérőszámok adatainak manuális törlésekor díjat számítunk fel. Elavult elemzési adatok (az adatmegőrzési házirendnél régebbi adatok) a rendszer díjmentesen törlődik. Azt javasoljuk, hogy az adatmegőrzési szabályzat beállítása alapján, hogy mennyi ideig szeretné megőrizni a tárolási elemzési adatokat a fiókjához. További információkért [lásd: Számlázás a tárolási mutatók.](storage-analytics-metrics.md#billing-on-storage-metrics)
   >

1. Amikor befejezte a figyelési konfigurációt, válassza a **Mentés lehetőséget.**

Az alapértelmezett metrikák jelennek meg a diagramok on the storage account blade, valamint az egyes szolgáltatás blades (blob, várólista, tábla és fájl). Miután engedélyezte a mutatókat egy szolgáltatáshoz, akár egy órát is igénybe vehet, amíg az adatok megjelennek a diagramokon. Bármely mérőszámdiagram **szerkesztése** lehetőséget választva beállíthatja, hogy mely mérőszámok jelenjenek meg a diagramon.

A metrikák gyűjtését és naplózását az **Állapot** beállítás kikapcsolva beállításával tilthatja **le.**

> [!NOTE]
> Az Azure Storage [táblatároló](storage-introduction.md#table-storage) használatával tárolja a tárfiók metrikákat, és tárolja a metrikákat a fiókjában lévő táblákban. További információk: [A mérőszámok tárolásának módját.](storage-analytics-metrics.md#how-metrics-are-stored)
>

## <a name="customize-metrics-charts"></a>Mérőszámok diagramjainak testreszabása

Az alábbi eljárással választhatja ki, hogy mely társzámokat szeretné megtekinteni a metrikadiagramokban.

1. Kezdje azzal, hogy megjelenít egy tárolási metrikadiagramot az Azure Portalon. A **tárfiók panelen** és az egyes szolgáltatások (blob, várólista, tábla, fájl) **Metrikák** paneljén diagramokat talál.

   Ebben a példában a **tárfiók panelen**megjelenő alábbi diagramot használja:

   ![Diagramkiválasztása az Azure Portalon](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Kattintson a diagram tetszőleges pontjára a diagram szerkesztéséhez.

1. Ezután válassza ki a mérőszámok **időtartományát** a diagramon megjelenítendő mutatókközött, és azt a **szolgáltatást** (blob, várólista, tábla, fájl), amelynek metrikáit meg kívánja jeleníteni. Itt az elmúlt heti metrikák vannak kiválasztva a blob szolgáltatás megjelenítéséhez:

   ![Időtartomány és szolgáltatás kiválasztása a Diagram szerkesztése panelen](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. Jelölje ki a diagramon megjeleníteni kívánt egyes **mutatókat,** majd kattintson az **OK**gombra.

   ![Egyéni metrikus kijelölés a Diagram szerkesztése panelen](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

A diagram beállításai nincsenek hatással a tárfiókban lévő figyelési adatok gyűjtésére, összesítésére vagy tárolására.

### <a name="metrics-availability-in-charts"></a>Mérőszámok elérhetősége diagramokban

Az elérhető mérőszámok listája a legördülő menüben kiválasztott szolgáltatásés a szerkesztés alatt álló diagram egységtípusa alapján változik. Például csak akkor választhat ki százalékos mérőszámokat, például *percentNetworkError* és *PercentThrottlingError értékeket,* ha olyan diagramot szerkeszt, amely százalékban jeleníti meg az egységeket:

![Hibaszázalék-diagram kérése az Azure Portalon](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Metrikák felbontása

A **Diagnosztika** területen kiválasztott mérőszámok határozzák meg a fiókjához elérhető mérőszámok felbontását:

* **Az összesített** figyelés olyan metrikákat biztosít, mint a kimenő forgalom, a rendelkezésre állás, a késés és a sikerességi százalékok. Ezek a metrikák a blob, tábla, fájl és várólista-szolgáltatások összesítése.
* **API-nként** nyújt finomabb felbontást, metrikák állnak rendelkezésre az egyes tárolási műveletek, a szolgáltatásiszintű összesítések mellett.

## <a name="configure-metrics-alerts"></a>Metrikák riasztásának konfigurálása

Riasztásokat hozhat létre, amelyek értesítik, ha elérte a tárolási erőforrás-metrikák küszöbértékeket.

1. A **Riasztási szabályok panel**megnyitásához görgessen le a **Menü panel** **MONITORING** szakaszába, és válassza a **Riasztások (klasszikus)** lehetőséget.
2. Válassza **a Metrikariasztás hozzáadása (klasszikus)** lehetőséget a **Riasztási szabály hozzáadása** panel megnyitásához.
3. Adja meg az új riasztási szabály **nevét** és **leírását.**
4. Válassza ki azt a **metrikát,** amelyhez riasztást, riasztási **feltételt**és **küszöbértéket**szeretne hozzáadni. A küszöbérték egység típusa a kiválasztott mérőszámtól függően változik. Például a "count" a *ContainerCount*egységtípusa, míg a *PercentNetworkError* metrika egysége százalék.
5. Válassza ki az **Időszak lehetőséget.** Az időszakon belül eléri vagy meghaladja a küszöbértéket elérő mérőszámok riasztást váltanak ki.
6. (Nem kötelező) **E-mail** és **webhook-értesítések konfigurálása.** A webhookokról további információt a [Webhook konfigurálása egy Azure-metrikariasztáson című témakörben talál.](../../azure-monitor/platform/alerts-webhooks.md) Ha nem konfigurálja az e-mail vagy webhook-értesítéseket, a riasztások csak az Azure Portalon jelennek meg.

!["Riasztási szabály hozzáadása" panel az Azure Portalon](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Mérőszámok diagramjainak hozzáadása a portál irányítópultjához

Az Azure Storage-metrikák diagramok bármelyik tárfiók hoz a portál irányítópultján.

1. Válassza az **Irányítópult szerkesztése** lehetőséget az irányítópult megtekintése közben az [Azure Portalon.](https://portal.azure.com)
1. A **Mozaikgyűjteményben**válassza a **Csempék keresése** > **típus**szerint lehetőséget.
1. Válassza **a Type** > **Storage fiókok lehetőséget.**
1. Az **Erőforrások alkalmazásban**válassza ki azt a tárfiókot, amelynek metrikáit hozzá szeretné adni az irányítópulthoz.
1. Válassza a Kategóriák**figyelése** **Categories** > lehetőséget.
1. Húzza a diagramcsempét az irányítópultra a megjeleníteni kívánt mérőszámhoz. Ismételje meg a műveletet az irányítópulton megjeleníteni kívánt összes mutatóesetében. A következő képen a "Blobok – összes kérelmek" diagram van kiemelve példaként, de az összes diagram elérhető az irányítópulton.

   ![Csempegyűjtemény az Azure Portalon](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. Ha befejezte a diagramok hozzáadását, válassza a **Kész testreszabás lehetőséget** az irányítópult tetején.

Miután hozzáadta a diagramokat az irányítópulthoz, tovább testreszabhatja őket a Metrikadiagramok testreszabása című részben leírtak szerint.

## <a name="configure-logging"></a>Naplózás konfigurálása

Utasíthatja az Azure Storage-t, hogy mentse a diagnosztikai naplókat a blob, a tábla és a várólista-szolgáltatások kéréseinek olvasásához, írásához és törléséhez. A beállított adatmegőrzési szabályzat ezekre a naplókra is vonatkozik.

> [!NOTE]
> Az Azure Files jelenleg támogatja a Storage Analytics metrikák, de még nem támogatja a naplózást.
>

1. Az [Azure Portalon](https://portal.azure.com)válassza **a Tárfiókok**lehetőséget, majd a tárfiók nevét a tárfiók panel megnyitásához.
1. Válassza **a Diagnosztikai beállítások (klasszikus)** lehetőséget a menüpanel **Figyelés (klasszikus)** szakaszában.

    ![Diagnosztika menüpont az Azure Portal on MONITORING területen.](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Győződjön meg arról, hogy az **Állapot** beállítás **Be**, és válassza ki azokat a **szolgáltatásokat,** amelyekhez engedélyezni szeretné a naplózást.

    ![Konfigurálja a naplózást az Azure Portalon.](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. Kattintson a **Mentés** gombra.

A diagnosztikai naplók egy *$logs* nevű blobtárolóba kerülnek a tárfiókban. Megtekintheti a naplóadatokat egy tárolókezelő, például a [Microsoft Storage Explorer,](https://storageexplorer.com)vagy programozott módon a tárolóügyfél-kódtár vagy a PowerShell használatával.

A $logs tároló eléréséről a [Storage analytics naplózása című](storage-analytics-logging.md)témakörben talál további információt.

## <a name="next-steps"></a>További lépések

* További részletek a [társzolgáltatások metrikáiról, a naplózásról és](storage-analytics.md) a számlázásról.
