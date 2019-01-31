---
title: Azure Storage-fiók figyelése |} A Microsoft Docs
description: Ismerje meg az Azure storage-fiók monitorozása az Azure portal használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/31/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d437c6e2327fc9771f9567a37bfbf8ad82713b2b
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55452643"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Az Azure Portal tárfiók figyelése

[Az Azure Storage Analytics](../storage-analytics.md) metrikákat nyújt az összes tárolási szolgáltatások és a blobok, üzenetsorok, naplók és a táblák. Használhatja a [az Azure portal](https://portal.azure.com) konfigurálása, mely metrikákat és naplókat tárolja, amely a fiókjához, és adja meg a mérőszámadatokat vizuális ábrázolásai diagramok konfigurálása.

> [!NOTE]
> Nincsenek figyelési adatok az Azure Portalon vizsgálata kapcsolódó költségeket. További információkért lásd: [Storage Analytics és a számlázás](/rest/api/storageservices/Storage-Analytics-and-Billing).
>
> Jelenleg az Azure Files Storage Analytics mérőszámainak áttekintését támogatja, de egyelőre nem támogatják a naplózást.
> 
> A Storage Analytics és más eszközök használatával azonosítsa, diagnosztizálása és hibaelhárítása az Azure Storage szolgáltatással kapcsolatos problémák a részletes útmutatót lásd: [figyelése, diagnosztizálása és hibaelhárítása a Microsoft Azure Storage](../storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>A storage-fiók figyelésének konfigurálása

1. Az a [az Azure portal](https://portal.azure.com)válassza **tárfiókok**, majd a tárfiók nevét a fiók-irányítópult megnyitásához.
1. Válassza ki **diagnosztikai** a a **figyelés** részében menü.

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Válassza ki a **típus** metrikák adatok az egyes **szolgáltatás** szeretné figyelni, és a **adatmegőrzési** az adatok. A figyelés beállítást is letilthatók **állapota** való **ki**.

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   Az adatmegőrzési házirend beállítása, helyezze át a **megőrzés (nap)** csúszka, vagy adja meg az adatok megőrzése, 1 és 365 nap. Új tárfiókok esetén az alapérték hét nap. Ha nem szeretne adatmegőrzési szabály beállításához, adja meg a nulla. Ha egy adatmegőrzési házirend sem, akár a monitorozási adatok törlését.

   > [!WARNING]
   > A számlázás a metrikák adatait manuálisan törlésekor. Elavult elemzési adatok (az adatmegőrzési-nál régebbi adatok) nem törli azokat a rendszer költségek nélkül. Azt javasoljuk, hogy mennyi ideig szeretné megőrizni a storage analytics-adatok a fiók alapján adatmegőrzési beállítás. Lásd: [milyen díjat tegye díjak storage mérőszámainak engedélyezésével?](../common/storage-enable-and-view-metrics.md#what-charges-do-you-incur-when-you-enable-storage-metrics) további információt.
   >

1. Ha befejezte a figyelési konfigurációtól, válassza ki a **mentése**.

Metrikák alapértelmezett készletét diagramokat, a storage-fiók panelen, valamint az adott szolgáltatás paneleken (blob, üzenetsor, tábla és fájl) jelenik meg. Miután engedélyezte a metrikákat egy szolgáltatáshoz, igénybe vehet egy órát adatai megjelennek a diagramokban. Választhat **szerkesztése** bármelyik metrika diagramban [konfigurálása, mely metrikákat](#how-to-customize-metrics-charts) jelennek meg a diagramon.

Letilthatja a gyűjtemény metrikák és naplózás, beállítás **állapot** való **ki**.

> [!NOTE]
> Az Azure Storage használ [táblatároló](../common/storage-introduction.md#table-storage) való tárolása a storage-fiókot, és tárolja a metrikák a metrikák a fiókjában táblákban. További információkért lásd:. [Metrikák módjára](../common/storage-analytics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Mérőszámdiagramok testreszabása

Az alábbi eljárás segítségével válassza ki, melyik storage-mérőszámok megtekintéséhez a teljesítménymetrikák diagramja. 

1. Indítsa el a storage metrikadiagram megjelenítése az Azure Portalon. A diagramok talál a **tárfiók panelén** és a a **metrikák** egy adott szolgáltatás (blob, queue, table, fájl) panelen.

   Ebben a példában használja az alábbi ábra, amely akkor jelenik meg a **tárfiók panelén**:

   ![Diagram kiválasztása az Azure Portalon](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Kattintson bárhová a diagram szerkesztése a diagramon belül.

1. Ezután válassza ki a **időtartomány** a diagramon megjelenítendő metrikák és a **szolgáltatás** (blob, queue, table, fájlt) amelynek megjelenítendő mérőszámokat. Itt az elmúlt egy hét metrikák megjelenítéséhez a blob szolgáltatás jelölt ki:

   ![A diagram szerkesztése panel idő tartomány és a szolgáltatás kiválasztása](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. Válassza ki az egyes **metrikák** kellett például látható a diagramban, majd kattintson a **OK**.

   ![A diagram szerkesztése panelen egyéni metrika kiválasztása](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

A diagram beállításai nincsenek hatással a gyűjtemény, az összesítés vagy a tárolás figyelés a tárfiókban lévő adatokat.

### <a name="metrics-availability-in-charts"></a>Diagramok mérőszámok rendelkezésre állás érdekében

Rendelkezésre álló metrikák változnak, melyik szolgáltatás választott ki a listából, és az egység típusát, a diagram szerkesztése listája. Százalékos mérőszám jelölje ki például *percentnetworkerror értéket mutatnak* és *percentthrottlingerror értéket mutatnak* csak akkor, ha százalékban egységek megjelenítő diagram szerkesztése:

![Kérelem hiba százalékos diagram az Azure Portalon](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Metrikák felbontás

A kiválasztott metrikák **diagnosztikai** határozza meg, a metrikák elérhető a fiók felbontása:

* **Összesített** monitorozása biztosítja a bejövő/kimenő forgalom, elérhetőség, késés és sikeres százalékos metrikákat. Ezek a metrikák, a blob, table, file és queue szolgáltatások vannak összesítve.
* **API /** biztosít kifinomultabb feloldási metrikákkal egyes tárolási műveletek, továbbá a szolgáltatásiszint-összesítések.

## <a name="configure-metrics-alerts"></a>Metrikák riasztások konfigurálása

Arra az esetre, amikor a küszöbértékeket a rendszer elérte a tárolási erőforrás-mérőszámok riasztásokat is létrehozhat.

1. Megnyitásához a **riasztási szabályok panel**, görgessen le a **figyelés** szakaszában a **menü panel** , és válassza ki **riasztások (klasszikus)**.
2. Válassza ki **metrikariasztás hozzáadása (klasszikus)** megnyitásához a **riasztási szabály hozzáadása** panel
3. Adjon meg egy **neve** és **leírás** az Új riasztási szabály.
4. Válassza ki a **metrika** szeretné hozzáadni egy riasztást a riasztás a **feltétel**, és a egy **küszöbérték**. A küszöbérték egység írja be a mérőszám úgy döntött, attól függően változik. Például a "count" nem egység típusú *ContainerCount*, miközben az egység a *percentnetworkerror értéket mutatnak* metrika százalékos.
5. Válassza ki a **időszak**. Metrikák, amely eléri vagy meghaladja a küszöbértéket a időszakon belül megjelenik egy figyelmeztetés.
6. (Nem kötelező) Konfigurálása **E-mail** és **Webhook** értesítések. További információk a webhookok,: [webhook konfigurálása az Azure metrikariasztás](../../azure-monitor/platform/alerts-webhooks.md). Ha nem adja meg e-mailben vagy webhook értesítések, riasztások csak az Azure Portalon fog megjelenni.

![Az Azure Portalon "A riasztási szabály hozzáadása" panel](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>A portál irányítópultján mérőszámdiagramok hozzáadása

A tárfiókok bármely Azure Storage mérőszámdiagramok adhat hozzá a portál irányítópultján.

1. Válassza ki kattintson **irányítópult szerkesztése** megtekintése az irányítópulton a során a [az Azure portal](https://portal.azure.com).
1. Az a **Csempetárral**válassza **található csempék által** > **típus**.
1. Válassza ki **típus** > **tárfiókok**.
1. A **erőforrások**, válassza ki a tárfiókot, amelynek metrikák hozzáadása az irányítópulthoz való szeretné.
1. Válassza ki **kategóriák** > **figyelési**.
1. És húzza a diagram csempe alakzatot a szeretné metrika megjelenik az irányítópulton. Ismételje meg az összes használni kívánt metrikák az irányítópulton jelenik meg. Az alábbi ábrán a "Blobok – kérések összesen" diagram például ki van jelölve, de a diagramok érhetők el elhelyezésre az irányítópulton.

   ![Az Azure Portalon csempetárral](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. Válassza ki **testreszabás kész** tetején az irányítópult hozzáadása diagramok elkészült.

Miután hozzáadott diagramokat az irányítópulton, további testre szabhatja a [mérőszámdiagramok testreszabása](#how-to-customize-metrics-charts).

## <a name="configure-logging"></a>Naplózás konfigurálása

Mentse a diagnosztikai naplók olvasási, írási és törlési kérelmeket a blob, table és queue szolgáltatások az Azure Storage találhatók. Az adatmegőrzési házirend, állítsa be ezeket a naplókat is vonatkozik.

> [!NOTE]
> Jelenleg az Azure Files Storage Analytics mérőszámainak áttekintését támogatja, de egyelőre nem támogatják a naplózást.
>

1. Az a [az Azure portal](https://portal.azure.com), jelölje be **tárfiókok**, majd nyissa meg a storage-fiók panelen a tárfiók nevére.
1. Válassza ki **diagnosztikai** a a **figyelés** részében menü.

    ![Diagnosztikai menüelem figyelés alatt az Azure Portalon.](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)
    
1. Győződjön meg arról **állapot** értékre van állítva **a**, és válassza ki a **szolgáltatások** szeretné naplózásának engedélyezése a.

    ![Naplózás konfigurálása az Azure Portalon.](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. Kattintson a **Save** (Mentés) gombra.

Egy blobtárolót a diagnosztikai naplók mentett *$logs* a storage-fiókban. A naplózási adatokat, például a storage explorer használatával is megtekintheti a [Microsoft Storage Explorer](http://storageexplorer.com), vagy programozott módon, a storage ügyféloldali kódtára vagy a PowerShell segítségével.

További információ a $logs tároló elérésekor: [tárolási naplózás engedélyezése és Teljesítménynapló-adatok elérése](/rest/api/storageservices/enabling-storage-logging-and-accessing-log-data).

## <a name="next-steps"></a>További lépések

* További részleteket talál a kapcsolatos [mérőszámok, naplózás, és a számlázási](../storage-analytics.md) a Storage Analytics.
* [Engedélyezze az Azure Storage-mérőszámok és nézet mérőszámadatokat](../storage-enable-and-view-metrics.md) PowerShell-lel és a C# használatával programozott módon.
