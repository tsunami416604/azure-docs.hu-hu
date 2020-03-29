---
title: Az Azure traffic analytics gyakran feltett kérdések | Microsoft dokumentumok
description: Válaszok at kaphat a forgalomelemzéssel kapcsolatos leggyakoribb kérdésekre.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: damendo
ms.openlocfilehash: 5e31ed905f05070c8715a63ef3386b0006df0a75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840621"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>A Traffic Analytics gyakran tesz fel kérdéseket

Ez a cikk egy helyen gyűjti össze az Azure Network Watcher forgalomelemzésével kapcsolatos leggyakoribb kérdéseket.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Melyek a forgalomelemzés használatának előfeltételei?

A Traffic Analytics a következő előfeltételeket igényli:

- A Network Watcher engedélyezett előfizetés.
- A figyelni kívánt NSG-k számára engedélyezve vannak a hálózati biztonsági csoport (NSG) folyamatnaplói.
- Egy Azure Storage-fiók, a nyers folyamatnaplók tárolására.
- Egy Azure Log Analytics munkaterület, olvasási és írási hozzáféréssel.

A forgalomelemzés engedélyezéséhez a fióknak meg kell felelnie az alábbi szolgáltatások egyikének:

- A fióknak rendelkeznie kell a következő szerepköralapú hozzáférés-vezérlési (RBAC) szerepkörök bármelyikével az előfizetés hatókörében: tulajdonos, közreműködő, olvasó vagy hálózati közreműködő.
- Ha a fiók nincs hozzárendelve a korábban felsorolt szerepkörök egyikéhez, akkor a következő műveletekhez rendelt egyéni szerepkörhöz kell hozzárendelni az előfizetés szintjén.
            
    - Microsoft.Network/applicationGateways/read
    - Microsoft.Network/connections/read
    - Microsoft.Network/loadBalancers/read 
    - Microsoft.Network/localNetworkGateways/read 
    - Microsoft.Network/networkInterfaces/read 
    - Microsoft.Network/networkSecurityGroups/read 
    - Microsoft.Network/publicIPAddresses/read
    - Microsoft.Network/routeTables/read
    - Microsoft.Network/virtualNetworkGateways/read 
    - Microsoft.Network/virtualNetworks/read
        
Az előfizetéshez rendelt szerepkörök ellenőrzése:

1. Jelentkezzen be az **Azure-ba a Login-AzAccount segítségével.** 

2. Válassza ki a szükséges előfizetést a **Select-AzSubscription**használatával. 

3. A megadott felhasználóhoz rendelt összes szerepkör listázásához használja a **Get-AzRoleAssignment -SignInName [felhasználói levelezés] -IncludeClassicAdministrators parancsot.** 

Ha nem lát kimenetet, lépjen kapcsolatba a megfelelő előfizetési rendszergazdával, és szerezzen hozzáférést a parancsok futtatásához. További részletekért [olvassa el a Szerepköralapú hozzáférés-vezérlés kezelése az Azure PowerShell használatával című témakört.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)


## <a name="in-which-azure-regions-is-traffic-analytics-available"></a>Mely Azure-régiókban érhető el a Traffic Analytics?

Az NSG-k forgalomelemzését az alábbi támogatott régiók bármelyikében használhatja:
- Közép-Kanada
- USA nyugati középső régiója
- USA keleti régiója
- USA 2. keleti régiója
- USA északi középső régiója
- USA déli középső régiója
- USA középső régiója
- USA nyugati régiója
- USA nyugati régiója, 2.
- Közép-Franciaország
- Nyugat-Európa
- Észak-Európa
- Dél-Brazília
- Az Egyesült Királyság nyugati régiója
- Az Egyesült Királyság déli régiója
- Kelet-Ausztrália
- Délkelet-Ausztrália 
- Kelet-Ázsia
- Délkelet-Ázsia
- Dél-Korea középső régiója
- Közép-India
- Dél-India
- Kelet-Japán
- Nyugat-Japán
- USA-beli államigazgatás – Virginia
- Kína Keleti 2

