---
title: Azure-forgalmat analytics kapcsolatos gyakori kérdések |} Microsoft Docs
description: Egyes forgalom analytics leggyakrabban feltett kérdésekre adott válaszok.
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
ms.openlocfilehash: a4b87d92751c84d96bc70915d16adae7943c145e
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062877"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Gyakori kérdések a forgalom elemzés

Ez a cikk gyűjt egy helyen sok Azure hálózati figyelőt a forgalom analytics leggyakrabban feltett kérdésekre.

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Mik azok a forgalom analytics használatának előfeltételei

Forgalom elemzés szükséges a következő előfeltételek teljesülését:

- Engedélyezett hálózati figyelőt előfizetés.
- Hálózati biztonsági csoport (NSG) folyamat naplók a figyelni kívánt NSG-k engedélyezve.
- Egy Azure Storage-fiók tárolására nyers flog naplókat.
- Egy Azure Naplóelemzés munkaterületen, az olvasási és írási hozzáférést.

Fiókja meg kell felelnie az alábbi forgalom analytics engedélyezése:

- A fiókot hozzá kell rendelni a következő szerepkörben, az előfizetés szintjén: fiókadminisztrátor, szolgáltatás-rendszergazda vagy társadminisztrátoraként.
- A fiók az előfizetési hatókört rendelkeznie kell a következő szerepköralapú hozzáférés-vezérlést (RBAC) szerepkörök egyikét sem: tulajdonos, közreműködő, olvasó vagy hálózat közreműködő.
- Ha a fiókjához nincs hozzárendelve a fent felsorolt szerepkörök egyikére, akkor hozzá kell rendelni egy egyéni biztonsági szerepkört, amely hozzá van rendelve a következő műveletek, az előfizetés szintjén.
            
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
        
A felhasználó az előfizetéshez rendelt szerepkörök ellenőrzése:

1. Jelentkezzen be az Azure-bA **Login-AzureRmAccount**. 

2. Válassza ki a kötelező előfizetést használatával **Select-AzureRmSubscription**. 

3. Kilistázhatja az adott felhasználóhoz rendelt összes szerepkör **Get-AzureRmRoleAssignment - SignInName [a felhasználó e-mail címe] - IncludeClassicAdministrators**. 

Kimenetet nem látható, ha lépjen kapcsolatba a megfelelő előfizetés rendszergazda is elérheti a parancsok futtatásához. További részletekért lásd: [kezelése szerepköralapú hozzáférés-vezérlés az Azure PowerShell](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-powershell).


## <a name="in-which-azure-regions-are-traffic-analytics-available"></a>Mely Azure-ban régiók érhetők el forgalom analytics?

Az NSG-ket, sem a következő régiókban is használhatja a forgalom analytics: nyugati középső Régiójában, USA keleti régiója, USA keleti régiója 2, északi középső Régiójában, déli középső Régiójában, USA középső RÉGIÓJA, USA nyugati régiója, USA 2. nyugati régiója, Nyugat-Európa, Észak-Európa, Egyesült Királyság nyugati régiója, Egyesült Királyság déli régiója, Kelet-Ausztrália, Ausztrália délkeleti és Délkelet-Ázsia. A Naplóelemzési munkaterület léteznie kell a nyugati középső Régiójában, USA keleti régiója, Nyugat-Európa, Egyesült Királyság déli régiója, Ausztrália délkeleti, vagy a Délkelet-Ázsia régióban.

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Az NSG-ket engedélyezhető a folyamatot is naplókat lehet, mint a saját munkaterület különböző régiókban?

Ezek az NSG-k Igen, mint a Naplóelemzési munkaterület különböző régiókban lehet.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Egyetlen munkaterület konfigurálható több NSG-ket?

Igen.

## <a name="can-i-use-an-existing-workspace"></a>Használhatja a meglévő munkaterülethez?

