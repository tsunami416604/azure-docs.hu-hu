---
title: Az Azure Monitor metrikadiagramjainak hibaelhárítása
description: A metrikadiagramok létrehozásával, testreszabásával és értelmezésével kapcsolatos problémák elhárítása
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: e1ad4e53596b8228bdef5beb18aa250a9512c49f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659662"
---
# <a name="troubleshooting-metrics-charts"></a>Metrikadiagramok hibaelhárítása

Ezt a cikket akkor használhatja, ha problémákba ütközik a diagramok létrehozásával, testreszabásával vagy értelmezésével kapcsolatban az Azure Metrics Explorer alkalmazásban. Ha még nem ismeri a metrikákat, ismerje meg a [metrikakezelő](metrics-getting-started.md) és a [metrikakezelő speciális funkcióinak első lépéseit.](metrics-charts.md) A konfigurált metrikadiagramokra [vonatkozó példákat](metric-chart-samples.md) is láthat.

## <a name="cant-find-your-resource-to-select-it"></a>Nem található az erőforrás a kijelöléshez

Az **Erőforrás kiválasztása** elemre kattintott, de nem látja az erőforrását az erőforrás-választó párbeszédpanelen.

**Megoldás:** Metrika-kezelő megköveteli, hogy válassza ki az előfizetések és erőforráscsoportok, mielőtt felsorolja a rendelkezésre álló erőforrásokat. Ha nem látja az erőforrását:

1. Győződjön meg arról, hogy a megfelelő előfizetést választotta ki az **Előfizetés** legördülő menüben. Ha az előfizetés nincs a listában, kattintson a **Címtár + Előfizetés beállításai** elemre és adja hozzá az erőforrását tartalmazó előfizetést.

1. Győződjön meg arról, hogy a megfelelő erőforráscsoport van kiválasztva.
    > [!WARNING]
    > A legjobb teljesítmény érdekében a metrikaböngésző első megnyitásakor az **Erőforráscsoport** legördülő menüben nincs előre kijelölt erőforráscsoport. Legalább egy csoportot ki kell választania ahhoz, hogy megjelenjenek az erőforrások.

## <a name="chart-shows-no-data"></a>A diagram nem jelenít meg adatokat

Előfordulhat, hogy a diagramok nem jelenítenek meg adatokat a megfelelő erőforrások és mutatók kiválasztása után. Ezt a viselkedést a következő okok oka idálhatja:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>A Microsoft.Insights erőforrás-szolgáltató nincs regisztrálva az előfizetéshez

A metrikák megtekintéséhez a *Microsoft.Insights* erőforrás-szolgáltatónak regisztrálva kell lennie az előfizetésében. Sok esetben a regisztrálás automatikus (azaz egy riasztási szabály konfigurálása után bármely erőforráshoz testreszabhatja a diagnosztikai beállításokat, vagy automatikus skálázási szabályt konfigurálhat). Ha a Microsoft.Insights erőforrás-szolgáltató nincs regisztrálva, manuálisan kell regisztrálnia az [Azure-erőforrás-szolgáltatók és -típusok](../../azure-resource-manager/management/resource-providers-and-types.md)című csoportban ismertetett lépések végrehajtásával.

**Megoldás:** Nyissa meg **az Előfizetések**, **Erőforrás-szolgáltatók** lapot, és ellenőrizze, hogy a *Microsoft.Insights* regisztrálva van-e az előfizetéséhez.

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Nem rendelkezik megfelelő hozzáférési engedélyekkel az erőforráshoz

