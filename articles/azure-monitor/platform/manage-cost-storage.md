---
title: Használat és költségek kezelése az Azure Log Analytics |} A Microsoft Docs
description: Ismerje meg, hogyan módosíthatja, a díjszabással és kezelése az adatok mennyisége és a megőrzési házirend a Log Analytics-munkaterület az Azure-ban.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: d6b0093e396e87b528bd8ccbdb8b5454c06dfac1
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106290"
---
# <a name="manage-usage-and-costs-for-log-analytics"></a>A Log Analytics használat és költségek kezelése

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan tarthatja a költségeket, a Log Analytics által az adatok megőrzési időszak beállítása.  További kapcsolódó információt a következő cikkekben talál.
> - [Adathasználat elemzése a Log Analytics](manage-cost-storage.md) azt ismerteti, hogyan elemezheti, és riasztást küldjön az adathasználatot.
> - [Használat és becsült költségek figyelése](usage-estimated-costs.md) ismerteti, hogyan lehet megtekinteni a használati és becsült költségek figyelési funkciók eltérő díjszabási modelleket a több Azure-ban. Emellett bemutatja, hogyan lehet módosítani a díjszabási modellt.

A log Analytics méretezési és támogatási gyűjtése, az indexelés és a vállalati adatok naponta bármilyen forrásból származó nagy mennyiségű tárolására tervezték vagy üzembe helyezve az Azure-ban.  Ez lehet egy elsődleges illesztőprogram, a szervezet számára, miközben költséghatékonyságot végső soron az alapul szolgáló illesztőprogram. Ebből a célból, fontos ismerni a nem a Log Analytics-munkaterület költségét csak alapján összegyűjtött adatok mennyisége is a kiválasztott csomag függ, és mennyi ideig úgy döntött, hogy a csatlakoztatott források által létrehozott adatokat tárolni.  

Ez a cikk áttekintettük, hogyan proaktívan figyelheti adatok mennyisége és a storage növekedési, és azok a kapcsolódó költségek szabályozására vonatkozó korlátok beállításához. 

Lehet, hogy a költség az adatok jelentős függően az alábbi tényezőket: 

- Jön létre, és a munkaterület betöltött adatok mennyiségét 
    - Engedélyezett felügyeleti megoldások száma
    - Figyelt rendszerek száma
    - Minden figyelt erőforrásból gyűjtött adatok típusa 
- Mennyi ideig kívánja megőrizni az adatokat 

## <a name="understand-your-workspaces-usage-and-estimated-cost"></a>A munkaterület használati és becsült költségek ismertetése
Log Analytics teszi egyszerűvé, mire a költségek várhatóan legutóbbi használati minták alapján.  Ehhez használja **Log Analytics-használat és becsült költségek** segítségével áttekintheti és elemezheti az adathasználatot. Az egyes megoldások által összegyűjtött adatok mennyiségét jeleníti meg, mennyi adatot alatt marad, és a költségek becslése betöltött adatok és adatmegőrzés minden csomagban foglalt adatmennyiségen felüli mennyisége alapján.

