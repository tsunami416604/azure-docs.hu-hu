---
title: Naplóriasztások az Azure monitorban
description: Az eseményindító e-mailek, az értesítéseket, az elemzési lekérdezés által megadott feltételek teljesülnek az Azure Alerts webhelyek URL-címek (webhookok), vagy az automation hívni.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 70f53ed06daad8adf10ef5a88f0672f86d6a8b48
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004128"
---
# <a name="log-alerts-in-azure-monitor"></a>Naplóriasztások az Azure monitorban
Ez a cikk ismerteti a riasztások részleteinek közé tartoznak a különböző típusú riasztások belül támogatott a [Azure Alerts](../platform/alerts-overview.md) és a felhasználó használhat az Azure elemzési platform alapjaként, mert így.

Riasztás áll számára létrehozott lekérdezési szabály [Azure Monitor](../learn/tutorial-viewdata.md) vagy [Application Insights](../app/cloudservices.md#view-azure-diagnostics-events). A használattal kapcsolatos további információkért lásd: [riasztások létrehozása az Azure-ban](../platform/alerts-log.md)

> [!NOTE]
> Népszerű naplóadatait [Azure Monitor](../learn/tutorial-viewdata.md) is már elérhető az Azure monitorban metrika-platformon. A Részletek nézetben [naplók riasztási metrika](../platform/alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Log search riasztásiszabály - definíció- és típusok

Az Azure Alerts naplókeresési szabályokat hoz létre megadott naplólekérdezések rendszeres időközönként való automatikus futtatására.  Ha a naplólekérdezés eredménye megfelel bizonyos feltételeknek, létrejön egy riasztásbejegyzés. A szabály ekkor automatikusan futtathat egy vagy több műveletet [Műveletcsoportok](../../azure-monitor/platform/action-groups.md) használatával. [Az Azure Monitoring közreműködői](../../azure-monitor/platform/roles-permissions-security.md) szerepkör létrehozása, módosítása és frissítése a riasztások lehet szükség; hozzáférés- és lekérdezés végrehajtási jogosultságokat a riasztási szabály vagy a riasztási lekérdezés analytics cél(ok) együtt. Ha a felhasználó létrehozása nem fér hozzá a riasztási szabály vagy a riasztási lekérdezés – az összes analytics cél(ok) a szabály létrehozása meghiúsulhat, vagy a riasztási szabály lesz végrehajtva a részleges eredményeket.

Log search szabályok határozzák meg a következő adatokat:

- **Lekérdezés jelentkezzen**.  Akkor következik be, a lekérdezést, amely minden alkalommal lefut a riasztási szabályt.  A lekérdezés által visszaadott rekordok segítségével megállapítható, hogy van-e riasztást aktiválását. Elemzési lekérdezés legyen egy adott Log Analytics-munkaterületen vagy az Application Insights alkalmazást, és akár ívelhet át több [több Log Analytics és az Application Insights-erőforrások](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) biztosított a felhasználó rendelkezik hozzáférési jogosultsága ahhoz, hogy a külső az alkalmazások. Elemzési parancsokat és kombinációk nem kompatibilisek a naplóriasztások; használata a további részletek megtekintéséhez [riasztási lekérdezések jelentkezzen be az Azure Monitor](../../azure-monitor/platform/alerts-log-query.md).

    > [!IMPORTANT]
    > Riasztás **nem** támogatja [funkciók](../log-query/functions.md) biztonsági okokból. És felhasználói kell adja meg a teljes analytics-lekérdezést, és teljes hozzáféréssel & végrehajtási jogokat vele egy riasztási szabály létrehozásához.

- **Időszak**.  Meghatározza az időtartományt a lekérdezés. A lekérdezés csak azokat a rekordokat adja vissza, amelyek az aktuális idő ezen tartományában jöttek létre. Adott időszakban korlátozza az adatokat, a visszaélések megelőzése érdekében naplólekérdezés beolvasott, és minden olyan alkalommal parancs megkerüli (például ezelőtt) napló lekérdezésben használt. <br>*Például ha az adott időszakban 60 percre van beállítva, és a lekérdezés futtatásakor: 1:15-kor, csak a rekordok között 12:15-kor és 1:15-kor létrehozott ad vissza log lekérdezés végrehajtásához. Ha a napló lekérdezés paranccsal például ezelőtt időt használ (7 nap), a naplólekérdezés fogja futtatni a rendszer csak a 12:15-kor és 1:15 PM - adatait, mintha az adatok csak az elmúlt 60 perc alatt állnak. Hét nap adatait a lekérdezési napló esetében nem.*

- **Gyakoriság**.  Itt adhatja meg, hogy milyen gyakran kell futtatni a lekérdezést. 5 perc és 24 óra között bármilyen érték lehet. Egyenlő vagy kisebb, mint az adott időszakban kell lennie.  Ha az értéke nagyobb, mint az adott időszakban, majd, kockázati éppen nem talált rekordokat.<br>*Vegyük példaként egy 30 perces időtartammal és 60 perces gyakoriságot is.  Ha a lekérdezés fut, 1:00-kor, 12:30 és 1:00 Órakor közötti rekordok adja vissza.  Amikor legközelebb szeretné futtatni a lekérdezést 2:00-t, ha ad vissza rekordok 1:30 és 2:00 között.  1:00 és 1:30 között létrehozott rekordokat szeretne soha nem értékelhető ki.*

- **Küszöbérték**.  A Naplókeresés eredménye értékeli ki a meghatározásához, hogy egy riasztást kell létrehozni.  A küszöbérték nem azonos a különféle keresési naplóriasztási szabály.

Legyen szó a lekérdezési szabályok jelentkezzen [Azure Monitor](../learn/tutorial-viewdata.md) vagy [Application Insights](../app/cloudservices.md#view-azure-diagnostics-events), kétféle típusú lehet. Ezek a típusok leírását a következő szakaszok részletesen ismertetjük.

- **[Az eredmények száma](#number-of-results-alert-rules)**. Egyetlen riasztás jön létre, amikor a naplóbeli keresés által visszaadott rekordokat meghaladja a megadott szám.
- **[Metrikus egység](#metric-measurement-alert-rules)**.  A megadott küszöbértéket meghaladó értékek naplóbeli keresés eredményei az egyes objektumok létrehozott riasztás.

Riasztási szabályok típusai közötti különbségek az alábbiak szerint.

- *Az eredmények száma* riasztási szabályok mindig létrehoz egy egyetlen riasztást, ideje *metrikamérési* riasztási szabály minden objektumon meghaladja a küszöbértéket, riasztást hoz létre.
- *Az eredmények száma* riasztási szabályok létrehozása egy riasztás a küszöbérték túllépésekor egy alkalommal. *Metrikus egység* riasztási szabályok riasztást hozhat létre, a küszöbérték túllépésekor a bizonyos számú alkalommal egy adott idő alatt.

### <a name="number-of-results-alert-rules"></a>Eredmények riasztási szabályok száma

**Az eredmények száma** riasztási szabályok egyetlen riasztás létrehozása, ha a keresési lekérdezés által visszaadott rekordok száma meghaladja a küszöbértéket. Riasztási szabály az ilyen típusú eseményeket, mint például a Windows-eseménynaplók, Syslog, WebApp válasz és egyéni naplók használata ideális.  Érdemes lehet, hogy hozzon létre egy riasztást, ha egy adott hibaesemény jön létre, vagy ha több hibaesemények jönnek létre egy adott időtartamon belül.

**Küszöbérték**: Eredmények riasztási szabályok számos küszöbértéke nagyobb vagy kisebb, mint egy adott érték.  Ha a naplóbeli keresés által visszaadott rekordok száma megfelel a feltételeknek, egy riasztás jön létre.

A riasztás egy adott eseményhez, beállítva eredmények száma 0-nál nagyobbnak, és a egy egyszeri esemény, a lekérdezés futtatott legutóbbi indítása óta létrehozott ellenőrzése. Egyes alkalmazások bejelentkezhetnek alkalmanként hiba, amely nem feltétlenül hoz létre riasztást.  Az alkalmazás például ismételje meg a folyamat által létrehozott hibaesemény és a következő alkalommal majd sikeres.  Ebben az esetben előfordulhat, hogy nem szeretné a riasztás létrehozása, kivéve, ha több esemény egy adott időtartamon belül jönnek létre.  

Bizonyos esetekben érdemes hiányában az esemény riasztás létrehozásához.  Egy folyamatot például előfordulhat, hogy jelentkezzen jelzi, hogy megfelelően működik-e rendszeres eseményekhez.  Ha ez nem egy ilyen eseményt jelentkezik a egy adott időtartamon belül, egy riasztást kell létrehozni.  Ebben az esetben akkor értékre kell állítania a küszöbérték **1-nél kisebb**.

#### <a name="example-of-number-of-records-type-log-alert"></a>Rekordok száma típusú riasztás – példa

Példaként vegyünk egy forgatókönyvet, ahol érdemes figyelembe venni, amikor a webes alkalmazás lehetővé teszi a felhasználók számára, 500-as kóddal választ (vagyis) belső kiszolgálóhiba. Riasztási szabály vznikla a következő adatokkal:  

- **Lekérdezés:** kérelmek |} ahol resultCode == "500"<br>
- **Időszak:** 30 perc<br>
- **Riasztási időköz:** öt perc alatt<br>
- **Küszöbérték:** 0-nál nagyobb<br>

A riasztás lenne a lekérdezés futtatásával 5 percenként, a 30 percnyi adat - rekordot keres, ahol volt az eredménykód a 500-as. Ha még egy ilyen rekord található, a riasztás akkor aktiválódik, és eseményindítók a beállított műveleteket.

### <a name="metric-measurement-alert-rules"></a>Metrikamérési riasztási szabályok

**Metrikus egység** riasztási szabályok az egyes objektumok riasztás létrehozása, a lekérdezés egy értéket, amely meghalad egy megadott küszöbértéket.  A következő közötti különbségeket az rendelkeznek **eredmények száma** riasztási szabályok.

- **Összesített függvény**: Meghatározza, hogy a számítás végrehajtott műveletek, és egy numerikus mezőjében összesítendő.  Ha például **count()** rekordok számát adja vissza a lekérdezés, **avg(CounterValue)** az időtartamra, az AVG mező átlagát adja vissza. A lekérdezés aggregátumfüggvényt nevű/nevű kell lennie: AggregatedValue és a egy numerikus értéket adjon meg. 

- **A mező csoport**: Egy rekord egy aggregált értékre jön létre minden egyes példányánál ezt a mezőt, és riasztást minden létrehozható.  Például, ha szeretne az egyes számítógépekhez riasztást hoz létre, használja **számítógépenként**. Arra az esetre vannak több csoport mező van megadva a riasztáshoz kapcsolódó lekérdezés, a felhasználó megadhatja, hogy melyik mezőt rendezéséhez használandó eredmények használatával a **összesített a** (metricColumn) paramétert

    > [!NOTE]
    > *Összesített a* (metricColumn) lehetőség érhető el a Metrikamérés típusú riasztások az Application Insights és a naplófájlok riasztások [scheduledQueryRules API használatával konfigurálva a Log Analytics](alerts-log-api-switch.md) csak.

- **Intervallum**:  Határozza meg az időintervallum, amelyben az adatok összesítve.  Például, ha a megadott **öt perc alatt**, létrehozott egy rekord minden példányához az a csoportmező, 5 perces időközönként a riasztás a megadott időszakra összesített értéket jelenít.

    > [!NOTE]
    > Lekérdezési időköz megadása bin függvény kell használható. Bin() egyenlőtlen időintervallumok - eredményezhet, a riasztás automatikusan átalakul bin parancs bin_at parancsot a megfelelő időben futásidőben, rögzített ponttal eredmények biztosítása. Metrikus egység típusú riasztás úgy tervezték, hogy bin() parancs három példánnyal rendelkező lekérdezések használata
    
- **Küszöbérték**: A küszöbérték metrikamérési riasztási szabályok összesített érték és a egy aktivista álláspontokkal határozzák meg.  A Naplókeresés az adatpontok meghaladja ezt az értéket, ha figyelembe vette megsértése.  Ha bármely objektumához az eredményeket az illetéktelen behatolásokat száma meghaladja a megadott értéket, majd riasztást az adott objektum jön létre.

Helytelen konfigurálása a *összesített a* vagy *metricColumn* beállítás hatására misfire riasztási szabályokat. További információkért lásd: [metrikamérési riasztási szabályt helytelen elhárítása](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect).

#### <a name="example-of-metric-measurement-type-log-alert"></a>Metrikus típus riasztás – példa

Példaként vegyünk egy forgatókönyvet, ahol szeretett volna egy riasztás minden olyan számítógép túllépése processzorhasználat 90 %-os háromszor több mint 30 perc.  Riasztási szabály vznikla a következő adatokkal:  

- **Lekérdezés:** Teljesítményoptimalizált |} ahol ObjectName == "Processzor" és a CounterName == "processzoridő" |} summarize AggregatedValue = avg(CounterValue) bin (TimeGenerated, 5m), a számítógép szerint<br>
- **Időszak:** 30 perc<br>
- **Riasztási időköz:** öt perc alatt<br>
- **Összesített érték:** Nagyobb, mint 90<br>
- **Eseményindító riasztás alapja:** Teljes feltöri a 2-nél nagyobb<br>

A lekérdezés minden olyan számítógépen átlagos értékét hozna létre, 5 perces időközönként.  Ez a lekérdezés szeretné futtatni át 5 percenként összegyűjtött adatokat az előző 30 perc.  Mintaadatok három számítógép az alább látható.

![Mintául szolgáló lekérdezés eredményei](media/alerts-unified-log/metrics-measurement-sample-graph.png)

Ebben a példában külön riasztások létrehozott srv02 és srv03 mivel ezek a 90 %-os küszöbértéket megsértették háromszor keresztül az adott időszakban.  Ha a **eseményindító riasztás alapján:** értékről **folyamatos** és a egy riasztás akkor hozhatók létre csak srv03 óta, a három egymást követő minták küszöbértéket megsértették.

## <a name="log-search-alert-rule---firing-and-state"></a>Keresés riasztási szabály - elsőre és állapota

Keresés riasztási szabály a logikai való megfelelően konfigurációja és az egyéni elemzési lekérdezés, használja a felhasználó határozza működik. Analytics-lekérdezések – amelyek eltérőek lehetnek az egyes riasztási szabály óta a pontos feltétel, vagy akár indoklás miért érdemes a a riasztási szabály logikáját eseményindító van beágyazva. Azure-riasztások van az adott alapul szolgáló kiváltó belül a eredményeihez szűkös információi, ha a keresés riasztási szabály küszöbértékét feltétele teljesül, vagy túllépte a. Így a naplóriasztások hivatkozunk, például állapot nélküli, és minden alkalommal, amikor a naplózott keresési eredményeknek ahhoz, hogy a riasztások a megadott küszöbértéket fog aktiválódni *eredmények száma* vagy *metrikamérési* típusa feltétel. És -beli naplóriasztási szabályok folyamatosan tartsa aktiválja, mindaddig, amíg a riasztási feltétel teljesülésekor által biztosított; egyéni elemzési lekérdezés eredménye anélkül, hogy a riasztás minden első feloldva. Az elemzési lekérdezés; felhasználó által megadott belső maszkolva van, a pontos kiváltó hiba figyelési logikáját Nincs nem azt jelenti, hogy melyik Azure-riasztások von következtetni e naplózott keresési eredményeknek nem felel meg a küszöbérték azt jelzi, hogy a probléma megoldási szerint.

Most már feltételezik, hogy rendelkezünk egy úgynevezett riasztási szabály *Contoso Naplóriasztás*, a konfiguráció szerint a [száma az eredmények típusú riasztás biztosított](#example-of-number-of-records-type-log-alert). 
- 1:05 du.: Ha a Contoso-Log-riasztás hajtott végre Azure-riasztások a naplózott keresési eredményeknek kurzorműveletnek 0 rekordot; alább a küszöbérték, és ezért nem aktiválja a riasztást. 
- A következő verzió továbbfejlesztésében 1: alapszintűről mikor Contoso Naplóriasztás hajtott végre Azure-riasztások, a naplózott keresési eredményeknek megadott 5 rekordjának; meghaladja a küszöbértéket, és a riasztást kiváltó után minél hamarabb elindításával a [műveletcsoport](../platform/action-groups.md) társítva. 
- 1:15-kor mikor Contoso Naplóriasztás hajtott végre Azure-riasztások, a naplózott keresési eredményeknek megadott 2 rekordok; meghaladja a küszöbértéket, és a riasztást kiváltó után minél hamarabb elindításával a [műveletcsoport](../platform/action-groups.md) társítva.
- Jelenleg a következő verzió továbbfejlesztésében du. 1:20 mikor Contoso Naplóriasztás hajtott végre az Azure riasztás, a naplózott keresési eredményeknek most megadott újra 0 rekordot; alább a küszöbérték, és ezért nem aktiválja a riasztást.

De a fenti listán szereplő esetben 1:15 PM -, Azure-riasztások nem tudja megállapítani, hogy az észlelés időpontja: 1:10 alapul szolgáló problémák továbbra is fennállnak-e és van-e nettó új hibák; felhasználó által megadott lekérdezést is kell figyelembe véve korábbi rekordok -, Azure-riasztások biztos lehet. Ezért, járjon el, ha a Contoso Naplóriasztási oldalán err hajtja végre: 1:15-kor, konfigurált [műveletcsoport](../platform/action-groups.md) újra lesz elindítva. Du. 1:20 Ha rekordokat nem láthatók – Azure-riasztások nem lehet róla, hogy most már a rekordok okának megoldódott; ezért a Contoso-Log-riasztás fog megoldott riasztás Azure-irányítópult és/vagy értesítéseket figyelmezteti a riasztás feloldása nem változott.


## <a name="pricing-and-billing-of-log-alerts"></a>Árak és számlázás az riasztások

Naplóriasztásokra vonatkozó díjszabás érvényes van megadva a [Azure Monitor szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/monitor/) lapot. Az Azure-számlák tartoznak, a riasztások jelentésekként jelennek meg a típus `microsoft.insights/scheduledqueryrules` együtt:

- Az Application Insights-erőforrás-csoport és riasztás tulajdonságai együtt pontos riasztás neve mellett látható riasztások
- Riasztások a Log Analytics jelenik meg az erőforráscsoportot és a riasztás tulajdonságai; valamint pontos riasztás neve Ha létrehozott [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 
- Riasztások a Log Analytics riasztási néven látható jelentkezzen `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` erőforráscsoportot és a riasztás tulajdonságai, ha létrehozás keresztül [örökölt Log Analytics API](api-alerts.md) vagy az Azure portal használatát **nélkül** új API-t önkéntesen átváltása

    > [!NOTE]
    > Ha érvénytelen karaktert `<, >, %, &, \, ?, /` jelen, akkor fogja írni a `_` szerepel a számlán. A riasztási szabályok használatával számlázását a létrehozott scheduleQueryRules erőforrást törölni [örökölt Log Analytics API](api-alerts.md) -felhasználónak kell törölni az eredeti ütemezés és a riasztási művelet használatával [örökölt Log Analytics API](api-alerts.md)

## <a name="next-steps"></a>További lépések
* Ismerje meg [létrehozása a naplóriasztások az Azure-ban](../platform/alerts-log.md).
* Megismerheti [naplóriasztások az Azure-ban a webhookok](alerts-log-webhook.md).
* Ismerje meg [Azure-riasztások](../platform/alerts-overview.md).
* Tudjon meg többet [Application Insights](../app/analytics.md).
* Tudjon meg többet [Azure Monitor log-lekérdezések](../log-query/log-query-overview.md).    
