---
title: Azure Monitor naplók használatának és költségeinek kezelése | Microsoft Docs
description: Megtudhatja, hogyan módosíthatja a díjszabási tervet, és hogyan kezelheti a Log Analytics munkaterület adatmennyiségét és adatmegőrzési szabályzatát Azure Monitorben.
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
ms.date: 11/05/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: dc784fa2dd5317932294af6e9c9d36dcce7d32f1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274397"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>A használat és a költségek kezelése Azure Monitor naplókkal

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan értelmezhető és szabályozható a Azure Monitor naplók költségei. A kapcsolódó cikkek, a [monitorozási használat és a becsült költségek](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) azt írják le, hogyan lehet megtekinteni a használati és becsült költségeket több Azure-figyelési funkció között különböző díjszabási modellekhez.

> [!NOTE]
> Az ebben a cikkben látható árak és költségek csak példaként szolgálnak. 

Azure Monitor naplókat úgy tervezték, hogy a nagyvállalati forrásból származó vagy az Azure-ban üzembe helyezett nagy mennyiségű adat gyűjtését, indexelését és tárolását naponta kialakítsa és támogassa.  Ez lehet egy elsődleges illesztőprogram, a szervezet számára, miközben költséghatékonyságot végső soron az alapul szolgáló illesztőprogram. Ebből a célból fontos tisztában lenni azzal, hogy egy Log Analytics munkaterület díja nem csak a gyűjtött adatok mennyiségétől függ, hanem a kiválasztott csomagtól, és azt is, hogy mennyi ideig kell tárolnia a csatlakoztatott forrásokból generált adatokat.  

Ebben a cikkben áttekintjük, hogyan lehet proaktív módon figyelni a betöltött adatmennyiséget és a tárterület növekedését, valamint korlátokat meghatározni a kapcsolódó költségek szabályozására. 

## <a name="pricing-model"></a>Díjszabási modell

