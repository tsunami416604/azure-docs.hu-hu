---
title: Az Azure traffic analytics – gyakori kérdések |} A Microsoft Docs
description: Tudnivalók a traffic analytics leggyakrabban feltett kérdésekre választ kaphat.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: jdial
ms.openlocfilehash: 3938427c23993f0546e7df62da88dadaf3353118
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549371"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>A TRAFFIC Analytics – gyakori kérdések

Ez a cikk összegyűjti a egy helyen számos, a traffic analytics az Azure Network Watcher leggyakrabban feltett kérdésekre.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Mik a traffic analytics használatának előfeltételei?

A TRAFFIC Analytics a következő előfeltételek szükségesek:

- A Network Watcher előfizetés engedélyezve van.
- Hálózati biztonsági csoport (NSG) folyamatnaplóit engedélyezve az NSG-t szeretné figyelni.
- Egy Azure Storage-fiók, nyers forgalmi naplók tárolásához.
- Az Azure Log Analytics-munkaterületet, az olvasási és írási hozzáférés.

Felhasználói fiók megfelel a traffic analytics engedélyezéséhez a következők egyikét:

- A fióknak rendelkeznie kell a következő szerepköralapú hozzáférés-vezérlés (RBAC) szerepkörök közül bármelyik, az előfizetések szintjén: tulajdonos, közreműködő, olvasó vagy hálózati közreműködő.
- Ha a fiókjához nincs hozzárendelve egy a korábban felsorolt szerepkörbe, akkor hozzá kell rendelni egy egyéni biztonsági szerepkört, amely hozzá van rendelve a következő műveletek, az előfizetés szintjén.
            
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
        
A felhasználó előfizetéshez hozzárendelt szerepkörök ellenőrzése:

1. Jelentkezzen be az Azure-bA **bejelentkezési-AzAccount**. 

2. Válassza ki a kötelező előfizetést használatával **Select-AzSubscription**. 

3. Az adott felhasználóhoz rendelt szerepkörök listájában, használja a **Get-AzRoleAssignment - SignInName [felhasználó e-mail címe] - IncludeClassicAdministrators**. 

Kimenetet nem jelennek meg, ha lépjen kapcsolatba a megfelelő előfizetés rendszergazdája hozzáférhet a parancsok futtatásához. További részletekért lásd: [szerepköralapú hozzáférés-vezérlés kezelése az Azure PowerShell-lel](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).


## <a name="in-which-azure-regions-is-traffic-analytics-available"></a>Melyik Azure-régióban érhető el a Traffic Analytics?

A traffic analytics használhat NSG-k, a következő támogatott régiók egyikében:
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
- Korea középső régiója
- Közép-India
- Dél-India
- Kelet-Japán
- Nyugat-Japán
- USA-beli államigazgatás – Virginia

A Log Analytics-munkaterület léteznie kell a következő régióban:
- Közép-Kanada
- USA nyugati középső régiója
- USA nyugati régiója, 2.
- USA keleti régiója
- Közép-Franciaország
- Nyugat-Európa
- Az Egyesült Királyság déli régiója
- Délkelet-Ausztrália
- Délkelet-Ázsia 
- Korea középső régiója
- Közép-India
- Kelet-Japán
- USA-beli államigazgatás – Virginia

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Az NSG-k engedélyezhető a flow is, mint a saját munkaterület különböző régiókban lehet naplókat?

Igen, ezek az NSG-k, mint a Log Analytics-munkaterület különböző régiókban is lehetnek.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Egyetlen munkaterületen belül konfigurálhatók több NSG-ket?

Igen.

## <a name="can-i-use-an-existing-workspace"></a>Használhatja egy meglévő munkaterületet?

Igen. Ha kiválaszt egy meglévő munkaterületet, ügyeljen arra, hogy azt az új lekérdezési nyelvre migrálták. Ha nem szeretne frissítse a munkaterületet, létre kell hozzon létre egy újat. Az új lekérdezési nyelvre kapcsolatos további információkért lásd: [Azure Monitor-naplók frissítése új naplókeresésre](../log-analytics/log-analytics-log-search-upgrade.md).

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>Az Azure Storage-fiókom lehet egy adott előfizetéshez, és a Log Analytics-munkaterületnek kell egy másik előfizetésben?

Igen, az Azure Storage-fiók lehet egy adott előfizetéshez, és a Log Analytics-munkaterület lehet egy másik előfizetésben található.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Tárolhatok nyers naplók egy másik előfizetéshez?

