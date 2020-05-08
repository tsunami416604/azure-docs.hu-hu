---
title: Az Azure Metrikaböngésző speciális funkciói
description: További információ a Azure Monitor speciális funkcióiról Metrikaböngésző
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: e6ff33b6a23cb85649a8811a8bef27ab455ab9e6
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980898"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Az Azure Metrikaböngésző speciális funkciói

> [!NOTE]
> Ez a cikk azt feltételezi, hogy ismeri a Metrikaböngésző alapszintű funkcióit. Ha Ön új felhasználó, és szeretné megismerni, hogyan hozhatja létre első mérőszámát, tekintse meg [Az Azure Metrikaböngésző első lépéseivel foglalkozó](metrics-getting-started.md)témakört.

## <a name="metrics-in-azure"></a>Metrikák az Azure-ban

A [Azure monitor metrikái](data-platform-metrics.md) a mért értékek, valamint a begyűjtött és az idő múlásával tárolt számok sorozata. Léteznek standard (vagy "platform") mérőszámok és egyéni metrikák. A standard mérőszámokat maga az Azure platform kapja meg. A standard mérőszámok az Azure-erőforrások állapot-és használati statisztikáit tükrözik. Míg az egyéni metrikákat az alkalmazások az Application Insights API használatával küldik el az Azure-ba az [Egyéni eseményekhez és mérőszámokhoz](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics), a [Windows Azure Diagnostics (wad) bővítményhez](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview)vagy [Azure monitor Rest APIhoz](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-store-custom-rest-api).

## <a name="create-views-with-multiple-metrics-and-charts"></a>Nézetek létrehozása több metrikával és diagrammal

Létrehozhat olyan diagramokat, amelyek több mérőszámot ábrázolnak, vagy egyszerre több metrikai diagramot is megjeleníthetnek. Ez a funkció lehetővé teszi a következőket:

- a kapcsolódó metrikák korrelálása ugyanazon a gráfon, hogy az egyik érték hogyan kapcsolódjon egymáshoz
- a metrikák megjelenítése a különböző mértékegységekkel a közeli közelségben
- több erőforrás metrikáinak vizuális összesítése és összehasonlítása

Ha például 5 Storage-fiókkal rendelkezik, és szeretné tudni, hogy mekkora a teljes tárterület a közöttük, létrehozhat egy (halmozott) diagramot, amely az adott időpontokban szereplő értékek egyedi és összegét jeleníti meg.

### <a name="multiple-metrics-on-the-same-chart"></a>Több metrika ugyanazon a diagramon