Az Log Analytics alapértelmezett díjszabása az adatmennyiség betöltését és opcionálisan **a hosszú** adatmegőrzést is igénybe véve. Az adatmennyiséget a rendszer a tárolni kívánt adattárolás méretének megfelelően méri. Minden Log Analytics munkaterület külön szolgáltatásként lesz felszámítva, és hozzájárul az Azure-előfizetéshez tartozó számlához. Az adatfeldolgozás mennyisége az alábbi tényezőktől függően jelentős lehet: 

  - Az engedélyezett felügyeleti megoldások száma és konfigurációja (például 
  - Figyelt virtuális gépek száma
  - Az egyes figyelt virtuális gépekről összegyűjtött adatok típusa 
  
Az utólagos elszámolású modellen kívül a Log Analytics **kapacitás-foglalási** csomagokkal rendelkezik, amelyek lehetővé teszik, hogy az utólagos elszámolású díjszabáshoz képest akár 25%-ot is mentsen. A kapacitás foglalásának díjszabása lehetővé teszi, hogy a foglalást 100 GB/nap után vásárolja meg. A foglalási szint feletti használati díjakat az utólagos elszámolású díjszabás szerint számítjuk fel. A kapacitás foglalási szintjeinek 31 napos kötelezettségvállalási időszaka van. A kötelezettségvállalás időtartama alatt magasabb szintű kapacitást biztosító foglalási szintre válthat (amely a 31 napos kötelezettségvállalási időszakot indítja újra), de az utólagos elszámolású vagy az alacsonyabb kapacitású foglalási szintig nem léphet vissza, amíg a kötelezettségvállalási időszakot nem befejeződött. 
[További](https://azure.microsoft.com/pricing/details/monitor/) információ a log Analytics utólagos elszámolású és a kapacitás foglalásának díjszabásáról. 

Az adatmennyiség az összes díjszabási szinten az adatok karakterlánc-ábrázolásával lesz kiszámítva, ahogy a tárolásra készen áll. Az események méretének kiszámítása során az [összes adattípussal közös tulajdonságok](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties) nem szerepelnek, beleértve a `_ResourceId`, a `_ItemId`, a `_IsBillable` és a `_BilledSize`.

Azt is vegye figyelembe, hogy egyes megoldások, például a [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/) és az [Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/)saját díjszabási modellel rendelkeznek. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>A környezet kezelésével kapcsolatos költségek becslése 

Ha még nem használ Azure Monitor naplókat, a [Azure monitor árképzési számológép](https://azure.microsoft.com/pricing/calculator/?service=monitor) használatával megbecsülheti a log Analytics használatának költségeit. Először írja be a "Azure Monitor" kifejezést a keresőmezőbe, és kattintson az eredményül kapott Azure Monitor csempére. Görgessen le az oldalról Azure Monitorre, majd válassza a legördülő menüből a Log Analytics lehetőséget.  Itt megadhatja a virtuális gépek számát és a begyűjteni kívánt GB-nyi adatot. Egy tipikus Azure-beli virtuális gépről általában 1 – 3 GB adathónapot kell betölteni. Ha már kiértékeli Azure Monitor naplókat, saját környezetében is használhatja az adatstatisztikát. A [figyelt virtuális gépek számának](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) és a [munkaterület által betöltött adatmennyiségnek](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)a meghatározását alább találja. 

## <a name="understand-your-usage-and-estimate-costs"></a>A használati és becsült költségek megismerése

Ha most Azure Monitor naplókat használ, könnyen megismerheti, hogy milyen költségek várhatók a legutóbbi használati szokások alapján. Ehhez használja a **log Analytics használati és becsült költségeket** az adatfelhasználás áttekintéséhez és elemzéséhez. Ez azt mutatja, hogy az egyes megoldások milyen mennyiségű adatot gyűjtenek, mennyi adatot tartanak fenn, valamint a költségek becslését a betöltött adatok mennyisége és a benne foglalt mennyiség utáni további megőrzés alapján.

![Használat és becsült költségek](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Az adatok részletesebb megismeréséhez kattintson a **használati és becsült költségek** oldalon található diagramok valamelyikének jobb felső sarkában található ikonra. Most már dolgozhat a lekérdezés további részletek, amely a használat.  

![Naplók megtekintése](media/manage-cost-storage/logs.png)

A **használat és a becsült költségek** lapon áttekintheti az adatmennyiséget a hónapban. Ez magában foglalja a fogadott és a Log Analytics-munkaterület összes adatot.  A lap tetején található **használati** adatok lehetőségre kattintva megtekintheti a használati irányítópultot, amely a forrás, a számítógép és az ajánlat adatmennyiség-trendjeivel kapcsolatos információkat jeleníti meg. Ha szeretné megtekinteni és beállítani a napi korlátot, vagy módosítani szeretné a megőrzési időtartamot, kattintson az **adatkötet-kezelés**elemre.
 
Log Analytics díjak az Azure-elszámolások kerülnek. Az Azure-számlázás részleteit a Azure Portal vagy a [Azure elszámolási portál](https://account.windowsazure.com/Subscriptions)számlázási szakaszában tekintheti meg.  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Log Analytics használatának megtekintése az Azure-számlán 

Az Azure nagyszerű hasznos funkciókat biztosít a [Azure Cost Management + számlázási](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) központban. A "Cost Analysis" funkció például lehetővé teszi az Azure-erőforrások költségeinek megtekintését. Ha erőforrás-típus alapján (a Microsoft. operationalinsights/munkaterületre Log Analytics) szeretne szűrőt hozzáadni, akkor nyomon követheti a töltést.

A használatról további ismereteket kaphat, ha [letölti a használatot a Azure Portal](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). A letöltött számolótáblában naponta megtekintheti az Azure-erőforrásokon (például Log Analytics munkaterületen) való használatot. Ebben az Excel-táblázatban a Log Analytics munkaterületek használatáról az első szűrés a "mérési kategória" oszlopban az "elemzések és elemzések" (az örökölt árképzési szintek által használt) és a "Log Analytics" alapján jelenik meg, majd hozzáad egy szűrőt a "példányhoz AZONOSÍTÓ: "a munkaterület tartalmaz" oszlop. A használat megjelenik a "felhasznált mennyiség" oszlopban, és az egyes bejegyzések egysége a "mértékegység" oszlopban látható.  További részleteket a [Microsoft Azure számla megismeréséhez](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)talál. 

## <a name="changing-pricing-tier"></a>Árképzési réteg módosítása

A munkaterület Log Analytics díjszabási szintjének módosításához 

1. A Azure Portal nyissa meg a **használati és becsült költségeket** a munkaterületről, ahol megjelenik a munkaterület számára elérhető díjszabási szintek listája.

2. Tekintse át az egyes díjszabási szintek becsült költségeit. Ez a becslés az elmúlt 31 nap használaton alapul, ezért ez a költségbecslés az utolsó 31 napra támaszkodik, amely jellemző a szokásos használatra. Az alábbi példában láthatja, hogy az elmúlt 31 nap adatmintái alapján ez a munkaterület a 100 GB/nap kapacitás foglalási szintjéhez képest (#1) alacsonyabb, mint az utólagos elszámolású csomagnál (#2).  

    ![Árképzési szintek](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. A becsült költségeknek az utolsó 31 nap használaton alapuló áttekintése után, ha úgy dönt, hogy megváltoztatja az árképzési szintet, kattintson a **kiválasztás**elemre.  

[Az árképzési szintet Azure Resource Manager használatával is beállíthatja](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) az `sku` paraméterrel (`pricingTier` a Azure Resource Manager sablonban). 

## <a name="legacy-pricing-tiers"></a>Örökölt árképzési szintek

Azok az előfizetések, amelyek Log Analytics munkaterülettel vagy Application Insights-erőforrással rendelkeztek, 2018. április 2. előtt, vagy egy, a 2019. február 1. előtt elindított Nagyvállalati Szerződéshoz kapcsolódnak, továbbra is hozzáférhetnek a korábbi díjszabási szintek használatához: **ingyenes**, **különálló (GB-onként)** és **/node (OMS)** .  Az ingyenes díjszabási szinten lévő munkaterületek napi adatfeldolgozása a 500 MB-ra korlátozódik (kivéve a Azure Security Center által gyűjtött biztonsági adattípusokat), és az adatmegőrzés legfeljebb 7 napig tart. Az ingyenes díjszabási csomag kizárólag értékelési célokra szolgál. Az önálló vagy a csomópontok díjszabási szintjein lévő munkaterületek felhasználó által konfigurálható megőrzése legfeljebb 2 év lehet. 

A 2016. április előtt létrehozott munkaterületek az eredeti **standard** és **prémium** szintű díjszabáshoz is hozzáférnek, amely a 30 és 365 nap rögzített adatmegőrzéssel rendelkezik. Nem hozhatók létre új munkaterületek a **standard** vagy a **prémium** szintű díjszabásban, és ha a munkaterületet kihelyezték ezekből a rétegekből, nem helyezhető vissza. 

Az árképzési szintek korlátozásait [itt](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)találja.

> [!NOTE]
> A OMS E1 Suite, OMS E2 Suite vagy OMS-bővítmény a System Center rendszerhez szolgáltatásból származó jogosultságok használatához válassza az Log Analytics a *csomópontok* díjszabási szintjein.

## <a name="change-the-data-retention-period"></a>Módosítsa az Adatmegőrzés időtartama

Az alábbi lépéseket adatért által a munkaterületen milyen hosszú log konfigurálását ismertetik.

### <a name="default-retention"></a>Alapértelmezett megőrzés

A munkaterület alapértelmezett megőrzésének beállításához 
 
1. A Azure Portal a munkaterületen válassza a **használat és becsült költségek** lehetőséget a bal oldali panelen.
2. A **Felhasználás és becsült költségek** oldalon kattintson az oldal tetején található **Adatmennyiség-kezelés** elemre.
3. A panelen mozgassa a csúszkát a napok számának növeléséhez vagy csökkentéséhez, majd kattintson **az OK**gombra.  Ha az *ingyenes* szintet választja, nem fogja tudni módosítani az adatmegőrzési időszakot, és frissítenie kell a fizetős szintre a beállítás szabályozása érdekében.

    ![Munkaterület adatmegőrzési beállításának módosítása](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
A megőrzés a `retentionInDays` paraméter használatával is [beállítható Azure Resource Manageron keresztül](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) . Emellett, ha az adatmegőrzést 30 napra állítja be, a `immediatePurgeDataOn30Days` paraméter használatával azonnal törölheti a régebbi adatok törlését, ami a megfelelőséggel kapcsolatos forgatókönyvek esetében hasznos lehet. Ez a funkció csak Azure Resource Manageron keresztül érhető el. 

Két adattípus – `Usage` és `AzureActivity` – alapértelmezés szerint 90 napig őrzi meg a rendszer, és a 90 nap megtartására nem számítunk fel díjat. Ezek az adattípusok az adatfeldolgozási díjaktól is mentesek. 

### <a name="retention-by-data-type"></a>Megőrzés adattípus szerint

Az egyes adattípusok esetében más adatmegőrzési beállításokat is meg lehet adni. Minden adattípus a munkaterület alerőforrása. A SecurityEvent tábla például a következő módon kezelhető [Azure Resource Managerban](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) :

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Vegye figyelembe, hogy az adattípus (tábla) megkülönbözteti a kis-és nagybetűket.  Ha egy adott adattípus (ebben a példában a SecurityEvent) adattípusának aktuális adatmegőrzési beállításait szeretné lekérni, használja a következőt:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

Ha a munkaterület összes adattípusa esetében az adattípushoz tartozó adatmegőrzési beállításokat szeretné lekérni, egyszerűen hagyja ki az adott adattípust, például:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Ha egy adott adattípus (ebben a példában SecurityEvent) megőrzését 730 napra szeretné beállítani, tegye a következőt:

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

A `Usage` és `AzureActivity` adattípusok nem állíthatók be egyéni megőrzéssel. Ezek az alapértelmezett munkaterület-megőrzési vagy a 90 napos maximális értékkel lesznek végrehajtva. 

Egy nagyszerű eszköz, amellyel közvetlenül csatlakozhat Azure Resource Managerhoz az adattípusok megőrzésének beállításához az OSS-eszköz [ARMclient](https://github.com/projectkudu/ARMClient).  További információ a cikkek ARMclient: [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) és [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Íme egy példa a ARMClient használatával, amely a SecurityEvent-adatok 730 napos megőrzését állítja be:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!NOTE]
> Az adatmegőrzési költségek csökkentése érdekében az egyes adattípusok megőrzésének beállítása felhasználható.  A 2019 októberi (ha ez a funkció megjelent) óta gyűjtött adatok esetében az egyes adattípusok megőrzésének csökkentése csökkentheti az adatmegőrzési költségeket az idő múlásával.  A korábban összegyűjtött adatok esetében az egyes típusok alacsonyabb megőrzésének beállítása nem befolyásolja a megőrzési költségeket.  

## <a name="manage-your-maximum-daily-data-volume"></a>A maximális napi adatmennyiség kezelése

Konfigurálja a maximális napi adatmennyiséget, és korlátozhatja a napi bevitelt a munkaterületen, de körültekintően járjon el, mert a cél nem lehet eléri a napi korlátot.  Ellenkező esetben az a nap fennmaradó részében, ami hatással lehet más Azure-szolgáltatások és megoldások, amelynek funkció függhet, naprakész adatok legyenek elérhetők a munkaterület az adatok elvesznek.  Ennek eredményeképpen figyelje meg, és fogadjon megítélnie riasztást küld, ha is hatással van az informatikai szolgáltatásokat támogató erőforrásokban egészségügyi feltételeit.  A napi korlát arra szolgál, hogy kezelje az adatmennyiség váratlan növekedését a felügyelt erőforrásokból, és a korláton belül maradjon, vagy ha korlátozni szeretné a munkaterülethez tartozó nem tervezett díjakat.  

A napi korlátot, a gyűjtemény számlázható adattípusok leállítja az a nap hátralevő. A kijelölt Log Analytics munkaterülethez tartozó oldal tetején megjelenik egy figyelmeztető szalagcím, és a **LogManagement** kategóriában a *művelet* táblába egy műveleti esemény érkezik. Az adatgyűjtés a *napi korlát*alatt megadott alaphelyzetbe állítás időpontját követően fog folytatódni. Azt javasoljuk, hogy arra az esetre, ha a napi korlátot elérte a konfigurált Ez a művelet események alapján riasztási szabály meghatározása. 

> [!NOTE]
> A napi korlát nem állítja le az adatgyűjtést Azure Security Centerból, kivéve azokat a munkaterületeket, amelyekben a Azure Security Center a 2017. június 19. előtt volt telepítve. 

> [!NOTE]
> A napi korlát alkalmazása során felmerülő késés azt jelentheti, hogy a kupakot nem a megadott napi korlát szintjének megfelelően alkalmazza a rendszer. 

### <a name="identify-what-daily-data-limit-to-define"></a>Milyen napi korlátot meghatározásához azonosítása

Tekintse át [log Analytics használati és becsült költségét](usage-estimated-costs.md) , és Ismerje meg az adatfeldolgozási trendet, valamint azt, hogy mi a napi mennyiségi korlát. Kell tekinteni, körültekintően, mivel nem lehet az erőforrások figyeléséhez, a korlát elérése után. 

### <a name="set-the-daily-cap"></a>A napi korlát beállítása

A következő lépések azt ismertetik, hogyan konfigurálható egy korlát a Log Analytics munkaterület által naponta betöltött adatmennyiség kezelésére.  

1. A munkaterületen válassza a **Felhasználás és becsült költségek** lehetőséget a bal oldali panelen.
2. A kiválasztott munkaterület **használati és becsült költségek** lapján kattintson a lap tetején található **adatkötet-kezelés** elemre. 
3. A napi korlát alapértelmezés szerint **ki van kapcsolva** – az engedélyezéshez kattintson **a** be lehetőségre, majd állítsa be az adatmennyiség korlátját GB/nap értékre.

    ![Az adatkorlát konfigurálása Log Analytics](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Riasztás, ha elérte a napi korlátot

Hogy jelen visual köteg az Azure Portalon, ha az adatok korlát küszöbértéket, amíg ez a viselkedés nem feltétlenül igazítás azonnali figyelmet igénylő működési problémák kezelése.  Az Azure Monitor riasztási értesítés fogadására, létrehozhat egy új riasztási szabály.  További információt a [riasztások létrehozása, megtekintése és kezelése](alerts-metric.md)című témakörben talál.

Az első lépésekhez, az alábbiakban a riasztás az ajánlott beállításokat:

- Cél: Válassza ki a Log Analytics-erőforrás
- Feltételek: 
   - Jel neve: egyéni naplók keresése
   - Keresési lekérdezés: a művelet |} Ha részletes rendelkezik-e "termékváltozatként használja:
   - Alapján: eredmények száma
   - Feltétel: Nagyobb, mint
   - Küszöbérték: 0
   - Időszak: 5 (perc)
   - Gyakoriság: 5 (perc)
- Riasztási szabály neve: elérte a napi korlátot
- Súlyosság: Figyelmeztetés (Sev 1)

Riasztás van definiálva, és a eléri a korlátot, riasztás akkor aktiválódik, és hajtja végre a választ a műveletcsoport meghatározott. E-mailben és SMS-ben értesítheti a csapatot, vagy automatizálhatja a műveleteket webhookok, Automation runbookok vagy [külső ITSM-megoldásokkal való integráció](itsmc-overview.md#create-itsm-work-items-from-azure-alerts)révén. 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>A vártnál magasabb szintű használatot okozó hibák elhárítása

A magasabb szintű használatot a következők okozhatják:
- További csomópontok, mint a várt adatok küldése Log Analytics munkaterületre
- A vártnál több információ Log Analytics munkaterületre (például egy új megoldás használatának megkezdése vagy egy meglévő megoldás konfigurációjának megváltozása miatt)

## <a name="understanding-nodes-sending-data"></a>Az adatokat küldő csomópontok ismertetése

Ha meg szeretné ismerni, hogy hány csomópontot jelentettek a szívverések az ügynöktől az elmúlt hónap minden napján, használja a következőt:

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
Az adatokat küldő csomópontok száma a következő használatával határozható meg: 

```kusto
union withsource = tt * 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

A következő lekérdezéssel kérheti le az adatokat küldő csomópontok listáját (és az egyesek által küldött adatok mennyiségét):

```kusto
union withsource = tt * 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

> [!NOTE]
> Ezekkel a `union withsource = tt *` lekérdezésekkel takarékosan végezheti el az adattípusok megkeresését. Ez a lekérdezés a számítógép adatainak a használati adattípussal való lekérdezésének régi módját váltja fel.  

## <a name="understanding-ingested-data-volume"></a>A betöltött adatmennyiség ismertetése

A **használat és a becsült költségek** lapon az *adatfeldolgozás egy adott megoldási* diagramon megjeleníti az elküldött adatok teljes mennyiségét, valamint azt, hogy az egyes megoldások milyen mértékben küldik el az adatokat. Ez lehetővé teszi, hogy határozza meg a trendeket, például hogy a teljes adathasználat (vagy egy adott megoldás használatának) nő, állandó vagy csökken van hátra. 

### <a name="data-volume-by-solution"></a>Adatmennyiség megoldásonként

A számlázható adatmennyiség megoldás általi megtekintésére használt lekérdezés

```kusto
Usage 
| where TimeGenerated > startofday(ago(31d))
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(TimeGenerated, 1d), Solution | render barchart
```

Vegye figyelembe, hogy a záradék `where IsBillable = true` kiszűri az adattípusokat bizonyos olyan megoldásokból, amelyekhez nincs betöltési díj. 

### <a name="data-volume-by-type"></a>Adatmennyiség típus szerint

További részletezéssel tekintheti meg az adattípussal kapcsolatos adattrendeket:

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where TimeGenerated > startofday(ago(31d))
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(TimeGenerated, 1d), DataType | render barchart
```

Vagy megtekintheti az előző hónapra vonatkozó táblát megoldás és típus szerint.

```kusto
Usage 
| where TimeGenerated > startofday(ago(31d))
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Adatmennyiség számítógépenként

A `Usage` adattípus nem tartalmaz információkat a teljes szinten. Ha szeretné megtekinteni a betöltött adat **méretét** a számítógépen, használja a `_BilledSize` [tulajdonságot](log-standard-properties.md#_billedsize), amely a méretet adja meg bájtban:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

A `_IsBillable` [tulajdonság](log-standard-properties.md#_isbillable) azt határozza meg, hogy a betöltött adatok díjkötelesek-e.

A számítógépeken betöltött számlázható események **számának** megtekintéséhez használja a következőt: 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  | sort by eventCount nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Adatmennyiség Azure-erőforrás, erőforráscsoport vagy előfizetés alapján

Az Azure-ban üzemeltetett csomópontokból származó adatok esetén a __számítógépeken__tárolt adatok **mérete** megszerezhető, a _ResourceId [tulajdonságot](log-standard-properties.md#_resourceid)használva, amely az erőforrás teljes elérési útját biztosítja:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Az Azure-ban üzemeltetett csomópontokból származó adatok esetében az __Azure-előfizetések által__beolvasott adatok **mérete** megszerezhető, és a `_ResourceId` tulajdonságot a következő módon elemezheti:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize BillableDataBytes = sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

A `subscriptionId` módosítása `resourceGroup` megjeleníti a számlázható betöltött adatmennyiséget az Azure-erőforráscsoport alapján. 

> [!NOTE]
> A használati adattípus egyes mezői, miközben még mindig a séma része, elavultak, és az értékek már nem lesznek feltöltve. Ezek a **számítógépek** , valamint a betöltéssel kapcsolatos mezők (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** és **AverageProcessingTimeMs**).

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
| Rendszernapló:                     | Módosítsa a [rendszernapló konfigurációját](data-sources-syslog.md): <br> – Csökkentse a gyűjtésben részt vevő létesítmények számát <br> – Csak a szükséges eseményszinteket gyűjtse. Ne gyűjtsön például *Tájékoztatás* vagy *Hibakeresés* szintű eseményeket |
| AzureDiagnostics           | Az erőforrásnapló-gyűjtés módosítása a következőre: <br> – Csökkentse a Log Analytics számára naplókat küldő erőforrások számát <br> – Csak a szükséges naplókat gyűjtse |
| Megoldásadatok olyan számítógépekről, amelyeknek nincs szükségük a megoldásra | A [megoldáscélzási](../insights/solution-targeting.md) funkcióval megadhatja, hogy csak a szükséges számítógépcsoportoktól gyűjtsön adatokat. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Csomópontok beolvasása az egyes csomópontok díjszabási szintjei szerint

Azon számítógépek listájának lekéréséhez, amelyek csomópontként lesznek kiszámlázva, ha a munkaterület a csomópontok közötti örökölt díjszabási szinten található, keresse meg a **számlázott adattípusokat** küldő csomópontokat (egyes adattípusok ingyenesek). Ehhez használja a `_IsBillable` [tulajdonságot](log-standard-properties.md#_isbillable) , és használja a teljes tartománynév bal szélső mezőjét. Ez visszaadja a számlázott adatokat tartalmazó számítógépek számát (amely a csomópontok számlálásának és számlázásának részletessége):

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>Biztonsági és automatizálási csomópontok számának beolvasása

Ha a "per node (OMS)" díjszabási szinten van, akkor a használt csomópontok és megoldások száma alapján számítjuk fel a díjat, az elemzések és az elemzési csomópontok száma a **használat és a becsült költség** lapon jelenik meg a táblázatban.  

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
   - **A riasztás logikája** az *eredmények* és a **feltétel** **alapján** *meghaladja* a *0* **küszöbértéket**
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
   - **A riasztás logikája** az *eredmények* és a **feltétel** **alapján** *meghaladja* a *0* **küszöbértéket**
   - Az **Időszak** értékét állítsa *180* percre, a **Riasztási időköz** pedig legyen *60* perc, mivel a használati adatok csak óránként frissülnek.
- **Határozza meg a riasztás részleteit** az alábbiak megadásával:
   - A **Név** legyen *Több mint 100 GB várható adatmennyiség 24 órán belül*
   - A **Súlyosság** legyen *Figyelmeztetés*

Megadhat egy meglévő [műveletcsoportot](action-groups.md), illetve létrehozhat egy újat, hogy értesítést kapjon, amikor egy naplóriasztás megfelel a feltételeknek.

Riasztás fogadásakor kövesse a következő szakaszban leírt lépéseket a vártnál magasabb szintű használatot okozó hibák elhárításához.

## <a name="data-transfer-charges-using-log-analytics"></a>Adatátviteli díjak az Log Analytics használatával

Az adatok Log Analytics való küldése adatsávszélességi díjat eredményezhet. Az [Azure sávszélesség-díjszabási oldalán](https://azure.microsoft.com/pricing/details/bandwidth/)leírtak szerint a két régióban található Azure-szolgáltatások közötti adatforgalom a normál díjszabás szerint kifelé irányuló kimenő adatforgalom. A bejövő adatforgalom ingyenes. Ez a díj azonban nagyon kicsi (néhány%) az adatfeldolgozás Log Analytics költségeihez képest. Ennek következtében a Log Analytics költségeinek szabályozása a betöltött adatmennyiségre összpontosíthat, és útmutatást nyújtunk ennek [megértéséhez.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Hibaelhárítás, hogy miért Log Analytics már nem gyűjti az adatgyűjtést

Ha az örökölt ingyenes díjszabási szinten van, és naponta több mint 500 MB-nyi adat lett elküldve, az adatgyűjtés a nap hátralevő részében leáll. Napi korlát elérése a gyakori oka, hogy a Log Analytics leállítja az adatgyűjtést, vagy adatokat úgy tűnik, hogy hiányzik.  A log Analytics művelet típusú eseményt hoz létre, amikor adatgyűjtés indítása és leállítása. Futtassa a következő lekérdezést a keresésben, és ellenőrizze, hogy eléri-e a napi korlátot és a hiányzó adatértékeket: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Amikor az adatgyűjtés leáll, a OperationStatus **figyelmeztetést**jelenít meg. Az adatgyűjtés megkezdésekor a OperationStatus **sikeres**. A következő táblázat ismerteti az oka, hogy leállítja az adatgyűjtést, és folytathatja az adatgyűjtést javasolt művelet:  

|OK gyűjtemény leáll| Megoldás| 
|-----------------------|---------|
|Elérte az örökölt ingyenes díjszabási csomag napi korlátját |Várja meg, amíg a gyűjtemény indítja újra automatikusan a következő napon, vagy módosítása fizetős tarifacsomagra.|
|Elérte a munkaterület napi korlátját|Várjon, amíg a gyűjtemény automatikusan újraindul, vagy növelje a napi adatmennyiség kezelése című témakörben leírt napi adatmennyiség korlátját. A napi korlát alaphelyzetbe állításának ideje az **adatmennyiség-kezelés** oldalon látható. |
|Azure-előfizetés miatt felfüggesztett állapotban van:<br> Ingyenes próbaidőszak véget ért<br> Az Azure pass lejárt<br> Havonta költségkeret elérése (például az MSDN vagy a Visual Studio előfizetési)|Fizetős előfizetéssé alakítani<br> Távolítsa el a korlátot, vagy várjon, amíg a korlát alaphelyzetbe állítása|

Ha értesítést szeretne kapni az adatgyűjtés leállításakor, kövesse a *napi adatsapka* -riasztás létrehozása című témakörben leírt lépéseket az adatgyűjtés leállításakor. A következő témakörben ismertetett lépések végrehajtásával konfigurálhat egy e-mailt, webhookot vagy runbook műveletet a riasztási szabályhoz: [műveleti csoport létrehozása](action-groups.md) . 

## <a name="limits-summary"></a>Korlátok összegzése

Vannak további Log Analytics korlátok, amelyek némelyike a Log Analytics díjszabási szintjétől függ. Ezeket [itt](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)dokumentáljuk.


## <a name="next-steps"></a>További lépések

- A keresési nyelv használatának megismeréséhez tekintse meg a [naplóban megkeresett Azure monitor naplókat](../log-query/log-query-overview.md) . A keresési lekérdezésekkel további elemzéseket végezhet a használati adatokon.
- Az [új naplózási riasztás létrehozásával kapcsolatos](alerts-metric.md) szakaszban leírt lépéseket követve beállíthatja, hogy értesítést kapjon, ha teljesül egy keresési feltétel.
- A [megoldáscélzási](../insights/solution-targeting.md) funkcióval megadhatja, hogy csak a szükséges számítógépcsoportoktól gyűjtsön adatokat.
- Egy hatékony esemény-gyűjtési házirend konfigurálásához tekintse át [Azure Security Center szűrési házirendet](../../security-center/security-center-enable-data-collection.md).
- Módosítsa a [teljesítményszámlálók konfigurációját](data-sources-performance-counters.md).
- Az eseménygyűjtési beállítások módosításához tekintse meg az [eseménynaplók konfigurációját](data-sources-windows-events.md) leíró szakaszt.
- A rendszernapló-gyűjtési beállítások módosításához tekintse meg a [rendszernaplók konfigurációját](data-sources-syslog.md) leíró szakaszt.