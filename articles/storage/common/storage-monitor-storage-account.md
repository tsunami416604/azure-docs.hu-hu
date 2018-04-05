---
title: Egy Azure Storage-fiók figyelése |} Microsoft Docs
description: Megtudhatja, hogyan figyelheti egy Azure storage-fiókot az Azure portál használatával.
services: storage
documentationcenter: ''
author: tamram
manager: timlt
editor: tysonn
ms.assetid: b83cba7b-4627-4ba7-b5d0-f1039fe30e78
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: tamram
ms.openlocfilehash: ffc7d46bbfa4db47a47e416c395efdfc451cadc1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>A figyelő egy tárfiókot, Azure-portálon

[Az Azure Storage Analytics](../storage-analytics.md) metrikákat biztosít minden a tárolási szolgáltatások és a naplókat a BLOB, üzenetsorok, és a táblázatot. Használhatja a [Azure-portálon](https://portal.azure.com) mely metrikák és a naplók tárolja, a fiók konfigurálását és a metrikai adatok vizuális ábrázolásai biztosító diagramok konfigurálása.

> [!NOTE]
> Nincsenek figyelési adatok az Azure portálon vizsgálata kapcsolódó költségeket. További információkért lásd: [tárolási analitika és számlázási](/rest/api/storageservices/Storage-Analytics-and-Billing).
>
> Jelenleg az Azure Files Storage Analytics metrikák támogatja, de még nem támogatja a naplózást.
> 
> A részletes útmutatót a tárolási analitika és más eszközök segítségével azonosíthatja, diagnosztizálása és Azure tárolással kapcsolatos problémák elhárításához, lásd: [figyelése, diagnosztizálása és elhárítása a Microsoft Azure Storage](../storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>A tárfiók figyelésének konfigurálása

1. Az a [Azure-portálon](https://portal.azure.com), jelölje be **tárfiókok**, majd a tárfiók nevét az fiók irányítópult megnyitásához.
1. Válassza ki **diagnosztika** a a **figyelés** részében menü találhatja.

    ![MonitoringOptions](./media/storage-monitor-storage-account/stg-enable-metrics-00.png)

1. Válassza ki a **típus** metrikák adatok az egyes **szolgáltatás** kívánja figyelni, és a **adatmegőrzési** az adatok számára. Letilthatja a figyelés beállítás **állapot** való **ki**.

    ![MonitoringOptions](./media/storage-monitor-storage-account/stg-enable-metrics-01.png)

   A mérőszámok engedélyezheti az egyes szolgáltatásokhoz, amelyek mindegyikét új storage-fiókok alapértelmezés szerint engedélyezve vannak két típusa van:

   * **Összesített**: például be-és kilépési, a rendelkezésre állási, a késés és a sikeres százalékos metrikákat gyűjtő. A mérőszámok összesítése blobok, üzenetsorok, táblák és fájlszolgáltatások szerint történik.
   * **/ API**: mellett a összesített metrika, ugyanazokat az Azure Storage szolgáltatás API-ban minden tárolási műveletet metrikáját gyűjti.

   Az adatmegőrzési házirend beállítása, helyezze át a **megőrzés (nap)** csúszkát vagy adja meg az adatok megőrzése mellett, 1 és 365 közötti napok számát. Az új tárfiókok alapértelmezés hét nap. Ha nem szeretné, hogy egy megőrzési házirend, adja meg a nulla. Ha nincs megőrzési házirend, esetén a figyelési adatok törlését.

   > [!WARNING]
   > Metrikai adatok manuális törlésekor van szó. Elavult analytics (régebbi, mint a adatmegőrzési) adatokat a rendszer ingyenesen törlődik. Azt javasoljuk, hogy mennyi ideig szeretné megőrizni a tárolási analitikai adatok fiókja alapján adatmegőrzési beállítás. Lásd: [mi díja tegye Ön tudomásával storage mérőszámainak engedélyezésével?](../common/storage-enable-and-view-metrics.md#what-charges-do-you-incur-when-you-enable-storage-metrics) további információt.
   >

1. A figyelési konfiguráció befejezése után válassza ki a **mentése**.

A storage-fiók panelen, valamint a szolgáltatás paneleken (blob, várólista, a táblának és fájl) diagramok metrikák alapértelmezett készletét jelenik meg. Miután engedélyezte a metrikák egy szolgáltatáshoz, egy órát adatai megjelennek a diagramokat az azt is tarthat. Kiválaszthatja **szerkesztése** bármely metrika a diagram a [mely metrikáinak megadása](#how-to-customize-metrics-charts) jelennek meg a diagramon.

Értékre állításával letilthatja metrikák adatgyűjtési és -naplózás **állapot** való **ki**.

> [!NOTE]
> Használja az Azure Storage [table storage](../common/storage-introduction.md#table-storage) a használatmérés a tárfiókot, és tárolja a metrikák tárolására fiókja táblájában. További információkért lásd:. [Metrikák tárolási módját](../common/storage-analytics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Metrikák diagramok testreszabása

A következő eljárással válassza ki a mely storage mérőszámainak metrikák diagram megtekintése. 

1. Indítsa el a tárolási metrika diagram megjelenítése az Azure portálon. A diagramok talál a **storage-fiók panelen** és a a **metrikák** egy adott szolgáltatás (blob, várólista, tábla, fájl) panelen.

   Ebben a példában dolgozunk, az az alábbi táblázat, amely akkor jelenik meg a **storage-fiók panelen**:

   ![A diagram kijelölés Azure-portálon](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Ezután kattintson bárhová belül nyissa meg a diagramot a **metrika** panelen. Válassza ki **diagram szerkesztése** megnyitásához a **diagram szerkesztése lehetőséget** panelen.

   ![A diagram gomb diagram panelt a szerkesztése](./media/storage-monitor-storage-account/stg-customize-chart-01.png)

1. A a **diagram szerkesztése lehetőséget** panelen válassza a **időtartomány** a diagramon megjelenítendő metrikákat, és a **szolgáltatás** (blob, várólista, table, a fájl) amelynek metrikák szeretne megjeleníteni. Itt kiválasztottuk, hogy a blob szolgáltatás az elmúlt héten metrikák megjelenítéséhez:

   ![Tartomány- és időbeállítást a diagram szerkesztése lehetőséget a panelen](./media/storage-monitor-storage-account/stg-customize-chart-02.png)

1. Válassza ki az egyes **metrikák** kellett például látható a diagramban, majd kattintson a **OK**. Például Itt azt a kiválasztott megjelenítéséhez a *ContainerCount* és *ObjectCount* metrikák:

   ![Egyes kiválasztott diagram szerkesztése lehetőséget a panelen](./media/storage-monitor-storage-account/stg-customize-chart-03.png)

A diagram beállítások nem befolyásolják a gyűjteményt, összesítési vagy tárolási figyelés csak a metrikai adatok megtekintése a tárfiókban lévő adatokat.

### <a name="metrics-availability-in-charts"></a>Metrikák diagramok rendelkezésre állás érdekében

Azokat a szerkesztett melyik szolgáltatás választott ki a legördülő listán, és a diagram egységtípus alapján elérhető változásokat. Például kiválaszthatja például százalékos metrikák *PercentNetworkError* és *PercentThrottlingError* csak akkor, ha százalékos egységek megjelenítő diagram szerkesztése:

![Kérelem hiba százalékos diagram az Azure-portálon](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Metrikák felbontás

A diagnosztika kiválasztott metrikák meghatározza a érhető el a fiókjához metrikák felbontását:

* **Összesített** figyelés nyújt metrikák például be-és kilépési, a rendelkezésre állási, a késés és a sikeres százalékos aránya. A blob, table, fájlt és várólista-szolgáltatás a fenti metrikák összesítése.
* **/ API** biztosít egyeztetését feloldási metrikák elérhető egyes tárhelyműveletek, továbbá a szolgáltatásiszint-összesítések.

## <a name="configure-metrics-alerts"></a>Metrikák riasztások konfigurálása

Értesítést küldenek, ha a küszöbértékeket a rendszer elérte a storage erőforrás metrikáit riasztásokat hozhat létre.

1. Megnyitásához a **riasztási szabályok panel**, görgessen le a **figyelés** szakasza a **menü panel** válassza ki **riasztási szabályok**.
1. Válassza ki **riasztás hozzáadása** megnyitásához a **riasztási szabály felvétele** panel
1. Válassza ki a **erőforrás** (blob-, fájl, várólista, tábla) a legördülő listán, a, és írja be a **neve** és **leírása** az Új riasztási szabály.
1. Válassza ki a **metrika** szeretné hozzáadni egy riasztást, a riasztást a **feltétel**, és egy **küszöbérték**. A küszöbérték egység írja be a kiválasztott metrika függően változik. Például a "count" nem egység típusú *ContainerCount*, miközben a egységet a a *PercentNetworkError* metrika százalékos értéke.
1. Válassza ki a **időszak**. Amely eléri vagy meghaladja a küszöbértéket a időszakon belül metrikák riasztást vált ki.
1. (Választható) Konfigurálása **E-mail** és **Webhook** értesítések. A webhook további információkért lásd: [olyan webhook konfigurálása Azure metrika riasztást](../../monitoring-and-diagnostics/insights-webhooks-alerts.md). Ha nem adja meg e-mailben vagy webhook értesítések, a riasztás csak az Azure-portálon jelenik meg.

!["A riasztási szabály felvétele" panel az Azure-portálon](./media/storage-monitor-storage-account/stg-alert-rules-01.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>A portál irányítópultján metrikák diagramok hozzáadása

A storage-fiókok bármely Azure Storage metrikák diagramokat lehet hozzáadni a portál Irányítópultjára.

1. Jelölje be kattintson **Szerkesztés irányítópult** az irányítópult megtekintése közben a [Azure-portálon](https://portal.azure.com).
1. Az a **csempe gyűjtemény**, jelölje be **keresés tartalmazó csempék éppen úgy által** > **típus**.
1. Válassza ki **típus** > **tárfiókok**.
1. A **erőforrások**, válassza ki a tárfiókot, amelynek metrikák hozzáadja az irányítópulton.
1. Válassza ki **kategóriák** > **figyelési**.
1. Fogd és vidd a diagram csempére az irányítópulton megjelenített szeretné metrika-kiszolgálóra. Ismételje meg az irányítópulton megjelenített szeretné összes metrikát. Az alábbi képen ki van jelölve a "Blobok – összes lekérdezés" diagram példaként, de a diagramokat az irányítópulton elhelyezésre elérhető.

   ![Azure-portálon csempe gyűjteménye](./media/storage-monitor-storage-account/stg-customize-dashboard-01.png)
1. Válassza ki **végzett Testreszabás** elejére az irányítópulton, amikor elkészült a diagramok hozzáadásával.

Diagramokat az irányítópulton való felvételét, akkor további szabhatja azokat a [metrikák diagramok testreszabása](#how-to-customize-metrics-charts).

## <a name="configure-logging"></a>Naplózás konfigurálása

Mentse diagnosztikai naplókat a olvasási, írási és törlési kérések a blob, table és queue szolgáltatások az Azure Storage találhatók. Az adatmegőrzési házirend beállíthatja a naplók is vonatkozik.

> [!NOTE]
> Jelenleg az Azure Files Storage Analytics metrikák támogatja, de még nem támogatja a naplózást.
>

1. Az a [Azure-portálon](https://portal.azure.com), jelölje be **tárfiókok**, majd nyissa meg a storage-fiók panelen a tárfiók nevét.
1. Válassza ki **diagnosztika** a a **figyelés** részében menü találhatja.

    ![Diagnosztika menüpont figyelés alatt az Azure portálon.](./media/storage-monitor-storage-account/stg-enable-metrics-00.png)
    
1. Győződjön meg arról **állapot** értéke **a**, és válassza ki a **szolgáltatások** szeretné naplózásának engedélyezése a.

    ![Naplózás konfigurálása az Azure-portálon.](./media/storage-monitor-storage-account/stg-enable-logging-01.png)
1. Kattintson a **Save** (Mentés) gombra.

A diagnosztikai naplókat a tárfiókban lévő $logs nevű blob tárolóban lesznek mentve. A naplózási adatokat, például Tártallózóval használatával megtekintheti a [Microsoft Tártallózó](http://storageexplorer.com), vagy a storage ügyféloldali kódtár vagy a PowerShell programozott módon.

További információ a $logs tárolójának hozzáférésekor: [tárolási naplózás engedélyezése és használata naplóadatok](/rest/api/storageservices/enabling-storage-logging-and-accessing-log-data).

## <a name="next-steps"></a>További lépések

* Kapcsolatos további részletekért található [metrika, naplózási és számlázási](../storage-analytics.md) tárolási elemzéséhez.
* [Lehetővé teszik az Azure Storage-metrikák és a nézet metrikák adatok](../storage-enable-and-view-metrics.md) PowerShell használatával és a C# programozott módon.