A Log Analytics-munkaterületnek a következő régiókban kell léteznie:
- Közép-Kanada
- USA nyugati középső régiója
- USA keleti régiója
- USA 2. keleti régiója
- USA északi középső régiója
- USA déli középső régiója
- USA középső régiója
- USA nyugati régiója
- USA nyugati régiója, 2.
- Közép-Franciaország
- Nyugat-Európa
- Észak-Európa
- Az Egyesült Királyság nyugati régiója
- Az Egyesült Királyság déli régiója
- Kelet-Ausztrália
- Délkelet-Ausztrália
- Kelet-Ázsia
- Délkelet-Ázsia 
- Dél-Korea középső régiója
- Közép-India
- Kelet-Japán
- USA-beli államigazgatás – Virginia
- Kína Keleti 2

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Az NSG-k, amelyekhez engedélyezem a folyamatnaplókat, a munkaterületemnél különböző régiókban lehetnek?

Igen, ezek az NSG-k különböző régiókban lehetnek, mint a Log Analytics-munkaterület.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Több NSG konfigurálható egyetlen munkaterületen belül?

Igen.

## <a name="can-i-use-an-existing-workspace"></a>Használhatok meglévő munkaterületet?

Igen. Ha meglévő munkaterületet jelöl ki, győződjön meg arról, hogy az új lekérdezési nyelvre lett áttelepítve. Ha nem szeretné frissíteni a munkaterületet, újat kell létrehoznia. Az új lekérdezési nyelvről az [Azure Monitor naplóinak frissítése az új naplókeresésre](../log-analytics/log-analytics-log-search-upgrade.md)című témakörben talál további információt.

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>Lehet az Azure Storage-fiókomban egy előfizetés, és a Log Analytics-munkaterület egy másik előfizetésben?

Igen, az Azure Storage-fiók lehet egy előfizetésben, és a Log Analytics-munkaterület lehet egy másik előfizetésben.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Tárolhatok nyers naplókat egy másik előfizetésben?

Nem. A nyers naplók at tárolhatja bármely tárfiókban, ahol egy NSG engedélyezve van a folyamatnaplókhoz. Azonban mind a tárfiók és a nyers naplók kell ugyanabban az előfizetésben és régióban.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Mi a teendő, ha "Nem található" hiba miatt nem tudok NSG-t beállítani a forgalomelemzéshez?

Válasszon ki egy támogatott régiót. Ha nem támogatott régiót választ, "Nem található" hibaüzenet jelenik meg. A támogatott régiók a cikk korábbi listái.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Mi a teendő, ha az NSG folyamatnaplók lapján a "Nem sikerült betölteni" állapotot kapom?

A Microsoft.Insights-szolgáltatót regisztrálni kell a folyamatnaplózás megfelelő működéséhez. Ha nem biztos abban, hogy a Microsoft.Insights-szolgáltató regisztrálva van-e az előfizetéséhez, cserélje le az *xxxxx-xxxxx-xxxxxx-xxxx* fájlt a következő parancsban, és futtassa a következő parancsokat a PowerShellből:

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Én konfigurálta a megoldást. Miért nem látok semmit a műszerfalon?

Az irányítópult első alkalommal akár 30 percet is igénybe vehet. A megoldásnak először elegendő adatot kell összesítenie ahhoz, hogy értelmes elemzéseket nyerjen. Ezután jelentéseket készít. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Mi a teendő, ha a következő üzenet jelenik meg: "Nem találtunk adatokat ebben a munkaterületen a kiválasztott időintervallumhoz. Próbálja meg módosítani az időintervallumot, vagy válasszon másik munkaterületet."?

Próbálkozzon a következő lehetőségekkel:
- Módosítsa az időintervallumot a felső sávban.
- Válasszon másik Log Analytics-munkaterületet a felső sávon.
- Próbálja meg elérni a forgalomelemzés 30 perc után, ha a közelmúltban engedélyezve van.
    