Igen. Egy meglévő munkaterületen válassza ki, győződjön meg arról, hogy az új lekérdezés nyelvének áttelepült. Ha nem szeretné, hogy a munkaterület frissítése, létre kell hozzon létre egy újat. Az új lekérdezési nyelv kapcsolatos további információkért lásd: [Azure Naplóelemzés frissítsen az új naplófájl-keresési](../log-analytics/log-analytics-log-search-upgrade.md).

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-operations-management-suite-workspace-be-in-a-different-subscription"></a>Azure Storage-fiókom kell egy előfizetés, és egy másik előfizetésben kell az Operations Management Suite-munkaterülettel?

Igen, az Azure Storage-fiók lehet egy előfizetésre, és az Operations Management Suite-munkaterülettel lehet egy másik előfizetésben.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Tárolhatok nyers naplók egy másik előfizetésben?

Nem. Minden tárfiók, amelyekben engedélyezve van egy NSG folyamata naplók nyers naplók tárolhat. Azonban mind a tárfiók, és a nyers keresse meg az előfizetés és a régióban kell lennie.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Mi történik, ha egy NSG-t forgalom analytics "Nem található" hiba miatt nem tudja beállítani?

Válasszon egy támogatott régióban. Ha egy nem támogatott régióban, a "Nem található" hibaüzenetet kap. A támogatott régiók az ebben a cikkben találhatók.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Mi történik, ha az állapot kapok "nem sikerült betölteni," alatt az NSG folyamata naplók oldal?

A Microsoft.Insights szolgáltató működéséhez a naplózás folyamata regisztrálnia kell. Ha nem biztos abban, hogy regisztrálva van a Microsoft.Insights szolgáltató az előfizetéséhez, cserélje le a *xxxxx-xxxxx-xxxxxx-xxxx* a következő parancsot, és futtassa a következő parancsokat a PowerShell:

