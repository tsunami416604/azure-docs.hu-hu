---
title: Azure Monitor naplók használatának és költségeinek kezelése | Microsoft Docs
description: Megtudhatja, hogyan módosíthatja a díjszabási tervet, és hogyan kezelheti a Log Analytics munkaterület adatmennyiségét és adatmegőrzési szabályzatát Azure Monitorben.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: 5e325f7766e7b0d9764949eb3fbf9753d65db8b3
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619400"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>A használat és a költségek kezelése Azure Monitor naplókkal

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan szabályozhatja Azure Monitor költségeit a Log Analytics munkaterület adatmegőrzési időszakának beállításával.  A kapcsolódó információkért tekintse meg a következő cikket.
> - [Használat és becsült költségek figyelése](usage-estimated-costs.md) ismerteti, hogyan lehet megtekinteni a használati és becsült költségek figyelési funkciók eltérő díjszabási modelleket a több Azure-ban. Emellett bemutatja, hogyan lehet módosítani a díjszabási modellt.

Azure Monitor naplókat úgy tervezték, hogy a nagyvállalati forrásból származó vagy az Azure-ban üzembe helyezett nagy mennyiségű adat gyűjtését, indexelését és tárolását naponta kialakítsa és támogassa.  Ez lehet egy elsődleges illesztőprogram, a szervezet számára, miközben költséghatékonyságot végső soron az alapul szolgáló illesztőprogram. Ebből a célból fontos tisztában lenni azzal, hogy egy Log Analytics munkaterület díja nem csak a gyűjtött adatok mennyiségétől függ, hanem a kiválasztott csomagtól, és azt is, hogy mennyi ideig kell tárolnia a csatlakoztatott forrásokból generált adatokat.  

Ez a cikk áttekintettük, hogyan proaktívan figyelheti adatok mennyisége és a storage növekedési, és azok a kapcsolódó költségek szabályozására vonatkozó korlátok beállításához. 

Lehet, hogy a költség az adatok jelentős függően az alábbi tényezőket: 

- A munkaterületre generált és betöltött adatmennyiség 
    - Engedélyezett felügyeleti megoldások száma
    - Figyelt rendszerek száma
    - Az egyes figyelt erőforrásokból gyűjtött adatok típusa 
- Az az időtartam, ameddig az adatait meg szeretné őrizni 

## <a name="understand-your-workspaces-usage-and-estimated-cost"></a>A munkaterület használatának és becsült értékének megismerése

Azure Monitor naplók segítségével könnyen megismerheti, hogy a legutóbbi használati szokások alapján milyen költségek várhatóak. Ehhez használja a **log Analytics használati és becsült költségeket** az adatfelhasználás áttekintéséhez és elemzéséhez. Az egyes megoldások által összegyűjtött adatok mennyiségét jeleníti meg, mennyi adatot alatt marad, és a költségek becslése betöltött adatok és adatmegőrzés minden csomagban foglalt adatmennyiségen felüli mennyisége alapján.