![Használat és becsült költségek](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Ismerje meg részletesen az adatokat, kattintson a felső ikonra vagy a diagramokat, jobb a **használat és becsült költségek** lap. Most már dolgozhat a lekérdezés további részletek, amely a használat.  

![Naplók megtekintése](media/manage-cost-storage/logs.png)

Az a **használat és becsült költségek** lapon áttekintheti a továbbított adatmennyiség hónapban. Ez magában foglalja a fogadott és a Log Analytics-munkaterület összes adatot.  Kattintson a **használatról** adatok mennyiségi trendek forrás, a számítógépek és az ajánlat az adatokat a használati irányítópult megtekintése a lap tetején. Megtekintheti, és állítsa be a maximális napi adatmennyiséget, vagy módosíthatja a megőrzési időszak kattintson **adatmennyiség-kezelés**.
 
Log Analytics díjak az Azure-elszámolások kerülnek. A számlázás szakaszában az Azure Portal vagy a számlázás az Azure részleteit láthatja a [Azure Billing Portal](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Napi korlát
Konfigurálja a maximális napi adatmennyiséget, és korlátozhatja a napi bevitelt a munkaterületen, de körültekintően járjon el, mert a cél nem lehet eléri a napi korlátot.  Ellenkező esetben az a nap fennmaradó részében, ami hatással lehet más Azure-szolgáltatások és megoldások, amelynek funkció függhet, naprakész adatok legyenek elérhetők a munkaterület az adatok elvesznek.  Ennek eredményeképpen figyelje meg, és fogadjon megítélnie riasztást küld, ha is hatással van az informatikai szolgáltatásokat támogató erőforrásokban egészségügyi feltételeit.  A maximális napi adatmennyiséget célja, hogy a felügyelt erőforrásoktól érkező adatmennyiség váratlan növekedés kezeléséhez, és maradjon a határidőn belül, vagy ha egyszerűen korlátozhatja a munkaterületre vonatkozó nem tervezett költségek mennyiségét szeretné használni.  

A napi korlátot, a gyűjtemény számlázható adattípusok leállítja az a nap hátralevő. Egy figyelmeztetés szalagcím jelenik meg a kiválasztott Log Analytics-munkaterületet a lap tetején, és a egy műveletet a rendszer a *művelet* tábla alatt **LogManagement** kategória. Az adatgyűjtés után a visszaállítási idő alatt meghatározott folytatja *napi korlát lesz beállítva*. Azt javasoljuk, hogy arra az esetre, ha a napi korlátot elérte a konfigurált Ez a művelet események alapján riasztási szabály meghatározása. 

### <a name="identify-what-daily-data-limit-to-define"></a>Milyen napi korlátot meghatározásához azonosítása 
Felülvizsgálat [Log Analytics-használat és becsült költségek](usage-estimated-costs.md) megtudhatja, hogy az adatok betöltési trend és mi az a napi mennyiségi korlát meghatározásához. Kell tekinteni, körültekintően, mivel nem lehet az erőforrások figyeléséhez, a korlát elérése után. 

### <a name="manage-the-maximum-daily-data-volume"></a>A maximális napi adatmennyiség kezelése 
A következő lépések bemutatják, hogyan konfigurálása a Log Analytics lesz képes feldolgozni naponta adatmennyiség kezelése korlátozva.  

1. A munkaterületen válassza a **Felhasználás és becsült költségek** lehetőséget a bal oldali panelen.
2. Az a **felhasználás és becsült költségek** a kijelölt munkaterület oldalára, kattintson **adatmennyiség-kezelés** az oldal tetején. 
3. Napi korlát a következő **OFF** – alapértelmezés szerint kattintson **ON** az engedélyezéshez, és állítsa az mennyiségi korlát a GB/nap.<br><br> ![A log Analytics konfigurálása adatkorlát](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Riasztás, ha elérte a napi korlát
Hogy jelen visual köteg az Azure Portalon, ha az adatok korlát küszöbértéket, amíg ez a viselkedés nem feltétlenül igazítás azonnali figyelmet igénylő működési problémák kezelése.  Az Azure Monitor riasztási értesítés fogadására, létrehozhat egy új riasztási szabály.  További tudnivalókért lásd: [létrehozása, megtekintése és kezelése a riasztások](alerts-metric.md).      

Az első lépésekhez, az alábbiakban a riasztás az ajánlott beállításokat:

* Cél: Válassza ki a Log Analytics-erőforrás
* Feltételek: 
   * Jel neve: Egyéni naplók keresése
   * Keresési lekérdezés: A művelet |} Ha részletes rendelkezik-e "termékváltozatként használja:
   * Alapján: Eredmények száma
   * Feltétel: Nagyobb, mint
   * Küszöbérték: 0
   * Időszak: 5 (perc)
   * Gyakorisága: 5 (perc)
* Riasztási szabály neve: Elérte a napi korlátot
* Súlyosság: Figyelmeztetés (Sev 1)

Riasztás van definiálva, és a eléri a korlátot, riasztás akkor aktiválódik, és hajtja végre a választ a műveletcsoport meghatározott. Küldjön értesítést munkatársainak e-mailek és SMS-EK, valamint automatizálja a műveleteket webhookok, Automation-runbookok használatával vagy [integrálása egy külső ITSM-megoldással](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Módosítsa az Adatmegőrzés időtartama 
Az alábbi lépéseket adatért által a munkaterületen milyen hosszú log konfigurálását ismertetik.
 
1. A munkaterületen válassza a **Felhasználás és becsült költségek** lehetőséget a bal oldali panelen.
2. A **Felhasználás és becsült költségek** oldalon kattintson az oldal tetején található **Adatmennyiség-kezelés** elemre.
5. A panelen a csúszka segítségével növelheti vagy csökkentheti a napok számát, és kattintson a **OK**.  Ha a *ingyenes* szint, nem tudja módosítani az Adatmegőrzés időtartama, és annak érdekében, hogy ez a beállítás szabályozza a fizetős csomagra frissíteni szeretne.<br><br> ![Munkaterület megőrzése beállításának módosítása](media/manage-cost-storage/manage-cost-change-retention-01.png)

## <a name="legacy-pricing-tiers"></a>Örökölt tarifacsomagok

Nagyvállalati szerződéssel rendelkező ügyfelek 2018. július 1-aláírását, és akik már létrehozott Log Analytics-munkaterület az előfizetéshez, akkor továbbra is hozzáférhetnek a a *ingyenes* tervet. Ha az előfizetés nem kötődik meglévő EA-regisztrációhoz, a *ingyenes* szint nem érhető el, amikor egy új előfizetést a 2018. április 2. után hozzon létre egy munkaterületet.  7 napos megőrzés az adatok korlátozódik a *ingyenes* szint.  Az örökölt *önálló* vagy *Csomópontonkénti* rétegek, valamint a jelenlegi 2018 egyetlen tarifacsomagban a gyűjtött adatok érhető el az elmúlt 31 napra vonatkozó. A *ingyenes* csomag esetében a napi korlátja 500 MB-ot a betöltési, és ha rendszeresen túllépi az engedélyezett mennyiségi összegek, a munkaterület módosíthatja az adatgyűjtés meghaladja ezt a határt egy másik csomaghoz. 

> [!NOTE]
> Az OMS E1 csomag, OMS E2 csomagot vagy a System Center OMS bővítményének megvásárlásából származó jogosultságok használatához válassza a Log Analytics *Csomópontonkénti* tarifacsomag.

## <a name="changing-pricing-tier"></a>A tarifacsomag módosítása

Ha a Log Analytics-munkaterület hozzáfér az örökölt tarifacsomagok között örökölt tarifacsomag módosítása:

1. Az Azure Portalon a Log Analytics-előfizetések panelen válasszon ki egy munkaterületet.

2. A munkaterület ablaktáblán alatt **általános**válassza **tarifacsomag**.  

3. A **tarifacsomag**válassza ki a tarifacsomagot, majd kattintson a **kiválasztása**.  
    ![A kijelölt tarifacsomag](media/manage-cost-storage/workspace-pricing-tier-info.png)

A munkaterület helyezhetik át a jelenlegi tarifacsomag szeretne, ha szeretné [módosítása az előfizetés figyelése az Azure monitorban díjszabási modell](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs#moving-to-the-new-pricing-model) amelyek módosulnak, az adott előfizetésben minden munkaterület tarifacsomagját.

> [!NOTE]
> Ha a munkaterület Automation-fiókhoz van társítva, az *Önálló (GB-alapú)* tarifacsomag kiválasztása előtt törölnie kell az összes **Automation and Control** megoldást, és meg kell szüntetnie az Automation-fiók társítását. A megoldások megtekintéséhez és törléséhez kattintson a munkaterület panel **Általános** területén a **Megoldások** elemre. Az Automation-fiók társításának megszüntetéséhez kattintson az Automation-fiók nevére a **Tarifacsomag** panelen.

> [!NOTE]
> További információ (beállításáról a tarifacsomag ARM-n keresztül) [https://docs.microsoft.com/en-us/azure/azure-monitor/platform/template-workspace-configuration#create-a-log-analytics-workspace] és annak biztosítása érdekében, hogy az ARM üzembe helyezési lesz sikeres, függetlenül attól, hogy az előfizetés van a régi vagy új díjszabási modell. 


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>A Log Analytics már nem gyűjt adatokat okozó hibák elhárítása
Ha a régi ingyenes tarifacsomagban és a egy nap alatt több mint 500 MB mennyiségű adatot küldő adatgyűjtés leáll, a többi, a nap. Napi korlát elérése a gyakori oka, hogy a Log Analytics leállítja az adatgyűjtést, vagy adatokat úgy tűnik, hogy hiányzik.  A log Analytics művelet típusú eseményt hoz létre, amikor adatgyűjtés indítása és leállítása. A keresés, ellenőrizze, hogy vannak napi korlát elérése és adatok hiányoznak a következő lekérdezés futtatásával: 

`Operation | where OperationCategory == 'Data Collection Status' `

Ha leállítja az adatgyűjtést, a OperationStatus figyelmeztetés. Amikor megkezdi az adatgyűjtést, akkor a OperationStatus sikeres volt. A következő táblázat ismerteti az oka, hogy leállítja az adatgyűjtést, és folytathatja az adatgyűjtést javasolt művelet:  

|OK gyűjtemény leáll| Megoldás| 
|-----------------------|---------|
|Elérte az örökölt az ingyenes tarifacsomag napi korlátot |Várja meg, amíg a gyűjtemény indítja újra automatikusan a következő napon, vagy módosítása fizetős tarifacsomagra.|
|Elérte a napi korlát a munkaterület|Várja meg, indítsa újra automatikusan a gyűjtemény, vagy növelje a napi mennyiségi korlátot ismertetett kezelése a maximális napi adatmennyiséget. A napi korlát visszaállítási idő mutat be kapcsolva a **adatmennyiség-kezelés** lapot. |
|Azure-előfizetés miatt felfüggesztett állapotban van:<br> Ingyenes próbaidőszak véget ért<br> Az Azure pass lejárt<br> Havonta költségkeret elérése (például az MSDN vagy a Visual Studio előfizetési)|Fizetős előfizetéssé alakítani<br> Távolítsa el a korlátot, vagy várjon, amíg a korlát alaphelyzetbe állítása|

Értesítés az adatgyűjtés leáll, használja a leírt lépéseket követve *létrehozás napi adatkorlátjának* , ami arról értesíti, ha leállítja az adatgyűjtést, és kövesse a lépéseket használja leírt lépéseket követve adja hozzá a műveletek a riasztási szabály konfigurálása egy e-mailt, webhook vagy runbook műveletet a riasztási szabály. 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>A vártnál magasabb szintű használatot okozó hibák elhárítása
A magasabb szintű használatot a következők okozhatják:
- A vártnál több adatot küld a rendszer a Log Analytics számára
- A vártnál több csomópont küld adatokat a Log Analytics számára

### <a name="data-volume"></a>Adatmennyiség 
Az a **használat és becsült költségek** lapon a *adatbetöltés megoldásonként* küldött adatok teljes mennyiségét, és mekkora küld a rendszer egyes megoldások által látható diagramon. Ez lehetővé teszi, hogy határozza meg a trendeket, például hogy a teljes adathasználat (vagy egy adott megoldás használatának) nő, állandó vagy csökken van hátra. Ennek létrehozásához használt lekérdezés

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

Vegye figyelembe, hogy a záradék "ahol IsBillable = true" szűri ki az egyes megoldások, amelynek nem jár Adatbetöltési adattípusok. 

Lásd: adatok trendjeit adott adattípusok, például ha az IIS-naplók miatt az adatok tanulmánya szeretné tovább, részletesebben is megtekintheti a:

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

### <a name="nodes-sending-data"></a>Adatokat küldő csomópontok

Szeretné megtudni, minden nap az utolsó hónapban adatokat küldő számítógépek (csomópontok) számát, használata

`Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart`

A küldő számítógépek listájának lekérése **adattípusok számlázzuk** (egyes adattípusok olyan ingyenes), használhatja a [_IsBillable](log-standard-properties.md#isbillable) tulajdonság:

`union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName`

E `union withsource = tt *` , különböző adattípusok keresésekre végrehajtása költséges takarékosan kérdezi le. 

Ez is kiterjeszthető való visszatéréshez, a küldő számítógépek óránkénti száma számlázzuk adattípusok:

`union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc`

Megtekintheti a **mérete** számlázható események betöltött számítógépenként, használja a `_BilledSize` -tulajdonsággal, amely biztosítja a mérete (bájt):

`union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last `

Ez a lekérdezés a lekérdezés ezt a használati adatok típusa a régi módja váltja fel. 

Megtekintheti a **száma** számítógép betöltött események használata

`union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last`

A számítógép betöltött számlázható események száma használja 

`union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last`

Ha meg szeretné tekinteni a számlázható adattípusok darabszáma adatot küldenek, adott számítógéphez, használja:

`union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last `

> [!NOTE]
> Néhány, a használati adatok típusú mezőt közben továbbra is a séma elavultak, értékeik már fel van töltve lesz. Ezek a **számítógép** valamint Adatbetöltési kapcsolódó mezőket (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**,  **BatchesCapped** és **AverageProcessingTimeMs**.

Ezután részletesen áttekintjük az adatforrás egy adott típusú, Íme néhány hasznos példa a lekérdezésekre:

+ **Biztonsági** megoldás
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ **Naplókezelési** megoldás
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ **Perf** adattípus
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ **Esemény** adattípus
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ **Rendszernapló** adattípus
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ **AzureDiagnostics** adattípus
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>Tippek az adatmennyiség csökkentése

Néhány javaslatot a gyűjtött naplók mennyiségét csökkenti a következők:

| A nagy adatmennyiség forrása | Az adatmennyiség csökkentésének módja |
| -------------------------- | ------------------------- |
| Biztonsági események            | Válassza a [gyakori vagy minimális biztonsági események](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) lehetőséget <br> Módosítsa a biztonsági naplózási szabályzatot, hogy csak a szükséges eseményeket gyűjtse be. Tekintse át a következőkhöz való eseménygyűjtés szükségességét: <br> - [szűrőplatform naplózása](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [beállításjegyzék naplózása](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [fájlrendszer naplózása](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [kernelobjektum naplózása](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [leírókezelés naplózása](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> – Cserélhető tároló naplózása |
| Teljesítményszámlálók       | Módosítsa a [teljesítményszámlálók konfigurációját](data-sources-performance-counters.md): <br> – Csökkentse a gyűjtés gyakoriságát <br> – Csökkentse a teljesítményszámlálók számát |
| Eseménynaplók                 | Módosítsa az [eseménynaplók konfigurációját](data-sources-windows-events.md): <br> – Csökkentse a gyűjtött eseménynaplók számát <br> – Csak a szükséges eseményszinteket gyűjtse. Ne gyűjtsön például *Tájékoztatás* szintű eseményeket |
| Rendszernapló                     | Módosítsa a [rendszernapló konfigurációját](data-sources-syslog.md): <br> – Csökkentse a gyűjtésben részt vevő létesítmények számát <br> – Csak a szükséges eseményszinteket gyűjtse. Ne gyűjtsön például *Tájékoztatás* vagy *Hibakeresés* szintű eseményeket |
| AzureDiagnostics           | Az erőforrásnapló-gyűjtés módosítása a következőre: <br> – Csökkentse a Log Analytics számára naplókat küldő erőforrások számát <br> – Csak a szükséges naplókat gyűjtse |
| Megoldásadatok olyan számítógépekről, amelyeknek nincs szükségük a megoldásra | A [megoldáscélzási](../insights/solution-targeting.md) funkcióval megadhatja, hogy csak a szükséges számítógépcsoportoktól gyűjtsön adatokat. |

### <a name="getting-node-counts"></a>Első csomópont hibás 

Ha a "Száma csomópontonként (OMS)" tarifacsomag, akkor számítunk fel a csomópontok és megoldások száma alapján használja, a Insights számát, valamint a tábla, amelynek, számolunk fel az Analytics-csomópontok megjelennek a a **használat és becsült költségek**lapot.  

Tekintse meg a különböző biztonsági csomópontok számát, a lekérdezés használhatja:

`union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count`

Különböző automatizálási csomópontok számának megtekintéséhez használja a lekérdezés:

` ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc`

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>Riasztás létrehozása, amikor az adatgyűjtés szintje a vártnál magasabb
Ez a szakasz ismerteti, hogyan hozhat létre riasztást, ha:
- Az adatmennyiség meghalad egy megadott mennyiséget.
- Az adatmennyiség várhatóan meghalad egy megadott mennyiséget.

Az Azure-riasztások támogatják a keresési lekérdezéseket támogató [naplóriasztásokat](alerts-unified-log.md). 

A következő lekérdezés akkor ad vissza eredményt, ha több mint 100 GB adat lett összegyűjtve az elmúlt 24 órában:

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

A következő lekérdezés egy egyszerű képlettel előrejelzi, mikor fog a rendszer egy nap alatt több mint 100 GB adatot küldeni: 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

Ha más adatmennyiségre szeretne riasztást beállítani, módosítsa a lekérdezésekben a 100 értéket arra a GB mennyiségre, amely esetén riasztást szeretne kapni.

Az [új naplózási riasztás létrehozásával kapcsolatos](alerts-metric.md) szakaszban leírt lépéseket követve beállíthatja, hogy értesítést kapjon, ha az adatgyűjtés szintje a vártnál magasabb.

Az első lekérdezéshez tartozó riasztás létrehozásakor – amikor több mint 100 GB adat lett összegyűjtve 24 órán belül, állítsa be a következőket:  

- A **riasztási feltétel megadásával** határozza meg a célerőforrásként használt Log Analytics-munkaterületet.
- A **Riasztási feltételek** résznél az alábbiakat adja meg:
   - A **Jel neve** legyen **Egyéni naplókeresés**
   - A **Keresési lekérdezés** legyen a következő: `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`
   - A **Riasztási logika** **alapja** legyen az *eredmények száma*, a **Feltétel** pedig legyen *nagyobb mint* a következő **küszöbérték** : *0*
   - Az **Időszak** értékét állítsa *1440* percre, a **Riasztási időköz** pedig legyen *60* perc, mivel a használati adatok csak óránként frissülnek.
- **Határozza meg a riasztás részleteit** az alábbiak megadásával:
   - A **Név** legyen *Több mint 100 GB adatmennyiség 24 órán belül*
   - A **Súlyosság** legyen *Figyelmeztetés*

Megadhat egy meglévő [műveletcsoportot](action-groups.md), illetve létrehozhat egy újat, hogy értesítést kapjon, amikor egy naplóriasztás megfelel a feltételeknek.

A második lekérdezéshez tartozó riasztás létrehozásakor – amikor több mint 100 GB adat összegyűjtése várható 24 órán belül, állítsa be a következőket:

- A **riasztási feltétel megadásával** határozza meg a célerőforrásként használt Log Analytics-munkaterületet.
- A **Riasztási feltételek** résznél az alábbiakat adja meg:
   - A **Jel neve** legyen **Egyéni naplókeresés**
   - A **Keresési lekérdezés** legyen a következő: `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
   - A **Riasztási logika** **alapja** legyen az *eredmények száma*, a **Feltétel** pedig legyen *nagyobb mint* a következő **küszöbérték** : *0*
   - Az **Időszak** értékét állítsa *180* percre, a **Riasztási időköz** pedig legyen *60* perc, mivel a használati adatok csak óránként frissülnek.
- **Határozza meg a riasztás részleteit** az alábbiak megadásával:
   - A **Név** legyen *Több mint 100 GB várható adatmennyiség 24 órán belül*
   - A **Súlyosság** legyen *Figyelmeztetés*

Megadhat egy meglévő [műveletcsoportot](action-groups.md), illetve létrehozhat egy újat, hogy értesítést kapjon, amikor egy naplóriasztás megfelel a feltételeknek.

Riasztás fogadásakor kövesse a következő szakaszban leírt lépéseket a vártnál magasabb szintű használatot okozó hibák elhárításához.

## <a name="next-steps"></a>További lépések
* A keresési nyelv használatával kapcsolatban tekintse meg a [Log Analytics naplókeresési funkciójával](../log-query/log-query-overview.md) kapcsolatos cikket. A keresési lekérdezésekkel további elemzéseket végezhet a használati adatokon.
* Az [új naplózási riasztás létrehozásával kapcsolatos](alerts-metric.md) szakaszban leírt lépéseket követve beállíthatja, hogy értesítést kapjon, ha teljesül egy keresési feltétel.
* A [megoldáscélzási](../insights/solution-targeting.md) funkcióval megadhatja, hogy csak a szükséges számítógépcsoportoktól gyűjtsön adatokat.
* Egy hatékony esemény gyűjtési szabályzat konfigurálásához tekintse át a [az Azure Security Center szűrési szabályzatai](../../security-center/security-center-enable-data-collection.md).
* Módosítsa a [teljesítményszámlálók konfigurációját](data-sources-performance-counters.md).
* Az eseménygyűjtési beállítások módosításához tekintse meg az [eseménynaplók konfigurációját](data-sources-windows-events.md) leíró szakaszt.
* A rendszernapló-gyűjtési beállítások módosításához tekintse meg a [rendszernaplók konfigurációját](data-sources-syslog.md) leíró szakaszt.


