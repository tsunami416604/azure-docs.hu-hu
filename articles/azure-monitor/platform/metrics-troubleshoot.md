---
title: Hibaelhárítás az Azure Monitor mérőszám-diagramok
description: A létrehozását, testreszabása és értelmezése mérőszám-diagramok hibáinak elhárítása
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: cff1bbefc3c54f7f9c02c646bd9eef528fe28c73
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939573"
---
# <a name="troubleshooting-metrics-charts"></a>Mérőszámdiagramok hibaelhárítása

Ez a cikk akkor használja, ha közben felmerülő hibák létrehozásával, testreszabása vagy-diagramok a Azure metrikaböngésző értelmezése. Ha most ismerkedik a metrikákhoz, ismerje [Ismerkedés a metrikaböngésző](metrics-getting-started.md) és [funkcióira metrikaböngészőt speciális](metrics-charts.md). Emellett megtekintheti [példák](metric-chart-samples.md) a konfigurált metrikus diagramok.

## <a name="cant-find-your-resource-to-select-it"></a>Jelölje ki az erőforrás nem található

Amire kattintott a **válasszon ki egy erőforrást** gombra, de az erőforrás az erőforrás-választó párbeszédpanelen nem jelenik meg.

**Megoldás:** Metrikaböngésző megköveteli, hogy az előfizetések és -erőforráscsoportok megadása előtt válasszon ki rendelkezésre álló erőforrások listázása. Ha nem látja az erőforrás:

1. Győződjön meg arról, hogy kiválasztotta a megfelelő előfizetés a **előfizetés** legördülő listából. Ha az előfizetés nincs felsorolva, kattintson a a **címtár és előfizetés beállításainak** és az erőforrás-előfizetés hozzáadása.

1. Győződjön meg arról, hogy kiválasztotta a megfelelő erőforráscsoportot.
    > [!WARNING]
    > A legjobb teljesítmény érdekében a metrikaböngészőben első megnyitásakor a **erőforráscsoport** legördülő nincs előre kijelölt erőforrás csoporttal rendelkezik. Ahhoz, hogy olyan erőforrások válasszon ki legalább egy csoporthoz.

## <a name="chart-shows-no-data"></a>Diagram mutatja nincsenek adatok

Néha az a diagramok előfordulhat, hogy megjelenítése az adatok nem megfelelő erőforrások és a metrikák kiválasztása után. Ez a viselkedés a következő okok miatt számos oka lehet:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>Microsoft.Insights erőforrás-szolgáltató az előfizetés nincs regisztrálva.

A metrikák felfedezése igényel *Microsoft.Insights* erőforrás-szolgáltató regisztrálva az előfizetésében. Sok esetben regisztrálódik automatikusan (azaz riasztási szabály konfigurálása, bármely erőforrás diagnosztikai beállításainak testreszabása, vagy után automatikus skálázási szabály konfigurálása). Ha nincs regisztrálva a Microsoft.Insights erőforrás-szolgáltató, manuálisan kell regisztrálni, a következő lépésekben ismertetett [Azure-erőforrás-szolgáltatókat és típusaikat](../../azure-resource-manager/resource-manager-supported-services.md).

**Megoldás:** Nyissa meg **előfizetések**, **erőforrás-szolgáltatók** lapra, és ellenőrizze, hogy *Microsoft.Insights* az előfizetés regisztrálva van.

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Nincs elegendő hozzáférési jogosultsága ahhoz az erőforrás