```powershell-interactive
**Select-AzureRmSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzureRmResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>A megoldás I konfigurált. Miért nem jelenik meg semmit az irányítópulton?

Az irányítópult megjelenését, amikor első alkalommal akár 30 percig is eltarthat. A megoldás először kell összesíteni elég adat ahhoz, hogy a jelentéssel bíró insights származtatni. Ezután jelentéseket hoz létre. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Mi történik, ha ez az üzenet jelenik meg: "nem található adatokról a munkaterületen kijelölt alatt az időtartam alatt. Próbálja meg módosítani a időtartam alatt, vagy válasszon másik munkaterület. "?

Próbálja meg a következő beállításokat:
- Az időintervallumot a felső sávon.
- Válasszon egy másik Naplóelemzési munkaterület a felső sávon.
- Ha nemrég engedélyezve lett, próbálkozzon a forgalom analytics elérése 30 perc elteltével.
    
Ha a probléma továbbra is fennáll, a aggodalomra a [felhasználói hang fórum](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Mi történik, ha ez az üzenet jelenik meg: "a NSG elemzése flow naplók először. Ez a folyamat 20-30 percet is igénybe vehet. Ellenőrizze újra egy kis idő múlva. 2.) Ha a fenti lépést nem működik, és a munkaterületen a szabad SKU alatt áll, majd ellenőrizze az munkaterület használatának itt ellenőrzése túllépte a kvótát, ellenkező esetben tekintse meg – gyakori kérdések további információt. "?

Előfordulhat, hogy tekintse meg ezt az üzenetet, mert:
- Forgalom analytics nemrég lett engedélyezve, és előfordulhat, hogy nem még van elegendő összesített adatait alkotják ahhoz, hogy a jelentéssel bíró insights származtatni.
- Az Operations Management Suite-munkaterület ingyenes verzióját használja, és az túllépte a kvótát. Szükség lehet a munkaterület használatához egy nagyobb kapacitással rendelkező átjáróeszközt.
    
Ha a probléma továbbra is fennáll, a aggodalomra a [felhasználói hang fórum](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Mi történik, ha ez az üzenet jelenik meg: "tűnik tudunk az erőforrások adatait (topológia) és a tranzakciós adatokat. Eközben Ide kattintva tekintse meg az erőforrások adatait, és gyakran ismételt kérdéseket további információért tekintse meg. "?

Ön az erőforrások információi az irányítópulton; azonban folyamat kapcsolatos jelenleg nincs jelen. Adatok nem jelen lehet miatt nem az erőforrások közötti kommunikációs forgalom. Várjon 60 percet, és újbóli állapotának ellenőrzéséhez. Ha a probléma továbbra is fennáll, és biztos benne, hogy létezik-e a források között kommunikációs forgalom, a aggodalomra a [felhasználói hang fórum](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Beállítható forgalom analytics PowerShell használatával vagy egy Azure Resource Manager-sablon, vagy az ügyfél?

Konfigurálhatja a forgalom analytics 6.2.1 verziójával kezdődően a Windows PowerShell használatával. Folyamat naplózás és a forgalom analytics egy adott NSG a Set-parancsmag használatával, lásd: [Set-AzureRmNetworkWatcherConfigFlowLog](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermnetworkwatcherconfigflowlog?view=azurermps-6.3.0). A folyamat naplózás és a forgalom analytics állapotának lekérése egy adott NSG-t, tekintse meg a [Get-AzureRmNetworkWatcherFlowLogStatus](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkwatcherflowlogstatus?view=azurermps-6.3.0).

Jelenleg forgalom analytics konfigurálása az Azure Resource Manager-sablon nem használható.

Forgalom analytics konfigurálása az Azure Resource Manager-ügyfél használatával, lásd az alábbi példákat.

**Példa parancsmagban beállítva:**
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
**Példa parancsmagban beolvasása:**
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



## <a name="how-is-traffic-analytics-priced"></a>Hogyan áron a forgalom analytics?

Forgalom analytics forgalmi díjas. A mérési alapul folyamata napló adatok feldolgozása a szolgáltatás, és a Naplóelemzési munkaterület-naplók tárolásához, a létrejövő fokozott. További részletekért lásd: a [terv árképzési](https://azure.microsoft.com/en-us/pricing/details/network-watcher/). 

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Hogyan lehet keresse meg a földrajzi nézet a billentyűzet használatával?

A földrajzi lap két fő részeket tartalmazza:
    
- **Transzparens**: az a földrajzi térképen tetején szalagcím biztosít forgalomszűrők terjesztési (például központi telepítés, országokból származó forgalmat, és az esetleg kártékony) gombra. Ha egy gombot választja, a megfelelő szűrő a térképen. Például ha az aktív gombra kattint, a a térkép az aktív adatközpontokban a központi telepítésben mutatja be.
- **Térkép**: a fejléc alatt a térkép szakasz Azure adatközpontjaiban és országok forgalom eloszlását jeleníti meg.
    
### <a name="keyboard-navigation-on-the-banner"></a>A szalagcím billentyűparancsokkal
    
- Alapértelmezés szerint az a szalagcím földrajzi térképen oldalon érték az "Azure tartományvezérlők" szűrőt.
- Áthelyezése egy másik szűrőt, vagy használja a `Tab` vagy a `Right arrow` kulcs. Ugrás az előző, használja vagy a `Shift+Tab` vagy a `Left arrow` kulcs. Navigációs balról jobbra, lefelé felső követ.
- Nyomja le az `Enter` vagy a `Down` nyílbillentyűt kijelölt szűrőt alkalmazza. Szűréses választás és a központi telepítés alapján, egy vagy több csomópont térkép szakaszban vannak kiemelve.
- Váltás a Szalagcím és térkép, nyomja meg az `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-map"></a>Billentyűparancsokkal a térképen
    