Először [hozzon létre egy új diagramot](metrics-getting-started.md#create-your-first-metric-chart). Kattintson a **metrika hozzáadása** lehetőségre, és ismételje meg a lépéseket egy másik metrika egyazon diagramon való hozzáadásához.

   > [!NOTE]
   > Általában nem szeretné, hogy a metrikák különböző mértékegységekkel rendelkezzenek (például "ezredmásodpercek" és "kilobájtok"), vagy egy diagramon jelentősen eltérő méretezéssel. Ehelyett érdemes több diagramot használni. Kattintson a diagram hozzáadása gombra, és hozzon létre több diagramot a mérőszámok Explorerben.

### <a name="multiple-charts"></a>Több diagram

Kattintson a **diagram hozzáadása** lehetőségre, és hozzon létre egy másik metrikával rendelkező diagramot.

### <a name="order-or-delete-multiple-charts"></a>Több diagram megrendelése vagy törlése

Több diagram megrendeléséhez vagy törléséhez kattintson a három pontra ( **..** .) szimbólumra a diagram menü megnyitásához, majd válassza ki a **feljebb,** lejjebb **Move down**vagy **törölni**kívánt menüelemet.

## <a name="changing-aggregation"></a>Összesítés módosítása

Ha mérőszámot ad hozzá egy diagramhoz, a metrikák Explorer automatikusan kiválasztja az alapértelmezett összesítést. Az alapértelmezett érték az alapszintű, de egy másik összesítéssel további információkhoz juthat a metrikáról. A diagramokon a különböző összesítések megtekintéséhez meg kell ismernie, hogy a metrikák kezelője hogyan kezeli őket. 

A metrikák az adott időszakban rögzített mérési (vagy "metrikus értékek") sorozata. Amikor Kirajzol egy diagramot, a kiválasztott metrika értékei külön lesznek összesítve az *időgabona*során. Az időgabona méretét [a Metrikaböngésző Time Picker panelen](metrics-getting-started.md#select-a-time-range)választhatja ki. Ha nem az időgabona explicit kijelölését választja, a rendszer automatikusan kiválasztja az idő részletességét a jelenleg kijelölt időtartomány alapján. Az időkeret meghatározása után a rendszer összesíti az egyes időkeretek során rögzített metrikai értékeket, és a diagramra helyezi a Datapoint.

Tegyük fel például, hogy a diagram a **kiszolgáló válaszideje** mérőszámot mutatja az **elmúlt 24 órában** mért **átlagos** összesítés használatával:

- Ha az idő részletessége 30 percre van beállítva, a diagram 48 összesített datapoints (például a diagram ábrázolási területének 48 pontjának összekapcsolása). Ez egy óránként 24 óra x 2 datapoints. Mindegyik Datapoint az összes rögzített válaszidő *átlagát* jelöli az összes olyan kiszolgálói kérelem esetében, amelyek az egyes érintett 30 perces időszakok során fordultak elő.
- Ha 15 percre vált az időrészletesség, 96 összesített datapoints kap.  Ez egy óránként 24 óra x 4 datapoints.

A mérőszámok Explorerben öt alapvető statisztikai összesítési típus érhető el: **Sum**, **Count**, **min**, **Max**és **Average**. Az **összeg** összesítését időnként a **teljes** összesítésnek is nevezzük. Számos metrika esetében Metrikaböngésző elrejti a teljesen lényegtelen összesítéseket, és nem használható.

- **Sum (összeg** ) – az összesítési intervallumban rögzített összes érték összege
- **Count (darabszám** ) – az összesítési intervallumban rögzített mértékegységek száma. Vegye figyelembe, hogy a **Count** értéke megegyezik az **összeggel** abban az esetben, ha a metrikát az 1 értékkel együtt mindig rögzíti a rendszer. Ez gyakran előfordul, ha a metrika nyomon követi a különböző események számát, és az egyes mérések egy eseményt jelölnek (azaz a kód egy metrikai rekord kikapcsolását minden alkalommal, amikor új kérés érkezik)
- **Átlag** – az összesítési intervallumban rögzített metrikai értékek átlaga
- **Min** – az összesítési intervallumban rögzített legkisebb érték
- **Max** – az összesítési intervallumban rögzített legnagyobb érték

## <a name="apply-filters-to-charts"></a>Szűrők alkalmazása diagramokra

Szűrőket alkalmazhat a diagramokra, amelyek dimenziókat tartalmazó metrikákat jelenítenek meg. Ha például a "tranzakciók száma" mérőszámban szerepel egy dimenzió, "válasz típusa", amely azt jelzi, hogy a tranzakciók válaszai sikeresek vagy sikertelenek voltak, akkor a dimenzió szűrése csak a sikeres (vagy csak sikertelen) tranzakciók esetében fog kirajzolni egy diagram sort. 

### <a name="to-add-a-filter"></a>Szűrő hozzáadása

1. Válassza a **szűrő hozzáadása** a diagram felett lehetőséget.

2. Válassza ki a szűrni kívánt dimenziót (tulajdonságot)

   ![metrika képe](./media/metrics-charts/00006.png)

3. Válassza ki, hogy mely dimenzió értékeket kívánja felvenni a diagram ábrázolásakor (ez a példa a sikeres tárolási tranzakciók szűrését mutatja):

   ![metrika képe](./media/metrics-charts/00007.png)

4. Miután kiválasztotta a szűrő értékeit, kattintson a szűrési választóból a bezáráshoz. A diagramon most már látható, hogy hány tárolási tranzakciót sikerült végrehajtani:

   ![metrika képe](./media/metrics-charts/00008.png)

5. Az 1-4-es lépések megismétlésével több szűrőt is alkalmazhat ugyanarra a diagramra.



## <a name="apply-splitting-to-a-chart"></a>Felosztás alkalmazása diagramra

A mérőszámok dimenzió alapján való felosztásával megjelenítheti, hogy a metrika különböző szakaszai hogyan hasonlítanak össze egymással, és azonosítsa a dimenzió kihelyezett szegmenseit.

### <a name="apply-splitting"></a>Felosztás alkalmazása

1. Kattintson a diagram feletti **felosztás alkalmazása** elemre.
 
   > [!NOTE]
   > A felosztás nem használható olyan diagramokkal, amelyek több metrikával rendelkeznek. Emellett több szűrő is lehet, de egyetlen diagramon csak egy felosztási dimenzió alkalmazható.

2. Válassza ki azt a dimenziót, amelyre a diagramot szeretné szegmentálni:

   ![metrika képe](./media/metrics-charts/00010.png)

   A diagram most már több sort is megjelenít, egyet a dimenzió minden szegmenséhez:

   ![metrika képe](./media/metrics-charts/00012.png)

3. Kattintson a **csoportosítási választóból** a bezáráshoz.

   > [!NOTE]
   > A szűrést és a felosztást ugyanazon a dimenzión használhatja a forgatókönyv szempontjából lényegtelen szegmensek elrejtéséhez, és könnyebben olvasható diagramokat készíthet.

## <a name="lock-boundaries-of-chart-y-axis"></a>A diagram y tengelyének határainak zárolása

Az y tengely tartományának zárolása akkor lesz fontos, ha a diagramon a nagyobb értékek kisebb ingadozásai láthatók. 

Ha például a sikeres kérések mennyisége 99,99%-ról 99,5%-ra csökken, a szolgáltatás minőségének jelentős csökkenését jelenthetheti. Azonban a kis numerikus értékek ingadozása nehéz lenne, vagy az alapértelmezett diagram beállításainál is lehetetlen lenne. Ebben az esetben a diagram legalacsonyabb határát 99%-ra lehet zárolni, ami nyilvánvalóvá tenné ezt a kis csökkenést. 

Egy másik példa a rendelkezésre álló memória ingadozása, ahol az érték műszakilag soha nem éri el a 0 értéket. Ha a tartományt magasabb értékre javítja, a kihagyható a rendelkezésre álló memória. 

Az y tengely tartományának vezérléséhez használja a "..." diagram menü, és a speciális diagram beállításainak eléréséhez válassza a **diagram szerkesztése** lehetőséget. Módosítsa az Y tengely tartománya szakaszban található értékeket, vagy az **automatikus** gomb használatával térjen át az alapértelmezett értékekre.

![metrika képe](./media/metrics-charts/00014-manually-set-granularity.png)

> [!WARNING]
> Az y tengely azon határainak zárolása, amelyek a különböző számításokat vagy összegeket követik egy adott időtartamon belül (és így a darabszámot, a Sum, a minimum vagy a maximális összesítést használják) általában a rögzített idő részletességét kell megadnia az automatikus alapértékek helyett. Erre azért van szükség, mert a diagramok értékei változnak, amikor az időrészletességet automatikusan módosítja a felhasználó átméretezni a böngészőablakot, vagy az egyik képernyőfelbontásról egy másikra kerül. Az időbeli részletesség változása hatással van a diagram kinézetére, és az y tengely tartományának aktuális kijelölését érvényteleníti.

## <a name="change-colors-of-chart-lines"></a>Diagram vonalai színének módosítása

A diagramok konfigurálása után a rendszer automatikusan egy alapértelmezett paletta színeit rendeli hozzá a diagram soraihoz. Módosíthatja ezeket a színeket.

Egy diagramterület színének módosításához kattintson a diagramhoz tartozó jelmagyarázatban található színes sávra. Ekkor megnyílik a színválasztó párbeszédpanel. A színválasztó segítségével konfigurálja a vonal színét.

A diagram színeinek konfigurálása után a diagramot egy irányítópultra rögzítve maradnak. A következő szakasz bemutatja, hogyan rögzítheti a diagramokat.

![metrika képe](./media/metrics-charts/018.png)

## <a name="pin-charts-to-dashboards"></a>Diagramok rögzítése az irányítópultokon

A diagramok konfigurálása után érdemes lehet hozzáadni az irányítópultokhoz, hogy újra megtekinthető legyen, esetleg más figyelési telemetria kontextusában vagy a csapattal való megosztással.

Konfigurált diagram rögzítése az irányítópulton:

A diagram konfigurálása után a diagram jobb felső sarkában kattintson a **diagram műveletek** menüre, majd kattintson a **rögzítés az irányítópulton**elemre.

![metrika képe](./media/metrics-charts/00013.png)

## <a name="create-alert-rules"></a>Riasztási szabályok létrehozása

A mérőszámok metrika-alapú riasztási szabály alapján történő megjelenítéséhez a beállított feltételeket használhatja. Az új riasztási szabály a diagramon a cél erőforrás, metrika, felosztás és szűrés dimenziókat fogja tartalmazni. Ezeket a beállításokat később is módosíthatja a riasztási szabály létrehozási paneljén.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>Új riasztási szabály létrehozásához kattintson az **új riasztási szabály** elemre.

![Piros színnel jelölt új riasztási szabály gomb](./media/metrics-charts/015.png)

A riasztási szabály létrehozási paneljén a diagramon előre kitöltött mérőszám-dimenziók láthatók, így könnyebben hozhat létre egyéni riasztási szabályokat.

![Riasztási szabály létrehozása](./media/metrics-charts/016.png)

A metrikai riasztások beállításával kapcsolatos további információkért tekintse meg ezt a [cikket](alerts-metric.md) .

## <a name="troubleshooting"></a>Hibaelhárítás

*Nem látok semmilyen információt a diagramon.*

* A szűrők a panelen lévő összes diagramra érvényesek. Győződjön meg arról, hogy az egyik diagramra való összpontosítás közben nem állított be olyan szűrőt, amely kizárja az összes adathalmazt egy másikon.

* Ha különböző szűrőket szeretne beállítani különböző diagramokon, hozzon létre különböző lapokon, majd mentse őket külön kedvencekként. Ha szeretné, rögzítheti őket az irányítópulton, hogy azok egymás mellett is megjelenjenek.

* Ha a diagramot olyan tulajdonság szerint szegmentálja, amely nincs meghatározva a metrikán, akkor a diagramon nem lesz semmi. Próbálja meg törölni a szegmentálást (felosztás), vagy válasszon másik tulajdonságot.

## <a name="next-steps"></a>További lépések

  Olvassa el az [Egyéni KPI-irányítópultok létrehozását](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) ismertető témakört, amelyből megtudhatja, hogyan hozhat létre mérőszámokkal rendelkező, gyakorlati irányítópultokat.

