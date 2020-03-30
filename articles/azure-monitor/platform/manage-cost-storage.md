---
title: Az Azure Monitor-naplók használati és költségeinek kezelése | Microsoft dokumentumok
description: Megtudhatja, hogyan módosíthatja a díjszabási tervet, és kezelheti az adatok mennyiségét és adatmegőrzési szabályzatát a Log Analytics-munkaterülethez az Azure Monitorban.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 6911afa5dfcd14f9e5d1068acbcb2355200c5545
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479807"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Használat és költségek kezelése az Azure Figyelő naplókkal

> [!NOTE]
> Ez a cikk ismerteti, hogyan értheti és szabályozhatja az Azure Monitor naplók költségeit. Egy kapcsolódó cikk, [a használat figyelése és a becsült költségek](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) ismerteti, hogyan tekintheti meg a használat és a becsült költségek több Azure figyelési funkciók különböző díjszabási modellek.

> [!NOTE]
> Az ebben a cikkben feltüntetett összes ár és költség csak célokat szolgál. 

Az Azure Monitor-naplók méretezése és támogatása hatalmas mennyiségű adat gyűjtése, indexelése és tárolása naponta bármely forrásból a vállalaton belül, vagy az Azure-ban üzembe helyezett.  Bár ez lehet a szervezet elsődleges illesztőprogramja, a költséghatékonyság végső soron az alapul szolgáló illesztőprogram. Ebből a célból fontos megérteni, hogy a Log Analytics-munkaterület költsége nem csak az összegyűjtött adatok mennyiségén alapul, hanem a kiválasztott tervtől is függ, és attól, hogy mennyi ideig tárolja a csatlakoztatott forrásokból létrehozott adatokat.  

Ebben a cikkben áttekintjük, hogyan proaktív módon figyelheti a bevitt adatok mennyiségét és a tárolási növekedést, és korlátokat határozhat meg a kapcsolódó költségek szabályozásához. 

## <a name="pricing-model"></a>Díjszabási modell