- Bármely szűrőt a szalagcím a választott és megnyomva után `Ctrl+F6`, fókusz egyik kijelölt csomópont (**Azure datacenter** vagy **ország vagy régió**) a leképezési nézetben.
- A térkép többi kiemelt csomópontjának áthelyezéséhez használja vagy `Tab` vagy a `Right arrow` kulcsát, előre. Használjon `Shift+Tab` vagy a `Left arrow` visszafelé adatátviteli kulcsát.
- A térkép összes kijelölt csomópont kiválasztásához használja a `Enter` vagy `Down arrow` kulcs.
- Az ilyen csomópontokon kiválasztását, a fókusz a **információk eszköz mezőben** a csomópont. Alapértelmezés szerint fókusz mozgatása a lezárt gombra a **információk eszköz mezőben**. További áthelyezése belül a **mezőben** megtekintéséhez használja `Right arrow` és `Left arrow` -kulcsok előre és hátra, illetve helyezze át. Nyomja le `Enter` rendelkezik a célzott parancsgomb ugyanaz, mintha a **információk eszköz mezőben**.
- Amikor lenyomja az `Tab` közben koncentrál a **információ eszköz írja be**, a fókusz a végpontok az azonos kontinensen, mint a kijelölt csomópont található. Használja a `Right arrow` és `Left arrow` Váltás a fenti végpontokkal kulcsok.
- Más folyamat végpontok vagy kontinensre fürtök áthelyezéséhez használja `Tab` az előre és `Shift+Tab` az előző verziókkal való mozgás.
- Amikor a fókusz a van **kontinensen fürtök**, használja a `Enter` vagy `Down` nyílbillentyűk jelölje ki a végpontok a kontinensre fürtben található. Használja váltás végpontok és a Bezárás gombra a kontinensre fürt információk lapján, a `Right arrow` vagy `Left arrow` előre és hátra mozgás, illetve kulcsát. A tetszőleges végpontot használhatja `Shift+L` váltson át a kapcsolat sor a kijelölt csomópont a végpontnak. Lenyomhatja `Shift+L` újra, hogy a kijelölt végponttanúsítvány át.
        
### <a name="keyboard-navigation-at-any-stage"></a>Bármely szakaszában billentyűparancsokkal
    
- `Esc` a kibontott kijelölés bezárása.
- A `Up arrow` kulcs hajtja végre a művelet, mintha `Esc`. A `Down arrow` kulcs hajtja végre a művelet, mintha `Enter`.
- Használjon `Shift+Plus` a nagyításhoz, és `Shift+Minus` kicsinyítéshez.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Hogyan lehet keresse meg a virtuális hálózati topológia nézetben a billentyűzet használatával?

A virtuális hálózatok topológia lapon két fő szakaszból áll:
    
- **Transzparens**: A szalagcím felső részén a virtuális hálózatok topológia biztosít a forgalom terjesztési szűrők (például virtuális hálózatokhoz csatlakozó leválasztott virtuális hálózatok és nyilvános IP-címek) gombra. Ha egy gombot választja, a megfelelő szűrő a topológia. Például ha az aktív gombra kattint, a topológia kiemeli az aktív virtuális hálózatok a központi telepítésben.
- **Topológia**: a fejléc alatt a topológia szakasz a virtuális hálózatok közötti forgalom eloszlása jeleníti meg.
    
### <a name="keyboard-navigation-on-the-banner"></a>A szalagcím billentyűparancsokkal
    
