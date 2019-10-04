---
title: Azure Traffic Analytics – gyakori kérdések | Microsoft Docs
description: Választ kaphat a Traffic Analytics szolgáltatással kapcsolatos leggyakrabban feltett kérdésekre.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: kumud
ms.openlocfilehash: 45200e7620326dedcee92c579843e61bb07ff68e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68610245"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Traffic Analytics gyakori kérdések

Ez a cikk az Azure Network Watcher Traffic Analytics szolgáltatásával kapcsolatos leggyakrabban felmerülő kérdéseket ismerteti.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Mik a Traffic Analytics használatának előfeltételei?

Traffic Analytics a következő előfeltételek szükségesek:

- Network Watcher engedélyezett előfizetés.
- Hálózati biztonsági csoport (NSG) folyamatábrája engedélyezve a figyelni kívánt NSG.
- Egy Azure Storage-fiók a nyers folyamat naplófájljainak tárolására.
- Egy Azure Log Analytics munkaterület olvasási és írási hozzáféréssel.

A Traffic Analytics engedélyezéséhez a fióknak a következők valamelyikét kell teljesítenie:

- A fióknak a következő szerepköralapú hozzáférés-vezérlési (RBAC) szerepkörök valamelyikével kell rendelkeznie az előfizetés hatókörében: tulajdonos, közreműködő, olvasó vagy hálózati közreműködő.
- Ha a fiókja nincs hozzárendelve az egyik korábban felsorolt szerepkörhöz, hozzá kell rendelni egy egyéni szerepkörhöz, amelyhez az előfizetési szinten a következő műveletek vannak hozzárendelve.
            
    - Microsoft.Network/applicationGateways/read
    - Microsoft.Network/connections/read
    - Microsoft.Network/loadBalancers/read 
    - Microsoft.Network/localNetworkGateways/read 
    - Microsoft.Network/networkInterfaces/read 
    - Microsoft.Network/networkSecurityGroups/read 
    - Microsoft.Network/publicIPAddresses/read
    - Microsoft. Network/routeTables/READ
    - Microsoft.Network/virtualNetworkGateways/read 
    - Microsoft.Network/virtualNetworks/read
        
Az előfizetéshez felhasználóhoz rendelt szerepkörök ellenőrzését:

1. Jelentkezzen be az Azure-ba a **login-AzAccount**használatával. 

2. Válassza ki a szükséges előfizetést a **Select-AzSubscription**használatával. 

3. Az adott felhasználóhoz rendelt összes szerepkör listázásához használja a **Get-AzRoleAssignment-SignInName [felhasználói e-mail]-IncludeClassicAdministrators**lehetőséget. 

Ha nem lát kimenetet, forduljon a megfelelő előfizetés-rendszergazdához, és kérjen hozzáférést a parancsok futtatásához. További részletekért lásd: [szerepköralapú hozzáférés-vezérlés kezelése Azure PowerShellsal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).


## <a name="in-which-azure-regions-is-traffic-analytics-available"></a>Mely Azure-régiók Traffic Analytics elérhetők?

A NSG Traffic Analytics a következő támogatott régiókban használható:
- Közép-Kanada
- USA nyugati középső régiója
- East US
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

A Log Analytics munkaterület a következő régiókban kell, hogy legyen:
- Közép-Kanada
- USA nyugati középső régiója
- USA nyugati régiója
- USA nyugati régiója, 2.
- USA déli középső régiója
- USA középső régiója
- East US
- USA 2. keleti régiója
- Közép-Franciaország
- Nyugat-Európa
- Észak-Európa
- Az Egyesült Királyság déli régiója
- Kelet-Ausztrália
- Délkelet-Ausztrália
- Kelet-Ázsia
- Délkelet-Ázsia 
- Korea középső régiója
- Közép-India
- Kelet-Japán
- USA-beli államigazgatás – Virginia

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Engedélyezhető a NSG a különböző régiókban, mint a saját munkaterület?