Az Azure-ban mérőszámok való hozzáférést vezérlik [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md). Tagjának kell lennie [olvasó figyelése](../../role-based-access-control/built-in-roles.md#monitoring-reader), [közreműködő figyelése](../../role-based-access-control/built-in-roles.md#monitoring-contributor), vagy [közreműködői](../../role-based-access-control/built-in-roles.md#contributor) fedezheti fel az összes erőforrás metrikáit.

**Megoldás:** Győződjön meg arról, hogy megfelelő engedélyekkel rendelkezik, amelyről a metrikákat vizsgál meg az erőforrás.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>Az erőforrás a kiválasztott időtartományban nem küldik metrikák

Néhány erőforrás folyamatosan nem szolgáltat a metrikákat. Például Azure nem gyűjt metrikákat a leállított virtuális gépek. Egyéb erőforrások előfordulhat, hogy a metrikák kibocsátható, csak akkor, amikor bizonyos állapot akkor fordul elő. Például a tranzakciók feldolgozása tekintetében metrika legalább egy tranzakció szükséges. Ha a kijelölt időtartományban található tranzakciók, akkor a diagram természetes módon lehet üres. Emellett Bár a metrikák az Azure-ban a legtöbb percenként gyűjtött, van azonban néhány ritkábban gyűjtött. Ismerkedés a kívánt metrika kapcsolatos további információért a metrika dokumentációjában talál.

**Megoldás:** A diagram idejének módosításához szélesebb köre. Előfordulhat, hogy indítsa el az "Elmúlt 30 nap" használatával egy nagyobb idő részletessége (vagy az "automatikus idő részletessége" lehetőséget a függő entitások).

### <a name="you-picked-a-time-range-greater-than-30-days"></a>30 napnál több időtartomány kiválasztott

[A legtöbb mérőszámok az Azure-ban tárolt 93 napig](data-platform-metrics.md#retention-of-metrics). Azonban lekérdezheti, ha csak az adatok bármilyen egyetlen diagram érdemes legfeljebb 30 napig. Ez a korlátozás nem vonatkozik a [log mérőszámok](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

**Megoldás:** Ha megjelenik egy üres diagram, vagy a diagram csak jeleníti meg a metrikaadatok része, ellenőrizze, hogy a különbség a között kezdő - és a záró-dátumok a időválasztó nem haladja meg a 30 napos intervallumon.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>A zárolt y tengely tartományon kívül volt az összes metrikaértékek

Által [diagram y tengely határain zárolás](metrics-charts.md#lock-boundaries-of-chart-y-axis), akaratlanul győződjön meg a megjelenített diagramterület nem jelenik meg a diagram sor. Például ha y zárolva van, ezért egy 0 és 50 % közötti tartományba esik, és a mérőszám konstans értéke 100 %-os, a sor mindig jelenik meg a látható terület, a diagram üresen jelenik meg, ami kívül esik.

**Megoldás:** Győződjön meg arról, hogy a diagram y tengely határain kívül a metrikaértékek számos nincs zárolva. Ha az y tengely határok zárolva vannak, érdemes ideiglenesen alaphelyzetbe állítsa őket annak érdekében, hogy az értékek nem tartoznak a diagram tartományon kívül metrikát. Az y tengely tartománya zárolása nem ajánlott, hogy a diagramok az automatikus granularitással **sum**, **min**, és **maximális** összesítés, mert az értékeik változik a granularitási böngészőablak átméretezése, vagy egy másikra egy képernyőfelbontás lehetőséget. Váltása granularitási hagyhatja el a diagram üres területét.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>A vendég operációs rendszer metrikát egyszerre, de nem engedélyezte az Azure diagnosztikai bővítmény

Gyűjteménye **vendég operációs rendszer** metrikák az Azure Diagnostics bővítmény konfigurálása, és ne engedélyezze a szükséges a **diagnosztikai beállítások** az erőforrás panel.

**Megoldás:** Ha az Azure Diagnostics bővítmény engedélyezve van, de továbbra sem sikerül a metrikákat, kövesse az ismertetett lépéseket [– hibaelhárítási útmutató Azure Diagnostics bővítmény](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal). Lásd még a hibaelhárítási lépések [nem választhat a vendég operációs rendszer névtér és metrikák](metrics-troubleshoot.md#cannot-pick-guest-os-namespace-and-metrics)

## <a name="error-retrieving-data-message-on-dashboard"></a>"Adatok lekérése" hibaüzenet az irányítópulton

Ez a probléma akkor közös, ha az irányítópulton, amely később elavult, és az Azure-ból eltávolított metrikával lett létrehozva. Ellenőrizze, hogy az eset, nyissa meg a **metrikák** az erőforrást, és ellenőrizze a rendelkezésre álló metrikák, a metrika hatókörbe lapján. Ha a mérték nem látható, a metrika az Azure-ból el lett távolítva. Általában a metrika elavult, amikor nincs jobban új metrikát, amely a resource Health egy hasonló perspektíva biztosít.

**Megoldás:** Frissítse a hibás csempe kiválasztásával a egy másik metrikát a diagram irányítópultra. Is [tekintse át a listában az elérhető mérőszámok az Azure-szolgáltatásokhoz](metrics-supported.md).

## <a name="chart-shows-dashed-line"></a>Diagram mutatja szaggatott vonal

Az Azure mérőszámdiagramok szaggatott vonal stílusa használatával jelzi, hogy a hiányzó értékeket (más néven "null érték") között két ismert időfelbontási adatpont. Például, ha a időválasztó a kiválasztott "1 perces" idő részletesség, de a metrika: 07:26, 07:27., jelentett 07:29 és 07:30 (Megjegyzés: a második és harmadik adatpontok közötti percenkénti kimaradást), majd 07:27 és 07:29 kapcsolódik egy szaggatott vonallá változik, és csatlakozzon folytonos vonal minden egyéb adatpont. A szaggatott vonal csepp nullára, amikor a metrika használ **száma** és **sum** összesítést. Az a **átlagos**, **min** vagy **maximális** összesítések, a szaggatott vonal a két a legközelebbi ismert adatpontok kapcsolódik. Is ha az adatok nem található a diagram jobb vagy bal szélső részén, a szaggatott vonal bontja ki a irányát az a hiányzó adatpontok.
  ![metrika kép](./media/metrics-troubleshoot/missing-data-point-line-chart.png)

**Megoldás:** Ez a működésmód szándékos. Hasznos lehet a hiányzó adatpontokat azonosításához. A vonaldiagram kiváló választás az olyan nagy kapacitású mérőszámok trendek megjelenítése, de lehet, hogy nehéz értelmezni a metrikákhoz ritka értékekkel, különösen akkor, ha az értékek az aggregációs időköz corelating fontos. A szaggatott vonal megkönnyíti az ezekbe a diagramokba olvasása, de ha a diagram még mindig nem egyértelmű, fontolja meg a metrikák megtekintését is a különböző diagram típusát. Például az azonos metrika szórt diagram diagramot is egyértelműen mutatja egyes időfelbontási szint szerint csak egy pont jelenítenek meg, ha egy értéket tartalmaz, és a rendszer kihagyja az adatok pont érvényesítette mikor értéke hiányzik: ![metrika kép](./media/metrics-troubleshoot/missing-data-point-scatter-chart.png)

   > [!NOTE]
   > Ha továbbra is sávdiagram lesz látható a metrika, egér mozgatása a diagram segíthet az idő részletessége felmérheti a helyen, az egérmutató az adatpont kiemelésével.

## <a name="chart-shows-unexpected-drop-in-values"></a>Diagram váratlan eldobási értékeket jeleníti meg.

Sok esetben a mintavételezéskor a metrikaértékek csökkenését az adatok a diagramon látható egy félreértés. Összegek csökkenését tévessze vagy számolja, amikor a diagram mutatja-e a legújabb perc, mert az utolsó metrika adatpontok még nem fogadott és a feldolgozása még az Azure-ban. A szolgáltatástól függően a késés, a metrikák feldolgozási lehet, néhány perc alatt tartományon belül. Egy 1 és 5 perces granularitással legutóbbi időtartomány ábrázoló diagramokat, az érték az elmúlt pár percben legördülő válik több észrevehető: ![metrika kép](./media/metrics-troubleshoot/drop-in-values.png)

**Megoldás:** Ez a működésmód szándékos. Vagyunk győződve arról, hogy adatainak megjelenítése, amint az általunk kapott, akkor előnyös, akkor is, ha az adatok *részleges* vagy *hiányos*. Ha így tesz, hamarabb fontos részletes ismertetők és azonnal megkezdhető a vizsgálat indítása. Például egy metrika, amely a hibák számát jeleníti meg, egy részleges X érték jelenik meg arra kéri, hogy fordult elő legalább egy adott perc a hibák X. Akkor a probléma kivizsgálása azonnal elindítani, ahelyett várja meg, hogy hiba történt az ebben a percben, amely nem lehet fontosként pontos száma. A diagram érkező adatok teljes készlete, de ekkor is megjelenhetnek, új hiányos adatpontok újabb perc után frissül.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>Nem választhat a vendég operációs rendszer névtér és metrikák

Virtuális gépek és a virtual machine scale sets van két kategóriába mérőszámokat: **Virtuálisgép-gazda** az Azure üzemeltetési környezet által gyűjtött metrikák és **vendég operációs rendszer** által gyűjtött metrikák a [figyelőügynök](agents-overview.md) a virtuális gépeken futó. A figyelőügynök telepítését engedélyezésével [Azure diagnosztikai bővítmény](diagnostics-extension-overview.md).

Alapértelmezés szerint az Azure Storage-fiók, amelyre a válasszon a vendég operációs rendszer metrikák tárolódnak a **diagnosztikai beállítások** az erőforráscsoport lapján. A vendég operációs rendszer metrikák nem gyűjt, vagy metrikaböngésző nem tudja elérni őket, ha csak akkor jelenik meg a **virtuálisgép-gazda** metrika névtér:

![metrika kép](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**Megoldás:** Ha nem lát **vendég operációs rendszer** névteret és a mérőszámok a metrikaböngészőben:

1. Ellenőrizze, hogy [Azure diagnosztikai bővítmény](diagnostics-extension-overview.md) engedélyezve van, és metrikák gyűjthetők konfigurálva.
    > [!WARNING]
    > Nem használhat [Log Analytics-ügynököket](agents-overview.md#log-analytics-agent) (más néven a Microsoft Monitoring Agent, vagy a "MMA") küldése **vendég operációs rendszer** storage-fiókra.

1. Győződjön meg arról, hogy a tárfiók a tűzfal által nem védett.

1. Használja a [az Azure storage explorer](https://azure.microsoft.com/features/storage-explorer/) ellenőrzése, hogy a metrikák átvitele a storage-fiókra. Ha a metrikák nem gyűjt, kövesse a [– hibaelhárítási útmutató Azure Diagnostics bővítmény](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal).

## <a name="next-steps"></a>További lépések

* [Ismerkedés a Metrikaböngésző ismertetése](metrics-getting-started.md)
* [További információ a speciális funkcióit Metrikaböngésző](metrics-charts.md)
* [Rendelkezésre álló metrikák az Azure-szolgáltatások listáját lásd:](metrics-supported.md)
* [Példák: konfigurált diagramok](metric-chart-samples.md)