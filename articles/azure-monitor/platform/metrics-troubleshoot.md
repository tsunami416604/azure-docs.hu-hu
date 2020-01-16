---
title: Azure Monitor metrikai diagramok hibaelhárítása
description: A metrikai diagramok létrehozásával, testreszabásával vagy értelmezésével kapcsolatos hibák elhárítása
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 072e62d89e8febc4837c10874398daea3b8114ed
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75974872"
---
# <a name="troubleshooting-metrics-charts"></a>Metrikai diagramok hibaelhárítása

Ez a cikk akkor használható, ha a diagramok létrehozásával, testreszabásával vagy értelmezésével kapcsolatos problémákba ütközik az Azure mérőszámok Explorerben. Ha még nem ismeri a metrikákat, ismerkedjen meg a mérőszámok [Explorerrel](metrics-getting-started.md) és [a metrikák Explorer speciális funkcióival](metrics-charts.md). Megtekintheti a konfigurált metrikai diagramokra vonatkozó [példákat](metric-chart-samples.md) is.

## <a name="cant-find-your-resource-to-select-it"></a>Nem található az erőforrás a kiválasztáshoz

Az **Erőforrás kiválasztása** elemre kattintott, de nem látja az erőforrását az erőforrás-választó párbeszédpanelen.

**Megoldás:** A metrikák Explorer használatához az elérhető erőforrások listázása előtt ki kell választania az előfizetéseket és az erőforráscsoportokat. Ha nem látja az erőforrását:

1. Győződjön meg arról, hogy a megfelelő előfizetést választotta ki az **Előfizetés** legördülő menüben. Ha az előfizetés nincs a listában, kattintson a **Címtár + Előfizetés beállításai** elemre és adja hozzá az erőforrását tartalmazó előfizetést.

1. Győződjön meg arról, hogy a megfelelő erőforráscsoport van kiválasztva.
    > [!WARNING]
    > A legjobb teljesítmény érdekében a metrikák Explorer első megnyitásakor az **erőforráscsoport** legördülő menüjében nincsenek előre kiválasztott erőforráscsoportok. Legalább egy csoportot ki kell választania ahhoz, hogy megjelenjenek az erőforrások.

## <a name="chart-shows-no-data"></a>A diagram nem jelenít meg adattípust

Időnként előfordulhat, hogy a diagramok nem jelenítenek meg adatokat a megfelelő erőforrások és mérőszámok kiválasztása után. Ezt a viselkedést az alábbi okok közül több is okozhatja:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>Az előfizetéshez nincs regisztrálva a Microsoft. inbetekintés erőforrás-szolgáltatója

A metrikák megtekintéséhez a *Microsoft.Insights* erőforrás-szolgáltatónak regisztrálva kell lennie az előfizetésében. Sok esetben a regisztrálás automatikus (azaz egy riasztási szabály konfigurálása után bármely erőforráshoz testreszabhatja a diagnosztikai beállításokat, vagy automatikus skálázási szabályt konfigurálhat). Ha a Microsoft. inbetekintés erőforrás-szolgáltató nincs regisztrálva, akkor manuálisan kell regisztrálnia az [Azure erőforrás-szolgáltatók és típusok](../../azure-resource-manager/management/resource-providers-and-types.md)című témakörben ismertetett lépések szerint.

**Megoldás:** Nyissa meg az **előfizetések**, az **erőforrás-szolgáltatók** fület, és ellenőrizze, hogy regisztrálva van-e a *Microsoft. ininsight* az előfizetéséhez

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Nem rendelkezik elegendő hozzáférési jogosultsággal az erőforráshoz

