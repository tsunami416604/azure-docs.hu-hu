---
title: Riasztások naplózása Azure Monitor
description: E-mailek, értesítések, a webhelyek URL-címei (webhookok) vagy automatizálás, ha az Ön által megadott analitikai lekérdezési feltételek teljesülnek az Azure-riasztásokhoz.
author: yanivlavi
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 5/31/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: d0314e94e627a42ab55f9e91017acac0cdc8b541
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001620"
---
# <a name="log-alerts-in-azure-monitor"></a>Riasztások naplózása Azure Monitor

Ez a cikk részletesen ismerteti a naplózási riasztásokat az [Azure-riasztásokban](../../azure-monitor/platform/alerts-overview.md) támogatott riasztási típusok közül, és lehetővé teszi a felhasználók számára, hogy riasztások alapján használják az Azure elemzési platformját.

A naplózási riasztás a [Azure monitor naplókhoz](../../azure-monitor/learn/tutorial-viewdata.md) vagy [Application Insightshoz](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events)létrehozott naplóbeli keresési szabályokból áll. További információ a használatáról: [log-riasztások létrehozása az Azure-ban](../../azure-monitor/platform/alerts-log.md)

> [!NOTE]
> A [Azure monitor naplók](../../azure-monitor/learn/tutorial-viewdata.md) népszerű naplózási adatai mostantól a Azure monitor metrika platformján is elérhetők. Részletek nézetben a [naplók metrikai riasztása](../../azure-monitor/platform/alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Napló keresési riasztási szabálya – definíció és típusok

Az Azure Alerts naplókeresési szabályokat hoz létre megadott naplólekérdezések rendszeres időközönként való automatikus futtatására.  Ha a naplólekérdezés eredménye megfelel bizonyos feltételeknek, létrejön egy riasztásbejegyzés. A szabály ekkor automatikusan futtathat egy vagy több műveletet [Műveletcsoportok](../../azure-monitor/platform/action-groups.md) használatával. A naplózási riasztások létrehozásához, módosításához és frissítéséhez szükség lehet az [Azure monitoring közreműködői](../../azure-monitor/platform/roles-permissions-security.md) szerepkörre. a hozzáférés & lekérdezés végrehajtási jogosultsága a riasztási szabályban vagy a riasztási lekérdezésben lévő elemzési cél (ok) hoz. Ha a felhasználó nem rendelkezik hozzáféréssel a riasztási szabály vagy a riasztási lekérdezés összes analitikai céljához, akkor a szabály létrehozása meghiúsulhat, vagy a napló riasztási szabálya részleges eredménnyel lesz végrehajtva.

A naplók keresési szabályait a következő részletek határozzák meg:

- **Napló lekérdezése**  A riasztási szabály által kiváltott minden alkalommal futó lekérdezés.  A lekérdezés által visszaadott rekordok alapján megállapítható, hogy egy riasztás aktiválva van-e. Az elemzési lekérdezés lehet egy adott Log Analytics munkaterülethez vagy Application Insights alkalmazáshoz, és akár több Log Analytics is terjedhet, [és Application Insights erőforrásokhoz](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) , valamint az összes erőforrásra vonatkozó lekérdezési jogosultsággal rendelkezik. 
    > [!IMPORTANT]
    > a Application Insights-és naplózási riasztások [több erőforrással történő lekérdezési](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) támogatása a [scheduledQueryRules API-val konfigurált log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md) esetén.

    Bizonyos analitikai parancsok és kombinációk nem kompatibilisek a naplóbeli riasztásokban való használattal; További részletekért tekintse meg [a riasztási lekérdezéseket Azure monitorban](../../azure-monitor/platform/alerts-log-query.md).

- **Időtartam.**  Megadja a lekérdezés időtartományát. A lekérdezés csak azokat a rekordokat adja vissza, amelyek az aktuális idő ezen tartományában jöttek létre. Az időtartam korlátozza a naplózási lekérdezéshez beolvasott adatvesztést, és megkerüli a naplózási lekérdezésben használt idő (például a ago) utasításait. <br>*Ha például az időszak 60 percre van állítva, és a lekérdezés a 1:15 ÓRAKOR fut, csak a 12:15 PM és 1:15 PM között létrehozott rekordokat adja vissza a rendszer a napló lekérdezésének végrehajtásához. Most, hogy a napló lekérdezése a (z) (7D) időpontot használja, a napló lekérdezése csak a 12:15 PM és a 1:15 PM közötti adatértékre lesz futtatva, mintha az csak az elmúlt 60 percben található. És nem a log lekérdezésben megadott hét napig.*

- **Gyakoriság**.  Megadja, hogy a lekérdezés milyen gyakran fusson. 5 perc és 24 óra közötti érték lehet. Az időtartamnál kisebbnek vagy azzal egyenlőnek kell lennie.  Ha az érték nagyobb, mint az időszak, akkor a kockázati rekordok kimaradnak.<br>*Vegyünk például egy 30 perces időszakot és egy 60 perces gyakoriságot.  Ha a lekérdezés a 1:00-es verzióban fut, akkor a a 12:30 és a 1:00 PM közötti rekordokat adja vissza.  A lekérdezés következő futtatása a 2:00, ha az 1:30 és 2:00 közötti rekordokat adja vissza.  A 1:00 és 1:30 között létrehozott rekordok soha nem lesznek kiértékelve.*

- **Küszöbérték**.  A rendszer kiértékeli a napló keresésének eredményét, és megállapítja, hogy létre kell-e hozni egy riasztást.  A küszöbérték különbözik a naplók keresési riasztási szabályainak különböző típusaitól.

A naplóbeli keresési szabályok [Azure monitor naplók](../../azure-monitor/learn/tutorial-viewdata.md) vagy [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events)esetén lehetnek. Az alábbi szakaszokban részletesen ismertetjük ezeket a típusokat.

- **[Az eredmények száma](#number-of-results-alert-rules)** . Egyszeres riasztás jön létre, ha a naplóbeli keresés által visszaadott rekordok száma meghaladja a megadott számot.
- **[Metrika mértékegysége](#metric-measurement-alert-rules)** .  A napló keresési eredményeiben a megadott küszöbértéket meghaladó értékekkel létrehozott riasztás.

A riasztási szabályok típusai közötti különbségek a következők.

- *Az eredmények* riasztási szabályainak száma mindig egyetlen riasztást hoz létre, míg a *metrika mértékének* riasztási szabálya riasztást hoz létre minden olyan objektumhoz, amely meghaladja a küszöbértéket.
- Az eredmények riasztási szabályainak száma riasztást hoz létre *,* ha a küszöbértéket egy alkalommal túllépik. A *metrika mértékének* riasztási szabályai riasztást hozhatnak létre, ha a küszöbérték túllépi az adott időintervallumban megadott számú időt.

### <a name="number-of-results-alert-rules"></a>Eredmények riasztási szabályainak száma

Az eredmények riasztási szabályainak **száma** egyetlen riasztást hoz létre, ha a keresési lekérdezés által visszaadott rekordok száma meghaladja a megadott küszöbértéket. Ez a típusú riasztási szabály ideális az olyan események, mint például a Windows-eseménynaplók, a syslog, a WebApp-válaszok és az egyéni naplók használata esetén.  Előfordulhat, hogy létre kell hoznia egy riasztást, ha egy adott hiba esemény jön létre, vagy ha egy adott időszakon belül több hiba jön létre.

**Küszöbérték**: az eredmények riasztási szabályainak küszöbértéke nagyobb vagy kisebb, mint egy adott érték.  Ha a naplóbeli keresés által visszaadott rekordok száma megegyezik ezzel a feltétellel, akkor létrejön egy riasztás.

Ha egyetlen eseményről szeretne riasztást kapni, állítsa az eredmények számát 0-nál nagyobb értékre, és keresse meg a lekérdezés legutóbbi futtatása óta létrehozott egyetlen esemény előfordulását. Előfordulhat, hogy egyes alkalmazások egy alkalmankénti hibát naplóznak, amely nem feltétlenül eredményez riasztást.  Előfordulhat például, hogy az alkalmazás megpróbálkozik a hiba eseményt létrehozó folyamattal, majd a következő alkalommal sikeres lesz.  Ebben az esetben előfordulhat, hogy nem szeretné létrehozni a riasztást, kivéve, ha egy adott időszakon belül több esemény jön létre.  

Bizonyos esetekben érdemes lehet riasztást létrehozni egy esemény hiányában.  Egy folyamat például naplózhatja a rendszeres eseményeket, hogy jelezze, hogy megfelelően működik-e.  Ha egy adott időszakon belül nem naplózza ezeket az eseményeket, a rendszer riasztást hoz létre.  Ebben az esetben a küszöbértéket 1-nél **kisebb**értékre kell állítani.

#### <a name="example-of-number-of-records-type-log-alert"></a>Példa a rekordok típusára – napló típusú riasztás

Vegyünk egy olyan forgatókönyvet, amelyben tudni szeretné, hogy a webalapú alkalmazás az 500-as kóddal (azaz) belső kiszolgálóhiba esetén választ ad a felhasználóknak. Hozzon létre egy riasztási szabályt a következő részletekkel:  

- **Lekérdezés:** kérelmek | where resultCode = = "500"<br>
- **Időtartam:** 30 perc<br>
- **Riasztás gyakorisága:** öt perc<br>
- **Küszöbérték:** Nagyobb, mint 0<br>

Ezt követően a riasztás 5 percenként futtatja a lekérdezést, és 30 percnyi adattal keresi azokat a rekordokat, amelyeknél az eredmény kódja 500 volt. Ha még egy ilyen rekord is található, a riasztást küld, és elindítja a konfigurált műveletet.

### <a name="metric-measurement-alert-rules"></a>Metrika mértékének riasztási szabályai

A **metrika mértékének** riasztási szabályai riasztást hoznak létre egy lekérdezés minden objektumához egy olyan értékkel, amely meghaladja a megadott küszöbértéket és a megadott kiváltó feltételt. Az eredmények riasztási szabályainak **számától** eltérően a **metrika mértékének** riasztási szabályai működnek, ha az elemzési eredmények idősorozatot biztosítanak. A következő eltérések az eredmények riasztási szabályainak **száma** alapján különböznek.

- **Összesítő függvény**: meghatározza a végrehajtott számítást, és potenciálisan egy numerikus mezőt az összesítéshez.  Például a **Count ()** függvény a lekérdezésben lévő rekordok számát adja vissza, az **AVG (kártyabirtokos számlájának megterhelését)** a kártyabirtokos számlájának megterhelését mező átlagát adja vissza az intervallumban. A lekérdezésben szereplő összesítő függvény neve/neve: AggregatedValue, és meg kell adnia egy numerikus értéket. 

- **Csoport mező**: a mező minden példányához létrejön egy összesített értéket tartalmazó rekord, és mindegyikhez riasztást lehet létrehozni.  Ha például riasztást szeretne készíteni az egyes számítógépekhez, akkor azt a **számítógép**fogja használni. Ha a riasztási lekérdezésben több csoportosítási mező van megadva, a felhasználó megadhatja, hogy melyik mezőt kell használni az eredmények rendezéséhez az **összesítő on** (metricColumn) paraméter használatával.

    > [!NOTE]
    > Az *aggregált on* (metricColumn) beállítás a metrikák mértékének naplózási riasztásai a Application Insights és a naplózási riasztások esetében a [scheduledQueryRules API használatával konfigurált log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md) esetén.

- **Intervallum**: az az időtartam, ameddig az adatokat összesíti.  Ha például **öt percet**adott meg, a rendszer egy rekordot hoz létre a csoport mező minden egyes példányához, amely a riasztáshoz megadott időszakban 5 perces időközönként összesítve lesz.

    > [!NOTE]
    > Az intervallum meghatározásához a bin függvényt kell használni a lekérdezésben. Mivel a bin () nem egyenlő időintervallumokat eredményezhet – a riasztás automatikusan átalakítja a bin-parancsot, hogy a megfelelő idő elteltével bin_at parancsot, hogy az eredményeket egy rögzített ponttal biztosítsa. A naplózási riasztás metrikájának típusa úgy van kialakítva, hogy olyan lekérdezésekkel működjön, amelyek legfeljebb három bin () paranccsal működnek.
    
- **Küszöbérték**: a metrika mértékének riasztási szabályainak küszöbértékét egy összesített érték és számos megsértés határozza meg.  Ha a naplóbeli keresés bármely adatpontja meghaladja ezt az értéket, a rendszer megszegésnek tekinti.  Ha az eredményekben lévő bármelyik objektum megszegése meghaladja a megadott értéket, akkor a rendszer riasztást hoz létre az adott objektumra vonatkozóan.

Az *összesítő on* vagy a *metricColumn* beállítás helytelen konfigurációja riasztási szabályokat okozhat a gyújtáskihagyás. További információ: hibaelhárítás, [Ha a metrika mértékének riasztási szabálya helytelen](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect).

#### <a name="example-of-metric-measurement-type-log-alert"></a>Példa a metrikai mérési típus naplójának riasztására

Vegye fontolóra azt a helyzetet, amikor riasztást szeretne kapni, ha bármely számítógép 30 percen belül meghaladta a 90%-os processzor-kihasználtságot.  Hozzon létre egy riasztási szabályt a következő részletekkel:  

- **Lekérdezés:** Perf | where ObjectName = = "processzor" és CounterName = = "% Processor Time" | AggregatedValue = AVG (kártyabirtokos számlájának megterhelését) összesítése raktárhely alapján (TimeGenerated, 5m), számítógép<br>
- **Időtartam:** 30 perc<br>
- **Riasztás gyakorisága:** öt perc<br>
- **Riasztási logika – feltétel & küszöbérték:** Nagyobb, mint 90<br>
- **Csoport mezője (aggregált):** Számítógép
- **Riasztás elindítása a következő alapján:** 2. nagyobb teljes szabálysértések<br>

A lekérdezés 5 perces időközönként létrehoz egy átlagos értéket az egyes számítógépekhez.  A lekérdezés 5 percenként fut az elmúlt 30 percben gyűjtött adatok esetében. Mivel a kiválasztott csoportos mező (aggregált) oszlopos számítógép – a AggregatedValue a "számítógép" különböző értékeire van felosztva, az egyes számítógépek átlagos processzor-kihasználtsága pedig 5 perces időtartamra van meghatározva.  A lekérdezési eredmény (mondjuk) három számítógép esetén a következő lesz:.


|TimeGenerated [UTC] |Számítógép  |AggregatedValue  |
|---------|---------|---------|
|20xx-xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

Ha a lekérdezés eredményét szeretné ábrázolni, a következőnek kell megjelennie:.

![Példa lekérdezés eredményeire](media/alerts-unified-log/metrics-measurement-sample-graph.png)

Ebben a példában a három számítógép esetében 5 percből álló raktárhelyeken láthatjuk az átlagos processzor kihasználtságát 5 percen belül kiszámítva. A 90-es küszöbértéket csak egyszer Srv01 meg a 1:25-es raktárhelyen. Ezzel szemben a srv02 meghaladja az 90 küszöbértéket 1:10, 1:15 és 1:25 raktárhelyeken; míg a srv03 meghaladja a 90 küszöbértéket, 1:10, 1:15, 1:20 és 1:30.
Mivel a riasztás úgy van beállítva, hogy a teljes szabálysértések alapján aktiválja az aktiválást, láthatjuk, hogy a srv02 és a srv03 csak a feltételeket elégíti ki. Ezért a rendszer külön riasztásokat hoz létre a srv02 és a srv03, mivel a több időrekeszben kétszer megszegték a 90%-os küszöbértéket.  Ha a következő *alapuló trigger-riasztást a rendszer* a *folyamatos szabálysértésekhez* konfigurálta, akkor a riasztás **csak** a srv03 miatt lett kiváltva, mivel a küszöbértéket a 1:10 és 1:20 közötti három egymást követő időszakra vonatkozóan megsértette. És **nem** a srv02 esetében, mivel megsértette a küszöbértéket két egymást követő, 1:10 és 1:15 közötti időszakra.

## <a name="log-search-alert-rule---firing-and-state"></a>Napló keresési riasztási szabálya – égetés és állapot

A naplózási keresési riasztási szabályok csak a lekérdezésben felépített logikán működnek. A riasztási rendszer nem rendelkezik más környezettel a rendszer, a szándék, vagy a lekérdezés által vélelmezett kiváltó ok állapotával. Ilyenkor a naplózási riasztások állapota kevésbé. A feltételek "igaz" vagy "hamis" értékkel vannak kiértékelve minden alkalommal, amikor futtatják őket.  A riasztás minden alkalommal aktiválódik, amikor a riasztási feltétel kiértékelése "igaz", függetlenül attól, hogy a rendszer korábban elindította.    

Lássuk ezt a viselkedést működés közben egy gyakorlati példával. Tegyük fel, hogy van egy *contoso-log-riasztás*nevű log riasztási szabály, amely a következő példában látható módon van konfigurálva: a [log-riasztások száma](#example-of-number-of-records-type-log-alert). A feltétel egy egyéni riasztási lekérdezés, amelynek célja, hogy a naplókban megkeresse a 500-es eredmény kódját. Ha a naplókban még egy 500-es eredmény-kód található, a riasztás feltétele igaz. 

Az Azure riasztási rendszer az alábbi időközönként ellenőrzi a *contoso-log-riasztás*feltételeit.


| Time    | Naplóbeli keresési lekérdezés által visszaadott rekordok száma | Naplózási feltétel evalution | Eredmény 
| ------- | ----------| ----------| ------- 
| 1:05 PM | 0 rekord | 0 nem > 0, így hamis |  A riasztás nem tűz. Nincs hívott művelet.
| 1:10 PM | 2 rekord | 2 > 0 igaz  | Riasztási tüzek és műveleti csoportok hívása. Riasztási állapot aktív.
| 1:15 PM | 5 rekord | 5 > 0 igaz  | Riasztási tüzek és műveleti csoportok hívása. Riasztási állapot aktív.
| 1:20 PM | 0 rekord | 0 nem > 0, így hamis |  A riasztás nem tűz. Nincs hívott művelet. A riasztás állapota aktív marad.

Az előző eset használata példaként:

A 1:15 PM Azure-riasztások nem tudják megállapítani, hogy a 1:10-kor észlelt mögöttes problémák fennmaradnak-e, és ha a rekordok nettó új hibák vagy a régebbi hibák megismétlése 1:10 órakor történik. A felhasználó által megadott lekérdezés esetleg nem veszi figyelembe a korábbi rekordokat, és a rendszer nem tudja. Az Azure-riasztások rendszere figyelmezteti az err-re, és a riasztást és a kapcsolódó műveleteket 1:15 ÓRAKOR újra elindítják. 

1:20 ÓRAKOR, ha nulla rekordokat észleltek a 500-es eredmény kódjával, az Azure-riasztások nem biztosak abban, hogy a 500-as verzióban a 1:10-es és a 1:15 PM-ből származó találati kód megoldódott Nem tudja, hogy az 500-es hiba miatti hibák ugyanezen okból történnek-e. Ezért a *contoso-log-Alert* nem módosul az Azure riasztási irányítópulton **megoldottra** , és/vagy az értesítéseket a rendszer nem küldi el a riasztás feloldása érdekében. Csak Ön, aki megérti az elemzési lekérdezésbe ágyazott logika pontos feltételeit vagy okát, [a riasztást szükség szerint lezártként jelölheti](alerts-managing-alert-states.md) meg.

## <a name="pricing-and-billing-of-log-alerts"></a>A naplózási riasztások díjszabása és számlázása

A naplózási riasztásokra vonatkozó díjszabás a [Azure monitor díjszabási](https://azure.microsoft.com/pricing/details/monitor/) oldalán található. Az Azure-számlákban a naplózási riasztások típusa `microsoft.insights/scheduledqueryrules` a következővel:

- Riasztások naplózása Application Insights megjelenített riasztások pontos neve mellett az erőforráscsoport és a riasztás tulajdonságaival együtt
- Riasztások naplózása Log Analytics megjelenített riasztások pontos neve, az erőforráscsoport és a riasztás tulajdonságaival együtt. a [SCHEDULEDQUERYRULES API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) -val való létrehozáskor

Az [örökölt log Analytics API](../../azure-monitor/platform/api-alerts.md) riasztási műveleteket és ütemterveket tartalmaz log Analytics mentett keresés részeként, és nem a megfelelő [Azure-erőforrásokat](../../azure-resource-manager/resource-group-overview.md). Ezért az Azure-ban való számlázáshoz az Azure Portal Log Analytics használatával létrehozott, az [új API](../../azure-monitor/platform/alerts-log-api-switch.md) -ra való áttérés vagy az [örökölt log Analytics API](../../azure-monitor/platform/api-alerts.md) -Hidden pszeudo riasztási `microsoft.insights/scheduledqueryrules` szabályok segítségével történő számlázást kell engedélyezni az ilyen elavult naplózási riasztásokhoz. Az `microsoft.insights/scheduledqueryrules` számlázására létrehozott rejtett pszeudo-szabályok az erőforráscsoport és a riasztás tulajdonságaival együtt `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`ként jelennek meg.

> [!NOTE]
> Ha az érvénytelen karakterek, például a `<, >, %, &, \, ?, /` szerepelnek, a rendszer a rejtett pszeudo-szabály nevében lecseréli a `_`ra, és így az Azure-számlán is szerepel.

Ha el szeretné távolítani a riasztási szabályok számlázásához létrehozott rejtett scheduleQueryRules-erőforrásokat az [örökölt log Analytics API](api-alerts.md)-val, a felhasználó a következők bármelyikét végezheti el:

- Bármelyik felhasználó [átválthatja a riasztási szabályok API-beállításait a log Analytics munkaterületen](../../azure-monitor/platform/alerts-log-api-switch.md) , és a riasztási szabályok elvesztésével vagy a figyelés Azure Resource Manager megfelelő [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)-val való áthelyezésével. Így nincs szükség a kiszámlázható pszeudo-riasztási szabályok elvégzésére.
- Ha a felhasználó nem szeretné váltani az API-beállításokat, a felhasználónak **törölnie** kell az eredeti és a riasztási műveletet az [örökölt log Analytics API](api-alerts.md) -val vagy a törléssel [Azure Portal az eredeti napló riasztási szabálya](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal) alapján.

A riasztási szabályok a [régi log Analytics API](api-alerts.md)-val való számlázásához létrehozott rejtett scheduleQueryRules-erőforrások mellett a Put művelethez hasonló módosítási műveletek sikertelenek lesznek. Mivel a `microsoft.insights/scheduledqueryrules` típusú pszeudo-szabályok a [régi log Analytics API](api-alerts.md)használatával létrehozott riasztási szabályok számlázására szolgálnak. A riasztási szabályok módosítását [örökölt log Analytics API](api-alerts.md) -val kell elvégezni (vagy) [a felhasználó átválthatja a riasztási szabályok API-BEÁLLÍTÁSAIT](../../azure-monitor/platform/alerts-log-api-switch.md) a [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) használatára.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg [, hogyan hozhat létre naplóbeli riasztásokat az Azure](../../azure-monitor/platform/alerts-log.md)-ban.
* Ismerkedjen meg [a webhookokkal a log-riasztásokban az Azure-ban](alerts-log-webhook.md).
* Ismerje meg az [Azure-riasztásokat](../../azure-monitor/platform/alerts-overview.md).
* További információ a [Application Insightsról](../../azure-monitor/app/analytics.md).
* További információ a [log Analyticsról](../../azure-monitor/log-query/log-query-overview.md).
