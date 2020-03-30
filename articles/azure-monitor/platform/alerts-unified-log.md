---
title: Riasztások naplózása az Azure Monitorban
description: E-mailek, értesítések, hívás webhelyek URL-címek (webhookok) vagy automatizálás, ha a megadott analitikus lekérdezési feltételek teljesülnek az Azure Alerts.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 5/31/2019
ms.subservice: alerts
ms.openlocfilehash: a6abf4665c27771497037da35f85bb540e6e904e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665221"
---
# <a name="log-alerts-in-azure-monitor"></a>Riasztások naplózása az Azure Monitorban

Ez a cikk a naplóriasztások részleteit tartalmazza az [Azure-riasztások](../../azure-monitor/platform/alerts-overview.md) ban támogatott riasztások egyik típusa, és lehetővé teszi a felhasználók számára, hogy az Azure elemzési platformját használják a riasztás alapjaként.

A naplóriasztás az [Azure Monitor-naplókhoz](../../azure-monitor/learn/tutorial-viewdata.md) vagy az [Application Insightshoz](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events)létrehozott naplókeresési szabályokból áll. Ha többet szeretne megtudni a használatáról, olvassa el a [naplóriasztások létrehozása az Azure-ban](../../azure-monitor/platform/alerts-log.md)

> [!NOTE]
> Az Azure [Monitor-naplók](../../azure-monitor/learn/tutorial-viewdata.md) népszerű naplóadatai már az Azure Monitor metrikaplatformján is elérhetők. A részletekért nézet, [Metrika riasztás naplók](../../azure-monitor/platform/alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Keresési riasztási szabály naplózása – definíció és típusok

Az Azure Alerts naplókeresési szabályokat hoz létre megadott naplólekérdezések rendszeres időközönként való automatikus futtatására.  Ha a naplólekérdezés eredménye megfelel bizonyos feltételeknek, létrejön egy riasztásbejegyzés. A szabály ekkor automatikusan futtathat egy vagy több műveletet [Műveletcsoportok](../../azure-monitor/platform/action-groups.md) használatával. A naplóriasztások létrehozásához, módosításához és frissítéséhez [azure-figyelési közreműködői](../../azure-monitor/platform/roles-permissions-security.md) szerepkörre lehet szükség; hozzáférési & lekérdezés végrehajtási jogai az elemzési cél(ok) riasztási szabály vagy riasztási lekérdezés. Abban az esetben, ha a felhasználó létrehozása nem fér hozzá az összes elemzési cél(ok) a riasztási szabály vagy riasztási lekérdezés - a szabály létrehozása sikertelen lehet, vagy a napló riasztási szabály végrehajtása részleges eredményekkel.

A naplókeresési szabályokat a következő adatok határozzák meg:

- **Naplólekérdezés**.  A riasztási szabály kiváltódásakor minden alkalommal lefutó lekérdezés.  A lekérdezés által visszaadott rekordok segítségével megállapítható, hogy a riasztást el kell-e indítani. Az Analytics-lekérdezés lehet egy adott Log Analytics-munkaterülethez vagy Application Insights-alkalmazáshoz, és akár [több Log Analytics- és Application Insights-erőforrásra](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) is kiterjedhet, feltéve, hogy a felhasználó rendelkezik hozzáféréssel, valamint lekérdezési jogokkal az összes erőforráshoz. 
    > [!IMPORTANT]
    > [erőforrásközi lekérdezések](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) támogatása az Application Insights naplóriasztásaiban és a log analytics-riasztások naplóriasztásaiban, amelyek csak [az scheduledQueryRules API-val konfigurálva vannak.](../../azure-monitor/platform/alerts-log-api-switch.md)

    Egyes analitikus parancsok és kombinációk nem kompatibilisek a naplóriasztásokban való használattal; további részletekért tekintse meg, [naplózza a riasztási lekérdezéseket az Azure Monitorban.](../../azure-monitor/platform/alerts-log-query.md)

- **Időszak**.  Megadja a lekérdezés időtartományát. A lekérdezés csak azokat a rekordokat adja vissza, amelyek az aktuális idő ezen tartományában jöttek létre. Az időtartam korlátozza a naplólekérdezéshez beolvasott adatokat a visszaélések megelőzése érdekében, és megkerüli a naplólekérdezésben használt bármely alkalommal (például ezelőtt) használt parancsokat. <br>*Ha például az időtartam 60 percre van állítva, és a lekérdezés 1:15-kor fut, a naplólekérdezés végrehajtásához csak a 12:15 és 13:15 között létrehozott rekordok at adja vissza a rendszer. Most, ha a naplólekérdezés az időparancsot használja, mint korábban (7d), a naplólekérdezés csak 12:15 és 13:15 között fut - mintha az adatok csak az elmúlt 60 percben léteznének. És nem a naplólekérdezésben megadott hét napos adatokra.*

- **Gyakoriság**.  Itt adható meg, hogy milyen gyakran fusson a lekérdezés. Lehet bármilyen érték között 5 perc és 24 óra. Egyenlőnek vagy kisebbnek kell lennie az időtartamnál.  Ha az érték nagyobb, mint az időszak, akkor azt kockáztatja, hogy a rekordok kimaradnak.<br>*Vegyünk például egy 30 perces és 60 perces gyakorisági időtartamot.  Ha a lekérdezés 1:00-kor fut, 12:30 és 13:00 óra között ad vissza rekordokat.  A lekérdezés következő futtatása 2:00, amikor 1:30 és 2:00 között ad vissza rekordokat.  Az 1:00 és 1:30 között létrehozott rekordok at soha nem lesz kiértékelve.*

- **Küszöbértéket**.  A naplókeresés eredményeit a rendszer kiértékeli annak meghatározásához, hogy létre kell-e hozni egy riasztást.  A küszöbérték eltér a különböző típusú naplókeresési riasztási szabályok.

Naplókeresési szabályok legyenek az [Azure Monitor naplók](../../azure-monitor/learn/tutorial-viewdata.md) vagy Application [Insights,](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events)két típusa lehet. Az alábbi szakaszok részletesen ismertetik ezeket a típusokat.

- **[Az eredmények száma](#number-of-results-alert-rules)**. Egyetlen riasztás jön létre, ha a naplókeresés által visszaadott számrekordok száma meghaladja a megadott számot.
- **[Metrikus mérés](#metric-measurement-alert-rules)**.  A naplókeresés eredményei között a megadott küszöbértéket meghaladó értékekkel rendelkező minden objektumhoz létrehozott riasztás.

A riasztási szabálytípusok közötti különbségek a következők.

- *Eredmények száma riasztási* szabályok mindig egyetlen riasztást hoz létre, míg *a metrikamérési* riasztási szabály riasztást hoz létre minden olyan objektumhoz, amely meghaladja a küszöbértéket.
- *Eredmények száma* riasztási szabályok riasztást hoz létre, ha a küszöbértéket egyetlen alkalommal túllépi. *Metrikamérési* riasztási szabályok riasztást hozhat létre, ha a küszöbértéket egy adott időintervallumban bizonyos számú alkalommal túllépik.

### <a name="number-of-results-alert-rules"></a>Eredmények száma riasztási szabályok

**Eredmények száma** riasztási szabályok hozzon létre egy riasztást, ha a keresési lekérdezés által visszaadott rekordok száma meghaladja a megadott küszöbértéket. Az ilyen típusú riasztási szabály ideális olyan eseményekkel való használatra, mint a Windows eseménynaplók, a Syslog, a WebApp Response és az egyéni naplók.  Előfordulhat, hogy egy adott hibaesemény létrehozásakor vagy egy adott időszakon belül több hibaesemény létrehozásakor szeretne riasztást létrehozni.

**Küszöbérték:** Az eredmények száma riasztási szabályok küszöbértéke nagyobb vagy kisebb, mint egy adott érték.  Ha a naplókeresés által visszaadott rekordok száma megfelel ennek a feltételnek, akkor a rendszer riasztást hoz létre.

Ha egyetlen eseményre szeretne riasztást adni, állítsa az eredmények számát 0-nál nagyobbra, és ellenőrizze, hogy a lekérdezés legutóbbi futtatása óta létrehozott egyetlen esemény előfordulása történt-e. Egyes alkalmazások olyan alkalmi hibákat naplózhatnak, amelyek nem feltétlenül riasztást adnak ki.  Előfordulhat például, hogy az alkalmazás újra próbálkozik a hibaeseményt létrehozó folyamattal, majd a következő alkalommal sikeres lesz.  Ebben az esetben előfordulhat, hogy nem szeretné létrehozni a riasztást, kivéve, ha egy adott időszakon belül több esemény jön létre.  

Bizonyos esetekben esemény hiányában érdemes lehet riasztást létrehozni.  Egy folyamat például naplózhatja a rendszeres eseményeket, jelezve, hogy megfelelően működik.  Ha nem naplózza az egyik ilyen eseményt egy adott időszakon belül, akkor riasztást kell létrehozni.  Ebben az esetben a küszöbértéket **1-nél kisebbre**kell állítania.

#### <a name="example-of-number-of-records-type-log-alert"></a>Példa a Rekordok száma típusú naplóriasztásra

Fontolja meg egy olyan forgatókönyv, amelyben szeretné tudni, hogy a web-alapú alkalmazás ad választ a felhasználók kód 500 (azaz) belső kiszolgálóhiba. Hozzon létre egy riasztási szabályt a következő adatokkal:  

- **Lekérdezés:** kérések | ahol resultCode == "500"<br>
- **Időszak:** 30 perc<br>
- **Riasztási gyakoriság:** öt perc<br>
- **Küszöbérték:** Nagyobb, mint 0<br>

Ezután a riasztás 5 percenként futtatja a lekérdezést, 30 perces adatokkal - minden olyan rekordot keres, ahol az eredménykód 500 volt. Ha még egy ilyen rekord található, elindítja a riasztást, és elindítja a konfigurált műveletet.

### <a name="metric-measurement-alert-rules"></a>Metrikamérési riasztási szabályok

**Metrikamérési** riasztási szabályok riasztást hoznak létre a lekérdezés minden olyan objektumához, amelynek értéke meghaladja a megadott küszöbértéket és a megadott eseményindító feltételt. Az **eredmények riasztási szabályainak számával** ellentétben **a metrikamérési riasztási** szabályok akkor működnek, ha az elemzési eredmény idősorozatot biztosít. A következő különböző eltérésekkel rendelkeznek **az eredmények száma riasztási** szabályoktól.

- **Összesítő függvény**: Meghatározza a végrehajtott számítást, és potenciálisan egy számmezőösszesítést.  A **count()** például a lekérdezésrekordjainak számát adja eredményül, az **átlag(CounterValue)** pedig a CounterValue mező átlagát adja vissza az intervallumban. A lekérdezés összesítő függvényét meg kell nevezni/meghívni: AggregatedValue, és numerikus értéket kell megadni. 

- **Csoportmező:** A mező minden egyes példányához egy összesített értékkel rendelkező rekord jön létre, és mindegyikhez riasztás hozható létre.  Ha például minden számítógéphez szeretne riasztást létrehozni, akkor a Számítógép segítségével használja a **használatát.** Abban az esetben, ha több csoportmező van megadva a riasztási lekérdezésben, a felhasználó megadhatja, hogy melyik mező használható az eredmények rendezéséhez az **Összesítő on** (metricColumn) paraméter használatával

    > [!NOTE]
    > *Az Összesítés on* (metricColumn) beállítás az Application Insights metrikamérési típusú naplóriasztásaihoz és a log analytics-riasztások naplóriasztásaihoz érhető el, amelyek csak [az ütemezettQueryRules API-val konfigurálva érhetők el.](../../azure-monitor/platform/alerts-log-api-switch.md)

- **Intervallum**: Az adatok összesítésének időintervallumát határozza meg.  Ha például öt **percet**adott meg, a csoportmező minden egyes példányához létrejön egy rekord, amely a riasztáshoz megadott időtartam alatt 5 perces időközönként összesítve lesz.

    > [!NOTE]
    > Az intervallum megadásához a raktárhely függvényt kell használni a lekérdezésben. Mivel a bin() egyenlőtlen időintervallumokat eredményezhet - a riasztás automatikusan átalakítja a bin parancsot bin_at parancsra a megfelelő idővel futásidőben, hogy biztosítsa a rögzített ponttal rendelkező eredményeket. A naplóriasztás metrikamérési típusa a legfeljebb három példányt kió bin() parancsú lekérdezésekkel való együttműködésre szolgál.
    
- **Küszöbérték:** A metrikamérési riasztási szabályok küszöbértékét egy összesített érték és számos megsértése határozza meg.  Ha a naplókeresés bármely adatpontja meghaladja ezt az értéket, akkor az szabályszegésnek minősül.  Ha az eredményekben lévő objektumok megsértése meghaladja a megadott értéket, akkor riasztás jön létre az adott objektumhoz.

*Az Összesítő* be vagy *a metricColumn* beállítás helytelen konfigurálása riasztási szabályok gyújtáskimaradását okozhatja. További információt a Hibaelhárítás című témakörben talál, [ha a metrikamérési riasztási szabály helytelen.](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect)

#### <a name="example-of-metric-measurement-type-log-alert"></a>Példa a Metrikamérési típusú napló riasztására

Fontolja meg egy olyan forgatókönyv, amelyben szeretne riasztást, ha bármely számítógép túllépte a processzor kihasználtsága 90%-os háromszor 30 perc alatt.  Hozzon létre egy riasztási szabályt a következő adatokkal:  

- **Lekérdezés:** Perf | ahol ObjectName == "Processzor" és CounterName == "% processzoridő" | aggregatedValue = átlag(CounterValue) összegzése raktárhely(TimeGenerated, 5m), Számítógép szerint<br>
- **Időszak:** 30 perc<br>
- **Riasztási gyakoriság:** öt perc<br>
- **Riasztási logika – feltétel & küszöbérték:** 90-nél nagyobb<br>
- **Csoportmező (összesített):** Számítógép
- **Riasztás aktiválása a következők alapján:** Összes jogsértés Nagyobb, mint 2<br>

A lekérdezés 5 perces időközönként hozlétre egy átlagos értéket az egyes számítógépekhez.  Ez a lekérdezés 5 percenként futna az előző 30 percben gyűjtött adatokhoz. Mivel a kiválasztott csoportmező (Összesített) oszlopos "Számítógép" – az AggregatedValue a "Számítógép" különböző értékeire van felosztva, és az egyes számítógépek átlagos processzorkihasználtságát 5 perces időtárolóra határozzák meg.  Minta lekérdezés eredménye (mondjuk) három számítógép, lenne az alábbiak szerint.


|Időgenerálva [UTC] |Computer  |Összesített érték  |
|---------|---------|---------|
|20xx-xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

Ha a lekérdezés eredményét ki kell rajzolni, akkor a következőképpen jelenik meg.

![Lekérdezések eredményeinek mintája](media/alerts-unified-log/metrics-measurement-sample-graph.png)

Ebben a példában azt látjuk, a tárolók 5 mins mind a három számítógép - átlagos processzorkihasználtság számított 5 mins. Küszöb 90 megsértisék srv01 csak egyszer 1:25 bin. Ehhez képest az srv02 1:10, 1:15 és 1:25 rekeszeknél meghaladja a 90-es küszöbértéket; míg az srv03 1:10, 1:15, 1:20 és 1:30 órakor meghaladja a 90-es küszöbértéket.
Mivel a riasztás úgy van beállítva, hogy a teljes szabályszegések alapján aktiválódik, több mint kettő, azt látjuk, hogy srv02 és srv03 csak a feltételeknek felel meg. Ezért külön riasztásokjönnek létre az srv02 és az srv03 esetében, mivel kétszer érték el a 90%-os küszöbértéket több időtárolókban.  Ha az *Eseményindító riasztás alapján:* paraméter helyett **only** *konfigurálva folyamatos megsértése beállítás,* majd egy riasztást csak srv03, mivel átlépte a küszöbértéket három egymást követő idő tárolók 1:10-1:20. És **nem** srv02, mivel átlépte a küszöbértéket két egymást követő idő ládák 1:10-1:15.

## <a name="log-search-alert-rule---firing-and-state"></a>Keresési riasztási szabály naplózása – kilövés és állapot

A naplókeresési riasztási szabályok csak a lekérdezésbe beépített logikán működnek. A riasztási rendszer nem rendelkezik a rendszer állapotának, a szándéknak vagy a lekérdezés által sugallt kiváltó oknak más környezetével. Mint ilyen, a naplóriasztásokat állapotnélkülinek nevezzük. A feltételek minden futtatáskor "TRUE" vagy "FALSE" értékűek.  A riasztás minden alkalommal aktiválódik, amikor a riasztási feltétel kiértékelése "TRUE", függetlenül attól, hogy korábban aktiválódik.    

Lássuk ezt a viselkedést akcióban egy gyakorlati példával. Tegyük fel, hogy van egy *Contoso-Log-Alert*nevű naplóriasztási szabályunk, amely az [Eredmények típusa naplóriasztásban megadott példában](#example-of-number-of-records-type-log-alert)látható módon van konfigurálva. A feltétel egy egyéni riasztási lekérdezés, amelyet úgy terveztek, hogy 500 eredménykódot keressen a naplókban. Ha még egy 500 találatkód található a naplókban, a riasztás állapota igaz. 

Az alábbi időközönként az Azure riasztási rendszer kiértékeli a *Contoso-Log-Alert feltételét.*


| Time    | A naplókeresési lekérdezés által visszaadott rekordok száma | Naplófeltétel-értékelés | Eredmény 
| ------- | ----------| ----------| ------- 
| 13:05 | 0 rekord | 0 nem > 0 olyan HAMIS |  A riadó nem lő. Nincs hívott művelet.
| 13:10 | 2 rekord | 2 > 0 így IGAZ  | Riasztási tüzek és akciócsoportok hívják. Riasztási állapot AKTÍV.
| 13:15 | 5 rekord | 5 > 0 így IGAZ  | Riasztási tüzek és akciócsoportok hívják. Riasztási állapot AKTÍV.
| 13:20 | 0 rekord | 0 nem > 0 olyan HAMIS |  A riadó nem lő. Nincs hívott művelet. A riasztási állapot aktív állapotban maradt.

Az előző eset használata példaként:

1:15-kor az Azure-riasztások nem tudják megállapítani, hogy az 1:10-kor látott mögöttes problémák megmaradnak-e, és hogy a rekordok nettó új hibák vagy régebbi hibák ismétlődései-e 1:10-kor. A felhasználó által biztosított lekérdezés lehet, hogy nem veszi figyelembe a korábbi rekordokat, és a rendszer nem tudja. Az Azure riasztási rendszer úgy van kialakítva, hogy az óvatosság oldalán tévedjen, és 1:15-kor újra elindítja a riasztást és a kapcsolódó műveleteket. 

1:20-kor, amikor nulla rekordok láthatók 500 találatkóddal, az Azure-riasztások nem lehetnek biztosak abban, hogy az 500 eredménykód oka 1:10-kor és 13:15-kor megoldva van.At 1:20 PM when zero records are seen with 500 result code, Azure alerts can't be certain that the cause of 500 result code seen at 1:10 pm and 1:15 PM is now solved. Nem tudja, hogy az 500-as hibaproblémák ugyanezen okok miatt újra megtörténnek-e. Ezért *a Contoso-Log-Alert* nem változik az Azure Alert irányítópultján **feloldott** állapotra, és/vagy nem küld értesítéseket a riasztás feloldása miatt. Csak ön, aki megérti a pontos feltételét vagy okát a logika beágyazott analytics lekérdezés, [megjelölheti a riasztást lezárt,](alerts-managing-alert-states.md) ha szükséges.

## <a name="pricing-and-billing-of-log-alerts"></a>A naplóértesítések ára és számlázása

A naplóriasztásokra vonatkozó díjszabás az [Azure Monitor díjszabási](https://azure.microsoft.com/pricing/details/monitor/) oldalán található. Az Azure-számlákban a naplóriasztások `microsoft.insights/scheduledqueryrules` típusként jelennek meg:

- Riasztások naplózása az Application Insights-on, amely pontos riasztási névvel, valamint erőforráscsoport- és riasztási tulajdonságokkal jelenik meg
- A log analytics riasztásai pontos riasztási névvel, valamint erőforráscsoport- és riasztási tulajdonságokkal jelennek meg a naplóban; [ütemezésiQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) használatával történő létrehozáskor

Az [örökölt Log Analytics API](../../azure-monitor/platform/api-alerts.md) riasztási műveleteket és ütemezéseket a Log Analytics mentett keresés részeként, és nem megfelelő Azure [Resources.](../../azure-resource-manager/management/overview.md) Ezért az Azure Portal használatával a Log Analytics számára létrehozott ilyen örökölt naplóriasztások számlázásának engedélyezése [új API-ra](../../azure-monitor/platform/alerts-log-api-switch.md) `microsoft.insights/scheduledqueryrules` való áttérés **vagy** a régi Log Analytics [API-n](../../azure-monitor/platform/api-alerts.md) keresztül – rejtett pszeudoriasztási szabályok jönnek létre az Azure-beli számlázáshoz. A rejtett pszeudo `microsoft.insights/scheduledqueryrules` riasztási `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` szabályok a számlázáshoz az erőforráscsoport és a riasztási tulajdonságok együtt látható módon létrehozott.

> [!NOTE]
> Ha érvénytelen karakterek, `<, >, %, &, \, ?, /` például jelen vannak, `_` a rendszer lecseréli őket a rejtett pszeudo riasztási szabály nevében, és így az Azure-számlán is.

A [riasztási](api-alerts.md)szabályok számlázásához létrehozott rejtett scheduleQueryRules erőforrások eltávolításához a felhasználó az alábbi célokra is elkészítheti az alábbi hibákat:

- Bármelyik felhasználó [válthat API-preferenciát a Log Analytics-munkaterület riasztási szabályaihoz,](../../azure-monitor/platform/alerts-log-api-switch.md) és a riasztási szabályok elvesztése nélkül, vagy figyelheti az Azure Resource Manager-nek megfelelő [scheduledQueryRules API-ra](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)való áttérést. Ezáltal kiküszöböli a pszeudo rejtett riasztási szabályok számlázási szükségességét.
- Vagy ha a felhasználó nem szeretne váltani az API-preferencia között, a felhasználónak törölnie kell az eredeti ütemezést, és riasztási műveletet kell **végrehajtania** az [örökölt Log Analytics API használatával,](api-alerts.md) vagy törölnie kell az Azure [Portalon az eredeti naplóriasztási szabályt](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal)

Emellett a rejtett scheduleQueryRules erőforrások hoz létre a riasztási szabályok számlázása a [régi Log Analytics API-t,](api-alerts.md)minden módosítási művelet, mint a PUT sikertelen lesz. Mivel `microsoft.insights/scheduledqueryrules` a típus pszeudoszabályok számlázása céljából a riasztási szabályok segítségével létrehozott örökölt Log Analytics API.As the type pseudo rules are for billing the alert rules created using [legacy Log Analytics API.](api-alerts.md) A riasztási szabályok módosítását [az örökölt Log Analytics API](api-alerts.md) használatával (vagy) a felhasználó [átválthat az API-beállításokat a riasztási szabályokhoz az](../../azure-monitor/platform/alerts-log-api-switch.md) [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) használatához.

## <a name="next-steps"></a>További lépések

* Ismerje [meg, hogyan hozhat létre naplóriasztásokat az Azure-ban.](../../azure-monitor/platform/alerts-log.md)
* Ismerje meg [a webhookokat az Azure-beli naplóriasztásokban.](alerts-log-webhook.md)
* További információ [az Azure-riasztásokról.](../../azure-monitor/platform/alerts-overview.md)
* További információ az [Application Insights](../../azure-monitor/app/analytics.md)ról.
* További információ a [Log Analytics szolgáltatásról.](../../azure-monitor/log-query/log-query-overview.md)
