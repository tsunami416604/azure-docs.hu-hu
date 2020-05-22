---
title: Azure Monitor naplók használatának és költségeinek kezelése
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
ms.date: 05/21/2020
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 6e6be4cd0f8053d356183a75c5a012dee0bd8c68
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83771315"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>A használat és a költségek kezelése Azure Monitor naplókkal

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan értelmezhető és szabályozható a Azure Monitor naplók költségei. A kapcsolódó cikkek, a [monitorozási használat és a becsült költségek](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) azt írják le, hogyan lehet megtekinteni a használati és becsült költségeket több Azure-figyelési funkció között különböző díjszabási modellekhez. Az ebben a cikkben látható árak és költségek csak példaként szolgálnak. 

Azure Monitor naplókat úgy tervezték, hogy a nagyvállalati forrásból származó vagy az Azure-ban üzembe helyezett nagy mennyiségű adat gyűjtését, indexelését és tárolását naponta kialakítsa és támogassa.  Habár ez lehet a szervezet elsődleges illesztőprogramja, a költséghatékonyság végül a mögöttes illesztőprogram. Ebből a célból fontos tisztában lenni azzal, hogy egy Log Analytics munkaterület díja nem csak a gyűjtött adatok mennyiségétől függ, hanem a kiválasztott csomagtól, és azt is, hogy mennyi ideig kell tárolnia a csatlakoztatott forrásokból generált adatokat.  

Ebben a cikkben áttekintjük, hogyan lehet proaktív módon figyelni a betöltött adatmennyiséget és a tárterület növekedését, valamint korlátokat meghatározni a kapcsolódó költségek szabályozására. 

## <a name="pricing-model"></a>Díjszabási modell

Az Log Analytics alapértelmezett díjszabása az adatmennyiség betöltését és opcionálisan **a hosszú** adatmegőrzést is igénybe véve. Az adatmennyiséget a rendszer a tárolni kívánt adattárolás méretének megfelelően méri. Minden Log Analytics munkaterület külön szolgáltatásként lesz felszámítva, és hozzájárul az Azure-előfizetéshez tartozó számlához. Az adatfeldolgozás mennyisége az alábbi tényezőktől függően jelentős lehet: 

  - Az engedélyezett felügyeleti megoldások száma és a konfigurációjuk
  - Figyelt virtuális gépek száma
  - Az egyes figyelt virtuális gépekről összegyűjtött adatok típusa 
  
