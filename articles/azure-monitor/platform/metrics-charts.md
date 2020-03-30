---
title: Az Azure Metrikaböngésző speciális funkciói
description: További információ az Azure Monitor mérőszámokkal kapcsolatos kezelő speciális funkcióiról
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 2df1e0bb7d586edb13dc86e163f0e5728608d2a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371597"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Az Azure Metrikaböngésző speciális funkciói

> [!NOTE]
> Ez a cikk feltételezi, hogy ismeri a Metrikakezelő alapvető funkcióit. Ha Ön új felhasználó, és szeretné megtudni, hogyan hozhat létre az első metrikadiagramot, olvassa el az Azure Metrics Explorer – Első lépések az [Azure Metrics Explorer alkalmazással című témakört.](metrics-getting-started.md)

## <a name="metrics-in-azure"></a>Metrikák az Azure-ban

[Metrikák az Azure Monitor a](data-platform-metrics.md) mért értékek és a számok, amelyek az idő múlásával gyűjtött és tárolt sorozata. Vannak szabványos (vagy "platform") metrikák és egyéni metrikák. A standard metrikákat maga az Azure platform biztosítja. A standard metrikák az Azure-erőforrások állapot- és használati statisztikáit tükrözik. Míg az egyéni metrikákat az alkalmazások az Application Insights API-val egyéni [eseményekhez és metrikákhoz,](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) [a Windows Azure Diagnosztika (WAD) bővítményvagy](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview)az Azure Monitor REST API használatával küldik el az [Azure-ba.](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-store-custom-rest-api)

## <a name="create-views-with-multiple-metrics-and-charts"></a>Nézetek létrehozása több mutatóval és diagrammal

Létrehozhat olyan diagramokat, amelyek több metrikavonalat ábrázolnak, vagy egyszerre több metrikadiagramot jelenítmeg. Ez a funkció lehetővé teszi, hogy:

- korrelálja a kapcsolódó mutatókat ugyanazon a grafikonon, hogy lássa, hogyan kapcsolódik az egyik érték egy másikhoz
- különböző mértékegységekkel rendelkező mérőszámok megjelenítése közvetlen közelségben
- több erőforrás mutatóinak vizuális összesítése és összehasonlítása

Ha például 5 tárfiókkal rendelkezik, és szeretné tudni, hogy mennyi teljes terület van felhasználva közöttük, létrehozhat egy (halmozott) területdiagramot, amely az adott időpontban az összes érték egyénét és összegét jeleníti meg.

### <a name="multiple-metrics-on-the-same-chart"></a>Több mutató ugyanazon a diagramon