![Használat és becsült költségek](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Ismerje meg részletesen az adatokat, kattintson a felső ikonra vagy a diagramokat, jobb a **használat és becsült költségek** lap. Most már dolgozhat a lekérdezés további részletek, amely a használat.  

![Naplók megtekintése](media/manage-cost-storage/logs.png)

A **használat és a becsült költségek** lapon áttekintheti az adatmennyiséget a hónapban. Ez magában foglalja a fogadott és a Log Analytics-munkaterület összes adatot.  A lap tetején található **használati** adatok lehetőségre kattintva megtekintheti a használati irányítópultot, amely a forrás, a számítógép és az ajánlat adatmennyiség-trendjeivel kapcsolatos információkat jeleníti meg. Megtekintheti, és állítsa be a maximális napi adatmennyiséget, vagy módosíthatja a megőrzési időszak kattintson **adatmennyiség-kezelés**.
 
Log Analytics díjak az Azure-elszámolások kerülnek. A számlázás szakaszában az Azure Portal vagy a számlázás az Azure részleteit láthatja a [Azure Billing Portal](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Napi korlát

Konfigurálja a maximális napi adatmennyiséget, és korlátozhatja a napi bevitelt a munkaterületen, de körültekintően járjon el, mert a cél nem lehet eléri a napi korlátot.  Ellenkező esetben az a nap fennmaradó részében, ami hatással lehet más Azure-szolgáltatások és megoldások, amelynek funkció függhet, naprakész adatok legyenek elérhetők a munkaterület az adatok elvesznek.  Ennek eredményeképpen figyelje meg, és fogadjon megítélnie riasztást küld, ha is hatással van az informatikai szolgáltatásokat támogató erőforrásokban egészségügyi feltételeit.  A napi korlát arra szolgál, hogy kezelje az adatmennyiség váratlan növekedését a felügyelt erőforrásokból, és a korláton belül maradjon, vagy ha korlátozni szeretné a munkaterülethez tartozó nem tervezett díjakat.  

A napi korlátot, a gyűjtemény számlázható adattípusok leállítja az a nap hátralevő. Egy figyelmeztetés szalagcím jelenik meg a kiválasztott Log Analytics-munkaterületet a lap tetején, és a egy műveletet a rendszer a *művelet* tábla alatt **LogManagement** kategória. Az adatgyűjtés után a visszaállítási idő alatt meghatározott folytatja *napi korlát lesz beállítva*. Azt javasoljuk, hogy arra az esetre, ha a napi korlátot elérte a konfigurált Ez a művelet események alapján riasztási szabály meghatározása. 

> [!NOTE]
> A napi korlát nem állítja le a Azure Security Centerból származó adatok gyűjtését.

### <a name="identify-what-daily-data-limit-to-define"></a>Milyen napi korlátot meghatározásához azonosítása

Felülvizsgálat [Log Analytics-használat és becsült költségek](usage-estimated-costs.md) megtudhatja, hogy az adatok betöltési trend és mi az a napi mennyiségi korlát meghatározásához. Kell tekinteni, körültekintően, mivel nem lehet az erőforrások figyeléséhez, a korlát elérése után. 

### <a name="manage-the-maximum-daily-data-volume"></a>A maximális napi adatmennyiség kezelése

A következő lépések azt ismertetik, hogyan konfigurálható egy korlát a Log Analytics munkaterület által naponta betöltött adatmennyiség kezelésére.  

1. A munkaterületen válassza a **Felhasználás és becsült költségek** lehetőséget a bal oldali panelen.
2. Az a **felhasználás és becsült költségek** a kijelölt munkaterület oldalára, kattintson **adatmennyiség-kezelés** az oldal tetején. 
3. Napi korlát a következő **OFF** – alapértelmezés szerint kattintson **ON** az engedélyezéshez, és állítsa az mennyiségi korlát a GB/nap.

    ![Az adatkorlát konfigurálása Log Analytics](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Riasztás, ha elérte a napi korlátot

Hogy jelen visual köteg az Azure Portalon, ha az adatok korlát küszöbértéket, amíg ez a viselkedés nem feltétlenül igazítás azonnali figyelmet igénylő működési problémák kezelése.  Az Azure Monitor riasztási értesítés fogadására, létrehozhat egy új riasztási szabály.  További információt a riasztások [létrehozása, megtekintése és kezelése](alerts-metric.md)című témakörben talál.

Az első lépésekhez, az alábbiakban a riasztás az ajánlott beállításokat:

- Cél: Válassza ki a Log Analytics erőforrást
- Feltételek: 
   - Jel neve: Egyéni naplók keresése
   - Keresési lekérdezés: Művelet | ahol a Részletek "túlkvóta"
   - Alapja: Eredmények száma
   - Feltétel: Nagyobb, mint
   - Küszöbérték: 0
   - Időszak 5 (perc)
   - Frekvencia 5 (perc)
- Riasztási szabály neve: Elérte a napi adatkorlátot
- Súlyosság: Figyelmeztetés (1. pont)

Riasztás van definiálva, és a eléri a korlátot, riasztás akkor aktiválódik, és hajtja végre a választ a műveletcsoport meghatározott. Küldjön értesítést munkatársainak e-mailek és SMS-EK, valamint automatizálja a műveleteket webhookok, Automation-runbookok használatával vagy [integrálása egy külső ITSM-megoldással](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Módosítsa az Adatmegőrzés időtartama

Az alábbi lépéseket adatért által a munkaterületen milyen hosszú log konfigurálását ismertetik.
 
1. A munkaterületen válassza a **Felhasználás és becsült költségek** lehetőséget a bal oldali panelen.
2. A **Felhasználás és becsült költségek** oldalon kattintson az oldal tetején található **Adatmennyiség-kezelés** elemre.
3. A panelen a csúszka segítségével növelheti vagy csökkentheti a napok számát, és kattintson a **OK**.  Ha a *ingyenes* szint, nem tudja módosítani az Adatmegőrzés időtartama, és annak érdekében, hogy ez a beállítás szabályozza a fizetős csomagra frissíteni szeretne.

    ![Munkaterület adatmegőrzési beállításának módosítása](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
Az adatmegőrzés az [ARM-n keresztül](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) is beállítható a `dataRetention` paraméter használatával. Emellett, ha az adatmegőrzést 30 napra állítja be, a `immediatePurgeDataOn30Days` paraméter használatával azonnal törölheti a régebbi adatok törlését, ami a megfelelőséggel kapcsolatos forgatókönyvek esetében hasznos lehet. Ez a funkció csak az ARM-on keresztül érhető el. 

## <a name="legacy-pricing-tiers"></a>Örökölt árképzési szintek

Azok az előfizetések, amelyek Log Analytics munkaterülettel vagy Application Insights-erőforrással rendelkeztek az 2018. április 2. előtt, vagy a 2019. február 1. előtt elindított Nagyvállalati Szerződéshoz kapcsolódnak, továbbra is hozzáférhetnek a régi díjszabási szintek használatához: **Ingyenes**, **önálló (GB-onként)** és **/node (OMS)** .  Az ingyenes díjszabási szinten lévő munkaterületek napi adatfeldolgozása a 500 MB-ra korlátozódik (kivéve a Azure Security Center által gyűjtött biztonsági adattípusokat), és az adatmegőrzés legfeljebb 7 napig tart. Az ingyenes díjszabási csomag kizárólag értékelési célokra szolgál. Az önálló vagy a csomópontok díjszabási szintjein lévő munkaterületek felhasználó által konfigurálható megőrzése legfeljebb 2 év lehet. 

A 2016. április előtt létrehozott munkaterületek is hozzáférnek az eredeti **standard** és **prémium** szintű díjszabáshoz, amelyekben a rögzített adatmegőrzés 30 és 365 nap. Nem hozhatók létre új munkaterületek a **standard** vagy a **prémium** szintű díjszabásban, és ha a munkaterületet kihelyezték ezekből a rétegekből, nem helyezhető vissza. 

Az árképzési szintek korlátozásait [itt](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces)találja.

> [!NOTE]
> A OMS E1 Suite, OMS E2 Suite vagy OMS-bővítmény a System Center rendszerhez szolgáltatásból származó jogosultságok használatához válassza az Log Analytics a *csomópontok* díjszabási szintjein.


## <a name="changing-pricing-tier"></a>Árképzési réteg módosítása

Ha a Log Analytics munkaterület örökölt díjszabási csomagokhoz fér hozzá, a régi árképzési szintek közötti váltáshoz:

1. Az Azure Portalon a Log Analytics-előfizetések panelen válasszon ki egy munkaterületet.

2. A munkaterület ablaktáblán alatt **általános**válassza **tarifacsomag**.  

3. A **tarifacsomag**válassza ki a tarifacsomagot, majd kattintson a **kiválasztása**.  
    ![A kijelölt tarifacsomag](media/manage-cost-storage/workspace-pricing-tier-info.png)

Az árképzési [szintet az ARM-on keresztül](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) is beállíthatja a `ServiceTier` paraméter használatával. 

## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Hibaelhárítás, hogy miért Log Analytics már nem gyűjti az adatgyűjtést

Ha az örökölt ingyenes díjszabási szinten van, és naponta több mint 500 MB-nyi adat lett elküldve, az adatgyűjtés a nap hátralevő részében leáll. Napi korlát elérése a gyakori oka, hogy a Log Analytics leállítja az adatgyűjtést, vagy adatokat úgy tűnik, hogy hiányzik.  A log Analytics művelet típusú eseményt hoz létre, amikor adatgyűjtés indítása és leállítása. Futtassa a következő lekérdezést a keresésben, és ellenőrizze, hogy eléri-e a napi korlátot és a hiányzó adatértékeket: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Amikor az adatgyűjtés leáll, a OperationStatus **figyelmeztetést**jelenít meg. Az adatgyűjtés megkezdésekor a OperationStatus **sikeres**. A következő táblázat ismerteti az oka, hogy leállítja az adatgyűjtést, és folytathatja az adatgyűjtést javasolt művelet:  

|OK gyűjtemény leáll| Megoldás| 
|-----------------------|---------|
|Elérte az örökölt ingyenes díjszabási csomag napi korlátját |Várja meg, amíg a gyűjtemény indítja újra automatikusan a következő napon, vagy módosítása fizetős tarifacsomagra.|
|Elérte a munkaterület napi korlátját|Várjon, amíg a gyűjtemény automatikusan újraindul, vagy növelje a napi adatmennyiség kezelése című témakörben leírt napi adatmennyiség korlátját. A napi korlát alaphelyzetbe állításának ideje az adatmennyiség- **kezelés** oldalon látható. |
|Azure-előfizetés miatt felfüggesztett állapotban van:<br> Ingyenes próbaidőszak véget ért<br> Az Azure pass lejárt<br> Havonta költségkeret elérése (például az MSDN vagy a Visual Studio előfizetési)|Fizetős előfizetéssé alakítani<br> Távolítsa el a korlátot, vagy várjon, amíg a korlát alaphelyzetbe állítása|

Ha értesítést szeretne kapni az adatgyűjtés leállításakor, kövesse a *napi adatsapka* -riasztás létrehozása című témakörben leírt lépéseket az adatgyűjtés leállításakor. A következő témakörben ismertetett lépések végrehajtásával konfigurálhat egy e-mailt, webhookot vagy runbook műveletet a riasztási szabályhoz: [műveleti csoport létrehozása](action-groups.md) . 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>A vártnál magasabb szintű használatot okozó hibák elhárítása

A magasabb szintű használatot a következők okozhatják:
- További csomópontok, mint a várt adatok küldése Log Analytics munkaterületre
- Több, a vártnál több információ Log Analytics munkaterületre

## <a name="understanding-nodes-sending-data"></a>Az adatokat küldő csomópontok ismertetése

A szívveréseket jelentő számítógépek számának az elmúlt hónapban való megismeréséhez használja a következőt:

```kusto
Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```

Azon számítógépek listájának lekéréséhez, amelyek csomópontként lesznek kiszámlázva, ha a munkaterület a csomópontok közötti örökölt díjszabási szinten található, keresse meg a **számlázott** adattípusokat küldő csomópontokat (egyes adattípusok ingyenesek). Ehhez használja a `_IsBillable` [tulajdonságot](log-standard-properties.md#_isbillable) , és használja a teljes tartománynév bal szélső mezőjét. Ez visszaadja a számlázott adatokkal rendelkező számítógépek listáját:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Az észlelt számlázható csomópontok száma a következőképpen becsülhető fel: 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| billableNodes=dcount(computerName)
```

> [!NOTE]
> Ezeket `union withsource = tt *` a lekérdezéseket takarékosan használhatja az adattípusok megkereséséhez. Ez a lekérdezés a számítógép adatainak a használati adattípussal való lekérdezésének régi módját váltja fel.  

A ténylegesen számlázott adatok mennyiségének pontosabb kiszámítása a számlázott adattípusokat küldő számítógépek száma óránként. (A csomópontok közötti örökölt díjszabási szinten lévő munkaterületek esetében Log Analytics kiszámítja a csomópontok számát, amelyeknek óradíjat kell fizetnie.) 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="understanding-ingested-data-volume"></a>A betöltött adatmennyiség ismertetése

Az a **használat és becsült költségek** lapon a *adatbetöltés megoldásonként* küldött adatok teljes mennyiségét, és mekkora küld a rendszer egyes megoldások által látható diagramon. Ez lehetővé teszi, hogy határozza meg a trendeket, például hogy a teljes adathasználat (vagy egy adott megoldás használatának) nő, állandó vagy csökken van hátra. Ennek létrehozásához használt lekérdezés

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

Vegye figyelembe, hogy a záradék "ahol IsBillable = true" szűri ki az egyes megoldások, amelynek nem jár Adatbetöltési adattípusok. 

Lásd: adatok trendjeit adott adattípusok, például ha az IIS-naplók miatt az adatok tanulmánya szeretné tovább, részletesebben is megtekintheti a:

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

### <a name="data-volume-by-computer"></a>Adatmennyiség számítógépenként

Ha meg szeretné tekinteni a számítógépen betöltött számlázható események **méretét** , használja `_BilledSize` a (z) [tulajdonságot](log-standard-properties.md#_billedsize), amely a méretet adja meg bájtban:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize Bytes=sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

A `_IsBillable` [tulajdonság](log-standard-properties.md#_isbillable) azt határozza meg, hogy a betöltött adatok díjkötelesek-e.

A számítógépeken betöltött **számlázható** események számának megtekintéséhez használja a következőt: 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount=count() by computerName  | sort by count_ nulls last
```

Ha meg szeretné tekinteni a számlázható adattípusok darabszáma adatot küldenek, adott számítógéphez, használja:

```kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Adatmennyiség Azure-erőforrás, erőforráscsoport vagy előfizetés alapján

Az Azure-ban üzemeltetett csomópontokból származó adatok esetén a __számítógépeken__betöltött számlázható események **mérete** a _ResourceId [tulajdonság](log-standard-properties.md#_resourceid)használatával történik, amely az erőforrás teljes elérési útját biztosítja:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Az Azure-ban üzemeltetett csomópontokból származó adatok esetében az Azure `_ResourceId` -előfizetések __által__betöltött számlázható események **mérete** az alábbiak szerint elemezhető:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

A `subscriptionId` ( `resourceGroup` z) értékre váltásakor megjelenik az Azure-erőforráscsoport által betöltött számlázható adatmennyiség. 


> [!NOTE]
> A használati adattípus egyes mezői, miközben még mindig a séma része, elavultak, és az értékek már nem lesznek feltöltve. Ezek a **számítógép** valamint Adatbetöltési kapcsolódó mezőket (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**,  **BatchesCapped** és **AverageProcessingTimeMs**.

### <a name="querying-for-common-data-types"></a>Gyakori adattípusok lekérdezése

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

### <a name="getting-security-and-automation-node-counts"></a>Biztonsági és automatizálási csomópontok számának beolvasása

Ha a "Száma csomópontonként (OMS)" tarifacsomag, akkor számítunk fel a csomópontok és megoldások száma alapján használja, a Insights számát, valamint a tábla, amelynek, számolunk fel az Analytics-csomópontok megjelennek a a **használat és becsült költségek**lapot.  

Tekintse meg a különböző biztonsági csomópontok számát, a lekérdezés használhatja:

```kusto
union
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
| count
```

Különböző automatizálási csomópontok számának megtekintéséhez használja a lekérdezés:

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="create-an-alert-when-data-collection-is-high"></a>Riasztás létrehozása, ha az adatgyűjtés magas

Ez a szakasz ismerteti, hogyan hozhat létre riasztást, ha:
- Az adatmennyiség meghalad egy megadott mennyiséget.
- Az adatmennyiség várhatóan meghalad egy megadott mennyiséget.

Az Azure-riasztások támogatják a keresési lekérdezéseket támogató [naplóriasztásokat](alerts-unified-log.md). 

A következő lekérdezés akkor ad vissza eredményt, ha több mint 100 GB adat lett összegyűjtve az elmúlt 24 órában:

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type 
| where DataGB > 100
```

A következő lekérdezés egy egyszerű képlettel előrejelzi, mikor fog a rendszer egy nap alatt több mint 100 GB adatot küldeni: 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type 
| where EstimatedGB > 100
```

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

## <a name="limits-summary"></a>Korlátok összegzése

Vannak további Log Analytics korlátok, amelyek némelyike a Log Analytics díjszabási szintjétől függ. Ezeket [itt](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces)dokumentáljuk.


## <a name="next-steps"></a>További lépések

- A keresési nyelv használatának megismeréséhez tekintse meg a naplóban megkeresett [Azure monitor naplókat](../log-query/log-query-overview.md) . A keresési lekérdezésekkel további elemzéseket végezhet a használati adatokon.
- Az [új naplózási riasztás létrehozásával kapcsolatos](alerts-metric.md) szakaszban leírt lépéseket követve beállíthatja, hogy értesítést kapjon, ha teljesül egy keresési feltétel.
- A [megoldáscélzási](../insights/solution-targeting.md) funkcióval megadhatja, hogy csak a szükséges számítógépcsoportoktól gyűjtsön adatokat.
- Egy hatékony esemény-gyűjtési házirend konfigurálásához tekintse át [Azure Security Center szűrési házirendet](../../security-center/security-center-enable-data-collection.md).
- Módosítsa a [teljesítményszámlálók konfigurációját](data-sources-performance-counters.md).
- Az eseménygyűjtési beállítások módosításához tekintse meg az [eseménynaplók konfigurációját](data-sources-windows-events.md) leíró szakaszt.
- A rendszernapló-gyűjtési beállítások módosításához tekintse meg a [rendszernaplók konfigurációját](data-sources-syslog.md) leíró szakaszt.