- Alapértelmezés szerint az a virtuális hálózatok topológia lapon a szalagcím érték a "Csatlakoztatott Vnetek" szűrőt.
- Helyezze át egy másik szűrőt, használja a `Tab` előre billentyűt. Ugrás az előző, használja a `Shift+Tab` kulcs. Navigációs balról jobbra, lefelé felső követ.
- Nyomja le az `Enter` a kijelölt szűrőt. A szűrő kiválasztását és a központi telepítés alapján vannak kiemelve topológia szakaszban egy vagy több csomópontja (virtuális hálózat esetén).
- A Szalagcím és a topológia közötti váltáshoz nyomja le az `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Billentyűparancsokkal a topológia
    
- Bármely szűrőt a szalagcím a választott és megnyomva után `Ctrl+F6`, fókusz egyik kijelölt csomópont (**VNet**) a topológia nézetben.
- A topológia e nézetében más kiemelt csomópontjaira áthelyezéséhez használja a `Shift+Right arrow` kulcsát, előre. 
- A kijelölt csomópont, fókusz a **információk eszköz mezőben** a csomópont. Alapértelmezés szerint fókusz áthelyezése a **további részleteket** gombra a **információk eszköz mezőben**. További áthelyezése belül a **mezőben** megtekintéséhez használja a `Right arrow` és `Left arrow` -kulcsok előre és hátra, illetve helyezze át. Nyomja le `Enter` rendelkezik a célzott parancsgomb ugyanaz, mintha a **információk eszköz mezőben**.
- Az ilyen csomópontokon kiválasztását, látogasson el a kapcsolatainak, egy billentyűkombináció lenyomásával a `Shift+Left arrow` kulcs. Fókusz áthelyezése a **információk eszköz mezőben** , hogy a kapcsolat. Bármikor, a fókusz is megjelenítjük csomópont billentyűkombináció lenyomásával `Shift+Right arrow` újra.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Hogyan lehet keresse meg az alhálózat topológia nézetben a billentyűzet használatával?

A virtuális alhálózatok topológia lapon két fő szakaszból áll:
    
- **Transzparens**: felső részén a virtuális alhálózatok topológia szalagcím biztosít a forgalom terjesztési szűrők (például aktív, közepes és átjáró alhálózatok) gombra. Ha egy gombot választja, a megfelelő szűrő a topológia. Például ha az aktív gombra kattint, a topológia kiemeli az aktív virtuális alhálózat, a központi telepítésben.
- **Topológia**: a fejléc alatt a topológia szakasz a forgalom eloszlása virtuális alhálózatok között jeleníti meg.
    
### <a name="keyboard-navigation-on-the-banner"></a>A szalagcím billentyűparancsokkal
    
- Alapértelmezés szerint az a virtuális alhálózatok topológia lapon a szalagcím érték a "Alhálózatok" szűrőt.
- Helyezze át egy másik szűrőt, használja a `Tab` előre billentyűt. Ugrás az előző, használja a `Shift+Tab` kulcs. Navigációs balról jobbra, lefelé felső követ.
- Nyomja le az `Enter` a kijelölt szűrőt. Szűréses választás és a központi telepítés alapján vannak kiemelve topológia szakaszban egy vagy több csomópontot (alhálózat).
- A Szalagcím és a topológia közötti váltáshoz nyomja le az `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Billentyűparancsokkal a topológia
    
- Bármely szűrőt a szalagcím a választott és megnyomva után `Ctrl+F6`, fókusz egyik kijelölt csomópont (**alhálózati**) a topológia nézetben.
- A topológia e nézetében más kiemelt csomópontjaira áthelyezéséhez használja a `Shift+Right arrow` kulcsát, előre. 
- A kijelölt csomópont, fókusz a **információk eszköz mezőben** a csomópont. Alapértelmezés szerint fókusz áthelyezése a **további részleteket** gombra a **információk eszköz mezőben**. További áthelyezése belül a **mezőben** megtekintéséhez használja `Right arrow` és `Left arrow` -kulcsok előre és hátra, illetve helyezze át. Nyomja le `Enter` rendelkezik a célzott parancsgomb ugyanaz, mintha a **információk eszköz mezőben**.
- Az ilyen csomópontokon kiválasztását, látogasson el a kapcsolatainak, egy billentyűkombináció lenyomásával `Shift+Left arrow` kulcs. Fókusz áthelyezése a **információk eszköz mezőben** , hogy a kapcsolat. Bármikor, a fókusz is megjelenítjük csomópont billentyűkombináció lenyomásával `Shift+Right arrow` újra.    