Az utólagos elszámolású modellen kívül a Log Analytics **kapacitás-foglalási** csomagokkal rendelkezik, amelyek lehetővé teszik, hogy az utólagos elszámolású díjszabáshoz képest akár 25%-ot is mentsen. A kapacitás foglalásának díjszabása lehetővé teszi, hogy a foglalást 100 GB/nap után vásárolja meg. A foglalási szint feletti használati díjakat az utólagos elszámolású díjszabás szerint számítjuk fel. A kapacitás foglalási szintjeinek 31 napos kötelezettségvállalási időszaka van. A kötelezettségvállalás időtartama alatt magasabb szintű kapacitást biztosító foglalási szintre válthat (amely a 31 napos kötelezettségvállalási időszakot újraindítja), de az utólagos elszámolású vagy az alacsonyabb kapacitású foglalási szintig nem léphet vissza a kötelezettségvállalási időszak végéig. A kapacitás foglalási szintjeinek számlázása napi rendszerességgel történik. [További](https://azure.microsoft.com/pricing/details/monitor/) információ a log Analytics utólagos elszámolású és a kapacitás foglalásának díjszabásáról. 

Az adatmennyiség az összes díjszabási szinten az adatok karakterlánc-ábrázolásával lesz kiszámítva, ahogy a tárolásra készen áll. Az [összes adattípussal közös tulajdonságok](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties) nem szerepelnek az események méretének kiszámításakor, beleértve a következőket:, `_ResourceId` `_ItemId` `_IsBillable` és `_BilledSize` .

Azt is vegye figyelembe, hogy egyes megoldások, például az [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/), az [Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/) és a [Configuration Management](https://azure.microsoft.com/pricing/details/automation/) saját díjszabási modellel rendelkeznek. 

### <a name="log-analytics-dedicated-clusters"></a>Dedikált fürtök Log Analytics

Log Analytics dedikált fürtök egyetlen felügyelt Azure Adatkezelő-fürtbe tartozó munkaterületek gyűjteményei, amelyek olyan speciális forgatókönyveket támogatnak, mint például az [ügyfél által felügyelt kulcsok](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys).  Log Analytics dedikált fürtök csak a kapacitás-foglalás díjszabási modelljét támogatják, amely 1000 GB/nap, az utólagos elszámolású díjszabáshoz képest 25%-os kedvezménnyel. A foglalási szint feletti használati díjakat az utólagos elszámolású díjszabás szerint számítjuk fel. A fürt kapacitásának foglalása a foglalási szint növelése után 31 napos kötelezettségvállalási időszakot tartalmaz. A kötelezettségvállalási időszak alatt a kapacitás foglalási szintje nem csökkenthető, de bármikor megnövelhető. További információ a [log Analytics-fürtök létrehozásáról](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#create-cluster-resource) és [a munkaterületek társításáról](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#workspace-association-to-cluster-resource).  

A fürt kapacitásának foglalási szintje a (z) paraméterrel a (z) programozott módon Azure Resource Manager használatával van konfigurálva `Capacity` `Sku` . A a `Capacity` GB egységben van megadva, és 1000 GB/nap vagy több értékkel rendelkezhet a 100 GB/nap növekményekben. [Itt](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#create-cluster-resource)részletesen tájékozódhat. Ha a fürtnek 2000 GB/nap feletti foglalásra van szüksége, lépjen kapcsolatba velünk a következő címen: [LAIngestionRate@microsoft.com](mailto:LAIngestionRate@microsoft.com) .

Mivel a betöltött adatmennyiség számlázása a fürt szintjén történik, a fürthöz társított munkaterületek már nem rendelkeznek díjszabási szinttel. A rendszer összesíti a fürthöz társított egyes munkaterületekről betöltött adatmennyiségeket a fürt napi számlájának kiszámításához. Vegye figyelembe, hogy a [Azure Security Center](https://docs.microsoft.com/azure/security-center/) -tól származó csomópont-hozzárendelések a munkaterület szintjén lesznek alkalmazva az összesített adatoknak a fürt összes munkaterülete közötti összesítése előtt. Az adatmegőrzés továbbra is a munkaterület szintjén kerül számlázásra. Vegye figyelembe, hogy a fürt számlázása a fürt létrehozásakor kezdődik, függetlenül attól, hogy a munkaterületek hozzá lettek-e rendelve a fürthöz. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>A környezet kezelésével kapcsolatos költségek becslése 

Ha még nem használ Azure Monitor naplókat, a [Azure monitor árképzési számológép](https://azure.microsoft.com/pricing/calculator/?service=monitor) használatával megbecsülheti a log Analytics használatának költségeit. Először írja be a "Azure Monitor" kifejezést a keresőmezőbe, és kattintson az eredményül kapott Azure Monitor csempére. Görgessen le az oldalról Azure Monitorre, majd válassza a legördülő menüből a Log Analytics lehetőséget.  Itt megadhatja a virtuális gépek számát és a begyűjteni kívánt GB-nyi adatot. Egy tipikus Azure-beli virtuális gépről általában 1 – 3 GB adathónapot kell betölteni. Ha már kiértékeli Azure Monitor naplókat, saját környezetében is használhatja az adatstatisztikát. A [figyelt virtuális gépek számának](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) és a [munkaterület által betöltött adatmennyiségnek](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)a meghatározását alább találja. 

## <a name="understand-your-usage-and-estimate-costs"></a>A használati és becsült költségek megismerése

Ha most Azure Monitor naplókat használ, könnyen megismerheti, hogy milyen költségek várhatók a legutóbbi használati szokások alapján. Ehhez használja a **log Analytics használati és becsült költségeket** az adatfelhasználás áttekintéséhez és elemzéséhez. Ez azt mutatja, hogy az egyes megoldások milyen mennyiségű adatot gyűjtenek, mennyi adatot tartanak fenn, valamint a költségek becslését a betöltött adatok mennyisége és a benne foglalt mennyiség utáni további megőrzés alapján.

![Használat és becsült költségek](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Az adatok részletesebb megismeréséhez kattintson a **használati és becsült költségek** oldalon található diagramok valamelyikének jobb felső sarkában található ikonra. Ezzel a lekérdezéssel a használat további részleteit is megismerheti.  

![Naplók nézet](media/manage-cost-storage/logs.png)

A **használat és a becsült költségek** lapon áttekintheti az adatmennyiséget a hónapban. Ebbe beletartozik az Log Analytics munkaterületen fogadott és megőrzött összes adat.  A lap tetején található **használati** adatok lehetőségre kattintva megtekintheti a használati irányítópultot, amely a forrás, a számítógép és az ajánlat adatmennyiség-trendjeivel kapcsolatos információkat jeleníti meg. Ha szeretné megtekinteni és beállítani a napi korlátot, vagy módosítani szeretné a megőrzési időtartamot, kattintson az **adatkötet-kezelés**elemre.
 
Log Analytics díjak hozzáadódnak az Azure-számlához. Az Azure-számlázás részleteit a Azure Portal vagy a [Azure elszámolási portál](https://account.windowsazure.com/Subscriptions)számlázási szakaszában tekintheti meg.  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Log Analytics használatának megtekintése az Azure-számlán 

Az Azure nagyszerű hasznos funkciókat biztosít a [Azure Cost Management + számlázási](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) központban. A "Cost Analysis" funkció például lehetővé teszi az Azure-erőforrások költségeinek megtekintését. Először vegyen fel egy szűrőt "erőforrástípus" (a Microsoft. operationalinsights/munkaterületre Log Analytics és a Microsoft. operationalinsights/munkaterület Log Analytics fürtök esetében) lehetővé teszi, hogy nyomon kövesse a Log Analytics költeni. Ezután a "csoportosítás" lehetőségnél válassza a "mérési kategória" vagy a "mérőszám" lehetőséget.  Vegye figyelembe, hogy az egyéb szolgáltatások, például a Azure Security Center és az Azure Sentinel is számlázzák a használatot Log Analytics munkaterület-erőforrásokkal szemben. Ha meg szeretné tekinteni a hozzárendelést a szolgáltatásnév számára, akkor a diagram helyett a táblázat nézetet is kiválaszthatja. 

A használatról további ismereteket kaphat, ha [letölti a használatot a Azure Portal](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). A letöltött számolótáblában naponta megtekintheti az Azure-erőforrásokon (például Log Analytics munkaterületen) való használatot. Ebben az Excel-számolótáblában a Log Analytics-munkaterületek használatáról az első szűrés a "kategória kategóriája" oszlopban, hogy megjelenjen a "Log Analytics", "megállapítások és elemzések (az örökölt árképzési szintek némelyike által használt) és a" Azure Monitor "(a kapacitás foglalásának díjszabása alapján), majd egy olyan szűrőt ad hozzá a" példány-azonosító "oszlophoz, amely a" munkaterület tartalmaz "vagy a" fürt tartalmaz "(az utóbbi Log Analytics-fürt használatát tartalmazza). A használat megjelenik a "felhasznált mennyiség" oszlopban, és az egyes bejegyzések egysége a "mértékegység" oszlopban látható.  További részleteket a [Microsoft Azure számla megismeréséhez](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)talál. 

## <a name="changing-pricing-tier"></a>Árképzési réteg módosítása

A munkaterület Log Analytics díjszabási szintjének módosításához 

1. A Azure Portal nyissa meg a **használati és becsült költségeket** a munkaterületről, ahol megjelenik a munkaterület számára elérhető díjszabási szintek listája.

2. Tekintse át az egyes díjszabási szintek becsült költségeit. Ez a becslés az elmúlt 31 nap használaton alapul, ezért ez a költségbecslés az utolsó 31 napra támaszkodik, amely jellemző a szokásos használatra. Az alábbi példában láthatja, hogy az elmúlt 31 nap adatmintái alapján ez a munkaterület a 100 GB/nap kapacitás foglalási szintjéhez képest (#1) alacsonyabb, mint az utólagos elszámolású csomagnál (#2).  

    ![Árképzési szintek](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. A becsült költségeknek az utolsó 31 nap használaton alapuló áttekintése után, ha úgy dönt, hogy megváltoztatja az árképzési szintet, kattintson a **kiválasztás**elemre.  

[Az árképzési szintet Azure Resource Manager használatával is beállíthatja](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) a `sku` ( `pricingTier` Azure Resource Manager sablonban) paraméterrel. 

## <a name="legacy-pricing-tiers"></a>Örökölt árképzési szintek

Azok az előfizetések, amelyek Log Analytics munkaterülettel vagy Application Insights-erőforrással rendelkeztek, 2018. április 2. előtt, vagy egy, a 2019. február 1. előtt elindított Nagyvállalati Szerződéshoz kapcsolódnak, továbbra is hozzáférhetnek a korábbi díjszabási szintek használatához: **ingyenes**, **különálló (GB-onként)** és **/node (OMS)**.  Az ingyenes díjszabási szinten lévő munkaterületek napi adatfeldolgozása a 500 MB-ra korlátozódik (kivéve a [Azure Security Center](https://docs.microsoft.com/azure/security-center/)által gyűjtött biztonsági adattípusokat), és az adatmegőrzés legfeljebb 7 napig tart. Az ingyenes díjszabási csomag kizárólag értékelési célokra szolgál. Az önálló vagy a csomópontok díjszabási szintjein lévő munkaterületek esetében a felhasználó által konfigurálható megőrzési idő 30 – 730 nap.

A/csomópontok díjszabási szintjei a figyelt virtuális gépeken (csomópontokon) óránkénti részletességgel jelennek meg. Minden figyelt csomópont esetében a munkaterület 500 MB adatmennyiséget foglal le naponta, amely nem számlázható. Ez a foglalás a munkaterület szintjén összesítve történik. Az összesített napi adatmennyiség fölött betöltött adatokat GB-onként számítjuk fel adatmennyiségként. Vegye figyelembe, hogy a számlán a szolgáltatás Log Analytics használat **Insight and Analytics** lesz, ha a munkaterület a csomópontok közötti díjszabási szinten van. 

> [!TIP]
> Ha a munkaterület a **csomópontok** díjszabási szintjéhez fér hozzá, de azt szeretné tudni, hogy az utólagos elszámolású szinten olcsóbb-e, akkor [az alábbi lekérdezéssel](#evaluating-the-legacy-per-node-pricing-tier) egyszerűen kérhet javaslatot. 

A 2016. április előtt létrehozott munkaterületek az eredeti **standard** és **prémium** szintű díjszabáshoz is hozzáférnek, amely a 30 és 365 nap rögzített adatmegőrzéssel rendelkezik. Nem hozhatók létre új munkaterületek a **standard** vagy a **prémium** szintű díjszabásban, és ha a munkaterületet kihelyezték ezekből a rétegekből, nem helyezhető vissza.

Vannak olyan viselkedések is, amelyek az örökölt Log Analytics rétegek használata és a használat számlázása [Azure Security Center](https://docs.microsoft.com/azure/security-center/). 

1. Ha a munkaterület az örökölt standard vagy prémium szinten található, Azure Security Center számlázása csak Log Analytics adatfeldolgozás esetén történik, nem pedig csomópont.
2. Ha a munkaterület a csomópontok közötti örökölt, akkor a Azure Security Center az aktuális [Azure Security Center node-alapú díjszabási modell](https://azure.microsoft.com/pricing/details/security-center/)alapján számítjuk fel. 
3. Más díjszabási csomagokban (beleértve a kapacitás-foglalásokat is), ha a Azure Security Center 2017. június 19. előtt engedélyezték, Azure Security Center számlázása csak Log Analytics adatfeldolgozás esetén történik. Ellenkező esetben a Azure Security Center a jelenlegi Azure Security Center node-alapú díjszabási modell alapján számítjuk fel.

Az árképzési szintek korlátozásait [itt](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)találja.

> [!NOTE]
> A OMS E1 Suite, OMS E2 Suite vagy OMS-bővítmény a System Center rendszerhez szolgáltatásból származó jogosultságok használatához válassza az Log Analytics a *csomópontok* díjszabási szintjein.

## <a name="change-the-data-retention-period"></a>Az adatmegőrzési időtartam módosítása

A következő lépések azt írják le, hogyan kell konfigurálni, hogy a rendszer mennyi ideig tárolja a napló adatait a munkaterületen. Az adatmegőrzés az összes munkaterülethez 30 – 730 napig (2 év) állítható be, kivéve, ha az örökölt ingyenes díjszabási szintet használják. [További](https://azure.microsoft.com/pricing/details/monitor/) információ a hosszabb adatmegőrzés díjszabásáról. 

### <a name="default-retention"></a>Alapértelmezett megőrzés

A munkaterület alapértelmezett megőrzésének beállításához 
 
1. A Azure Portal a munkaterületen válassza a **használat és becsült költségek** lehetőséget a bal oldali panelen.
2. A **Felhasználás és becsült költségek** oldalon kattintson az oldal tetején található **Adatmennyiség-kezelés** elemre.
3. A panelen mozgassa a csúszkát a napok számának növeléséhez vagy csökkentéséhez, majd kattintson **az OK**gombra.  Ha az *ingyenes* szintet választja, nem fogja tudni módosítani az adatmegőrzési időszakot, és frissítenie kell a fizetős szintre a beállítás szabályozása érdekében.

    ![Munkaterület adatmegőrzési beállításának módosítása](media/manage-cost-storage/manage-cost-change-retention-01.png)

A megőrzési idő csökkentése után a legrégebbi adatok eltávolítása előtt több napos türelmi időszakot is megtarthat. 
    
A megőrzés a paraméter használatával is [beállítható Azure Resource Manageron keresztül](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) `retentionInDays` . Emellett, ha az adatmegőrzést 30 napra állítja be, a paraméter használatával azonnal törölheti a régebbi adatok törlését, `immediatePurgeDataOn30Days` ami a megfelelőséggel kapcsolatos forgatókönyvek esetében hasznos lehet. Ez a funkció csak Azure Resource Manageron keresztül érhető el. 


Alapértelmezés szerint 90 a rendszer két adattípust (-- `Usage` és `AzureActivity` --) tart fenn, és ez a 90 nap megtartása esetén nem számít fel díjat. Ha a munkaterület megtartása 90 nap fölé emelkedik, az adattípusok megőrzése is megnövekszik.  Ezek az adattípusok az adatfeldolgozási díjaktól is mentesek. 

A munkaterületen alapuló Application Insights-erőforrások (,,,,,,,, és) adattípusai `AppAvailabilityResults` `AppBrowserTimings` `AppDependencies` alapértelmezés szerint `AppExceptions` `AppEvents` `AppMetrics` `AppPageViews` `AppPerformanceCounters` `AppRequests` `AppSystemEvents` `AppTraces` 90 napra is megmaradnak, és ez a 90 nap megtartása esetén nem számítunk fel díjat. A megőrzésük adattípusú funkció használatával állítható be. 


### <a name="retention-by-data-type"></a>Megőrzés adattípus szerint

Az egyes adattípusok esetében több adatmegőrzési beállítást is megadhat 30 – 730 napig (kivéve a korábbi ingyenes díjszabási szinten lévő munkaterületek esetében). Minden adattípus a munkaterület alerőforrása. A SecurityEvent tábla például a következő módon kezelhető [Azure Resource Managerban](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) :

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

Az érvényes értékek `retentionInDays` 30 és 730 között vannak.

Az `Usage` és az `AzureActivity` adattípusok nem állíthatók be egyéni megőrzéssel. Ezek az alapértelmezett munkaterület-megőrzési vagy a 90 napos maximális értékkel lesznek végrehajtva. 

Egy nagyszerű eszköz, amellyel közvetlenül csatlakozhat Azure Resource Managerhoz az adattípusok megőrzésének beállításához az OSS-eszköz [ARMclient](https://github.com/projectkudu/ARMClient).  További információ a cikkek ARMclient: [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) és [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Íme egy példa a ARMClient használatával, amely a SecurityEvent-adatok 730 napos megőrzését állítja be:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> Az adatmegőrzési költségek csökkentése érdekében az egyes adattípusok megőrzésének beállítása felhasználható.  A 2019 októberi (ha ez a funkció megjelent) óta gyűjtött adatok esetében az egyes adattípusok megőrzésének csökkentése csökkentheti az adatmegőrzési költségeket az idő múlásával.  A korábban összegyűjtött adatok esetében az egyes típusok alacsonyabb megőrzésének beállítása nem befolyásolja a megőrzési költségeket.  

## <a name="manage-your-maximum-daily-data-volume"></a>A maximális napi adatmennyiség kezelése

Beállíthatja a napi korlátot, és korlátozhatja a napi betöltést a munkaterülethez, de körültekintően használhatja, mert a cél nem a napi korlát.  Ellenkező esetben a nap hátralevő részében elveszíti az adatok mennyiségét, ami hatással lehet más Azure-szolgáltatásokra és-megoldásokra, amelyek működése a munkaterületen elérhető naprakész adatoktól függ.  Ennek eredményeképpen az IT-szolgáltatásokat támogató erőforrások állapotára vonatkozó riasztások megfigyelésére és fogadására van lehetőség.  A napi korlát arra szolgál, hogy kezelje az adatmennyiség váratlan növekedését a felügyelt erőforrásokból, és a korláton belül maradjon, vagy ha korlátozni szeretné a munkaterülethez tartozó nem tervezett díjakat.  

A napi korlát elérésekor a számlázható adattípusok gyűjteménye a nap hátralevő részében leáll. (A napi korlát alkalmazásával járó késés azt jelentheti, hogy a korlátot a rendszer nem alkalmazza a megadott napi korlát szintjének megfelelően.) A kijelölt Log Analytics munkaterülethez tartozó oldal tetején megjelenik egy figyelmeztető szalagcím, és a **LogManagement** kategóriában a *művelet* táblába egy műveleti esemény érkezik. Az adatgyűjtés a *napi korlát*alatt megadott alaphelyzetbe állítás időpontját követően fog folytatódni. Javasoljuk, hogy a műveleti esemény alapján határozzon meg egy riasztási szabályt, amely úgy van konfigurálva, hogy értesítést kapjon a napi adatkorlát elérésekor. 

> [!WARNING]
> A napi korlát nem állítja le az adatgyűjtést Azure Security Centerból, kivéve azokat a munkaterületeket, amelyekben a Azure Security Center a 2017. június 19. előtt volt telepítve. 

### <a name="identify-what-daily-data-limit-to-define"></a>A definiálni kívánt napi adatkorlát meghatározása

Tekintse át [log Analytics használati és becsült költségét](usage-estimated-costs.md) , és Ismerje meg az adatfeldolgozási trendet, valamint azt, hogy mi a napi mennyiségi korlát. Figyelembe kell venni az ellátást, mivel elnyerte az erőforrásait a korlát elérésekor. 

### <a name="set-the-daily-cap"></a>A napi korlát beállítása

A következő lépések azt ismertetik, hogyan konfigurálható egy korlát a Log Analytics munkaterület által naponta betöltött adatmennyiség kezelésére.  

1. A munkaterületen válassza a **Felhasználás és becsült költségek** lehetőséget a bal oldali panelen.
2. A kiválasztott munkaterület **használati és becsült költségek** lapján kattintson a lap tetején található **adatkötet-kezelés** elemre. 
3. Alapértelmezés szerint a napi korlát **ki van kapcsolva** ? kattintson **a be** gombra a engedélyezéséhez, majd állítsa be az adatmennyiség korlátját GB/nap értékre.

    ![Az adatkorlát konfigurálása Log Analytics](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Riasztás, ha elérte a napi korlátot

Noha a Azure Portal vizualizációs mutatót mutatunk be, amikor eléri az adatkorlátot, ez a viselkedés nem feltétlenül igazodik az azonnali beavatkozást igénylő működési problémák kezeléséhez.  Riasztási értesítés fogadásához létrehozhat egy új riasztási szabályt Azure Monitorban.  További információt a [riasztások létrehozása, megtekintése és kezelése](alerts-metric.md)című témakörben talál.

Az első lépésekhez tekintse meg a riasztás ajánlott beállításait:

- Cél: válassza ki a Log Analytics erőforrást
- Kritériumok 
   - Jel neve: egyéni naplók keresése
   - Keresési lekérdezés: művelet | ahol a Részletek "túlkvóta"
   - A következő alapján: az eredmények száma
   - Feltétel: nagyobb, mint
   - Küszöbérték: 0
   - Időszak: 5 (perc)
   - Gyakoriság: 5 (perc)
- Riasztási szabály neve: elérte a napi adatkorlátot
- Súlyosság: figyelmeztetés (1. pont)

Miután meghatározta a riasztást, és elérte a korlátot, a rendszer riasztást indít el, és végrehajtja a műveleti csoportban definiált választ. E-mailben és SMS-ben értesítheti a csapatot, vagy automatizálhatja a műveleteket webhookok, Automation runbookok vagy [külső ITSM-megoldásokkal való integráció](itsmc-overview.md#create-itsm-work-items-from-azure-alerts)révén. 

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
Az adatokat küldő csomópontok számának lekérése az elmúlt 24 órában a lekérdezés használatával: 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

A következő lekérdezéssel kérheti le az adatokat küldő csomópontok listáját (és az egyesek által küldött adatok mennyiségét):

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

> [!TIP]
> Ezekben a `union *` lekérdezésekben takarékosan használhatja az adattípusokat az [erőforrás-igényes](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization#query-performance-pane) végrehajtáshoz. Ha **számítógépeken** nincs szükség az eredményekre, akkor a használati adattípus lekérdezése (lásd alább).

## <a name="understanding-ingested-data-volume"></a>A betöltött adatmennyiség ismertetése

A **használat és a becsült költségek** lapon az *adatfeldolgozás egy adott megoldási* diagramon megjeleníti az elküldött adatok teljes mennyiségét, valamint azt, hogy az egyes megoldások milyen mértékben küldik el az adatokat. Ez lehetővé teszi olyan trendek meghatározását, mint például, hogy a teljes adatfelhasználás (vagy egy adott megoldás használata) egyre növekszik-e, és továbbra is állandó vagy csökkenő marad. 

### <a name="data-volume-for-specific-events"></a>Adott eseményekhez tartozó adatmennyiség

Ha meg szeretné vizsgálni az egyes események betöltött adatainak méretét, lekérdezheti az adott táblázatot (ebben a példában `Event` ), majd korlátozhatja a lekérdezést a fontos eseményekre (ebben a példában az 5145-es vagy a 5156-es azonosítójú esemény):

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

Vegye figyelembe, hogy a záradék `where IsBillable = true` kiszűri az adattípusokat bizonyos olyan megoldásokból, amelyekhez nincs betöltési díj. 

### <a name="data-volume-by-solution"></a>Adatmennyiség megoldásonként

A számlázható adatmennyiségnek az előző hónapban a megoldással való megtekintésére használt lekérdezés (az utolsó részleges nap kivételével) a következő:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

A () záradéka `TimeGenerated` csak annak biztosítására szolgál, hogy a Azure Portal lekérdezési élménye az alapértelmezett 24 órában is megtekinthető maradjon. A használati adatok típusának használatakor, `StartTime` valamint `EndTime` az eredmények megjelenítéséhez használt időgyűjtők. 

### <a name="data-volume-by-type"></a>Adatmennyiség típus szerint

További részletezéssel tekintheti meg az adattípussal kapcsolatos adattrendeket:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

Vagy megtekintheti az előző hónapra vonatkozó táblát megoldás és típus szerint.

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Adatmennyiség számítógépenként

Az `Usage` adattípus nem tartalmaz információkat a számítógép szintjén. Ha szeretné megtekinteni a betöltött adat **méretét** a számítógépen, használja a `_BilledSize` [tulajdonságot](log-standard-properties.md#_billedsize), amely a méretet adja meg bájtban:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

A `_IsBillable` [tulajdonság](log-standard-properties.md#_isbillable) azt határozza meg, hogy a betöltött adatok díjkötelesek-e. 

A számítógépeken betöltött számlázható események **számának** megtekintéséhez használja a következőt: 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  | sort by eventCount nulls last
```

> [!TIP]
> Ezekben a `union  *` lekérdezésekben takarékosan használhatja az adattípusokat az [erőforrás-igényes](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization#query-performance-pane) végrehajtáshoz. Ha **számítógépeken** nincs szükség az eredményekre, akkor a lekérdezés a használati adattípuson történik.

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Adatmennyiség Azure-erőforrás, erőforráscsoport vagy előfizetés alapján

Az Azure-ban üzemeltetett csomópontokból származó adatok esetén a __számítógépeken__tárolt adatok **mérete** megszerezhető, a _ResourceId [tulajdonságot](log-standard-properties.md#_resourceid)használva, amely az erőforrás teljes elérési útját biztosítja:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Az Azure-ban üzemeltetett csomópontokból származó adatok esetében az __Azure-előfizetések által__beolvasott adatok **mérete** az alábbiak szerint elemezhető `_ResourceId` :

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize BillableDataBytes = sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

A (z `subscriptionId` ) értékre váltásakor `resourceGroup` megjelenik az Azure-erőforráscsoport által betöltött számlázható adatmennyiség. 

> [!TIP]
> Ezekben a `union  *` lekérdezésekben takarékosan használhatja az adattípusokat az [erőforrás-igényes](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization#query-performance-pane) végrehajtáshoz. Ha nincs szüksége az eredményekre az előfizetés, a erőforrás-csoport vagy az erőforrás neve alapján, akkor a lekérdezés a használati adatok típusát adja meg.

> [!WARNING]
> A használati adattípus egyes mezői, miközben még mindig a séma része, elavultak, és az értékek már nem lesznek feltöltve. Ezek a **számítógépek** , valamint a betöltéssel kapcsolatos mezők (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** és **AverageProcessingTimeMs**).


### <a name="querying-for-common-data-types"></a>Gyakori adattípusok lekérdezése

Az adatforrások egy adott adattípussal való mélyebb feltárásához Íme néhány hasznos példa a lekérdezésekre:

+ **Munkaterület-alapú Application Insights** erőforrások
  - [További információ](https://docs.microsoft.com/azure/azure-monitor/app/pricing#data-volume-for-workspace-based-application-insights-resources)
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

### <a name="tips-for-reducing-data-volume"></a>Az adatmennyiség csökkentésére szolgáló tippek

Néhány javaslat a gyűjtött naplók mennyiségének csökkentésére:

| A nagy adatmennyiség forrása | Az adatmennyiség csökkentésének módja |
| -------------------------- | ------------------------- |
| Biztonsági események            | Válassza a [gyakori vagy minimális biztonsági események](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) lehetőséget <br> Módosítsa a biztonsági naplózási szabályzatot, hogy csak a szükséges eseményeket gyűjtse be. Tekintse át a következőkhöz való eseménygyűjtés szükségességét: <br> - [szűrőplatform naplózása](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [beállításjegyzék naplózása](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [fájlrendszer naplózása](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [kernelobjektum naplózása](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [leírókezelés naplózása](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> – Cserélhető tároló naplózása |
| Teljesítményszámlálók       | Módosítsa a [teljesítményszámlálók konfigurációját](data-sources-performance-counters.md): <br> – Csökkentse a gyűjtés gyakoriságát <br> – Csökkentse a teljesítményszámlálók számát |
| Eseménynaplók                 | Módosítsa az [eseménynaplók konfigurációját](data-sources-windows-events.md): <br> – Csökkentse a gyűjtött eseménynaplók számát <br> – Csak a szükséges eseményszinteket gyűjtse. Ne gyűjtsön például *Tájékoztatás* szintű eseményeket |
| Rendszernapló                     | Módosítsa a [rendszernapló konfigurációját](data-sources-syslog.md): <br> – Csökkentse a gyűjtésben részt vevő létesítmények számát <br> – Csak a szükséges eseményszinteket gyűjtse. Ne gyűjtsön például *Tájékoztatás* vagy *Hibakeresés* szintű eseményeket |
| AzureDiagnostics           | Az erőforrásnapló-gyűjtés módosítása a következőre: <br> – Csökkentse a Log Analytics számára naplókat küldő erőforrások számát <br> – Csak a szükséges naplókat gyűjtse |
| Megoldásadatok olyan számítógépekről, amelyeknek nincs szükségük a megoldásra | A [megoldás célcsoportja](../insights/solution-targeting.md) csak a szükséges számítógépek adatainak gyűjtésére használható. |

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

A különböző Automation-csomópontok számának megtekintéséhez használja a következő lekérdezést:

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

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>Az örökölt csomópontok díjszabási szintjeinek kiértékelése

Az, hogy az örökölt **csomópontok** díjszabási szintjéhez hozzáféréssel rendelkező munkaterületek jobban ki vannak-e kapcsolva az adott szinten, vagy az aktuális **utólagos** elszámolású vagy **kapacitás foglalási** szinten gyakran nehéz az ügyfelek számára felmérni az értékelést.  Ez magában foglalja a megfigyelt csomópontok közötti rögzített költség és a csomópontok díjszabási szintje közötti kompromisszumot, valamint a 500 MB/csomópont/nap adatfoglalást, valamint a betöltött adatmennyiség (GB-onként) költségét. 

Az értékelés megkönnyítése érdekében a következő lekérdezéssel javaslatot tehet az optimális díjszabási csomagra a munkaterület használati mintái alapján.  Ez a lekérdezés az elmúlt 7 napban egy munkaterületre betöltött figyelt csomópontokat és adatmennyiségeket vizsgálja, és minden nap esetében kiértékeli, hogy melyik árképzési csomag legyen optimális. A lekérdezés használatához meg kell adnia a

1. azt jelzi, hogy a munkaterület használ-e Azure Security Center a vagy a értékre való beállításával. `workspaceHasSecurityCenter` `true` `false` 
2. frissítse az árakat, ha vannak meghatározott kedvezmények, és
3. Itt adhatja meg, hogy hány napig kell visszakeresni és elemezni a beállítást `daysToEvaluate` . Ez akkor hasznos, ha a lekérdezésen túl sokáig próbálkozik a 7 napos adatmennyiség megkeresésével. 

Az árképzési csomagra vonatkozó javaslat lekérdezése:

```kusto
// Set these parameters before running query
let workspaceHasSecurityCenter = true;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your montly price per monitored nodes
let PerNodeOveragePrice = 2.30; // Enter your price per GB for data overage in the Per Node pricing tier
let PerGBPrice = 2.30; // Enter your price per GB in the Pay-as-you-go pricing tier
let daysToEvaluate = 7; // Enter number of previous days look at (reduce if the query is taking too long)
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
let StartDate = startofday(datetime_add("Day",-1*daysToEvaluate,now()));
let EndDate = startofday(now());
union withsource = tt * 
| where TimeGenerated >= StartDate and TimeGenerated < EndDate
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join kind=leftouter (
    Heartbeat 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend PerGBDailyCost = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)) * PerGBPrice,
             DataGB * PerGBPrice)
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerNodeOveragePrice
| extend Recommendation = iff(PerNodeDailyCost < PerGBDailyCost, "Per Node tier", 
             iff(NonSecurityDataGB > 85., "Capacity Reservation tier", "Pay-as-you-go (Per GB) tier"))
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, Recommendation | sort by day asc
//| project day, Recommendation // Comment this line to see details
| sort by day asc
```

Ez a lekérdezés nem a használat kiszámításának pontos replikálása, hanem a legtöbb esetben a díjszabási csomagra vonatkozó javaslatok nyújtásán fog működni.  

> [!NOTE]
> A OMS E1 Suite, OMS E2 Suite vagy OMS-bővítmény a System Center rendszerhez szolgáltatásból származó jogosultságok használatához válassza az Log Analytics a *csomópontok* díjszabási szintjein.

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

## <a name="data-transfer-charges-using-log-analytics"></a>Adatátviteli díjak az Log Analytics használatával

Az adatok Log Analytics való küldése adatsávszélességi díjat eredményezhet. Az [Azure sávszélesség-díjszabási oldalán](https://azure.microsoft.com/pricing/details/bandwidth/)leírtak szerint a két régióban található Azure-szolgáltatások közötti adatforgalom a normál díjszabás szerint kifelé irányuló kimenő adatforgalom. A bejövő adatforgalom ingyenes. Ez a díj azonban nagyon kicsi (néhány%) az adatfeldolgozás Log Analytics költségeihez képest. Ennek következtében a Log Analytics költségeinek szabályozása a betöltött adatmennyiségre összpontosíthat, és útmutatást nyújtunk ennek [megértéséhez.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Hibaelhárítás, hogy miért Log Analytics már nem gyűjti az adatgyűjtést

Ha az örökölt ingyenes díjszabási szinten van, és naponta több mint 500 MB-nyi adat lett elküldve, az adatgyűjtés a nap hátralevő részében leáll. A napi korlát elérésének gyakori oka, hogy Log Analytics leállítja az adatgyűjtést, vagy az adatok hiányoznak.  A Log Analytics egy típusú eseményt hoz létre az adatgyűjtés indításakor és leállításakor. Futtassa a következő lekérdezést a keresésben, és ellenőrizze, hogy eléri-e a napi korlátot és a hiányzó adatértékeket: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Amikor az adatgyűjtés leáll, a OperationStatus **figyelmeztetést**jelenít meg. Az adatgyűjtés megkezdésekor a OperationStatus **sikeres**. Az alábbi táblázat leírja, hogy az adatgyűjtés miért leáll és egy javasolt művelet az adatgyűjtés folytatásához:  

|Az OK gyűjtése leáll| Megoldás| 
|-----------------------|---------|
|Elérte az örökölt ingyenes díjszabási csomag napi korlátját |Várjon, amíg a gyűjtemény automatikusan újraindul, vagy váltson egy fizetős díjszabási csomagra.|
|Elérte a munkaterület napi korlátját|Várjon, amíg a gyűjtemény automatikusan újraindul, vagy növelje a napi adatmennyiség kezelése című témakörben leírt napi adatmennyiség korlátját. A napi korlát alaphelyzetbe állításának ideje az **adatmennyiség-kezelés** oldalon látható. |
|Az Azure-előfizetés felfüggesztett állapotban van, a következő okból:<br> Az ingyenes próbaverzió véget ért<br> Az Azure pass lejárt<br> Elérte a havi költségkeretet (például MSDN-vagy Visual Studio-előfizetéssel)|Átállás a fizetős verzióra<br> Törlési korlát, vagy várjon, amíg a korlát alaphelyzetbe nem áll|

Ha értesítést szeretne kapni az adatgyűjtés leállításakor, kövesse a *napi adatsapka* -riasztás létrehozása című témakörben leírt lépéseket az adatgyűjtés leállításakor. A következő témakörben ismertetett lépések végrehajtásával konfigurálhat egy e-mailt, webhookot vagy runbook műveletet a riasztási szabályhoz: [műveleti csoport létrehozása](action-groups.md) . 

## <a name="limits-summary"></a>Korlátok összegzése

Vannak további Log Analytics korlátok, amelyek némelyike a Log Analytics díjszabási szintjétől függ. Ezeket [itt](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)dokumentáljuk.


## <a name="next-steps"></a>További lépések

- A keresési nyelv használatának megismeréséhez tekintse meg a [naplóban megkeresett Azure monitor naplókat](../log-query/log-query-overview.md) . A keresési lekérdezésekkel további elemzéseket végezhet a használati adatokon.
- Az [új naplózási riasztás létrehozásával kapcsolatos](alerts-metric.md) szakaszban leírt lépéseket követve beállíthatja, hogy értesítést kapjon, ha teljesül egy keresési feltétel.
- A [megoldás célcsoportja](../insights/solution-targeting.md) csak a szükséges számítógépek adatainak gyűjtésére használható.
- Egy hatékony esemény-gyűjtési házirend konfigurálásához tekintse át [Azure Security Center szűrési házirendet](../../security-center/security-center-enable-data-collection.md).
- [Teljesítményszámláló konfigurációjának](data-sources-performance-counters.md)módosítása
- Az események gyűjtési beállításainak módosításához tekintse át az [Eseménynapló konfigurációját](data-sources-windows-events.md).
- A syslog-gyűjtemény beállításainak módosításához tekintse át a [syslog konfigurációját](data-sources-syslog.md).