Igen, ezek a NSG különböző régiókban lehetnek, mint a Log Analytics munkaterülete.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Több NSG is konfigurálható egyetlen munkaterületen belül?

Igen.

## <a name="can-i-use-an-existing-workspace"></a>Használhatok egy meglévő munkaterületet?

Igen. Ha egy meglévő munkaterületet választ ki, győződjön meg arról, hogy az új lekérdezési nyelvre lett migrálva. Ha nem szeretné frissíteni a munkaterületet, egy újat kell létrehoznia. További információ az új lekérdezési nyelvről: [Azure monitor naplók frissítése az új](../log-analytics/log-analytics-log-search-upgrade.md)naplók keresésére.

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>Használhatom az Azure Storage-fiókomat egy előfizetésben, és az Log Analytics munkaterületem egy másik előfizetésben is szerepelhet?

Igen, az Azure Storage-fiókja lehet egy előfizetésben, és a Log Analytics munkaterület egy másik előfizetésben is lehet.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Tárolhatok nyers naplókat egy másik előfizetésben?

Nem. A nyers naplókat bármely olyan Storage-fiókban tárolhatja, ahol a NSG engedélyezve van. A Storage-fióknak és a nyers naplóknak azonban ugyanahhoz az előfizetéshez és régióhoz kell tartoznia.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Mi a teendő, ha nem találok NSG a Traffic Analytics szolgáltatáshoz egy "nem található" hiba miatt?

Válasszon egy támogatott régiót. Ha nem támogatott régiót választ, "nem található" hibaüzenet jelenik meg. A támogatott régiók a cikk korábbi részében vannak felsorolva.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Mi a helyzet, ha a "nem sikerült betölteni" állapotot, a NSG flow-naplók lapon?

A megfelelő működés érdekében regisztrálni kell a Microsoft. bepillantást nyújtó szolgáltatót a flow naplózásához. Ha nem biztos abban, hogy regisztrálva van-e a Microsoft. betekintő szolgáltató az előfizetéséhez, cserélje le az *xxxxx-xxxxx-xxxxxx-XXXX* parancsot a következő parancsra, és futtassa a következő parancsokat a powershellből:

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Konfiguráltam a megoldást. Miért nem látok semmit az irányítópulton?

Az irányítópult első alkalommal akár 30 percet is igénybe vehet. A megoldásnak először elegendő adatokat kell összeállítania ahhoz, hogy értelmes elemzéseket lehessen származtatni. Ezután jelentéseket hoz létre. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Mi a teendő, ha a következő üzenet jelenik meg: "A munkaterületen nem találhatók a megadott időintervallumra vonatkozó adategységek. Próbálja meg módosítani az időtartamot, vagy válasszon másik munkaterületet. "?

Próbálkozzon a következő beállításokkal:
- Módosítsa az időintervallumot a felső sávon.
- Válasszon egy másik Log Analytics munkaterületet a felső sávon.
- Ha nemrég engedélyezte, próbálja meg elérni a Traffic Analytics szolgáltatást 30 perc elteltével.
    
