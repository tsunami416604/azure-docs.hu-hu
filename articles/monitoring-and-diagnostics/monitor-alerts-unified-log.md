---
title: Naplóriasztások az Azure monitorban
description: Az eseményindító e-mailek, az értesítéseket, az elemzési lekérdezés által megadott feltételek teljesülnek az Azure Alerts webhelyek URL-címek (webhookok), vagy az automation hívni.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: fd1fb978fb47c69b2eb672bc27baee73dfdd0a29
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "42057581"
---
# <a name="log-alerts-in-azure-monitor---alerts"></a>Az Azure Monitor - riasztásokat a riasztások 
Ez a cikk ismerteti a riasztások részleteinek közé tartoznak a különböző típusú támogatott belül az új riasztások [Azure Alerts](monitoring-overview-unified-alerts.md) és a felhasználó használhat az Azure elemzési platform alapjaként, mert így.


Riasztás létre naplóbeli keresés szabályból áll [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) vagy [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events). Díjszabás a riasztások mindig elérhető legyen a [Azure Monitor szolgáltatás díjszabása](https://azure.microsoft.com/en-us/pricing/details/monitor/) lapot. Az Azure-számlák tartoznak, a riasztások jelentésekként jelennek meg a típus `microsoft.insights/scheduledqueryrules` együtt:
- Az Application Insights-erőforrás-csoport és riasztás tulajdonságai együtt pontos riasztás neve mellett látható riasztások
- A Log Analytics riasztási néven látható naplóriasztások `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` erőforráscsoportot és a riasztás tulajdonságai

    > [!NOTE]
    > A név minden mentett keresést, ütemezését és a Log Analytics API-val létrehozott műveleteket kisbetűs kell lennie. Ha érvénytelen karaktert `<, >, %, &, \, ?, /` vannak használja – ezek helyébe a `_` szerepel a számlán.

## <a name="log-search-alert-rule---definition-and-types"></a>Log search riasztásiszabály - definíció- és típusok

Az Azure Alerts naplókeresési szabályokat hoz létre megadott naplólekérdezések rendszeres időközönként való automatikus futtatására.  Ha a naplólekérdezés eredménye megfelel bizonyos feltételeknek, létrejön egy riasztásbejegyzés. A szabály ekkor automatikusan futtathat egy vagy több műveletet [Műveletcsoportok](monitoring-action-groups.md) használatával. 

Log search szabályok határozzák meg a következő adatokat:
- **Lekérdezés jelentkezzen**.  Akkor következik be, a lekérdezést, amely minden alkalommal lefut a riasztási szabályt.  A lekérdezés által visszaadott rekordok segítségével meghatározhatja, hogy létrejöjjön-e riasztást. *Az Azure Application Insights* lekérdezés is tartalmazhatnak [alkalmazások közti hívások](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery), ha a felhasználó rendelkezik hozzáférési jogokat a külső alkalmazások számára. 

    > [!IMPORTANT]
    > A Suppport [közötti alkalmazás lekérdezés az Application Insights](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery) 2 vagy több alkalmazások használata korlátozza a funkció előzetes verzió – és a felhasználói élmény a változhat. A használati [munkaterület lekérdezés közötti](https://dev.loganalytics.io/oms/documentation/3-Using-the-API/CrossResourceQuery) és [erőforrások közötti lekérdezési Log Analytics](../log-analytics/log-analytics-cross-workspace-search.md) jelenleg **nem támogatott** Azure alerts szolgáltatásban.

- **Időszak**.  Meghatározza az időtartományt a lekérdezés. A lekérdezés csak azokat a rekordokat adja vissza, amelyek az aktuális idő ezen tartományában jöttek létre. Adott időszakban korlátozza az adatokat, a visszaélések megelőzése érdekében naplólekérdezés beolvasott, és minden olyan alkalommal parancs megkerüli (például ezelőtt) napló lekérdezésben használt. <br>*Például ha az adott időszakban 60 percre van beállítva, és a lekérdezés futtatásakor: 1:15-kor, csak a rekordok között 12:15-kor és 1:15-kor létrehozott ad vissza log lekérdezés végrehajtásához. Ha a napló lekérdezés paranccsal például ezelőtt időt használ (7 nap), a naplólekérdezés fogja futtatni a rendszer csak a 12:15-kor és 1:15 PM - adatait, mintha az adatok csak az elmúlt 60 perc alatt állnak. Hét nap adatait a lekérdezési napló esetében nem.*
- **Gyakoriság**.  Itt adhatja meg, hogy milyen gyakran kell futtatni a lekérdezést. 5 perc és 24 óra között bármilyen érték lehet. Egyenlő vagy kisebb, mint az adott időszakban kell lennie.  Ha az értéke nagyobb, mint az adott időszakban, majd, kockázati éppen nem talált rekordokat.<br>*Vegyük példaként egy 30 perces időtartammal és 60 perces gyakoriságot is.  Ha a lekérdezés fut, 1:00-kor, 12:30 és 1:00 Órakor közötti rekordok adja vissza.  Amikor legközelebb szeretné futtatni a lekérdezést 2:00-t, ha ad vissza rekordok 1:30 és 2:00 között.  1:00 és 1:30 között létrehozott rekordokat szeretne soha nem értékelhető ki.*
- **Küszöbérték**.  A Naplókeresés eredménye értékeli ki a meghatározásához, hogy egy riasztást kell létrehozni.  A küszöbérték nem azonos a különféle keresési naplóriasztási szabály.

Log search szabályokat kell azt a [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) vagy [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events), kétféle típusú lehet. Ezek a típusok leírását a következő szakaszok részletesen ismertetjük.

- **[Az eredmények száma](#number-of-results-alert-rules)**. Egyetlen riasztás jön létre, amikor a naplóbeli keresés által visszaadott rekordokat meghaladja a megadott szám.
- **[Metrikus egység](#metric-measurement-alert-rules)**.  A megadott küszöbértéket meghaladó értékek naplóbeli keresés eredményei az egyes objektumok létrehozott riasztás.

Riasztási szabályok típusai közötti különbségek az alábbiak szerint.

- *Az eredmények száma* riasztási szabályok mindig létrehoz egy egyetlen riasztást, ideje *metrikamérési* riasztási szabály minden objektumon meghaladja a küszöbértéket, riasztást hoz létre.
- *Az eredmények száma* riasztási szabályok létrehozása egy riasztás a küszöbérték túllépésekor egy alkalommal. *Metrikus egység* riasztási szabályok riasztást hozhat létre, a küszöbérték túllépésekor a bizonyos számú alkalommal egy adott idő alatt.

### <a name="number-of-results-alert-rules"></a>Eredmények riasztási szabályok száma
**Az eredmények száma** riasztási szabályok egyetlen riasztás létrehozása, ha a keresési lekérdezés által visszaadott rekordok száma meghaladja a küszöbértéket. Riasztási szabály az ilyen típusú eseményeket, mint például a Windows-eseménynaplók, Syslog, WebApp válasz és egyéni naplók használata ideális.  Érdemes lehet, hogy hozzon létre egy riasztást, ha egy adott hibaesemény jön létre, vagy ha több hibaesemények jönnek létre egy adott időtartamon belül.

**Küszöbérték**: küszöbértéke egy eredmények riasztási szabályok száma nagyobb vagy kisebb, mint egy adott érték.  Ha a naplóbeli keresés által visszaadott rekordok száma megfelel a feltételeknek, egy riasztás jön létre.

A riasztás egy adott eseményhez, beállítva eredmények száma 0-nál nagyobbnak, és a egy egyszeri esemény, a lekérdezés futtatott legutóbbi indítása óta létrehozott ellenőrzése. Egyes alkalmazások bejelentkezhetnek alkalmanként hiba, amely nem feltétlenül hoz létre riasztást.  Az alkalmazás például ismételje meg a folyamat által létrehozott hibaesemény és a következő alkalommal majd sikeres.  Ebben az esetben előfordulhat, hogy nem szeretné a riasztás létrehozása, kivéve, ha több esemény egy adott időtartamon belül jönnek létre.  

Bizonyos esetekben érdemes hiányában az esemény riasztás létrehozásához.  Egy folyamatot például előfordulhat, hogy jelentkezzen jelzi, hogy megfelelően működik-e rendszeres eseményekhez.  Ha ez nem egy ilyen eseményt jelentkezik a egy adott időtartamon belül, egy riasztást kell létrehozni.  Ebben az esetben akkor értékre kell állítania a küszöbérték **1-nél kisebb**.

#### <a name="example"></a>Példa
Példaként vegyünk egy forgatókönyvet, ahol érdemes figyelembe venni, amikor a webes alkalmazás lehetővé teszi a felhasználók számára, 500-as kóddal választ (vagyis) belső kiszolgálóhiba. Riasztási szabály vznikla a következő adatokkal:  
- **Lekérdezés:** kérelmek |} ahol resultCode == "500"<br>
- **Adott időszakban:** 30 perc<br>
- **Riasztási időköz:** öt perc alatt<br>
- **Küszöbérték:** 0-nál nagyobb<br>

A riasztás lenne a lekérdezés futtatásával 5 percenként, a 30 percnyi adat - rekordot keres, ahol volt az eredménykód a 500-as. Ha még egy ilyen rekord található, a riasztás akkor aktiválódik, és eseményindítók a beállított műveleteket.

### <a name="metric-measurement-alert-rules"></a>Metrikamérési riasztási szabályok

- **Metrikus egység** riasztási szabályok az egyes objektumok riasztás létrehozása, a lekérdezés egy értéket, amely meghalad egy megadott küszöbértéket.  A következő közötti különbségeket az rendelkeznek **eredmények száma** riasztási szabályok.
- **Összesített függvény**: meghatározza, hogy a számítás végrehajtott műveletek, és egy numerikus mezőjében összesítendő.  Ha például **count()** rekordok számát adja vissza a lekérdezés, **avg(CounterValue)** az időtartamra, az AVG mező átlagát adja vissza. A lekérdezés aggregátumfüggvényt nevű/nevű kell lennie: AggregatedValue és a egy numerikus értéket adjon meg. 
- **A mező csoport**: egy rekord egy aggregált értékre, ez a mező minden egyes példányánál jön létre, és a egy riasztás minden létrehozható.  Például, ha szeretne az egyes számítógépekhez riasztást hoz létre, használja **számítógépenként** 

    > [!NOTE]
    > A metrikamérési riasztási szabályok az Application Insights alapuló megadhatja a mező az adatok csoportosításához. Ehhez használja a **az összesített** lehetőség a szabály-definícióban.   
    
- **Intervallum**: határozza meg az időintervallum, amelyben az adatok összesítve.  Például, ha a megadott **öt perc alatt**, létrehozott egy rekord minden példányához az a csoportmező, 5 perces időközönként a riasztás a megadott időszakra összesített értéket jelenít.

    > [!NOTE]
    > Lekérdezési időköz megadása bin függvény kell használható. Bin() egyenlőtlen időintervallumok - eredményezhet, a riasztás egy rögzített ponttal eredmények biztosítása automatikusan átalakítja bin parancs bin_at parancs futásidőben, megfelelő idő
    
- **Küszöbérték**: metrikamérési riasztási szabályok küszöbértéke összesített érték és a egy aktivista álláspontokkal van definiálva.  A Naplókeresés az adatpontok meghaladja ezt az értéket, ha figyelembe vette megsértése.  Ha bármely objektumához az eredményeket az illetéktelen behatolásokat száma meghaladja a megadott értéket, majd riasztást az adott objektum jön létre.

#### <a name="example"></a>Példa
Példaként vegyünk egy forgatókönyvet, ahol szeretett volna egy riasztás minden olyan számítógép túllépése processzorhasználat 90 %-os háromszor több mint 30 perc.  Riasztási szabály vznikla a következő adatokkal:  

- **Lekérdezés:** Teljesítményoptimalizált |} ahol ObjectName == "Processzor" és a CounterName == "%-ban a processzoron" |} summarize AggregatedValue = avg(CounterValue) bin (TimeGenerated, 5 m), a számítógép szerint<br>
- **Adott időszakban:** 30 perc<br>
- **Riasztási időköz:** öt perc alatt<br>
- **Összesített érték:** nagyobb, mint 90<br>
- **Eseményindító riasztás alapján:** összesen feltöri a 2-nél nagyobb<br>

A lekérdezés minden olyan számítógépen átlagos értékét hozna létre, 5 perces időközönként.  Ez a lekérdezés szeretné futtatni át 5 percenként összegyűjtött adatokat az előző 30 perc.  Mintaadatok három számítógép az alább látható.

![Mintául szolgáló lekérdezés eredményei](./media/monitor-alerts-unified/metrics-measurement-sample-graph.png)

Ebben a példában külön riasztások létrehozott srv02 és srv03 mivel ezek a 90 %-os küszöbértéket megsértették 3 alkalommal keresztül az adott időszakban.  Ha a **eseményindító riasztás alapján:** értékről **folyamatos** és a egy riasztás akkor hozhatók létre csak srv03 óta, a három egymást követő minták küszöbértéket megsértették.


## <a name="log-search-alert-rule---creation-and-modification"></a>Log search riasztásiszabály - létrehozási és -módosítási

Riasztás, valamint a hozzá tartozó consisting log search riasztási szabály megtekinthető, létrehozva, vagy módosítani:
- Azure Portal
- REST API-k (beleértve a Powershellen keresztül)
- Az Azure Resource Manager-sablonok

### <a name="azure-portal"></a>Azure Portal
A bevezetése óta az [új Azure-riasztások](monitoring-overview-unified-alerts.md)már a felhasználók kezelhetik az összes riasztási típusokat, az Azure Portalon egyetlen helyről, és a használati hasonló lépéseket. Tudjon meg többet [új Azure-riasztások segítségével](monitor-alerts-unified-usage.md).

Emellett a felhasználók is tökéletes az elemzési platform az Azure-ban választott lekérdezéseit, majd *importálja azokat az értesítések használatra menti a lekérdezés*. Hajtsa végre a lépéseket:
- *Az Application Insights*: ideális analitika portálon ellenőrizze a lekérdezés, és az eredményeket. Egyedi névvel, majd mentse *megosztott lekérdezések*.
- *A Log Analytics*: ideális naplófájl-keresési lekérdezést és az eredmények ellenőrzése. Majd egy kategóriába mentse egyedi névvel.

Majd ha [naplóriasztás létrehozása a riasztások ](monitor-alerts-unified-usage.md), láthatja, hogy a korábban mentett lekérdezés jeltípus állapottal **Log (mentett lekérdezés)**; az alábbi példában szemléltetett módon: ![mentett lekérdezés riasztások importálása](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog-new.png)

> [!NOTE]
> Használatával **Log (mentett lekérdezés)** riasztások importálását eredményez. Ezért az elemzés után végzett módosításokat nem reflektív keresési naplóriasztási szabály és fordítva.

### <a name="rest-apis"></a>REST API-k
API-k a megadott riasztások RESTful, és az Azure Resource Manager REST API-n keresztül érhetők el naplók. Ezért keresztül is elérhető PowerShell-lel, k, valamint egyéb beállítások.

A részleteket, valamint a REST API-val kapcsolatos példák; csak az itt található:
- [Log Analytics-riasztás REST API](../log-analytics/log-analytics-api-alerts.md) – hozhat létre, és a log search riasztási szabályok kezelése az Azure Log Analytics
- [Az Azure Monitor ütemezett lekérdezési szabályok REST API-val](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) – hozhat létre, és az Azure Application Insights keresés naplóriasztási szabály kezelése

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
A felhasználó is használhatja a rugalmasságot [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) létrehozni és frissíteni az erőforrás - létrehozása vagy frissítése a naplóriasztások esetében.

A részleteket, valamint a példák a Resource Manager-sablonokkal; csak az itt található:
- [Mentett keresés és a riasztások kezelése](monitor-alerts-unified-log-template.md#managing-log-alert-on-log-analytics) a riasztásokra az Azure Log Analyticsre épül
- [Ütemezett lekérdezési szabály](monitor-alerts-unified-log-template.md#managing-log-alert-on-application-insights) az Azure Application Insights alapuló riasztások
 

## <a name="next-steps"></a>További lépések
* Megismerheti [naplóriasztások az Azure-ban a webhookok](monitor-alerts-unified-log-webhook.md).
* Ismerje meg az új [Azure Alerts](monitoring-overview-unified-alerts.md).
* Tudjon meg többet [Application Insights](../application-insights/app-insights-analytics.md).
* Tudjon meg többet [Log Analytics](../log-analytics/log-analytics-overview.md).    