Először [hozzon létre egy új diagramot](metrics-getting-started.md#create-your-first-metric-chart). Kattintson **a Metrika hozzáadása gombra,** és ismételje meg a lépéseket, ha egy másik mérőszámot szeretne hozzáadni ugyanahhoz a diagramhoz.

   > [!NOTE]
   > Általában nem szeretné, hogy a metrikák különböző mértékegységekkel (azaz "ezredmásodperc" és "kilobájt") vagy jelentősen eltérő léptékkel rendelkeznek egy diagramon. Ehelyett érdemes több diagramot használni. Kattintson a Diagram hozzáadása gombra, ha több diagramot szeretne létrehozni a metrikakezelőben.

### <a name="multiple-charts"></a>Több diagram

Kattintson a **Diagram hozzáadása elemre,** és hozzon létre egy másik, másik mérőszámmal rendelkező diagramot.

### <a name="order-or-delete-multiple-charts"></a>Több diagram sorrendje vagy törlése

Több diagram rendeléséhez vagy törléséhez kattintson a három pont ( **...** ) szimbólumra a diagram menü megnyitásához, és válassza ki a megfelelő menüpontot a **Fel**, **Le**vagy a **Törlés menüpontra.**

## <a name="apply-filters-to-charts"></a>Szűrők alkalmazása diagramokra

Szűrőket alkalmazhat azokra a diagramokra, amelyek dimenziókkal rendelkező mutatókat jelenítek meg. Ha például a "Tranzakciószám" metrika "Választípus" dimenzióval rendelkezik, amely azt jelzi, hogy a tranzakciókból érkező válasz sikeres vagy sikertelen volt-e, akkor a dimenzió szűrése csak sikeres (vagy csak sikertelen) tranzakciók diagramvonalát ábrázolja. 

### <a name="to-add-a-filter"></a>Szűrő hozzáadása

1. Szűrő **hozzáadása** a diagram felett

2. Válassza ki, hogy melyik dimenziót (tulajdonságot) szeretné szűrni

   ![metrika képe](./media/metrics-charts/00006.png)

3. Válassza ki, hogy mely dimenzióértékeket szeretné felvenni a diagram nyomtatásakor (ebben a példában a sikeres tárolási tranzakciók kiszűrése látható):

   ![metrika képe](./media/metrics-charts/00007.png)

4. A szűrőértékek kiválasztása után kattintson a Szűrőválasztótól távolabbra a bezáráshoz. Most a diagram azt mutatja, hogy hány tárolási tranzakció sikertelen volt:

   ![metrika képe](./media/metrics-charts/00008.png)

5. Az 1-4.



## <a name="apply-splitting-to-a-chart"></a>Felosztás alkalmazása diagramra

A metrikát dimenzió szerint feloszthatja, hogy vizualizálja, hogyan viszonyulnak egymáshoz a metrika különböző szegmensei, és azonosíthatja a dimenzió külső szegmenseit.

### <a name="apply-splitting"></a>Felosztás alkalmazása

1. Kattintson az **Alkalmazás felosztása gombra** a diagram felett.
 
   > [!NOTE]
   > A felosztás nem használható több mutatóval rendelkező diagramokkal. Emellett több szűrőt is használhat, de csak egy felosztási dimenziót alkalmazhat egyetlen diagramra.

2. Válassza ki azt a dimenziót, amelyre szegmentálni szeretné a diagramot:

   ![metrika képe](./media/metrics-charts/00010.png)

   Most a diagram on-line, egy-egy méretszegmens:

   ![metrika képe](./media/metrics-charts/00012.png)

3. A bezáráshoz kattintson a **csoportosítási választótól** távolabbra.

   > [!NOTE]
   > A szűrés és a felosztás segítségével elrejtheti azokat a szegmenseket, amelyek nem relevánsak a forgatókönyv szempontjából, és könnyebben olvashatóvá teheti a diagramokat.

## <a name="lock-boundaries-of-chart-y-axis"></a>Diagram y tengelyhatárainak lezárása

Az y tengely tartományának zárolása fontossá válik, ha a diagram kisebb nagyobb értékek ingadozásait mutatja. 

Ha például a sikeres kérelmek száma 99,99%-ról 99,5%-ra csökken, az jelentősen csökkentheti a szolgáltatás minőségét. Az alapértelmezett diagrambeállításokból azonban nehéz, sőt lehetetlen lenne észrevenni egy kis numerikus értékingadozást. Ebben az esetben a diagram legalacsonyabb határát 99%-ra zárolhatja, ami ezt a kis csökkenést láthatóbbá tenné. 

Egy másik példa a rendelkezésre álló memória ingadozása, ahol az érték technikailag soha nem éri el a 0-t. Ha a tartományt magasabb értékre rögzíti, a rendelkezésre álló memória cseppjei könnyebben kiszúrhatók. 

Az y tengely tartományának szabályozásához használja a "..." diagrammenüt, és a **Diagram szerkesztése parancsra** a speciális diagrambeállítások eléréséhez. Módosítsa az Y tengely tartománya szakasz értékeit, vagy az **Automatikus** gombbal térjen vissza az alapértelmezett értékekhez.

![metrika képe](./media/metrics-charts/00014-manually-set-granularity.png)

> [!WARNING]
> Az y tengely határainak lezárása azon diagramok esetében, amelyek egy adott időszakban különböző számlálásokat vagy összegeket követnek nyomon (és így szám, összeg, minimum vagy maximális összesítések használatával), általában rögzített időrészletességet kell megadni az automatikus alapértelmezések helyett. Erre azért van szükség, mert a diagramok értékei megváltoznak, amikor a felhasználó automatikusan módosítja az időt a böngészőablak átméretezésével, vagy az egyik képernyőfelbontásról a másikra. Az idő részletességének ebből eredő változása a diagram megjelenését módosítja, és érvényteleníti az y tengely tartományának aktuális kijelölését.

## <a name="change-colors-of-chart-lines"></a>Diagramvonalak színeinek módosítása

A diagramok konfigurálása után a diagramvonalakhoz automatikusan hozzárendelünk egy színt az alapértelmezett palettáról. Meg tudod változtatni ezeket a színeket.

A diagramvonal színének módosításához kattintson a diagramnak megfelelő jelmagyarázat színes sávjára. Megnyílik a színválasztó párbeszédpanel. A színválasztóval konfigurálhatja a vonal színét.

A diagram színeinek konfigurálása után ez így is marad, amikor a diagramot az irányítópultra tűzi. A következő szakasz bemutatja, hogyan rögzítheti a diagramot.

> [!NOTE]
> Kiadási és közzétételi ütemezéskorlátai miatt a diagramvonalak színének módosítása esetén az Azure Portal [https://portal.azure.com/?feature.colorpicker=true](https://portal.azure.com/?feature.colorpicker=true)indításakor ideiglenesen át kell adni egy speciális **paramétert ?feature.colorpicker=true** . Ez a korlátozás hamarosan megszűnik. 

![metrika képe](./media/metrics-charts/018.png)

## <a name="pin-charts-to-dashboards"></a>Diagramok rögzítése irányítópultokra

A diagramok konfigurálása után érdemes lehet hozzáadni az irányítópultok, hogy megtekinthesse újra, esetleg más figyelési telemetriai adatok kontextusában, vagy ossza meg a csapat.

Konfigurált diagram rögzítése irányítópulton:

A diagram konfigurálása után kattintson a diagram jobb felső sarkában található **Diagramműveletek** menüre, majd a Rögzítés az **irányítópultra parancsra.**

![metrika képe](./media/metrics-charts/00013.png)

## <a name="create-alert-rules"></a>Riasztási szabályok létrehozása

A beállított feltételek segítségével a metrikák at egy metrika alapú riasztási szabály alapjaként jelenítheti meg. Az új riasztási szabály tartalmazza a célerőforrást, a metrikát, a felosztást és a szűrődimenziókat a diagramról. Ezeket a beállításokat később módosíthatja a riasztási szabály létrehozásának ablaktábláján.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>Új riasztási szabály létrehozásához kattintson az **Új riasztási szabály gombra.**

![Pirossal kiemelve új riasztási szabály gomb](./media/metrics-charts/015.png)

A rendszer a riasztási szabály létrehozásának ablaktáblájára kerül, amely a diagram alapul szolgáló metrikadimenzióit előre kitölti, hogy megkönnyítse az egyéni riasztási szabályok létrehozását.

![Riasztási szabály létrehozása](./media/metrics-charts/016.png)

Tekintse meg ezt a [cikket,](alerts-metric.md) ha többet szeretne megtudni a metrikariasztások beállításáról.

## <a name="troubleshooting"></a>Hibaelhárítás

*Nem látok adatokat a diagramon.*

* A szűrők az ablaktábla összes diagramjára vonatkoznak. Győződjön meg arról, hogy miközben egy diagramra összpontosít, nem állított be olyan szűrőt, amely kizárja a másik összes adatát.

* Ha különböző szűrőket szeretne beállítani a különböző diagramokon, hozza létre őket különböző pengelapokban, mentse őket külön kedvencekként. Ha szeretné, rögzítheti őket az irányítópulton, hogy egymás mellett láthassa őket.

* Ha a diagramot olyan tulajdonság szerint szegmentálja, amely nincs definiálva a mutatón, akkor a diagramon nem lesz semmi. Próbálja meg törölni a szegmentálást (felosztást), vagy válasszon másik tulajdonságot.

## <a name="next-steps"></a>További lépések

  Az [egyéni KPI-irányítópultok létrehozása című, a](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) metrikákkal rendelkező, végrehajtható irányítópultok létrehozásának bevált módszereiről olvashat.