Ha a problémák továbbra is fennállnak, a [User voice fórumon](https://feedback.azure.com/forums/217313-networking?category_id=195844)aggályokat vet fel.

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Mi van, ha ezt az üzenetet kapom: "Az NSG folyamatnaplóinak elemzése az első alkalommal. Ez a folyamat 20-30 percet is igénybe vehet. Nézz vissza egy idő után. 2) Ha a fenti lépés nem működik, és a munkaterület az ingyenes Termékváltozat alatt található, akkor ellenőrizze a munkaterület használatát itt a kvóta feletti érvényesítéshez, másrészt további információkért olvassa el a GYIK-et."?

Ez az üzenet a következők miatt jelenhet meg:
- Traffic Analytics nemrég engedélyezve lett, és lehet, hogy még nem összesített elég adatot, hogy érdemi elemzéseket nyerjen.
- A Log Analytics munkaterület ingyenes verzióját használja, és túllépte a kvótakorlátokat. Előfordulhat, hogy nagyobb kapacitású munkaterületet kell használnia.
    
Ha a problémák továbbra is fennállnak, a [User voice fórumon](https://feedback.azure.com/forums/217313-networking?category_id=195844)aggályokat vet fel.
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Mi a teendő, ha a következő üzenet jelenik meg: "Úgy tűnik, hogy rendelkezünk erőforrásadatokkal (Topológia), és nincs enek áramlási információ. Eközben kattintson ide az erőforrások adatainak megtekintéséhez, és további információkért olvassa el a GYIK-et."?

Az erőforrások adatai az irányítópulton találhatók; azonban nem állnak fenn áramlással kapcsolatos statisztikák. Előfordulhat, hogy az adatok nem jelennek meg, mert nincs kommunikációs áramlás az erőforrások között. Várjon 60 percet, és ellenőrizze újra az állapotot. Ha a probléma továbbra is fennáll, és biztos benne, hogy az erőforrások közötti kommunikáció létezik, aggályokat vet fel a [Felhasználói hang fórumban.](https://feedback.azure.com/forums/217313-networking?category_id=195844)

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Konfigurálhatom a forgalomelemzést PowerShell vagy Azure Resource Manager-sablon vagy -ügyfél használatával?

A forgalomelemzés konfigurálható a Windows PowerShell 6.2.1-es verziójától kezdve. Ha egy adott NSG-hez folyamatnaplózást és forgalomelemzést szeretne konfigurálni a Set parancsmag használatával, olvassa el a [Set-AzNetworkWatcherConfigFlowLog című témakört.](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) Egy adott NSG folyamatnaplózási és forgalomelemzési állapotának lehívásáról a [Get-AzNetworkWatcherFlowLogStatus című témakörben található.](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkwatcherflowlogstatus)

Jelenleg nem használhat Azure Resource Manager-sablont a forgalomelemzés konfigurálásához.

A forgalomelemzés konfigurálásához egy Azure Resource Manager-ügyfél használatával tekintse meg az alábbi példákat.

**Példa parancsmag beállítása:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<name of NSG>"
$TAstorageId = "/subscriptions/<storage subscription id>/resourcegroups/<storage resource group name> /providers/microsoft.storage/storageaccounts/<storage account name>"
$networkWatcherResourceGroupName = "<network watcher resource group name>"
$networkWatcherName = "<network watcher name>"

$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}',
    'properties': 
    {
        'storageId': '${TAstorageId}',
        'enabled': '<true to enable flow log or false to disable flow log>',
        'retentionPolicy' : 
        {
            days: <enter number of days like to retail flow logs in storage account>,
            enabled: <true to enable retention or false to disable retention>
        }
    },
    'flowAnalyticsConfiguration':
    {
                'networkWatcherFlowAnalyticsConfiguration':
      {
        'enabled':,<true to enable traffic analytics or false to disable traffic analytics>
        'workspaceId':'bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb',
        'workspaceRegion':'<workspace region>',
        'workspaceResourceId':'/subscriptions/<workspace subscription id>/resourcegroups/<workspace resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
        
      }

    }
}
"@
$apiversion = "2016-09-01"

armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/configureFlowlog?api-version=${apiversion}" $requestBody
```
**Példa parancsmag beszerezni:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<NSG name>"


$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}'
}
“@


armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/queryFlowLogStatus?api-version=${apiversion}" $requestBody
```


## <a name="how-is-traffic-analytics-priced"></a>Hogyan történik a Traffic Analytics ára?

A Traffic Analytics mérése. A mérés a szolgáltatás által a folyamatnapló-adatok feldolgozásán alapul, és az eredményül kapott bővített naplók at egy Log Analytics-munkaterületen tárolja. 

Például, mint egy az [árképzési terv](https://azure.microsoft.com/pricing/details/network-watcher/), figyelembe véve a nyugat-közép-amerikai régióban, ha a flow naplók tárolt adatok egy tárfiókban a Traffic Analytics által feldolgozott 10 GB és továbbfejlesztett naplók bevitt Log Analytics munkaterület 1 GB, akkor a vonatkozó díjak: 10 x 2.3$ + 1 x 2.76$ = 25.76$ $

## <a name="how-frequently-does-traffic-analytics-process-data"></a>Milyen gyakran dolgozza fel a Traffic Analytics az adatokat?

Tekintse meg a Traffic Analytics séma és az adatösszesítési dokumentum [adatösszesítési szakaszát](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema#data-aggregation)

## <a name="how-does-traffic-analytics-decide-that-an-ip-is-malicious"></a>Hogyan dönti el a Traffic Analytics, hogy egy IP rosszindulatú? 

A Traffic Analytics a Microsoft belső fenyegetésfelderítési rendszereire támaszkodik, hogy az IP-cím rosszindulatúnak minősüljen. Ezek a rendszerek különböző telemetriai forrásokat, például microsoftos termékeket és szolgáltatásokat, a Microsoft Digital Crimes Unit (DCU), a Microsoft Security Response Center (MSRC) és a külső hírcsatornákat használják ki, és sok intelligenciát építenek a tetejére. Ezen adatok egy része a Microsoft Internal. Ha egy ismert IP-cím rosszindulatúként van megjelölve, kérjük, emelje fel a támogatási jegyet a részletek megismeréséhez.

## <a name="how-can-i-set-alerts-on-traffic-analytics-data"></a>Hogyan állíthatok be riasztásokat a Traffic Analytics adataira?

A Traffic Analytics nem rendelkezik beépített támogatással a riasztásokhoz. Mivel azonban a Traffic Analytics-adatok a Log Analytics tárolja, egyéni lekérdezéseket írhat, és riasztásokat állíthat be rájuk. Lépéseket:
- Használhatja a shortlink a Log Analytics a Traffic Analytics. 
- A lekérdezések írásához használja az [itt dokumentált sémát](traffic-analytics-schema.md) 
- Kattintson az "Új riasztási szabály" gombra a riasztás létrehozásához
- A riasztás létrehozásához tekintse meg a [naplóriasztások dokumentációját](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

## <a name="how-do-i-check-which-vms-are-receiving-most-on-premise-traffic"></a>Hogyan ellenőrizhetem, hogy mely virtuális gépek kapják a legtöbb helyszíni forgalmat?

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            | where <Scoping condition>
            | mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
            | where isnotempty(vm) 
             | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
            | make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by vm
            | render timechart

  IP-k esetében:

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            //| where <Scoping condition>
            | mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
            | where isnotempty(IP) 
            | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
            | make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by IP
            | render timechart

Időben használja a formátumot: yyyy-mm-dd 00:00:00

## <a name="how-do-i-check-standard-deviation-in-traffic-recieved-by-my-vms-from-on-premise-machines"></a>Hogyan ellenőrizhetem a virtuális gépeim által a helyszíni gépekről fogadott forgalom szórását?

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            //| where <Scoping condition>
            | mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
            | where isnotempty(vm) 
            | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d
            | summarize deviation = stdev(traffic)  by vm


IP-k esetében:

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            //| where <Scoping condition>
            | mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
            | where isnotempty(IP) 
            | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d
            | summarize deviation = stdev(traffic)  by IP
            
## <a name="how-do-i-check-which-ports-are-reachable-or-bocked-between-ip-pairs-with-nsg-rules"></a>Hogyan tudom ellenőrizni, hogy mely portok elérhetők (vagy bocked) az IP párok között az NSG szabályaival

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and TimeGenerated between (startTime .. endTime)
            | extend sourceIPs = iif(isempty(SrcIP_s), split(SrcPublicIPs_s, " ") , pack_array(SrcIP_s)),
            destIPs = iif(isempty(DestIP_s), split(DestPublicIPs_s," ") , pack_array(DestIP_s))
            | mvexpand SourceIp = sourceIPs to typeof(string)
            | mvexpand DestIp = destIPs to typeof(string)
            | project SourceIp = tostring(split(SourceIp, "|")[0]), DestIp = tostring(split(DestIp, "|")[0]), NSGList_s, NSGRule_s, DestPort_d, L4Protocol_s, FlowStatus_s 
            | summarize DestPorts= makeset(DestPort_d) by SourceIp, DestIp, NSGList_s, NSGRule_s, L4Protocol_s, FlowStatus_s

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Hogyan navigálhatok a billentyűzet használatával a földrajzi térkép nézetben?

A földrajzi térkép oldal két fő szakaszt tartalmaz:
    
- **Szalagcím**: A földrajzi térkép tetején lévő szalagcím gombokat biztosít a forgalomelosztási szűrők kiválasztásához (például telepítés, országok/régiók ból származó forgalom és rosszindulatú). Amikor kiválaszt egy gombot, a megfelelő szűrő kerül a térképre. Ha például az Aktív gombot választja, a térkép kiemeli az aktív adatközpontokat a központi telepítésben.
- **Térkép**: A szalagcím alatt a térkép szakasz ban az Azure-adatközpontok és országok/régiók közötti forgalom eloszlása látható.
    
### <a name="keyboard-navigation-on-the-banner"></a>Billentyűzetnavigáció a szalagcímen
    
- Alapértelmezés szerint a választás a geotérkép-oldalon a szalagcím az "Azure DC" szűrő.
- Ha másik szűrőre szeretne `Tab` lépni, `Right arrow` használja a vagy a kulcsot. A hátralépéshez használja `Shift+Tab` a `Left arrow` vagy a kulcsot. Az előremutató navigáció balról jobbra halad, majd fentről lefelé halad.
- A `Enter` kijelölt `Down` szűrő alkalmazásához nyomja meg vagy a nyílbillentyűt. A szűrő kiválasztása és üzembe helyezése alapján a térképszakasz egy vagy több csomópontja ki van emelve.
- A szalagcím és a `Ctrl+F6`térkép közötti váltáshoz nyomja meg a gombot.
        
### <a name="keyboard-navigation-on-the-map"></a>Billentyűzetnavigáció a térképen
    
- Miután kiválasztotta a szalagcím `Ctrl+F6`bármely szűrőjét, és megnyomta a fókuszt, a fókusz a térképnézetben a kijelölt csomópontok **(Azure datacenter** vagy **Ország/régió)** egyikére kerül.
- Ha a térkép más kiemelt csomópontjaira szeretne `Tab` lépni, használja az előre irányuló mozgás egyik vagy a `Right arrow` kulcs átlépését. Használja, `Shift+Tab` `Left arrow` vagy a gombot a hátrafelé irányuló mozgáshoz.
- A térképen kijelölt csomópont kiválasztásához használja `Enter` `Down arrow` a vagy a kulcsot.
- Az ilyen csomópontok kiválasztásakor a fókusz a csomópont **információs eszközdobozára** kerül. Alapértelmezés szerint a fókusz az **Információs eszközmező**zárt gombjára kerül. A **Doboz** nézeten belüli `Right arrow` további `Left arrow` mozgáshoz használja és használja a billentyűket, hogy előre, illetve hátrafelé haladjon. A `Enter` lenyomásnak ugyanaz a hatása, mint az Információs eszközmező fókuszált **gombjának kijelölése.**
- Ha megnyomja, `Tab` miközben a fókusz az **Információs eszközmezőre**kerül, a fókusz a kijelölt csomóponttal azonos kontinens végpontjaira kerül. A `Right arrow` és `Left arrow` a billentyűkkel mozoghat ezeken a végpontokon.
- Más áramlási végpontokra vagy kontinens-fürtökre való áttéréshez használja `Tab` előre és `Shift+Tab` hátrafelé irányuló mozgást.
- Ha a fókusz a **kontinens-fürtökön**van, a vagy `Enter` `Down` a nyílbillentyűkkel jelölje ki a kontinens fürtön belüli végpontokat. A végpontok közötti lépéshez és a kontinensfürt információs mezőjének `Right arrow` `Left arrow` bezárás gombján a vagy a gombot használja előre, illetve hátrafelé. Bármely végponton átválthat `Shift+L` a kapcsolati vonalra a kijelölt csomópontról a végpontra. A megnyomása megnyomása `Shift+L` a kijelölt végpontra lépéshez.
        
### <a name="keyboard-navigation-at-any-stage"></a>Billentyűzetnavigáció bármely szakaszban
    
- `Esc`összecsukja a kibontott kijelölést.
- A `Up arrow` kulcs ugyanazt a `Esc`műveletet hajtja végre, mint a. A `Down arrow` kulcs ugyanazt a `Enter`műveletet hajtja végre, mint a.
- Nagyításra és `Shift+Plus` `Shift+Minus` kicsinyítésre használható.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Hogyan navigálhatok a virtuális hálózati topológia nézetben lévő billentyűzet használatával?

A virtuális hálózatok topológia lapja két fő szakaszt tartalmaz:
    
- **Szalagcím**: A virtuális hálózatok topológiájának tetején lévő szalagcím gombokat biztosít a forgalomelosztási szűrők kiválasztásához (például Csatlakoztatott virtuális hálózatok, Leválasztott virtuális hálózatok és Nyilvános IP-k). Amikor kiválaszt egy gombot, a megfelelő szűrő kerül a topológiára. Ha például az Aktív gombot választja, a topológia kiemeli a központi telepítésben lévő aktív virtuális hálózatokat.
- **Topológia**: A szalagcím alatt a topológia szakasz a virtuális hálózatok közötti forgalomeloszlást mutatja.
    
### <a name="keyboard-navigation-on-the-banner"></a>Billentyűzetnavigáció a szalagcímen
    
- Alapértelmezés szerint a szalagcím virtuális hálózatok topológiájának kiválasztása a "Csatlakoztatott virtuális hálózatok" szűrő.
- Ha másik szűrőre szeretne `Tab` lépni, a billentyűvel lépjen előre. Ha hátrafelé szeretne `Shift+Tab` lépni, használja a kulcsot. Az előremutató navigáció balról jobbra halad, majd fentről lefelé halad.
- Nyomja `Enter` meg a kijelölt szűrő alkalmazásához. A szűrő kiválasztása és üzembe helyezése alapján a topológia szakaszban egy vagy több csomópont (virtuális hálózat) van kiemelve.
- A szalagcím és a topológia `Ctrl+F6`közötti váltáshoz nyomja meg a gombot.
        
### <a name="keyboard-navigation-on-the-topology"></a>Billentyűzetnavigáció a topológián
    
- Miután kiválasztotta a szalagcím `Ctrl+F6`bármely szűrőjét, és lenyomta a gombot, a fókusz a topológia nézetben a kijelölt csomópontok **(VNet)** egyikére kerül.
- Ha a topológia nézetben más kiemelt csomópontokra `Shift+Right arrow` szeretne lépni, használja a kulcsot az előremozgatáshoz. 
- A kiemelt csomópontokon a fókusz a csomópont **Információs eszközdobozára** kerül. Alapértelmezés szerint a fókusz az Információs **eszközmező** **További részletek** gombjára kerül. A **Doboz** nézeten belüli további `Right arrow` `Left arrow` mozgáshoz használja a és a gombokat az előre, illetve hátralépéshez. A `Enter` lenyomásnak ugyanaz a hatása, mint az Információs eszközmező fókuszált **gombjának kijelölése.**
- Az ilyen csomópontok kiválasztásánál egyenként meglátogathatja az összes kapcsolatot a `Shift+Left arrow` gomb megnyomásával. A fókusz a kapcsolat **információs eszközdobozára** kerül. A fókusz bármikor visszatolható a csomópontra `Shift+Right arrow` az ismételt megnyomással.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Hogyan navigálhatok az alhálózati topológia nézetben lévő billentyűzet használatával?

A virtuális alhálózatok topológia lapja két fő szakaszt tartalmaz:
    
- **Szalagcím**: A virtuális alhálózatok topológiájának tetején lévő szalagcím gombokat biztosít a forgalomelosztási szűrők (például Aktív, Közepes és Átjáró alhálózatok) kiválasztásához. Amikor kiválaszt egy gombot, a megfelelő szűrő kerül a topológiára. Ha például az Aktív gombot választja, a topológia kiemeli a központi telepítés aktív virtuális alhálózatát.
- **Topológia**: A szalagcím alatt a topológia rész a virtuális alhálózatok közötti forgalomeloszlást mutatja.
    
### <a name="keyboard-navigation-on-the-banner"></a>Billentyűzetnavigáció a szalagcímen
    
- Alapértelmezés szerint a szalagcím virtuális alhálózatok topológiájának kezdőlapja az "Alhálózatok" szűrő.
- Ha másik szűrőre szeretne `Tab` lépni, a billentyűvel lépjen előre. Ha hátrafelé szeretne `Shift+Tab` lépni, használja a kulcsot. Az előremutató navigáció balról jobbra halad, majd fentről lefelé halad.
- Nyomja `Enter` meg a kijelölt szűrő alkalmazásához. A szűrő kiválasztása és üzembe helyezése alapján a topológia szakaszban egy vagy több csomópont (Alhálózat) van kiemelve.
- A szalagcím és a topológia `Ctrl+F6`közötti váltáshoz nyomja meg a gombot.
        
### <a name="keyboard-navigation-on-the-topology"></a>Billentyűzetnavigáció a topológián
    
- Miután kiválasztotta a szalagcím `Ctrl+F6`bármely szűrőjét, és lenyomta a gombot, a fókusz a topológia nézetben a kijelölt csomópontok (**Alhálózat**) egyikére kerül.
- Ha a topológia nézetben más kiemelt csomópontokra `Shift+Right arrow` szeretne lépni, használja a kulcsot az előremozgatáshoz. 
- A kiemelt csomópontokon a fókusz a csomópont **Információs eszközdobozára** kerül. Alapértelmezés szerint a fókusz az Információs **eszközmező** **További részletek** gombjára kerül. A **Doboz** nézeten belüli `Right arrow` további `Left arrow` mozgáshoz használja és használja a billentyűket, hogy előre, illetve hátrafelé haladjon. A `Enter` lenyomásnak ugyanaz a hatása, mint az Információs eszközmező fókuszált **gombjának kijelölése.**
- Az ilyen csomópontok kiválasztásánál egyenként meglátogathatja az összes kapcsolatot, a `Shift+Left arrow` gomb megnyomásával. A fókusz a kapcsolat **információs eszközdobozára** kerül. A fókusz bármikor visszatolható a csomópontra `Shift+Right arrow` az ismételt megnyomással.    