Ha a probléma továbbra is fennáll, aggodalomra ad [hangot a felhasználói](https://feedback.azure.com/forums/217313-networking?category_id=195844)hangfórumban.

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Mi a teendő, ha a következő üzenet jelenik meg: "A NSG-folyamatok naplózásának első alkalommal történő elemzése. A folyamat végrehajtása 20-30 percet is igénybe vehet. Kis idő elteltével térjen vissza. 2.) Ha a fenti lépés nem működik, és a munkaterület az ingyenes SKU alatt van, akkor ellenőrizze, hogy a munkaterület használatban van-e a kvóta feletti ellenőrzéshez. További információért tekintse meg a GYIK című témakört. "?

Ez az üzenet a következő okból jelenhet meg:
- Traffic Analytics a közelmúltban engedélyezték, és lehetséges, hogy még nincs elegendő adata ahhoz, hogy értelmes elemzéseket lehessen származtatni.
- Az Log Analytics munkaterület ingyenes verzióját használja, és túllépte a kvóta korlátait. Előfordulhat, hogy egy nagyobb kapacitású munkaterületet kell használnia.
    
Ha a probléma továbbra is fennáll, aggodalomra ad [hangot a felhasználói](https://feedback.azure.com/forums/217313-networking?category_id=195844)hangfórumban.
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Mi a teendő, ha a következő üzenet jelenik meg: "Úgy tűnik, hogy az erőforrások adatai (topológia) és a folyamatok adatai nincsenek. Eközben ide kattintva megtekintheti az erőforrások adatait, és további információkért tekintse meg a gyakori kérdéseket. "?

Az erőforrások információit látja az irányítópulton; azonban nem állnak rendelkezésre flow-vel kapcsolatos statisztikák. Előfordulhat, hogy az információk nem jelennek meg az erőforrások közötti kommunikáció során. Várjon 60 percet, majd vizsgálja újra az állapotot. Ha a probléma továbbra is fennáll, és biztos abban, hogy a kommunikáció több erőforrás között is fennáll, aggodalomra ad választ a [felhasználói](https://feedback.azure.com/forums/217313-networking?category_id=195844)hangfórumban.

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Konfigurálható a Traffic Analytics a PowerShell vagy egy Azure Resource Manager sablon vagy ügyfél használatával?

A Traffic Analytics a 6.2.1-es verziótól kezdve a Windows PowerShell használatával konfigurálható. A flow naplózásának és a Traffic Analytics konfigurálásához egy adott NSG a Set parancsmag használatával: [set-AzNetworkWatcherConfigFlowLog](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkwatcherconfigflowlog). A flow naplózásának és a Traffic Analytics állapotának egy adott NSG való lekéréséhez lásd: [Get-AzNetworkWatcherFlowLogStatus](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkwatcherflowlogstatus).

Jelenleg nem használhat Azure Resource Manager sablont a Traffic Analytics konfigurálásához.

A Traffic Analytics Azure Resource Manager-ügyfél használatával történő konfigurálásához tekintse meg az alábbi példákat.

**Parancsmag beállítása – példa:**
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
**Példa a parancsmag beszerzésére:**
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


## <a name="how-is-traffic-analytics-priced"></a>Hogyan Traffic Analytics díjszabása?

A Traffic Analytics mért. A mérés a folyamat naplófájljainak a szolgáltatás általi feldolgozásán alapul, és egy Log Analytics munkaterületen tárolja a létrejövő bővített naplókat. 

Például [Ha a flow](https://azure.microsoft.com/pricing/details/network-watcher/)az USA nyugati középső régióját veszi figyelembe, és egy Traffic Analytics által feldolgozott Storage-fiókban tárolt adatok naplózása történik meg, akkor a log Analytics munkaterületen betöltött, továbbfejlesztett naplók 1 GB, a vonatkozó díjak a következők: 10 x $2,3 + 1 x $2,76 = $25,76

## <a name="how-frequently-does-traffic-analytics-process-data"></a>Milyen gyakran Traffic Analytics az adatfeldolgozás?

Tekintse át [](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema#data-aggregation) Traffic Analytics séma és az adatösszesítési dokumentum adatösszesítési szakaszát.

## <a name="how-does-traffic-analytics-decide-that-an-ip-is-malicious"></a>Hogyan működik Traffic Analytics úgy, hogy az IP-cím rosszindulatú? 

A Traffic Analytics a Microsoft belső veszélyforrások felderítésére szolgáló rendszerekre támaszkodik, hogy az IP-címet rosszindulatúként tekintsék. Ezek a rendszerek különféle telemetria-forrásokat, például Microsoft-termékeket és-szolgáltatásokat, a Microsoft digitális bűncselekmények egységét (DCU), a Microsoft Security Response centert (MSRC) és a külső hírcsatornákat használják, és rengeteg intelligenciát építenek. Ezek közül néhányat a Microsoft belső. Ha egy ismert IP-cím rosszindulatúként van megjelölve, egy támogatási jegyet is megtudhat a részletekről.

## <a name="how-can-i-set-alerts-on-traffic-analytics-data"></a>Hogyan állíthatom be a riasztásokat a Traffic Analytics-adathalmazon?

A Traffic Analytics nem rendelkezik beépített támogatással a riasztásokhoz. Mivel azonban Traffic Analytics a rendszer az adattárolást Log Analytics tárolja, egyéni lekérdezéseket írhat, és riasztásokat állíthat be rajtuk. Lépéseket
- A Traffic Analytics Log Analytics shortlink is használhatja. 
- A lekérdezések megírásához használja az [itt dokumentált sémát](traffic-analytics-schema.md) 
- A riasztás létrehozásához kattintson az "új riasztási szabály" elemre.
- A riasztás [](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) létrehozásához tekintse meg a riasztások dokumentációját

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Hogyan lehet navigálni a Geo Térkép nézet billentyűzetének használatával?

A földrajzi Térkép oldal két fő szakaszt tartalmaz:
    
- **Szalagcím**: A Geo-Térkép tetején található szalagcím a forgalmi terjesztési szűrők (például üzembe helyezés, az országok/régiók közötti forgalom és a rosszindulatú) kiválasztására szolgáló gombokat tartalmaz. Egy gomb kiválasztásakor a rendszer a megfelelő szűrőt alkalmazza a térképen. Ha például az aktív gombot választja, a Térkép kiemeli az üzemelő példány aktív adatközpontját.
- **Térkép**: A szalagcím alatt a Térkép szakasz a forgalom eloszlását mutatja az Azure-adatközpontok és országok/régiók között.
    
### <a name="keyboard-navigation-on-the-banner"></a>A szalagcímen lévő billentyűzetes Navigálás
    
- Alapértelmezés szerint a szalagcím földrajzi Térkép oldalán a kijelölés az "Azure DCs" szűrő.
- Másik szűrőre való áthelyezéshez használja a `Tab` `Right arrow` vagy a kulcsot. A visszafelé mozgatáshoz használja a `Shift+Tab` `Left arrow` vagy a kulcsot. A navigáció továbbítása balról jobbra történik, majd felülről lefelé haladva.
- Nyomja `Enter` meg vagy `Down` a nyílbillentyűt a kiválasztott szűrő alkalmazásához. A szűrők kiválasztása és központi telepítése alapján a Térkép szakasz egy vagy több csomópontja ki van emelve.
- A szalagcím és a Térkép közötti váltáshoz `Ctrl+F6`nyomja meg a gombot.
        
### <a name="keyboard-navigation-on-the-map"></a>Navigálás a térképen a billentyűzeten
    
- Miután kiválasztott egy szűrőt a szalagcímre, és megnyomta `Ctrl+F6`a gombot, a fókusz az egyik kiemelt csomópontra (**Azure Datacenter** vagy **ország/régió**) kerül át a Térkép nézetben.
- A térképen található többi kijelölt csomópontra való áthelyezéshez használja `Tab` a vagy a `Right arrow` kulcsot a továbbítási mozgáshoz. Használja `Shift+Tab` a vagy `Left arrow` a billentyűt a visszafelé mozgatáshoz.
- A Térkép bármelyik Kiemelt csomópontjának kiválasztásához használja a `Enter` vagy `Down arrow` a kulcsot.
- Az ilyen csomópontok kiválasztásakor a fókusz áthelyezése a csomópont **információs eszköz mezőjébe** . Alapértelmezés szerint a fókusz a **tájékoztatási eszköz mezőben**lévő lezárt gombra lép. A **Box** nézeten belüli további áthelyezéshez használja `Right arrow` a `Left arrow` és a billentyűket a továbbításhoz és a visszafelé való mozgáshoz. A `Enter` gomb megnyomásakor a fókusz gombra kell kattintania a **tájékoztatási eszköz mezőben**.
- Amikor megnyomja `Tab` a fókuszt a **tájékoztatási eszköz mezőben**, a fókusz a kijelölt csomóponttal megegyező kontinensen lévő végpontokra kerül át. A és `Right arrow` `Left arrow` a kulcsok használatával áthelyezheti ezeket a végpontokat.
- Ha át szeretne térni más folyamat-végpontokra vagy kontinens- `Tab` fürtökre, használja `Shift+Tab` a továbbítási mozgáshoz és a visszafelé mozgatáshoz.
- Ha a fókusz a kontinensen lévő fürtökön található `Enter` , `Down` a vagy a nyílbillentyűk használatával kiemelheti a kontinensen belüli fürtben található végpontokat. Ha a végpontokon és a Bezárás gombra kattint a kontinens fürtjének információs mezőjében, használja a vagy `Right arrow` `Left arrow` a kulcsot a továbbításhoz és a visszafelé mozgáshoz. Bármelyik végponton használhatja `Shift+L` a kapcsolót a kiválasztott csomópontról a végpontra való váltáshoz. Az újbóli `Shift+L` megnyomásával átléphet a kiválasztott végpontra.
        
### <a name="keyboard-navigation-at-any-stage"></a>Billentyűzet-navigáció bármely fázisban
    
- `Esc`a kibontott kijelölés összecsukása.
- A `Up arrow` kulcs ugyanazokat a műveleteket hajtja végre, mint `Esc`a. A `Down arrow` kulcs ugyanazokat a műveleteket hajtja végre, mint `Enter`a.
- A `Shift+Plus` nagyításhoz és `Shift+Minus` a kicsinyítéshez használja a következőt:.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Hogyan lehet navigálni a virtuális hálózati topológia nézetben a billentyűzet használatával?

A virtuális hálózatok topológiájának lapja két fő szakaszt tartalmaz:
    
- **Szalagcím**: A virtuális hálózatok topológiájának tetején található szalagcím a forgalmi terjesztési szűrők (például a csatlakoztatott virtuális hálózatok, a leválasztott virtuális hálózatok és a nyilvános IP-címek) kiválasztására szolgáló gombokat tartalmaz. Egy gomb kiválasztásakor a rendszer a megfelelő szűrőt alkalmazza a topológián. Ha például az aktív gombot választja, a topológia kiemeli az üzemelő példány aktív virtuális hálózatait.
- **Topológia**: A szalagcím alatt a topológia szakasz a virtuális hálózatok közötti forgalom eloszlását mutatja.
    
### <a name="keyboard-navigation-on-the-banner"></a>A szalagcímen lévő billentyűzetes Navigálás
    
- Alapértelmezés szerint a szalagcímhez tartozó Virtual Networks topológia lapon a "Connected virtuális hálózatok" szűrő van kiválasztva.
- Másik szűrőre való áthelyezéshez használja a `Tab` kulcsot a továbbításhoz. A visszalépéshez használja a `Shift+Tab` kulcsot. A navigáció továbbítása balról jobbra történik, majd felülről lefelé haladva.
- Nyomja `Enter` meg a gombot a kiválasztott szűrő alkalmazásához. A szűrő kiválasztása és központi telepítése alapján a rendszer kiemel egy vagy több csomópontot (virtuális hálózatot) a topológia szakaszban.
- A szalagcím és a topológia közötti váltáshoz nyomja meg a `Ctrl+F6`gombot.
        
### <a name="keyboard-navigation-on-the-topology"></a>Billentyűzetes Navigálás a topológián
    
- Miután kiválasztott egy szűrőt a szalagcímen, és megnyomta `Ctrl+F6`a gombot, a fókusz a topológia nézet egyik kiemelt csomópontjára (**VNet**) kerül.
- A topológia nézet más kiemelt csomópontjaira való áthelyezéshez használja a `Shift+Right arrow` kulcsot a továbbítási mozgáshoz. 
- A Kiemelt csomópontokon a fókusz áthelyezése a csomópont **információs eszköz mezőjébe** . Alapértelmezés szerint a fókusz a **tájékoztatási eszköz mező** **További részletek** gombjára lép. A **Box** nézetben való további mozgáshoz használja a és `Right arrow` `Left arrow` a kulcsokat a továbbításhoz és a visszafelé való mozgáshoz. A `Enter` gomb megnyomásakor a fókusz gombra kell kattintania a **tájékoztatási eszköz mezőben**.
- Az ilyen csomópontok kiválasztásakor a kulcs megnyomásával megtekintheti az `Shift+Left arrow` összes kapcsolatát. A fókusz a kapcsolatok **információs eszköz mezőjére** lép. A fókuszt bármikor visszahelyezheti a csomópontra az újbóli megnyomásával `Shift+Right arrow` .
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Hogyan lehet navigálni a billentyűzettel az alhálózati topológia nézetben?

A virtuális alhálózatok topológiája lap két fő szakaszt tartalmaz:
    
- **Szalagcím**: A virtuális alhálózatok topológiájának tetején található szalagcím a forgalmi terjesztési szűrők (például aktív, közepes és átjárós alhálózatok) kiválasztására szolgáló gombokat tartalmaz. Egy gomb kiválasztásakor a rendszer a megfelelő szűrőt alkalmazza a topológián. Ha például az aktív gombot választja, a topológia kiemeli az aktív virtuális alhálózatot az üzemelő példányban.
- **Topológia**: A szalagcím alatt a topológia szakasz a virtuális alhálózatok közötti forgalom eloszlását mutatja.
    
### <a name="keyboard-navigation-on-the-banner"></a>A szalagcímen lévő billentyűzetes Navigálás
    
- Alapértelmezés szerint a szalagcímhez tartozó virtuális alhálózatok topológiájának kijelölése az "alhálózat" szűrő.
- Másik szűrőre való áthelyezéshez használja a `Tab` kulcsot a továbbításhoz. A visszalépéshez használja a `Shift+Tab` kulcsot. A navigáció továbbítása balról jobbra történik, majd felülről lefelé haladva.
- Nyomja `Enter` meg a gombot a kiválasztott szűrő alkalmazásához. A szűrők kiválasztása és központi telepítése alapján a rendszer kiemel egy vagy több csomópontot (alhálózatot) a topológia szakaszban.
- A szalagcím és a topológia közötti váltáshoz nyomja meg a `Ctrl+F6`gombot.
        
### <a name="keyboard-navigation-on-the-topology"></a>Billentyűzetes Navigálás a topológián
    
- Miután kiválasztott egy szűrőt a szalagcímen, és megnyomta `Ctrl+F6`a gombot, a fókusz a topológia nézet egyik kijelölt csomópontjára (alhálózata) helyezi át.
- A topológia nézet más kiemelt csomópontjaira való áthelyezéshez használja a `Shift+Right arrow` kulcsot a továbbítási mozgáshoz. 
- A Kiemelt csomópontokon a fókusz áthelyezése a csomópont **információs eszköz mezőjébe** . Alapértelmezés szerint a fókusz a **tájékoztatási eszköz mező** **További részletek** gombjára lép. A **Box** nézeten belüli további áthelyezéshez használja `Right arrow` a `Left arrow` és a billentyűket a továbbításhoz és a visszafelé való mozgáshoz. A `Enter` gomb megnyomásakor a fókusz gombra kell kattintania a **tájékoztatási eszköz mezőben**.
- Az ilyen csomópontok kiválasztásakor a kulcs megnyomásával `Shift+Left arrow` megtekintheti az összes kapcsolatát, egyet pedig eggyel. A fókusz a kapcsolatok **információs eszköz mezőjére** lép. A fókuszt bármikor visszahelyezheti a csomópontra az újbóli megnyomásával `Shift+Right arrow` .    

