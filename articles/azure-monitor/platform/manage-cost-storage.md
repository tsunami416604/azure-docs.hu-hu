---
title: Használat és költségek kezelése az Azure Monitor naplóira |} A Microsoft Docs
description: Ismerje meg, hogyan módosíthatja, a díjszabással és adatok mennyisége és a megőrzési házirend a Log Analytics-munkaterület az Azure monitorban kezelése.
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
ms.date: 06/06/2019
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: b7fa59f4086608a8bacabde21f0c02c108f1f5e8
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466729"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Használat és költségek az Azure Monitor naplóira kezelése

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan tarthatja kézben a költségeit az Azure monitorban az Adatmegőrzés időtartama a Log Analytics-munkaterület beállításával.  Tekintse meg a következő cikk további kapcsolódó információt.
> - [Használat és becsült költségek figyelése](usage-estimated-costs.md) ismerteti, hogyan lehet megtekinteni a használati és becsült költségek figyelési funkciók eltérő díjszabási modelleket a több Azure-ban. Emellett bemutatja, hogyan lehet módosítani a díjszabási modellt.

Az Azure Monitor naplóira méretezési és támogatási gyűjtése, az indexelés és a vállalati adatok naponta bármilyen forrásból származó nagy mennyiségű tárolására tervezték, vagy üzembe helyezve az Azure-ban.  Ez lehet egy elsődleges illesztőprogram, a szervezet számára, miközben költséghatékonyságot végső soron az alapul szolgáló illesztőprogram. Ennek érdekében fontos megérteni, hogy a költség, a Log Analytics-munkaterület nem csak alapján is a kiválasztott csomag függ, és mennyi ideig úgy döntött, hogy a csatlakozó forrásokból származó adatok tárolása összegyűjtött adatok mennyisége.  

Ez a cikk áttekintettük, hogyan proaktívan figyelheti adatok mennyisége és a storage növekedési, és azok a kapcsolódó költségek szabályozására vonatkozó korlátok beállításához. 

Lehet, hogy a költség az adatok jelentős függően az alábbi tényezőket: 

- Jön létre, és a munkaterület betöltött adatok mennyiségét 
    - Engedélyezett felügyeleti megoldások száma
    - Figyelt rendszerek száma
    - Minden figyelt erőforrásból gyűjtött adatok típusa 
- Mennyi ideig kívánja megőrizni az adatokat 

## <a name="understand-your-workspaces-usage-and-estimated-cost"></a>A munkaterület használati és becsült költségek ismertetése

Az Azure Monitor naplóira teszi egyszerűvé, mire a költségek várhatóan legutóbbi használati minták alapján. Ehhez használja **Log Analytics-használat és becsült költségek** segítségével áttekintheti és elemezheti az adathasználatot. Az egyes megoldások által összegyűjtött adatok mennyiségét jeleníti meg, mennyi adatot alatt marad, és a költségek becslése betöltött adatok és adatmegőrzés minden csomagban foglalt adatmennyiségen felüli mennyisége alapján.