Az Azure-ban a metrikák hozzáférését [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md) vezérli. Az erőforrások metrikáinak megtekintéséhez a [figyelési olvasó](../../role-based-access-control/built-in-roles.md#monitoring-reader), a [figyelési közreműködő](../../role-based-access-control/built-in-roles.md#monitoring-contributor) vagy a [közreműködő](../../role-based-access-control/built-in-roles.md#contributor) szerepkörhöz kell tartoznia.

**Megoldás:** Győződjön meg arról, hogy rendelkezik a megfelelő engedélyekkel ahhoz az erőforráshoz, amelyből a metrikákat vizsgálja.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>Az erőforrás nem bocsátott ki metrikákat a kiválasztott időtartományban

Néhány erőforrás nem bocsát ki folyamatosan metrikákat. Az Azure például nem gyűjt metrikákat a leállított virtuális gépekről. Más erőforrások csak olyan esetben bocsátanak ki metrikákat, amikor valamilyen feltétel teljesül. A tranzakciók feldolgozási idejét megjelenítő metrikákhoz például legalább egy tranzakció szükséges. Ha nincsenek tranzakciók a kiválasztott időtartományban, akkor a diagram üres lesz. És bár az Azure-ban a legtöbb metrika gyűjtése percenként történik, egyes metrikáké ritkábban. Az adott metrika dokumentációjában talál további részleteket a megtekinteni kívánt metrikáról.

**Megoldás:** Módosítsa a diagram idejét szélesebb tartományra. Az "Utolsó 30 nap" beállítással nagyobb időrészletességgel (vagy az "Automatikus időrészletesség" opcióval) kezdheti.

### <a name="you-picked-a-time-range-greater-than-30-days"></a>30 napnál nagyobb időtartományt választott

[Az Azure-ban a legtöbb metrika 93 napig érhető el](data-platform-metrics.md#retention-of-metrics). Nem kérdezheti le azonban 30 napnál nagyobb időszak adatait egyetlen diagramban. Ez a korlátozás a [naplóalapú metrikákra](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) nem vonatkozik.

**Megoldás:** Ha üres diagramot lát, vagy a diagram csak a metrikaadatok egy részét jeleníti meg, ellenőrizze, hogy az időválasztó kezdő- és záró dátumai közötti különbség nem haladja-e meg a 30 napos intervallumot.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Minden metrikaérték az y tengely zárolt tartományán kívülre esett

A [diagram y tengelye határainak zárolásával](metrics-charts.md#lock-boundaries-of-chart-y-axis) akaratlanul is azt állíthatja be, hogy a diagram megjelenítési területén ne jelenjen meg a diagramvonal. Ha az y tengely például a 0% és az 50% közötti tartományban van zárolva, és a metrika állandóan 100% értékű, akkor a vonal mindig a látható területen kívülre esik, így a diagram üresnek látszik.

**Megoldás:** Ellenőrizze, hogy a diagram y-tengelyhatárai nincsenek-e a metrikaértékek tartományán kívülre zárva. Ha az y tengely határai zárolva vannak, érdemes lehet ideiglenesen alaphelyzetbe állítani őket annak érdekében, hogy a metrikaértékek ne essenek a diagramtartományon kívül. Az y tengely tartományának zárolása nem ajánlott az **összeg**, **min** és **max** összesítésű diagramok automatikus részletessége esetén, mert az értékeik a böngészőablak átméretezésekor vagy az egyik képernyőfelbontásról egy másikra lépéskor a részletességgel együtt változnak. A részletesség módosításakor a diagram megjelenítési területe üres maradhat.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>Vendég operációsrendszer-metrikát keres, de nem engedélyezte az Azure diagnosztikai bővítményt

A **vendég operációs rendszerre** vonatkozó metrikák gyűjteményéhez konfigurálni kell az Azure Diagnostics bővítményt, vagy az erőforrás **Diagnosztikai beállítások** panelén kell azt engedélyezni.

**Megoldás:** Ha az Azure Diagnostics Extension engedélyezve van, de továbbra sem látja a metrikákat, kövesse az [Azure Diagnostics Extension hibaelhárítási útmutatójában](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal)ismertetett lépéseket. Lásd még a [Vendég operációsrendszer névtere és metrikák kiválasztásának hibaelhárítási lépéseit.](metrics-troubleshoot.md#cannot-pick-guest-os-namespace-and-metrics)

## <a name="error-retrieving-data-message-on-dashboard"></a>"Hiba az adatok beolvasásakor" üzenet az irányítópulton

Ez a probléma akkor fordulhat elő, amikor az irányítópult olyan metrikával jött létre, amely később elavult, és el lett távolítva az Azure-ból. Ennek ellenőrzéséhez nyissa meg az erőforrás **Metrikák** lapját, és ellenőrizze az elérhető metrikákat a metrikaválasztóban. Ha nem jelenik meg metrika, akkor a metrika el lett távolítva az Azure-ból. Egy metrika elavulása rendszerint azt jelenti, hogy rendelkezésre áll egy új, jobb metrika, amely hasonlóan mutatja be az erőforrás állapotát.

**Megoldás:** Frissítse a hibás csempét úgy, hogy egy másik mérőszámot választ az irányítópulton a diagramhoz. [Megtekintheti az Azure-szolgáltatásokhoz elérhető metrikák listáját](metrics-supported.md).

## <a name="chart-shows-dashed-line"></a>A diagram szaggatott vonalat jelenít meg

Az Azure metrikák diagramok használata szaggatott vonal stílus jelzi, hogy van egy hiányzó érték (más néven "null érték") két ismert időgabona-adatpont között. Ha például az időválasztóban az "1 perces" időrészletességet választotta, de a metrikát 07:26, 07:27, 07:29 és 07:30 (a második és a harmadik adatpont közötti perckülönbség) jelentették, akkor egy szaggatott vonal csatlakozik a 07:27 és 07:29 számhoz, és egy szilárd vonal csatlakozik minden más adatpontot. A szaggatott vonal nullára csökken, ha a metrika **a számláló** és **az összegösszesítés** thasználja. Az **átlagos**, **min** vagy **max** összesítések esetében a szaggatott vonal két legközelebbi ismert adatpontot köt össze. Ha pedig az adatok a diagram jobb vagy bal szélén hiányoznak, akkor rendszer meghosszabbítja a szaggatott vonalat a hiányzó adatpont irányába.
  ![metrika képe](./media/metrics-troubleshoot/missing-data-point-line-chart.png)

**Megoldás:** Ez a viselkedés szándékos. Ez a hiányzó adatpontok azonosításához hasznos. A vonaldiagram kiváló választás a nagy sűrűségű mérőszámok trendjeinek megjelenítéséhez, de nehéz lehet értelmezni a ritka értékekkel rendelkező mutatókat, különösen akkor, ha az értékek időgabonával való összevetése fontos. A szaggatott vonal megkönnyíti az ilyen diagramok leolvasását, ha azonban a diagram még mindig nem egyértelmű, érdemes lehet más diagramtípussal megtekinteni a metrikákat. Például egy ugyanahhoz a mérőszámhoz készült szétszórt nyomtatási diagram világosan megmutatja minden egyes szemcseszemeket, ha ![csak egy pontot jelenít meg, ha van egy érték, és teljesen kihagyja az adatpontot, ha az érték hiányzik: metrikakép](./media/metrics-troubleshoot/missing-data-point-scatter-chart.png)

   > [!NOTE]
   > Ha továbbra is vonaldiagramot szeretne használni a metrikához, akkor mozgassa az egérmutatót a diagram fölé, így az egérmutató helyén lévő adatpont kiemelése révén könnyebben felmérheti az időrészletességet.

## <a name="chart-shows-unexpected-drop-in-values"></a>A diagram az értékek váratlan csökkenését mutatja

Sok esetben a metrikaértékek észlelt csökkenése a diagramon látható adatokkal kapcsolatos félreértésből ered. Az összegek vagy a számok csökkenése félrevezető lehet, ha a diagramon a legfrissebb percek láthatók, mert az Azure még nem kapta meg vagy nem dolgozta fel a legutóbbi metrikaadatpontokat. A szolgáltatástól függően a metrikák feldolgozásának késése néhány perces lehet. Az 1 vagy 5 perces részletességgel rendelkező legutóbbi időtartományt mutató diagramok esetében az érték csökkenése az ![elmúlt néhány percben észrevehetőbbé válik: metrikus kép](./media/metrics-troubleshoot/drop-in-values.png)

**Megoldás:** Ez a viselkedés szándékos. Úgy gondoljuk, hogy az adatok lehető legkorábban történő megjelenítése akkor is előnyös, ha az adatok *részlegesek* vagy *nem teljesek*. Ennek köszönhetően hamarabb juthat fontos következtetésekre, és azonnal megkezdheti a vizsgálatot. Egy, a hibák számát megjelenítő metrika esetén például az X részleges érték elárulja, hogy egy adott percben legalább X hiba fordult elő. Azonnal megkezdheti a probléma vizsgálatát, és nem kell megvárnia, hogy pontosan hány hiba történt ebben a percben, ami talán nem is olyan fontos. A diagram a teljes adatkészlet fogadásakor frissül, de ekkor új nem teljes adatpontokat is jeleníthet meg a még frissebb percekből.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>A vendég operációsrendszer névtere és metrikák nem választhatók ki.

A virtuális gépek és a virtuálisgép-méretezési csoportok két metrikakategóriával rendelkeznek: az Azure-üzemeltetési környezet által gyűjtött **Virtuálisgép-gazdagépek** metrikái és a **vendég operációsrendszer(klasszikus)** metrikák, amelyeket a virtuális gépeken futó [figyelési ügynök](agents-overview.md) gyűjt. A figyelőügynök az [Azure Diagnostics bővítmény](diagnostics-extension-overview.md) engedélyezésével telepíthető.

Alapértelmezés szerint a Vendég operációs rendszer típusú metrikákat az Azure-tárfiók tartalmazza, amelyet az erőforrás **Diagnosztikai beállítások** lapján választhat ki. Ha a vendég operációs rendszer metrikáit nem gyűjti a rendszer, vagy a metrikaböngésző nem tudja elérni őket, akkor csak a **Virtuálisgép-gazda** metrika névterét látja:

![metrika képe](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**Megoldás:** Ha nem látja **a vendég operációsrendszer (klasszikus)** névteret és a metrikákat a metrikakezelőben:

1. Győződjön meg róla, hogy az [Azure Diagnostics bővítmény](diagnostics-extension-overview.md) engedélyezve van és be van állítva a metrikák gyűjtésére.
    > [!WARNING]
    > A [Log Analytics ügynök](agents-overview.md#log-analytics-agent) (más néven Microsoft Monitoring Agent, vagy MMA) használatával a **Vendég operációs rendszer** nem küldhető tárfiókba.

1. Győződjön meg arról, hogy a **Microsoft.Insights** erőforrás-szolgáltató regisztrálva van [az előfizetéshez.](metrics-troubleshoot.md#microsoftinsights-resource-provider-isnt-registered-for-your-subscription)

1. Győződjön meg arról, hogy a tárfiókot nem védi a tűzfal. Az Azure Portalnak hozzá kell férnie a tárfiókhoz, hogy lekérhesse a metrikai adatokat és összeállíthassa a diagramokat.

1. Az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) használatával ellenőrizze, hogy a metrikák a tárfiókba kerülnek-e. Ha a metrikák gyűjtése nem történik meg, kövesse az [Azure Diagnostics bővítmény hibaelhárítási útmutatóját](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal).

## <a name="next-steps"></a>További lépések

* [További információ a Metric Explorer első lépéseiről](metrics-getting-started.md)
* [További információ a Metric Explorer speciális szolgáltatásairól](metrics-charts.md)
* [Az Azure-szolgáltatásokhoz elérhető metrikák listájának megtekintése](metrics-supported.md)
* [A konfigurált diagramok példáinak megtekintése](metric-chart-samples.md)