Az Azure-ban a metrikák hozzáférését [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md) vezérli. Az erőforrások metrikáinak megtekintéséhez a [figyelési olvasó](../../role-based-access-control/built-in-roles.md#monitoring-reader), a [figyelési közreműködő](../../role-based-access-control/built-in-roles.md#monitoring-contributor) vagy a [közreműködő](../../role-based-access-control/built-in-roles.md#contributor) szerepkörhöz kell tartoznia.

**Megoldás:** Győződjön meg arról, hogy rendelkezik megfelelő engedélyekkel ahhoz az erőforráshoz, amelynek a mérőszámait vizsgálja.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>Az erőforrás nem bocsát ki mérőszámokat a kijelölt időtartományban

Néhány erőforrás nem ad ki folyamatosan metrikákat. Az Azure például nem gyűjt metrikákat a leállított virtuális gépekről. Más erőforrások csak olyan esetben adnak ki metrikákat, amikor valamilyen feltétel teljesül. Az egy tranzakció feldolgozási idejét megjelenítő metrikához például legalább egy tranzakció szükséges. Ha nincsenek tranzakciók a kiválasztott időtartományban, akkor a diagram üres lesz. És bár az Azure-ban a legtöbb metrika gyűjtése percenként történik, egyes metrikáké ritkábban. Az adott metrika dokumentációjában talál további részleteket a megtekinteni kívánt metrikáról.

**Megoldás:** A diagram időpontjának módosítása szélesebb tartományra Az "elmúlt 30 nap" kezdettől kezdve nagyobb részletességgel (vagy az "automatikus időrészletesség" lehetőséggel) lehet kezdeni.

### <a name="you-picked-a-time-range-greater-than-30-days"></a>30 napnál hosszabb időintervallumot választott ki

[Az Azure-ban a legtöbb metrika 93 napig érhető el](data-platform-metrics.md#retention-of-metrics). Nem kérdezheti le azonban 30 napnál nagyobb időszak adatait egyetlen diagramban. Ez a korlátozás a [naplóalapú metrikákra](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) nem vonatkozik.

**Megoldás:** Ha üres diagramot lát, vagy a diagram csak a metrikai adatok egy részét jeleníti meg, ellenőrizze, hogy az időválasztóban a kezdő és a záró dátumok közötti különbség nem haladja-e meg a 30 napos időközt.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Az összes metrikai érték a zárolt y tengely tartományán kívül esik

A [diagram y tengelye határainak zárolásával](metrics-charts.md#lock-boundaries-of-chart-y-axis) akaratlanul is azt állíthatja be, hogy a diagram megjelenítési területe ne jelenítse meg a diagramvonalat. Ha az y tengely például a 0% és az 50% közötti tartományban van zárolva, és a metrika állandóan 100% értékű, akkor a vonal mindig a látható területen kívülre esik, így a diagram üresnek látszik.

**Megoldás:** Ellenőrizze, hogy a diagram y tengelyének határai nem zárolva vannak-e a metrikai értékek tartományán kívül. Ha az y tengely határai zárolva vannak, érdemes lehet ideiglenesen alaphelyzetbe állítani őket annak érdekében, hogy a metrikaértékek ne essenek a diagramtartományon kívül. Az y tengely tartományának zárolása nem ajánlott az **összeg**, **min** és **max** összesítésű diagramok automatikus részletessége esetén, mert az értékeik a böngészőablak átméretezésekor vagy az egyik képernyőfelbontásról egy másikra lépéskor a részletességgel együtt változnak. A részletesség módosításakor a diagram megjelenítési területe üres maradhat.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>Egy vendég operációs rendszer metrikáját keresi, de nem engedélyezte az Azure diagnosztikai bővítményt

A **vendég operációs rendszer** metrikák gyűjteményéhez konfigurálni kell az Azure diagnosztikai bővítményt, vagy az erőforrás **Diagnosztikai beállítások** panelén kell azt engedélyezni.

**Megoldás:** Ha a Azure Diagnostics bővítmény engedélyezve van, de továbbra sem tudja megtekinteni a metrikákat, kövesse az [Azure Diagnostics bővítmény hibaelhárítási útmutatójában](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal)ismertetett lépéseket. Lásd még: a [vendég operációs rendszer névterének és metrikáinak nem](metrics-troubleshoot.md#cannot-pick-guest-os-namespace-and-metrics) kiválasztására vonatkozó hibaelhárítási lépések

## <a name="error-retrieving-data-message-on-dashboard"></a>"Hiba történt az adatok beolvasása közben" üzenet az irányítópulton

Ez a probléma akkor fordulhat elő, amikor az irányítópult olyan metrikával jött létre, amely később elavult, és el lett távolítva az Azure-ból. Ennek ellenőrzéséhez nyissa meg az erőforrás **Metrikák** lapját, és ellenőrizze az elérhető metrikákat a metrikaválasztóban. Ha nem jelenik meg metrika, akkor a metrika el lett távolítva az Azure-ból. Egy metrika elavulása rendszerint azt jelenti, hogy rendelkezésre áll egy új, jobb metrika, amely hasonlóan mutatja be az erőforrás állapotát.

**Megoldás:** Módosítsa a hibás csempét úgy, hogy kiválaszt egy alternatív metrikát a diagramhoz az irányítópulton. [Megtekintheti az Azure-szolgáltatásokhoz elérhető metrikák listáját](metrics-supported.md).

## <a name="chart-shows-dashed-line"></a>A diagram szaggatott vonalat mutat

Az Azure-metrikák diagramjai szaggatott vonal stílusát használják annak jelzésére, hogy a hiányzó érték (más néven "Null érték") két ismert időszelet adatpontja között van. Ha például az időválasztóban az "1 perc" idő részletességét adta meg, de a mérőszámot 07:26, 07:27, 07:29 vagy 07:30 értéken jelentették (jegyezze fel a második és harmadik adatpontok közötti percenkénti eltérést), akkor egy szaggatott vonal kapcsolódik az 07:27-hoz és az 07:29-hoz, és egy folytonos vonal kapcsolódik az összes többi adatpont. A szaggatott vonal nullára csökken, ha a metrika a **darabszámot** és a **Sum** összesítést használja. Az **AVG**, **min** vagy **Max** összesítések esetében a szaggatott vonal két legközelebbi ismert adatpontot kapcsol össze. Ha pedig az adatok a diagram jobb vagy bal szélén hiányoznak, akkor rendszer meghosszabbítja a szaggatott vonalat a hiányzó adatpont irányába.
  ![metrikai rendszerkép](./media/metrics-troubleshoot/missing-data-point-line-chart.png)

**Megoldás:** Ez a viselkedés a tervezés szerint történik. Ez a hiányzó adatpontok azonosításához hasznos. A vonalas diagram kiváló választás a nagy sűrűségű mérőszámok trendjeinek megjelenítéséhez, de nehéz lehet értelmezni a ritka értékekkel rendelkező metrikákat, különösen akkor, ha fontosak az időgabona-vel kapcsolatos kapcsolódó értékek. A szaggatott vonal megkönnyíti az ilyen diagramok leolvasását, ha azonban a diagram még mindig nem egyértelmű, érdemes lehet más diagramtípussal megtekinteni a metrikákat. Az egyazon mérőszámhoz tartozó elszórt diagram például világosan mutatja, hogy az egyes időkeretek csak akkor jelennek meg, ha van érték, és az adatpontot teljesen kihagyja, ha hiányzik az érték: ![metrikai képet](./media/metrics-troubleshoot/missing-data-point-scatter-chart.png)

   > [!NOTE]
   > Ha továbbra is vonaldiagramot szeretne használni a metrikához, akkor mozgassa az egérmutatót a diagram fölé, így az egérmutató helyén lévő adatpont kiemelése révén könnyebben felmérheti az időrészletességet.

## <a name="chart-shows-unexpected-drop-in-values"></a>A diagram az értékek váratlan csökkenését mutatja.

Sok esetben a metrikaértékek észlelt csökkenése a diagramon látható adatokkal kapcsolatos félreértésből ered. Az összegek vagy a számok csökkenése félrevezető lehet, ha a diagramon a legfrissebb percek láthatók, mert az Azure még nem kapta meg vagy nem dolgozta fel a legutóbbi metrikaadatpontokat. A szolgáltatástól függően a metrikák feldolgozásának késése néhány perces lehet. A legutóbbi időtartományt egy 1 vagy 5 perces részletességgel ábrázoló diagramok esetében az elmúlt néhány percben az érték egy cseppe lesz észrevehető: ![metrikai kép](./media/metrics-troubleshoot/drop-in-values.png)

**Megoldás:** Ez a viselkedés a tervezés szerint történik. Úgy gondoljuk, hogy az adatok lehető legkorábban történő megjelenítése akkor is előnyös, ha az adatok *részlegesek* vagy *nem teljesek*. Ennek köszönhetően hamarabb juthat fontos következtetésekre, és azonnal megkezdheti a vizsgálatot. Egy, a hibák számát megjelenítő metrika esetén például az X részleges érték elárulja, hogy egy adott percben legalább X hiba fordult elő. Azonnal megkezdheti a probléma vizsgálatát, és nem kell megvárnia, hogy pontosan hány hiba történt ebben a percben, ami talán nem is olyan fontos. A diagram a teljes adatkészlet fogadásakor frissül, de ekkor új nem teljes adatpontokat is jeleníthet meg a még frissebb percekből.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>A vendég operációs rendszer névtere és mérőszámai nem választhatók ki

A virtuális gépek és a virtuálisgép-méretezési csoportok két kategóriába sorolhatók: az Azure üzemeltetési környezete által összegyűjtött **virtuálisgép-gazdagép** -metrikák, valamint a virtuális gépeken futó [figyelési ügynök](agents-overview.md) által gyűjtött **vendég operációs rendszer (klasszikus)** mérőszámok. A figyelőügynök az [Azure Diagnostics bővítmény](diagnostics-extension-overview.md) engedélyezésével telepíthető.

Alapértelmezés szerint a Vendég operációs rendszer típusú metrikákat az Azure-tárfiók tartalmazza, amelyet az erőforrás **Diagnosztikai beállítások** lapján választhat ki. Ha a vendég operációs rendszer metrikáit nem gyűjti a rendszer, vagy a metrikaböngésző nem tudja elérni őket, akkor csak a **Virtuálisgép-gazda** metrika névterét látja:

![metrika képe](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**Megoldás:** Ha nem látja a **vendég operációs rendszer (klasszikus)** névterét és mérőszámait a metrikák Explorerben:

1. Győződjön meg róla, hogy az [Azure Diagnostics bővítmény](diagnostics-extension-overview.md) engedélyezve van és be van állítva a metrikák gyűjtésére.
    > [!WARNING]
    > A [Log Analytics ügynök](agents-overview.md#log-analytics-agent) (más néven Microsoft Monitoring Agent, vagy MMA) használatával a **Vendég operációs rendszer** nem küldhető tárfiókba.

1. Győződjön meg arról, hogy a **Microsoft. inbetekintés** erőforrás-szolgáltató [regisztrálva van az előfizetéséhez](metrics-troubleshoot.md#microsoftinsights-resource-provider-isnt-registered-for-your-subscription).

1. Győződjön meg arról, hogy a tárfiókot nem védi a tűzfal. Az Azure Portalnak hozzá kell férnie a tárfiókhoz, hogy lekérhesse a metrikai adatokat és összeállíthassa a diagramokat.

1. Az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) használatával ellenőrizze, hogy a metrikák a tárfiókba kerülnek-e. Ha a metrikák gyűjtése nem történik meg, kövesse az [Azure Diagnostics bővítmény hibaelhárítási útmutatóját](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal).

## <a name="next-steps"></a>Következő lépések

* [Ismerkedés a metrikus Explorer használatába](metrics-getting-started.md)
* [A metrika Explorer speciális funkcióinak megismerése](metrics-charts.md)
* [Az Azure-szolgáltatásokhoz elérhető metrikák listájának megtekintése](metrics-supported.md)
* [A konfigurált diagramok példáinak megtekintése](metric-chart-samples.md)