Nem. Minden tárfiók, amelyben engedélyezve van a hálózati biztonsági csoportok folyamatnaplóit nyers naplók tárolhatja. Azonban a tárfiók és a nyers naplókat is azonos előfizetésben és régióban kell lennie.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Mi történik, ha egy NSG-t, a traffic analytics "Nem található" hiba miatt nem tudja konfigurálni?

Válassza ki a támogatott régiók. Ha egy nem támogatott régió, a "Nem található" hibaüzenetet kap. A támogatott régiók korábban ebben a cikkben szerepel.

## <a name="why-am-i-getting-the-error-failed-to-update-flow-logs-settings-for--internalservererror-when-enabling-nsgs-in-us-gov-virginia"></a>Miért jelenik meg a hiba "folyamatnaplói beállításainak frissítése... sikertelen InternalServerError..." Amikor engedélyezi az NSG (US Gov Virginia)?

Ez az, ha nem áll újra regisztrált US Gov Virginia az előfizetéshez tartozó "Microsoft.Network" erőforrás-szolgáltató egy hiba miatt. A csapata dolgozik ennek a javítást. Áthidaló megoldásként kell [manuálisan regisztrálja újra "Microsoft.Network' RP](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-register-provider-errors). 

Ha a probléma tartósan fennáll, forduljon a támogatási. 

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Mi történik, ha az állapot kapok "nem sikerült betölteni," az NSG-t folyamat-naplók oldalán alatt?

A flow-naplózás helyes működéséhez regisztrálni kell a Microsoft.Insights szolgáltató. Ha nem biztos abban, hogy a a Microsoft.Insights szolgáltató regisztrálva van az előfizetéshez tartozó, cserélje le a *: xxxxx-xxxxx-xxxxxx-xxxx* a következő parancsot, és a PowerShellben futtassa a következő parancsokat:

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Konfiguráltam az megoldás. Miért nem jelenik meg semmit az irányítópult?

Az irányítópult jelenik meg az első alkalommal akár 30 percet is igénybe vehet. A megoldás kell először ahhoz, hogy egy értelmes elemzéseket elegendő adatokat tudnak összesíteni. Majd állít elő jelentést. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Mi történik, ha ez az üzenet jelenik meg: "Nem találtunk adatokat ezen a munkaterületen a kijelölt időintervallumban. Próbálja meg módosítani az időintervallum, vagy válasszon másik munkaterületet. "?

Próbálja ki a következő beállításokat:
- A felső sávon időintervallum módosításához.
- Egy másik Log Analytics-munkaterületen válassza a felső sávon.
- Ha nemrég lett engedélyezve, próbálkozzon a fér hozzá a traffic analytics 30 perc múlva.
    
Ha a probléma továbbra is fennáll, a aggályokat vethet a [User voice fórumán](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Mi történik, ha ez az üzenet jelenik meg: "A hálózati biztonsági csoportok folyamatnaplói először. Ez a folyamat 20-30 percet is igénybe vehet. Ellenőrizze újra egy kis idő múlva. 2.) Ha a fenti lépés nem válik, és a munkaterület az ingyenes Termékváltozat alatt áll, majd ellenőrizze a munkaterület használati itt ellenőrzése túllépte a kvótát, más hivatkoznak – gyakori kérdések további információt. "?

Mivel előfordulhat, hogy megjelenik ez az üzenet:
- A TRAFFIC Analytics nemrég lett engedélyezve, és előfordulhat, hogy nem még rendelkezik összesítve elegendő adat ahhoz, hogy egy értelmes elemzéseket.
- A Log Analytics-munkaterület az ingyenes verzió használ, és túllépte a kvótát. Előfordulhat, hogy szeretné használni a munkaterület egy nagyobb kapacitással.
    
Ha a probléma továbbra is fennáll, a aggályokat vethet a [User voice fórumán](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Mi történik, ha ez az üzenet jelenik meg: "Erőforrásadatok (topológia érhetők) el, nem tranzakciós adatok megjelenését. Tekintse meg az erőforrások adatait, és további információkért tekintse meg – gyakori kérdések, ide. "?

Az erőforrások információk jelennek meg az irányítópult; azonban nem flow-hoz kapcsolódó statisztikákat jelen. Adatok nem lehetnek jelen nincsenek az erőforrások közötti kommunikációs folyamatok miatt. 60 percig várjon, és újbóli ellenőrzése ennyi idő állapotát. Ha a probléma tartósan fennáll, és biztos benne, hogy létezik-e erőforrások közötti kommunikációs folyamatok, a aggályokat vethet a [User voice fórumán](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Konfigurálhatom úgy a traffic analytics PowerShell-lel vagy az Azure Resource Manager-sablon, vagy az ügyfél?

A traffic analytics 6.2.1 verzió frissítésétől kezdve a Windows PowerShell segítségével konfigurálhatja. Csoportforgalom naplózása és a traffic analytics egy adott NSG a Set parancsmag használatával, lásd: [Set-AzNetworkWatcherConfigFlowLog](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkwatcherconfigflowlog). A flow naplózási és a traffic analytics állapota egy adott NSG lekéréséhez lásd: [Get-AzNetworkWatcherFlowLogStatus](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkwatcherflowlogstatus).

Jelenleg a traffic analytics konfigurálása Azure Resource Manager-sablon nem használható.

A traffic analytics konfigurálása egy Azure Resource Manager-ügyfél használatával, lásd az alábbi példákat.

**Parancsmag-példával beállítása:**
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
**Parancsmag-példával lekérése:**
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


## <a name="how-is-traffic-analytics-priced"></a>Hogyan van a Traffic Analytics díjszabása?

A TRAFFIC Analytics forgalmi díjas. A mérési alapján flow naplózási adatok feldolgozása a szolgáltatás által, és a Log Analytics-munkaterület-naplók tárolásához, a létrejövő fokozott. 

Ha például forgalomért a [tarifacsomagjának](https://azure.microsoft.com/pricing/details/network-watcher/), USA nyugati középső régiójában, figyelembe véve, ha a Folyamatnaplók a Traffic Analytics által feldolgozott storage-fiókban tárolt adatok 10 GB-ot pedig a Log Analytics-munkaterület betöltött továbbfejlesztett naplók 1 GB-os, majd a alkalmazható díjak a következők: 10 x 2.3$ + 1 x 2.76$ = 25.76$

## <a name="how-frequently-does-traffic-analytics-process-data"></a>Milyen gyakran a Traffic Analytics adatok feldolgozása?

Tekintse meg a [összesítési adatszakasz](https://docs.microsoft.com/en-us/azure/network-watcher/traffic-analytics-schema#data-aggregation) a Traffic Analytics séma és adatok összesítési dokumentum

## <a name="how-does-traffic-analytics-decide-that-an-ip-is-malicious"></a>Hogyan nem a Traffic Analytics eldönteni, hogy egy IP-cím rosszindulatú? 

A TRAFFIC Analytics a Microsoft belső fenyegetés intelligencia rendszerek fogadniuk, kártékony IP-címet használ. Ezek a rendszerek kihasználni a különféle telemetriai forrásra, például egy Microsoft-termékek és szolgáltatások, a Microsoft Digital Crimes Unit (DCU), a Microsoft Security Response Center (MSRC), és külső hírcsatornák, és állítsa össze a megkönnyítése intelligencia rengeteg. Ezen adatok némelyike történik Mircosoft. Ha egy ismert IP-címet az első megjelölt malicios, hozzon létre egy támogatási jegyet a részleteket.

## <a name="how-can-i-set-alerts-on-traffic-analytics-data"></a>Hogyan állíthatok be riasztások a Traffic Analytics-adatok?

A TRAFFIC Analytics nincs beépített támogatás riasztásokat. Azonban mivel a Traffic Analytics-adatok a Log Analytics rendszer is egyéni lekérdezéseket írni, és riasztásokat állíthat be őket. Lépések:
- A Traffic Analytics Log Analytics is használhat a rövid hivatkozás. 
- Használja a [séma itt dokumentált](traffic-analytics-schema.md) a lekérdezések írása 
- Kattintson az "Új riasztási szabály" a riasztás létrehozása
- Tekintse meg [log riasztások dokumentáció](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/alerts-log) a riasztás létrehozása

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Hogyan navigálhat a földrajzi nézet a billentyűzet használatával?

A földrajzi térképen lap két fő szakaszokat tartalmazza:
    
- **Banner**: A földrajzi térkép felső szalagja gombok segítségével válassza ki a terjesztési forgalomszűrőinek (például üzembe helyezés, országok érkező forgalmat, és rosszindulatú) biztosít. Amikor kiválaszt egy gombot, a megfelelő szűrő alkalmazása a térképen. Például ha az aktív gombot választja, a térkép kiemeli a központi telepítésben az aktív adatközpontokban.
- **Térkép**: A szalagcím alatt a térkép szakasz bemutatja, az adatforgalom eloszlása az Azure-adatközpontok és az országok között.
    
### <a name="keyboard-navigation-on-the-banner"></a>A szalagcím a billentyűzetnavigációt
    
- Alapértelmezés szerint a kijelölést a transzparens földrajzi térképen oldalán a az "Azure tartományvezérlők" szűrőt.
- Másik szűrő áthelyezéséhez használja vagy a `Tab` vagy a `Right arrow` kulcsot. Ugrás az előző, használja vagy a `Shift+Tab` vagy a `Left arrow` kulcsot. Előre navigációs balról jobbra, felülről lefelé felső követ.
- Nyomja meg `Enter` vagy a `Down` nyílbillentyű kiválasztott szűrőt alkalmazza. Szűrőkijelölés és a központi telepítés alapján a térképen szakaszában egy vagy több csomópont emel ki.
- Váltás a Szalagcím és térkép, nyomja le a `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-map"></a>A térképen billentyűzetnavigációt
    
- Kiválasztott szalagcím bármely szűrőt és lenyomás után `Ctrl+F6`, a fókusz a kijelölt csomópontok egyikére (**Azure-adatközpont** vagy **ország/régió**) a leképezési nézetben.
- Használja a térkép más kiemelt csomópontjaira áthelyezéséhez `Tab` vagy a `Right arrow` előre adatátviteli kulcsával. Használat `Shift+Tab` vagy a `Left arrow` kulcs az előző verziókkal való áthelyezését.
- Válassza ki a kijelölt csomópont a térképen, használja a `Enter` vagy `Down arrow` kulcsot.
- Minden ilyen csomópontok kijelölését, a fókusz a **információs eszköz mező** a csomópontról. A lezárt gomb mozgatása fókusz alapértelmezés szerint a **információs eszköz mező**. További belüli áthelyezése a **Box** megtekintéséhez, használja `Right arrow` és `Left arrow` billentyűk használatával válthat előre és hátra, illetve. Billentyű `Enter` ugyanazon hatása, mint kiválasztania a fókuszban lévő gombot a **információs eszköz mező**.
- Amikor lenyomja `Tab` során a hangsúly a **információs eszköz mező**, a fókusz a végpontok a azonos kontinensen, mint a kijelölt csomópont. Használja a `Right arrow` és `Left arrow` billentyűk használatával válthat ezen végpontokon keresztül.
- Más végpontok folyamat vagy kontinensen fürtök áthelyezéséhez használja `Tab` előre mozgását és `Shift+Tab` az előző verziókkal való áthelyezését.
- Amikor a fókusz a van **kontinens fürtök**, használja a `Enter` vagy `Down` nyíl billentyűk kiemelheti a végpontok a kontinensen-fürtben. Való áthelyezéséhez végpontok és a Bezárás gombra a az információs dobozt kontinensen fürt használja, vagy a `Right arrow` vagy `Left arrow` előre és visszafelé irányuló adatáthelyezési, illetve kulcsára. Minden végponton használható `Shift+L` váltson át a kapcsolati sor a kijelölt csomópont a végponthoz. Nyomja le `Shift+L` újra, hogy a kiválasztott végpont át.
        
### <a name="keyboard-navigation-at-any-stage"></a>Bármely szakaszában billentyűzetnavigációt
    
- `Esc` a kibontott kijelölés bezárása.
- A `Up arrow` kulcs ugyanaz a művelet, hajt végre `Esc`. A `Down arrow` kulcs ugyanaz a művelet, hajt végre `Enter`.
- Használat `Shift+Plus` nagyítása, és `Shift+Minus` kicsinyítéshez.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Hogyan navigálhat a virtuális hálózati topológia e nézetében a billentyűzet használatával?

A virtuális hálózatok topológia lap két fő szakaszokat tartalmazza:
    
- **Banner**: A virtuális hálózatok topológia felső szalagja gombok segítségével válassza ki a terjesztési forgalomszűrőinek (például a csatlakoztatott virtuális hálózatok, az leválasztott virtuális hálózatok és a nyilvános IP-címek) biztosít. Amikor kiválaszt egy gombot, a megfelelő szűrő alkalmazása a következő topológiaműveletek. Például ha az aktív gombot választja, a topológia kiemeli az aktív virtuális hálózatok a központi telepítésben.
- **Topológia**: A szalagcím alatt a topológia című szakaszt a virtuális hálózatok közötti adatforgalom eloszlása látható.
    
### <a name="keyboard-navigation-on-the-banner"></a>A szalagcím a billentyűzetnavigációt
    
- Alapértelmezés szerint a kijelölést a szalagcím a virtuális hálózatok topológia oldalon a "Csatlakoztatott virtuális hálózatok" szűrőt.
- Másik szűrő való áthelyezéséhez használja a `Tab` billentyűt a folytatáshoz. Ugrás az előző, használja a `Shift+Tab` kulcsot. Előre navigációs balról jobbra, felülről lefelé felső követ.
- Nyomja meg `Enter` a kiválasztott szűrők alkalmazásához. A szűrőkijelölés és a központi telepítés alapján, a topológia szakaszában egy vagy több csomópontok (virtuális hálózat) emel ki.
- Váltás a Szalagcím és a topológiát, nyomja le a `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>A következő topológiaműveletek billentyűzetnavigációt
    
- Kiválasztott szalagcím bármely szűrőt és lenyomás után `Ctrl+F6`, a fókusz a kijelölt csomópontok egyikére (**VNet**) a topológia e nézetében.
- Szeretné áthelyezni a topológia e nézetében más kijelölt csomópontoknak, használja a `Shift+Right arrow` előre adatátviteli kulcsával. 
- Kiemelt csomópontokon fókusz áthelyezése a **információs eszköz mező** a csomópont. Alapértelmezés szerint fókusz áthelyezése a **további részleteket** gombot a **információs eszköz mező**. További belüli áthelyezése a **Box** megtekintéséhez, használja a `Right arrow` és `Left arrow` billentyűk használatával válthat előre és hátra, illetve. Billentyű `Enter` ugyanazon hatása, mint kiválasztania a fókuszban lévő gombot a **információs eszköz mező**.
- Az összes ilyen csomópontok kijelölését, keresse fel a kapcsolatokat, egy billentyűkombináció lenyomásával a `Shift+Left arrow` kulcsot. Fókusz áthelyezése a **információs eszköz mező** , hogy a kapcsolat. Bármikor, a fókusz is megjelenítjük a csomópontnak lenyomásával `Shift+Right arrow` újra.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Hogyan navigálhat az alhálózat topológia e nézetében a billentyűzet használatával?

A virtuális alhálózatok topológia lap két fő szakaszokat tartalmazza:
    
- **Banner**: A virtuális alhálózatok topológia felső szalagja gombok segítségével válassza ki a terjesztési forgalomszűrőinek (például aktív, a közepes és az átjáró alhálózatok) biztosít. Amikor kiválaszt egy gombot, a megfelelő szűrő alkalmazása a következő topológiaműveletek. Például ha az aktív gombot választja, a topológia kiemeli az aktív virtuális alhálózat, a központi telepítésben.
- **Topológia**: A szalagcím alatt a topológia című szakaszt a virtuális alhálózatok közötti adatforgalom eloszlása látható.
    
### <a name="keyboard-navigation-on-the-banner"></a>A szalagcím a billentyűzetnavigációt
    
- Alapértelmezés szerint a kijelölést a szalagcím a virtuális alhálózatok topológia oldalon a "Alhálózatok" szűrőt.
- Másik szűrő való áthelyezéséhez használja a `Tab` billentyűt a folytatáshoz. Ugrás az előző, használja a `Shift+Tab` kulcsot. Előre navigációs balról jobbra, felülről lefelé felső követ.
- Nyomja meg `Enter` a kiválasztott szűrők alkalmazásához. Szűrőkijelölés és a központi telepítés alapján, a topológia szakaszában egy vagy több csomópontot (alhálózat) emel ki.
- Váltás a Szalagcím és a topológiát, nyomja le a `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>A következő topológiaműveletek billentyűzetnavigációt
    
- Kiválasztott szalagcím bármely szűrőt és lenyomás után `Ctrl+F6`, a fókusz a kijelölt csomópontok egyikére (**alhálózati**) a topológia e nézetében.
- Szeretné áthelyezni a topológia e nézetében más kijelölt csomópontoknak, használja a `Shift+Right arrow` előre adatátviteli kulcsával. 
- Kiemelt csomópontokon fókusz áthelyezése a **információs eszköz mező** a csomópont. Alapértelmezés szerint fókusz áthelyezése a **további részleteket** gombot a **információs eszköz mező**. További belüli áthelyezése a **Box** megtekintéséhez, használja `Right arrow` és `Left arrow` billentyűk használatával válthat előre és hátra, illetve. Billentyű `Enter` ugyanazon hatása, mint kiválasztania a fókuszban lévő gombot a **információs eszköz mező**.
- Az összes ilyen csomópontok kijelölését, keresse fel a kapcsolatokat, egy lenyomásával `Shift+Left arrow` kulcsot. Fókusz áthelyezése a **információs eszköz mező** , hogy a kapcsolat. Bármikor, a fókusz is megjelenítjük a csomópontnak lenyomásával `Shift+Right arrow` újra.    