A Log Analytics alapértelmezett díjszabása egy **használatra szánt fizetési** modell, amely a bevitt adatmennyiségen alapul, és opcionálisan hosszabb adatmegőrzési. Az adatmennyiséget a rendszer a tárolandó adatok méretéből méri. Minden Log Analytics-munkaterület külön szolgáltatásként kerül felszámolásra, és hozzájárul az Azure-előfizetés számlájához. Az adatbetöltés mennyisége a következő tényezőktől függően jelentős lehet: 

  - Az engedélyezett felügyeleti megoldások száma és konfigurációja (pl. 
  - A figyelt virtuális gépek száma
  - Az egyes figyelt virtuális gépektől gyűjtött adatok típusa 
  
A csak a felosztó-ki-fel- modellek, Log Analytics **rendelkezik kapacitásfoglalási** szintek, amelyek lehetővé teszik, hogy mentse, mint 25%, mint a pay-as-you-go ár. A kapacitásfoglalás díjszabása lehetővé teszi, hogy 100 GB/nap-tól kezdődőfoglalást vásároljon. A foglalási szint feletti használatot a használatalapú fizetés díja alapján számlázunk. A kapacitáslefoglalási szintek 31 napos kötelezettségvállalási időszakkal rendelkeznek. A kötelezettségvállalási időszak alatt magasabb szintű kapacitásfoglalási szintre válthat (amely újraindítja a 31 napos kötelezettségvállalási időszakot), de nem léphet vissza a kiosztónként vagy alacsonyabb kapacitásfoglalási szintre, amíg a kötelezettségvállalási időszak meg nem történik. Kész. 
[További információ](https://azure.microsoft.com/pricing/details/monitor/) a Log Analytics többszöri fizetéses és kapacitásfoglalási díjszabásáról. 

Az összes tarifacsomagban az adatkötet az adatok tárolásra kész karakterlánc-ábrázolásából kerül kiszámításra. Az összes [adattípusra vonatkozó számos tulajdonság](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties) nem szerepel az `_ResourceId` `_ItemId`eseményméret számításában, beleértve a , a és `_IsBillable` `_BilledSize`a.

Vegye figyelembe, hogy egyes megoldások, például [az Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/) és az Azure [Sentinel,](https://azure.microsoft.com/pricing/details/azure-sentinel/)saját díjszabási modellel rendelkeznek. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>A környezet kezelési költségeinek becslése 

Ha még nem használja az Azure Monitor naplók, használhatja az [Azure Monitor díjkalkulátor](https://azure.microsoft.com/pricing/calculator/?service=monitor) becsléséhez a Log Analytics használatának költségét. Kezdje azzal, hogy beírja az "Azure Monitor" kifejezést a keresőmezőbe, és az eredményül kapott Azure Monitor csempére kattint. Görgessen le a lapon az Azure Monitor, és válassza a Log Analytics a Típus legördülő menüben.  Itt adhatja meg a virtuális gépek számát és az egyes virtuális gépektől várhatóan gyűjtött adatok GB-ját. Általában 1–3 GB-nyi adathónap egy tipikus Azure-beli virtuális gépből kerül betöltésre. Ha már kiértékeli az Azure Monitor naplók már, használhatja az adatok statisztikáit a saját környezetében. Az alábbiakban megtudhatja, hogyan határozhatja meg a [figyelt virtuális gépek számát](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) és a munkaterület által [betöltésalatt álló adatok mennyiségét.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume) 

## <a name="understand-your-usage-and-estimate-costs"></a>A használati és becslési költségek megismerése

Ha most használja az Azure Monitor naplók most, könnyen érthető, hogy milyen költségek valószínűleg alapulnak a legutóbbi használati minták. Ehhez használja **a Log Analytics-használat és a becsült költségek** adathasználat áttekintése és elemzése. Ez azt mutatja, hogy az egyes megoldások mennyi adatot gyűjtenek, mennyi adatot őriznek meg, és a költségek becsült mennyisége a bevitt adatok mennyisége és a benne foglalt összegen túli további adatmegőrzés.

![Használat és becsült költségek](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Az adatok részletesebb megismeréséhez kattintson a **használati és becsült költségek** oldalon, bármelyik diagram jobb felső részén található ikonra. Most már ezzel a lekérdezéssel további részleteket fedezhet fel a használatról.  

![Naplók nézet](media/manage-cost-storage/logs.png)

A **Használati és becsült költségek** lapon megtekintheti az adott hónap adatmennyiségét. Ez magában foglalja a Log Analytics-munkaterületen fogadott és megőrzött összes adatot.  Kattintson a lap tetején a **Használati részletek** elemre a használati irányítópult megtekintéséhez, amely a forrás, a számítógépek és az ajánlat szerint az adatmennyiség-trendekre vonatkozó információkat tartalmaz. A napi korlát megtekintéséhez és beállításához, illetve a megőrzési időszak módosításához kattintson az **Adatmennyiség-kezelés gombra.**
 
A Log Analytics-díjak hozzáadódnak az Azure-számlához. Az Azure-számla részleteit az Azure Portal Számlázás szakaszában vagy az [Azure számlázási portálon](https://account.windowsazure.com/Subscriptions)láthatja.  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>A Log Analytics használatának megtekintése az Azure-számlán 

Az Azure számos hasznos funkciót biztosít az [Azure Cost Management + Billing](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) hubon. Például a "Költségelemzés" funkció lehetővé teszi, hogy az Azure-erőforrásokra fordított kiadások megtekintéséhez. Ha erőforrástípus szerint szűrőt ad hozzá (a microsoft.operationalinsights/workspace for Log Analytics szolgáltatáshoz) lehetővé teszi a kiadások nyomon követését.

A használat jobb megértése az [Azure Portalról való letöltéssel](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)érhető el. A letöltött táblázatban láthatja az Azure-erőforrásonkénti (pl. Log Analytics-munkaterület) napi használatát. Ebben az Excel-számolótáblában a Log Analytics-munkaterületek használatának megtalálható a "Mérőkategória" oszlop első szűrésével, amely bemutatja az "Insights and Analytics" (néhány örökölt tarifacsomag által használt) és a "Log Analytics" megjelenítését, majd egy szűrőhozzáadásaa "Példány" azonosító" oszlop, amely "munkaterületet tartalmaz". A felhasználás a "Felhasznált mennyiség" oszlopban, az egyes bejegyzések egysége pedig a "Mértékegység" oszlopban látható.  További részletek a [Microsoft Azure-számlának megértéséhez](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)érhetők el. 

## <a name="changing-pricing-tier"></a>A tarifacsomag módosítása

A munkaterület Log Analytics-tarifacsomagjának 

1. Az Azure Portalon nyissa meg a használat és a **becsült költségek a** munkaterületről, ahol megjelenik a munkaterület számára elérhető tarifacsomagok listája.

2. Tekintse át az egyes tarifacsomagok becsült költségeit. Ez a becslés a használat utolsó 31 napján alapul, így ez a költségbecslés az utolsó 31 nap jellemző en alapul. Az alábbi példában láthatja, hogyan, az adatok minták alapján az elmúlt 31 nap, ez a munkaterület olcsóbb lenne a pay-as-you-go szint (#1) képest a 100 GB/day kapacitásfoglalási szint (#2).  

    ![Árképzési szintek](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. Miután áttekintette a becsült költségeket a használat utolsó 31 napja alapján, ha úgy dönt, hogy módosítja a tarifacsomagot, kattintson a **Kijelölés gombra.**  

A [tarifacsomagot az Azure Resource](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) Manager `sku` használatával`pricingTier` is beállíthatja (az Azure Resource Manager sablonban). 

## <a name="legacy-pricing-tiers"></a>Örökölt tarifacsomagok

Azok az előfizetések, akik **Free**2018. **Standalone (Per GB)** **Per Node (OMS)**  Az ingyenes tarifacsomag munkaterületeinek napi adatbetöltése 500 MB-ra korlátozódik (kivéve az Azure Security Center által gyűjtött biztonsági adattípusokat), és az adatmegőrzés 7 napra korlátozódik. Az ingyenes tarifacsomag csak értékelési célokat szolgál. Az önálló vagy csomópontonkénti tarifacsomagok munkaterületei legfeljebb 2 évig rendelkeznek a felhasználó által konfigurálható megőrzési szintekkel. 

A 2016 áprilisa előtt létrehozott munkaterületek is hozzáférhetnek az eredeti **standard** és **prémium** tarifacsomagokhoz, amelyek rögzített adatmegőrzési sorrendben 30, illetve 365 nap. Nem hozhatók létre új munkaterületek a **standard** vagy **prémium szintű** tarifacsomagokban, és ha egy munkaterületet áthelyeznek ezekből a rétegekből, akkor nem helyezhető vissza. 

A tarifacsomag-korlátozásokról itt [olvashat](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)bővebben.

> [!NOTE]
> Az OMS E1 Suite, AZ OMS E2 Suite vagy a System Center OMS bővítmény megvásárlásából származó jogosultságok használatához válassza a Log Analytics *csomópontonkénti* díjszabási szintjét.

## <a name="change-the-data-retention-period"></a>Az adatmegőrzési időtartam módosítása

Az alábbi lépések azt ismertetik, hogyan lehet beállítani, hogy mennyi ideig tárolják a naplóadatokat a munkaterületen.

### <a name="default-retention"></a>Alapértelmezett megőrzés

A munkaterület alapértelmezett adatmegőrzési beállításához 
 
1. Az Azure Portalon a munkaterületről válassza **a használati és becsült költségek a** bal oldali ablaktáblában.
2. A **Felhasználás és becsült költségek** oldalon kattintson az oldal tetején található **Adatmennyiség-kezelés** elemre.
3. Az ablaktáblán mozgassa a csúszkát a napok számának növeléséhez vagy csökkentéséhez, majd kattintson az **OK**gombra.  Ha az *ingyenes* szinten van, nem fogja tudni módosítani az adatmegőrzési időszakot, és frissítenie kell a fizetős szintre a beállítás szabályozásához.

    ![Munkaterület-adatmegőrzési beállítás módosítása](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
A megőrzési is [beállítható az](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) `retentionInDays` Azure Resource Manager a paraméter használatával. Emellett ha az adatmegőrzés30 napra állítja be, a paraméter használatával `immediatePurgeDataOn30Days` azonnal kiürítheti a régebbi adatokat, ami hasznos lehet a megfelelőségi forgatókönyvek esetén. Ez a funkció csak az Azure Resource Manager en keresztül érhető el. 

Két adattípus `Usage` - `AzureActivity` és -- alapértelmezés szerint 90 napig megmarad, és a 90 napos megőrzésért nem számítunk fel díjat. Ezek az adattípusok adatbetöltési díjaktól is mentesek. 

### <a name="retention-by-data-type"></a>Adattípus onkénti megőrzése

Az egyes adattípusokhoz különböző adatmegőrzési beállítások is megadhatók. Minden adattípus a munkaterület alerőforrása. Például a SecurityEvent tábla címezhető az [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) ben:

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Ne feledje, hogy az adattípus (tábla) a kis- és nagybetűket.  Egy adott adattípus adattípusonkénti aktuális adatmegőrzési beállításainak betöltéséhez (ebben a példában securityevent), használja a következőket:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

Ha a munkaterület összes adattípusára vonatkozóan szeretné lekérni az adattípusonkénti aktuális adatmegőrzési beállításokat, egyszerűen hagyja ki az adott adattípust, például:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Ha egy adott adattípus (ebben a példában securityevent) megőrzését 730 napra szeretné

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

Az `Usage` `AzureActivity` adattípusok nem állíthatók be egyéni adatmegőrzéssel. Az alapértelmezett munkaterület-megőrzési vagy 90 napos maximális időtartamot veszik igénybe. 

Az OSS-eszköz, az [ARMclient](https://github.com/projectkudu/ARMClient)egy nagyszerű eszköz, amellyel közvetlenül csatlakozhat az Azure Resource Managerhez az adatmegőrzés beállításához.  Tudjon meg többet armclient cikkek [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) és [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Íme egy példa az ARMClient használatával, amely a SecurityEvent adatokat 730 napos adatmegőrzésre állítja:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!NOTE]
> Az egyes adattípusok megőrzésének beállításával csökkentheti az adatmegőrzési költségeket.  A 2019 októberében (a funkció megjelenésekor) gyűjtött adatok esetében az egyes adattípusok megőrzésének csökkentése csökkentheti a megőrzési költséget.  A korábban gyűjtött adatok, beállítása alacsonyabb megőrzési egy adott típus nem befolyásolja a megőrzési költségeket.  

## <a name="manage-your-maximum-daily-data-volume"></a>A maximális napi adatmennyiség kezelése

Beállíthatja a napi korlátot, és korlátozhatja a munkaterület napi betöltését, de óvatosan, mivel a cél nem lehet a napi korlát elérése.  Ellenkező esetben a nap hátralevő részében adatokat veszít, ami hatással lehet más Azure-szolgáltatásokra és megoldásokra, amelyek funkciója a munkaterületen elérhető naprakész adatoktól függhet.  Ennek eredményeképpen a figyelési és fogadási riasztások fogadása, ha az informatikai szolgáltatásokat támogató erőforrások állapotának hatással van.  A napi korlát a felügyelt erőforrásokból származó adatmennyiség váratlan növekedésének kezelésére szolgál, és a korláton belül marad, vagy ha korlátozni szeretné a munkaterület nem tervezett díjait.  

A napi korlát elérésekor a számlázható adattípusok gyűjtése leáll a nap hátralévő részében. A kijelölt Log Analytics-munkaterület oldaltetején egy figyelmeztető szalagcím jelenik meg, és a **naplókezelés** kategória operation eseménye a *Művelet* táblába kerül. Az adatgyűjtés a Napi korlát ban meghatározott alaphelyzetbe állítási idő után folytatódik a *beállítási ekkor:*. Azt javasoljuk, hogy adjon meg egy riasztási szabályt ezen a műveleteseményen alapulva, amely úgy van beállítva, hogy értesítse a napi adatkorlát eléréséről. 

> [!NOTE]
> A napi korlát nem állítja le az Adatok gyűjtését az Azure Security Centerből, kivéve azokat a munkaterületeket, ahol az Azure Security Center telepítve volt 2017. 

> [!NOTE]
> A napi felső korlát alkalmazásával járó késleltetés azt jelentheti, hogy a kupakot nem alkalmazzák pontosan a megadott napi felső korlátszintjén. 

### <a name="identify-what-daily-data-limit-to-define"></a>Határozza meg, hogy milyen napi adatkorlátot kell meghatároznia

Tekintse át [a Log Analytics-használat és](usage-estimated-costs.md) a becsült költségek et, hogy megismerjék az adatbetöltési trendet, és hogy mi a meghatározandó napi mennyiségi korlát. Azt meg kell vizsgálni óvatosan, mivel nem lesz képes nyomon követni a források után a határértéket eléri. 

### <a name="set-the-daily-cap"></a>A napi korlát beállítása

Az alábbi lépések azt ismertetik, hogyan konfigurálhat egy korlátot a Log Analytics-munkaterület által naponta betöltést betöltési adatok mennyiségének kezelésére.  

1. A munkaterületen válassza a **Felhasználás és becsült költségek** lehetőséget a bal oldali panelen.
2. A kijelölt munkaterület **Használati és becsült költségek** lapján kattintson a lap tetején az **Adatmennyiség-kezelés** elemre. 
3. A napi korlát alapértelmezés szerint ki van **kapcsolva,** az engedélyezéshez kattintson **a BE** gombra, majd állítsa be az adatmennyiség-korlátot GB/nap értékben.

    ![A Log Analytics adatkorlátot konfigurál](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Figyelmeztetés a napi korlát elérésekor

Miközben az Azure Portalon vizuális jelzést jelenítünk meg, amikor az adatkorlát-küszöbérték teljesül, ez a viselkedés nem feltétlenül igazodik az azonnali figyelmet igénylő működési problémák kezeléséhez.  Riasztási értesítés fogadásához létrehozhat egy új riasztási szabályt az Azure Monitorban.  További információ: [riasztások létrehozása, megtekintése és kezelése.](alerts-metric.md)

A kezdéshez az alábbiakban a riasztás ajánlott beállításait találja:

- Cél: Válassza ki a Log Analytics-erőforrást
- Kritériumok: 
   - Jel neve: Egyéni naplókeresés
   - Keresési lekérdezés: Operation | ahol a részletek "OverQuota"
   - Az eredmények száma alapján
   - Állapot: Nagyobb, mint
   - Küszöbérték: 0
   - Időszak: 5 (perc)
   - Gyakoriság: 5 (perc)
- Riasztási szabály neve: Elérte a napi adatkorlátot
- Súlyosság: Figyelmeztetés (Sev 1)

A riasztás definiálása és a korlát elérése után egy riasztás aktiválódik, és végrehajtja a műveletcsoportban meghatározott választ. E-mailben és szöveges üzenetben értesítheti a csapatot, vagy automatizálhatja a műveleteket webhookok, Automation runbookok vagy [külső ITSM-megoldással való integráció használatával.](itsmc-overview.md#create-itsm-work-items-from-azure-alerts) 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>A vártnál magasabb szintű használatot okozó hibák elhárítása

A magasabb szintű használatot a következők okozhatják:
- A vártnál több csomópont küld adatokat a Log Analytics munkaterületre
- A vártnál több adat kerül elküldésre a Log Analytics-munkaterületre (például azért, mert új megoldást vagy egy meglévő megoldás konfigurációjának módosítását kezdik használni)

## <a name="understanding-nodes-sending-data"></a>Az adatokat küldő csomópontok ismertetése

Ha az elmúlt hónapban minden nap szeretné tudni, hogy hány, az ügynöktől szívdobogott szívverést jelző csomópontot

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
Az adatokat küldő csomópontok száma az elmúlt 24 órában a lekérdezést használja: 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

Az adatokat küldő csomópontok (és az egyes adatok által küldött adatok mennyiségének) lekérdezéséhez a következő lekérdezés használható:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

> [!NOTE]
> Használja `union withsource = tt *` ezeket a lekérdezéseket takarékosan, mint a vizsgálatok az adattípusok között költséges végrehajtani. Ez a lekérdezés lecseréli a számítógépenkénti adatok lekérdezésének régi módját a Használati adattípusra.  

## <a name="understanding-ingested-data-volume"></a>A bevitt adatok mennyiségének ismertetése

A **Használati és becsült költségek** lapon a *megoldásonkénti adatok betöltése* diagram az elküldött adatok teljes mennyiségét és az egyes megoldások által küldött mennyiséget jeleníti meg. Ez lehetővé teszi a trendek meghatározását, például azt, hogy a teljes adathasználat (vagy egy adott megoldás általi használat) növekszik, stabil vagy csökkenő marad.This allows you to determine trends such as whether the overall data usage (or usage by a particular solution) is growing, remaining steady or decreasing. 

### <a name="data-volume-for-specific-events"></a>Adatmennyiség adott eseményekhez

Egy adott eseményhalmaz bevitt adatainak méretét meg szeretné tekinteni, lekérdezheti az `Event`adott táblát (ebben a példában), majd korlátozhatja a lekérdezést az érdekes eseményekre (ebben a példában az 5145-ös vagy 5156-os azonosítójú eseményre):

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

Vegye figyelembe, `where IsBillable = true` hogy a záradék kiszűri az adattípusokat bizonyos megoldásokból, amelyeknél nincs betöltési díj. 

### <a name="data-volume-by-solution"></a>Adatmennyiség megoldásonként

A számlázható adatmennyiség megoldásonkénti megtekintéséhez használt lekérdezés az elmúlt hónapban (az utolsó részleges nap kivételével) a következő:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

A záradék `TimeGenerated` csak annak biztosítására, hogy a lekérdezési élmény az Azure Portalon az alapértelmezett 24 órán túl. A Használati adattípus `StartTime` használatakor, és `EndTime` azokat az időkategóriákat jelöli, amelyekhez az eredmények megjelennek. 

### <a name="data-volume-by-type"></a>Adatmennyiség típus szerint

Tovább részletezve megtekintheti az adattrendekadat-típusok alapján:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

Vagy ha meg szeretne látni egy táblázatot megoldás és az elmúlt hónapban,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Adatmennyiség számítógépenként

Az `Usage` adattípus nem tartalmaz teljesebb szintű információkat. A számítógépenként bevitt adatok **méretének** megtekintéséhez használja a `_BilledSize` [tulajdonságot,](log-standard-properties.md#_billedsize)amely bájtban adja meg a méretet:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

A `_IsBillable` [tulajdonság](log-standard-properties.md#_isbillable) határozza meg, hogy a bevitt adatok után díjat.

A számítógépenként bevitt számlázható események **számának** megtekintéséhez használja a 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  | sort by eventCount nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Adatmennyiség Azure-erőforrás, erőforráscsoport vagy előfizetés szerint

Az Azure-ban üzemeltetett csomópontokból származó adatok hozhatja be a __számítógépenként__idáig bevitt adatok **méretét,** használja a _ResourceId [tulajdonságot,](log-standard-properties.md#_resourceid)amely az erőforrás teljes elérési útját biztosítja:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Az Azure-ban üzemeltetett csomópontokból származó adatok esetén __azure-előfizetésenként__a bevitt `_ResourceId` adatok **méretét** kaphatja meg, elemezze a tulajdonságot a következőképpen:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize BillableDataBytes = sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

A `subscriptionId` `resourceGroup` rendszer a számlázható betöltési adatmennyiséget jeleníti meg az Azure erőforráscsoport szerint. 

> [!NOTE]
> A Használati adattípus néhány mezője, miközben még a sémában van, elavult, és értékeik már nem lesznek feltöltve. Ezek **a következők: Számítógép,** valamint a betöltéssel kapcsolatos mezők (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** és **AverageProcessingTimeMs**.

### <a name="querying-for-common-data-types"></a>Gyakori adattípusok lekérdezése

Ha mélyebbre szeretne ásni egy adott adattípus adatforrásában, az alábbi hasznos példalekérdezéseket olvashatja:

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

### <a name="tips-for-reducing-data-volume"></a>Tippek az adatmennyiség csökkentéséhez

Néhány javaslat az összegyűjtött naplók mennyiségének csökkentésére:

| A nagy adatmennyiség forrása | Az adatmennyiség csökkentésének módja |
| -------------------------- | ------------------------- |
| Biztonsági események            | Válassza a [gyakori vagy minimális biztonsági események](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) lehetőséget <br> Módosítsa a biztonsági naplózási szabályzatot, hogy csak a szükséges eseményeket gyűjtse be. Tekintse át a következőkhöz való eseménygyűjtés szükségességét: <br> - [szűrőplatform naplózása](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [beállításjegyzék naplózása](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [fájlrendszer naplózása](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [kernelobjektum naplózása](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [leírókezelés naplózása](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - ellenőrzés cserélhető tároló |
| Teljesítményszámlálók       | Módosítsa a [teljesítményszámlálók konfigurációját](data-sources-performance-counters.md): <br> – Csökkentse a gyűjtés gyakoriságát <br> – Csökkentse a teljesítményszámlálók számát |
| Eseménynaplók                 | Módosítsa az [eseménynaplók konfigurációját](data-sources-windows-events.md): <br> – Csökkentse a gyűjtött eseménynaplók számát <br> – Csak a szükséges eseményszinteket gyűjtse. Ne gyűjtsön például *Tájékoztatás* szintű eseményeket |
| Rendszernapló                     | Módosítsa a [rendszernapló konfigurációját](data-sources-syslog.md): <br> – Csökkentse a gyűjtésben részt vevő létesítmények számát <br> – Csak a szükséges eseményszinteket gyűjtse. Ne gyűjtsön például *Tájékoztatás* vagy *Hibakeresés* szintű eseményeket |
| AzureDiagnostics           | Az erőforrásnapló-gyűjtés módosítása a következőre: <br> – Csökkentse a Log Analytics számára naplókat küldő erőforrások számát <br> – Csak a szükséges naplókat gyűjtse |
| Megoldásadatok olyan számítógépekről, amelyeknek nincs szükségük a megoldásra | A [megoldáscélzás használatával](../insights/solution-targeting.md) csak a szükséges számítógépcsoportokból gyűjthet adatokat. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Csomópontok beszerzése a csomópontonkénti tarifacsomagban számlázva

Ha a rendszer csomópontokként számlázandó számítógépek listáját szeretné lekérni, ha a munkaterület az örökölt csomópontonkénti tarifacsomagban található, keresse meg azokat a csomópontokat, amelyek **számlázott adattípusokat** küldenek (egyes adattípusok ingyenesek). Ehhez használja a `_IsBillable` [tulajdonságot,](log-standard-properties.md#_isbillable) és használja a teljesen minősített tartománynév bal szélső mezőjét. Ez az óránként számlázott adatokkal rendelkező számítógépek számát adja vissza (amely a csomópontok számlálásának és számlázásának részletessége):

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>A biztonsági és automatizálási csomópontok számának beszerzése

Ha a "Csomópontonkénti (OMS)" tarifacsomagot használja, akkor a használt csomópontok és megoldások száma alapján számítjuk fel a díjat, a **számlázott** elemzési és elemzési csomópontok száma megjelenik a Használat és a Becsült költség oldalon található táblázatban.  

A különböző biztonsági csomópontok számának megtekintéséhez használhatja a lekérdezést:

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

Az eltérő Automatizálási csomópontok számának megtekintéséhez használja a lekérdezést:

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
| extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type 
| where DataGB > 100
```

A következő lekérdezés egy egyszerű képlettel előrejelzi, mikor fog a rendszer egy nap alatt több mint 100 GB adatot küldeni: 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type 
| where EstimatedGB > 100
```

Ha más adatmennyiségre szeretne riasztást beállítani, módosítsa a lekérdezésekben a 100 értéket arra a GB mennyiségre, amely esetén riasztást szeretne kapni.

Az [új naplózási riasztás létrehozásával kapcsolatos](alerts-metric.md) szakaszban leírt lépéseket követve beállíthatja, hogy értesítést kapjon, ha az adatgyűjtés szintje a vártnál magasabb.

Az első lekérdezéshez tartozó riasztás létrehozásakor – amikor több mint 100 GB adat lett összegyűjtve 24 órán belül, állítsa be a következőket:  

- A **riasztási feltétel megadásával** határozza meg a célerőforrásként használt Log Analytics-munkaterületet.
- A **Riasztási feltételek** résznél az alábbiakat adja meg:
   - A **Jel neve** legyen **Egyéni naplókeresés**
   - A **Keresési lekérdezés** legyen a következő: `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type | where DataGB > 100`
   - A **Riasztási logika****alapja legyen az ** *eredmények száma*, a **Feltétel** pedig legyen *nagyobb mint* a következő **küszöbérték **: *0*
   - Az **Időszak** értékét állítsa *1440* percre, a **Riasztási időköz** pedig legyen *60* perc, mivel a használati adatok csak óránként frissülnek.
- **Határozza meg a riasztás részleteit** az alábbiak megadásával:
   - A **Név** legyen *Több mint 100 GB adatmennyiség 24 órán belül*
   - A **Súlyosság** legyen *Figyelmeztetés*

Megadhat egy meglévő [műveletcsoportot](action-groups.md), illetve létrehozhat egy újat, hogy értesítést kapjon, amikor egy naplóriasztás megfelel a feltételeknek.

A második lekérdezéshez tartozó riasztás létrehozásakor – amikor több mint 100 GB adat összegyűjtése várható 24 órán belül, állítsa be a következőket:

- A **riasztási feltétel megadásával** határozza meg a célerőforrásként használt Log Analytics-munkaterületet.
- A **Riasztási feltételek** résznél az alábbiakat adja meg:
   - A **Jel neve** legyen **Egyéni naplókeresés**
   - A **Keresési lekérdezés** legyen a következő: `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type | where EstimatedGB > 100`
   - A **Riasztási logika****alapja legyen az ** *eredmények száma*, a **Feltétel** pedig legyen *nagyobb mint* a következő **küszöbérték **: *0*
   - Az **Időszak** értékét állítsa *180* percre, a **Riasztási időköz** pedig legyen *60* perc, mivel a használati adatok csak óránként frissülnek.
- **Határozza meg a riasztás részleteit** az alábbiak megadásával:
   - A **Név** legyen *Több mint 100 GB várható adatmennyiség 24 órán belül*
   - A **Súlyosság** legyen *Figyelmeztetés*

Megadhat egy meglévő [műveletcsoportot](action-groups.md), illetve létrehozhat egy újat, hogy értesítést kapjon, amikor egy naplóriasztás megfelel a feltételeknek.

Riasztás fogadásakor kövesse a következő szakaszban leírt lépéseket a vártnál magasabb szintű használatot okozó hibák elhárításához.

## <a name="data-transfer-charges-using-log-analytics"></a>Adatátviteli díjak a Log Analytics használatával

Adatok küldése a Log Analytics lehet nek adatsávszélesség díjakat. Az Azure [Bandwidth díjszabási lapján](https://azure.microsoft.com/pricing/details/bandwidth/)leírtak szerint az Azure-szolgáltatások közötti adatátvitel két régióban található, a normál adatátviteli díj ellenében felszámított kimenő adatátvitel. A bejövő adatátvitel ingyenes. Ez a díj azonban nagyon kicsi (néhány%) a Log Analytics-adatok betöltésének költségeihez képest. Ennek következtében a Log Analytics költségeinek szabályozásának a bevitt adatmennyiségre [here](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)kell összpontosítania, és útmutatást nyújtunk ennek megértéséhez.   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Hibaelhárítás, amely szerint a Log Analytics miért nem gyűjt már adatokat

Ha az örökölt ingyenes tarifacsomagot, és küldött több mint 500 MB adatot egy nap alatt, adatgyűjtés leáll a nap hátralevő részében. A napi korlát elérése gyakori oka annak, hogy a Log Analytics leállítja az adatgyűjtést, vagy úgy tűnik, hogy hiányzik az adatok.  A Log Analytics művelet típusú eseményt hoz létre, amikor az adatgyűjtés elindul és leáll. Futtassa a következő lekérdezést a keresésben, és ellenőrizze, hogy eléri-e a napi korlátot és a hiányzó adatokat: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Amikor az adatgyűjtés leáll, az OperationStatus **figyelmeztetés**. Az adatgyűjtés indításakor az OperationStatus **sikeres.** Az alábbi táblázat az adatgyűjtés leállításának okait és az adatgyűjtés folytatására javasolt műveleteket ismerteti:  

|Okgyűjtés immára leáll| Megoldás| 
|-----------------------|---------|
|Elérte az örökölt ingyenes tarifacsomag napi limitjét |Várjon a következő napig, amíg a gyűjtemény automatikusan újraindul, vagy váltson fizetős tarifacsomagra.|
|Elérte a munkaterület napi felső korlátját|Várja meg, amíg a gyűjtemény automatikusan újraindul, vagy növelje a maximális napi adatmennyiség kezelésében leírt napi adatmennyiség-korlátot. A napi korlát visszaállítási ideje az **Adatmennyiség-kezelés** oldalon jelenik meg. |
|Az Azure-előfizetés felfüggesztett állapotban van a következők miatt:<br> Ingyenes próbaverzió véget ért<br> Lejárt az Azure-bérlet<br> Elérte a havi költési korlátot (például MSDN- vagy Visual Studio-előfizetésesetén)|Átállás a fizetős verzióra<br> Korlát eltávolítása, vagy várakozás a korlát visszaállításáig|

Ha értesítést szeretne kapni az adatgyűjtés leállításáról, kövesse a *Napi adatkorlát-riasztás létrehozása* című részben leírt lépéseket, amelyeket az adatgyűjtés leállításakor szeretne értesíteni. A [műveletcsoport létrehozása](action-groups.md) című részben leírt lépésekkel konfigurálhat e-mail, webhook vagy runbook műveletet a riasztási szabályhoz. 

## <a name="limits-summary"></a>Korlátok összegzése

Vannak további Log Analytics-korlátozások, amelyek közül néhány függ a Log Analytics tarifacsomag. Ezek [itt](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)vannak dokumentálva.


## <a name="next-steps"></a>További lépések

- Tekintse meg [a Keresési keresések naplózása az Azure Monitor naplók, hogy megtanulják,](../log-query/log-query-overview.md) hogyan használhatja a keresési nyelvet. A keresési lekérdezésekkel további elemzéseket végezhet a használati adatokon.
- Az [új naplózási riasztás létrehozásával kapcsolatos](alerts-metric.md) szakaszban leírt lépéseket követve beállíthatja, hogy értesítést kapjon, ha teljesül egy keresési feltétel.
- A [megoldáscélzás használatával](../insights/solution-targeting.md) csak a szükséges számítógépcsoportokból gyűjthet adatokat.
- Hatékony eseménygyűjtési szabályzat konfigurálásához tekintse át [az Azure Security Center szűrési szabályzatát.](../../security-center/security-center-enable-data-collection.md)
- [Teljesítményszámláló-konfiguráció](data-sources-performance-counters.md)módosítása .
- Az eseménygyűjtési beállítások módosításához tekintse át [az eseménynapló konfigurációját.](data-sources-windows-events.md)
- A syslog gyűjtemény beállításainak módosításához tekintse át a [syslog konfigurációját.](data-sources-syslog.md)