![Használat és becsült költségek](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Ismerje meg részletesen az adatokat, kattintson a felső ikonra vagy a diagramokat, jobb a **használat és becsült költségek** lap. Most már dolgozhat a lekérdezés további részletek, amely a használat.  

![Naplók megtekintése](media/manage-cost-storage/logs.png)

Az a **használat és becsült költségek** lapon áttekintheti a továbbított adatmennyiség hónapban. Ez magában foglalja a fogadott és a Log Analytics-munkaterület összes adatot.  Kattintson a **használatról** megtekintheti a használati irányítópult adatokkal adatok mennyiségi trendek forrás, a számítógépek és az ajánlat az oldal tetején. Megtekintheti, és állítsa be a maximális napi adatmennyiséget, vagy módosíthatja a megőrzési időszak kattintson **adatmennyiség-kezelés**.
 
Log Analytics díjak az Azure-elszámolások kerülnek. A számlázás szakaszában az Azure Portal vagy a számlázás az Azure részleteit láthatja a [Azure Billing Portal](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Napi korlát

Konfigurálja a maximális napi adatmennyiséget, és korlátozhatja a napi bevitelt a munkaterületen, de körültekintően járjon el, mert a cél nem lehet eléri a napi korlátot.  Ellenkező esetben az a nap fennmaradó részében, ami hatással lehet más Azure-szolgáltatások és megoldások, amelynek funkció függhet, naprakész adatok legyenek elérhetők a munkaterület az adatok elvesznek.  Ennek eredményeképpen figyelje meg, és fogadjon megítélnie riasztást küld, ha is hatással van az informatikai szolgáltatásokat támogató erőforrásokban egészségügyi feltételeit.  Célja, hogy a maximális napi adatmennyiséget, a felügyelt erőforrásoktól érkező adatmennyiség váratlan növekedés kezeléséhez, és az időkorláton belül, vagy ha szeretné korlátozni a munkaterületre vonatkozó nem tervezett költségek mennyiségét úgy használható.  

A napi korlátot, a gyűjtemény számlázható adattípusok leállítja az a nap hátralevő. Egy figyelmeztetés szalagcím jelenik meg a kiválasztott Log Analytics-munkaterületet a lap tetején, és a egy műveletet a rendszer a *művelet* tábla alatt **LogManagement** kategória. Az adatgyűjtés után a visszaállítási idő alatt meghatározott folytatja *napi korlát lesz beállítva*. Azt javasoljuk, hogy arra az esetre, ha a napi korlátot elérte a konfigurált Ez a művelet események alapján riasztási szabály meghatározása. 

> [!NOTE]
> A napi korlát nem állítja le az adatgyűjtést az Azure Security Center.

### <a name="identify-what-daily-data-limit-to-define"></a>Milyen napi korlátot meghatározásához azonosítása

Felülvizsgálat [Log Analytics-használat és becsült költségek](usage-estimated-costs.md) megtudhatja, hogy az adatok betöltési trend és mi az a napi mennyiségi korlát meghatározásához. Kell tekinteni, körültekintően, mivel nem lehet az erőforrások figyeléséhez, a korlát elérése után. 

### <a name="manage-the-maximum-daily-data-volume"></a>A maximális napi adatmennyiség kezelése

A következő lépések bemutatják, hogyan konfigurálása a Log Analytics-munkaterület lesz képes feldolgozni naponta adatmennyiség kezelése korlátozva.  

1. A munkaterületen válassza a **Felhasználás és becsült költségek** lehetőséget a bal oldali panelen.
2. Az a **felhasználás és becsült költségek** a kijelölt munkaterület oldalára, kattintson **adatmennyiség-kezelés** az oldal tetején. 
3. Napi korlát a következő **OFF** – alapértelmezés szerint kattintson **ON** az engedélyezéshez, és állítsa az mennyiségi korlát a GB/nap.

    ![A log Analytics konfigurálása adatkorlát](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Riasztás, ha elérte a napi korlát

Hogy jelen visual köteg az Azure Portalon, ha az adatok korlát küszöbértéket, amíg ez a viselkedés nem feltétlenül igazítás azonnali figyelmet igénylő működési problémák kezelése.  Az Azure Monitor riasztási értesítés fogadására, létrehozhat egy új riasztási szabály.  További tudnivalókért lásd: [létrehozása, megtekintése és kezelése a riasztások](alerts-metric.md).

Az első lépésekhez, az alábbiakban a riasztás az ajánlott beállításokat:

- Cél: Válassza ki a Log Analytics-erőforrás
- Feltételek: 
   - Jel neve: Egyéni naplók keresése
   - Keresési lekérdezés: A művelet |} Ha részletes rendelkezik-e "termékváltozatként használja:
   - Alapján: Az eredmények száma
   - Feltétel: Nagyobb, mint
   - Küszöbérték: 0
   - Időszak: 5 (perc)
   - Gyakorisága: 5 (perc)
- Riasztási szabály neve: Elérte a napi korlátot
- Súlyosság: Figyelmeztetés (Sev 1)

Riasztás van definiálva, és a eléri a korlátot, riasztás akkor aktiválódik, és hajtja végre a választ a műveletcsoport meghatározott. Küldjön értesítést munkatársainak e-mailek és SMS-EK, valamint automatizálja a műveleteket webhookok, Automation-runbookok használatával vagy [integrálása egy külső ITSM-megoldással](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Módosítsa az Adatmegőrzés időtartama

Az alábbi lépéseket adatért által a munkaterületen milyen hosszú log konfigurálását ismertetik.
 
1. A munkaterületen válassza a **Felhasználás és becsült költségek** lehetőséget a bal oldali panelen.
2. A **Felhasználás és becsült költségek** oldalon kattintson az oldal tetején található **Adatmennyiség-kezelés** elemre.
3. A panelen a csúszka segítségével növelheti vagy csökkentheti a napok számát, és kattintson a **OK**.  Ha a *ingyenes* szint, nem tudja módosítani az Adatmegőrzés időtartama, és annak érdekében, hogy ez a beállítás szabályozza a fizetős csomagra frissíteni szeretne.

    ![Munkaterület megőrzése beállításának módosítása](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
A megőrzési is lehet [ARM-en keresztül](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) használatával a `dataRetention` paraméter. Ezenkívül ha az adatmegőrzés 30 napra, aktiválhat egy azonnali végleges törlésére régebbi adatokat az a `immediatePurgeDataOn30Days` paramétert, amely a megfelelőséggel kapcsolatos esetekben hasznos lehet. Ez a funkció csak ARM elérhetővé. 

## <a name="legacy-pricing-tiers"></a>Örökölt tarifacsomagok

Előfizetések, aki eddig egy Log Analytics-munkaterület és Application Insights-erőforrást, 2018. április 2. előtt, vagy 2019. február 1. előtt elindított nagyvállalati szerződéshez kapcsolódó továbbra is hozzáférhetnek a tarifacsomagok régebbi: **Ingyenes**, **önálló (GB-onként)** és **Csomópontonkénti (OMS)** .  Az ingyenes díjcsomag munkaterületein napi korlátja 500 MB (kivéve az Azure Security Center által gyűjtött biztonsági adattípusok) adatbetöltés fog rendelkezni, és az adatmegőrzés legfeljebb 7 napig. Az ingyenes díjcsomag kizárólag értékelési célokra szolgál. Az önálló vagy Csomópontonkénti tarifacsomagok munkaterületekre akár 2 évig fenntartása felhasználó által konfigurálható. 2016. április előtt létrehozott munkaterületeket is rendelkezik elérheti az eredeti **Standard** és **prémium** díjcsomagok árából. További részleteket az árképzési szint korlátozásai [Itt](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces).

> [!NOTE]
> Az OMS E1 csomag, OMS E2 csomagot vagy a System Center OMS bővítményének megvásárlásából származó jogosultságok használatához válassza a Log Analytics *Csomópontonkénti* tarifacsomag.

A Log Analytics legkorábbi belépők is van hozzáférése az eredeti tarifacsomagok **Standard** és **prémium**, amely rendre kijavítása adatmegőrzés, 30 és 365 nap. 

## <a name="changing-pricing-tier"></a>A tarifacsomag módosítása

Ha a Log Analytics-munkaterület hozzáfér az örökölt tarifacsomagok között örökölt tarifacsomag módosítása:

1. Az Azure Portalon a Log Analytics-előfizetések panelen válasszon ki egy munkaterületet.

2. A munkaterület ablaktáblán alatt **általános**válassza **tarifacsomag**.  

3. A **tarifacsomag**válassza ki a tarifacsomagot, majd kattintson a **kiválasztása**.  
    ![A kijelölt tarifacsomag](media/manage-cost-storage/workspace-pricing-tier-info.png)

Emellett [ARM-n keresztül a tarifacsomagot állítsa](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) használatával a `ServiceTier` paraméter. 

## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>A Log Analytics már nem gyűjt adatokat okozó hibák elhárítása

Ha a régi ingyenes tarifacsomagban és a egy nap alatt több mint 500 MB mennyiségű adatot küldő adatgyűjtés leáll, a többi, a nap. Napi korlát elérése a gyakori oka, hogy a Log Analytics leállítja az adatgyűjtést, vagy adatokat úgy tűnik, hogy hiányzik.  A log Analytics művelet típusú eseményt hoz létre, amikor adatgyűjtés indítása és leállítása. A keresés, ellenőrizze, hogy vannak napi korlát elérése és adatok hiányoznak a következő lekérdezés futtatásával: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Ha leállítja az adatgyűjtést, van-e a OperationStatus **figyelmeztetés**. Amikor elindul az adatgyűjtés, van-e a OperationStatus **sikeres**. A következő táblázat ismerteti az oka, hogy leállítja az adatgyűjtést, és folytathatja az adatgyűjtést javasolt művelet:  

|OK gyűjtemény leáll| Megoldás| 
|-----------------------|---------|
|Elérte az örökölt az ingyenes tarifacsomag napi korlátot |Várja meg, amíg a gyűjtemény indítja újra automatikusan a következő napon, vagy módosítása fizetős tarifacsomagra.|
|Elérte a napi korlát a munkaterület|Várja meg, indítsa újra automatikusan a gyűjtemény, vagy növelje a napi mennyiségi korlátot ismertetett kezelése a maximális napi adatmennyiséget. A napi korlát visszaállítási idő mutat be kapcsolva a **adatmennyiség-kezelés** lapot. |
|Azure-előfizetés miatt felfüggesztett állapotban van:<br> Ingyenes próbaidőszak véget ért<br> Az Azure pass lejárt<br> Havonta költségkeret elérése (például az MSDN vagy a Visual Studio előfizetési)|Fizetős előfizetéssé alakítani<br> Távolítsa el a korlátot, vagy várjon, amíg a korlát alaphelyzetbe állítása|

Értesíti, ha leállítja az adatgyűjtést, használja a témakörben ismertetett *létrehozás napi adatkorlátjának* riasztás értesíti, ha leállítja az adatgyűjtést. Az ismertetett lépésekkel [műveletcsoport létrehozása](action-groups.md) egy e-mail, webhook vagy runbook műveletet a riasztási szabály konfigurálása. 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>A vártnál magasabb szintű használatot okozó hibák elhárítása

A magasabb szintű használatot a következők okozhatják:
- Adatot küld a Log Analytics-munkaterület vártnál több csomópont
- Log Analytics-munkaterületnek küld a vártnál több adatot

## <a name="understanding-nodes-sending-data"></a>Adatokat küldő csomópontok ismertetése

Szeretné megtudni, a szívverések naponta az elmúlt hónapban jelentő számítógépek számát, használata

```kusto
Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```

Szeretne kapni, számlázunk ki, csomópontok, ha a munkaterület megtalálható az örökölt Csomópontonkénti tarifacsomag, amely számítógépek listáját, keressen a továbbító csomópontok **adattípusok számlázzuk** (az egyes adattípusok ingyenes). Ehhez használja a `_IsBillable` [tulajdonság](log-standard-properties.md#_isbillable) és a bal oldali mezőt, teljesen minősített tartománynév használata. Ez a számlázott adatok rendelkező számítógépek listáját adja vissza:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Látható számlázható csomópontjainak száma szerint becsülhető meg: 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| billableNodes=dcount(computerName)
```

> [!NOTE]
> E `union withsource = tt *` , különböző adattípusok keresésekre végrehajtása költséges takarékosan kérdezi le. Ez a lekérdezés a használati adatok típusa számítógép információk lekérdezése a régi módja váltja fel.  

Egy több pontos kiszámításához mi ténylegesen számítjuk fel, hogy a számítógépek óránként küldő számlázott adattípusok száma kap. (Örökölt Csomópontonkénti tarifacsomag kiválasztása a munkaterületekhez, a Log Analytics kiszámítja a csomópontok, amely óránként számlázunk kell.) 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="understanding-ingested-data-volume"></a>Understanding betöltött adatmennyiség

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

Megtekintéséhez a **mérete** számlázható események betöltött számítógépenként, használja a `_BilledSize` [tulajdonság](log-standard-properties.md#_billedsize), amely biztosítja, hogy a mérete (bájt):

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize Bytes=sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

A `_IsBillable` [tulajdonság](log-standard-properties.md#_isbillable) Megadja, hogy a betöltött adatok díjat számolunk.

A száma, hogy **számlázható** számítógépenként, betöltött események használata 

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

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Azure-erőforrás, erőforráscsoport vagy előfizetés alapján adatmennyiség

Az Azure-ban üzemeltetett csomópontok adatok lekérése a **mérete** betöltött számlázható események __számítógépenként__, használja a _ResourceId [tulajdonság](log-standard-properties.md#_resourceid), amely biztosítja, hogy a teljes elérési útja a erőforrás:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Az Azure-ban üzemeltetett csomópontok adatok beolvasása a **mérete** betöltött számlázható események __Azure-előfizetésenként__, elemezni a `_ResourceId` tulajdonsága mint:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

Módosítása `subscriptionId` való `resourceGroup` jelennek meg Azure-erőforráscsoport számlázható a feldolgozott adatmennyiség. 


> [!NOTE]
> Néhány, a használati adatok típusú mezőt közben továbbra is a séma elavultak, értékeik már fel van töltve lesz. Ezek a **számítógép** valamint Adatbetöltési kapcsolódó mezőket (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**,  **BatchesCapped** és **AverageProcessingTimeMs**.

### <a name="querying-for-common-data-types"></a>Általános adattípusok lekérdezése

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

### <a name="getting-security-and-automation-node-counts"></a>Első biztonsági és Automation-csomópont számát

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

## <a name="create-an-alert-when-data-collection-is-high"></a>Riasztás létrehozása, amikor az adatgyűjtés túl magas

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

## <a name="limits-summary"></a>Korlátozások összegzése

Van néhány további Log Analytics-korlátok, a, amelyek a Log Analytics tarifacsomagot függenek. Ezek vannak dokumentálva [Itt](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-limits).


## <a name="next-steps"></a>További lépések

- Lásd: [Naplókereséseket a Azure Monitor naplóira](../log-query/log-query-overview.md) megtudhatja, hogyan használhatja a keresési nyelv. A keresési lekérdezésekkel további elemzéseket végezhet a használati adatokon.
- Az [új naplózási riasztás létrehozásával kapcsolatos](alerts-metric.md) szakaszban leírt lépéseket követve beállíthatja, hogy értesítést kapjon, ha teljesül egy keresési feltétel.
- A [megoldáscélzási](../insights/solution-targeting.md) funkcióval megadhatja, hogy csak a szükséges számítógépcsoportoktól gyűjtsön adatokat.
- Egy hatékony esemény gyűjtési szabályzat konfigurálásához tekintse át a [az Azure Security Center szűrési szabályzatai](../../security-center/security-center-enable-data-collection.md).
- Módosítsa a [teljesítményszámlálók konfigurációját](data-sources-performance-counters.md).
- Az eseménygyűjtési beállítások módosításához tekintse meg az [eseménynaplók konfigurációját](data-sources-windows-events.md) leíró szakaszt.
- A rendszernapló-gyűjtési beállítások módosításához tekintse meg a [rendszernaplók konfigurációját](data-sources-syslog.md) leíró szakaszt.