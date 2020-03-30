---
title: Azure Networking Analytics-megoldás az Azure Monitorban | Microsoft dokumentumok
description: Az Azure Networking Analytics-megoldás használatával áttekintheti az Azure hálózati biztonsági csoportnaplókat és az Azure Application Gateway-naplókat.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2018
ms.openlocfilehash: 1045f86db5e1a9ed1979a266937974045e401e27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275567"
---
# <a name="azure-networking-monitoring-solutions-in-azure-monitor"></a>Azure-hálózati figyelési megoldások az Azure Monitorban

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az Azure Monitor a következő megoldásokat kínálja a hálózatok figyelésére:
* Hálózati teljesítményfigyelő (NPM)
    * A hálózat állapotának figyelése
* Az Azure Application Gateway elemzése
    * Az Azure Application Gateway naplói
    * Az Azure Application Gateway metrikák
* Megoldások a hálózati tevékenységek figyelésére és naplózására a felhőhálózaton
    * [Forgalmi elemzések](https://docs.microsoft.com/azure/networking/network-monitoring-overview#traffic-analytics) 
    * Azure Network Security Group Analytics

## <a name="network-performance-monitor-npm"></a>Hálózati teljesítményfigyelő (NPM)

A [Hálózati teljesítményfigyelő](https://docs.microsoft.com/azure/networking/network-monitoring-overview) felügyeleti megoldás egy hálózatfigyelési megoldás, amely figyeli a hálózatok állapotát, elérhetőségét és elérhetőségét.  A következők közötti kapcsolat figyelésére szolgál:

* Nyilvános felhő és helyszíni
* Adatközpontok és felhasználói helyek (fiókirodák)
* A többrétegű alkalmazások különböző rétegeit tároló alhálózatok.

További információt a [Hálózati teljesítményfigyelő című](https://docs.microsoft.com/azure/networking/network-monitoring-overview)témakörben talál.

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Az Azure Application Gateway és a Network Security Group elemzése
A megoldások használata:
1. Adja hozzá a felügyeleti megoldást az Azure Monitorhoz, és
2. Engedélyezze a diagnosztika, hogy a diagnosztika egy Log Analytics-munkaterület az Azure Monitor. Nem szükséges a naplók at azure blob storage.It is not necessary to write the logs to Azure Blob storage.

Engedélyezheti a diagnosztikát és a megfelelő megoldást az Application Gateway és a Networking biztonsági csoportok egyikéhez vagy mindkettőhöz.

Ha nem engedélyezi a diagnosztikai erőforrás-naplózást egy adott erőforrástípushoz, de telepíti a megoldást, az adott erőforrás irányítópult-paneljei üresek, és hibaüzenetet jelenítenek meg.

> [!NOTE]
> 2017 januárjában megváltozott az alkalmazásátjárókból és a hálózati biztonsági csoportokból a Log Analytics-munkaterületre küldött naplók támogatott módja. Ha az **Azure Networking Analytics (elavult)** megoldás, tekintse meg [a régi Hálózati elemzési megoldás áttelepítése a](#migrating-from-the-old-networking-analytics-solution) követendő lépéseket.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Tekintse át az Azure hálózati adatgyűjtésének részleteit
Az Azure Application Gateway elemzése és a Network Security Group elemzési megoldásai közvetlenül az Azure Application Gateways és a Network Security Groups diagnosztikai naplóit gyűjtik. Nem szükséges a naplókat írni az Azure Blob storage-ba, és nincs szükség ügynökre az adatgyűjtéshez.

Az alábbi táblázat az adatgyűjtési módszereket és az Azure Application Gateway-elemzéshez és a Network Security Group analitikához szükséges adatok gyűjtésének egyéb részleteit mutatja be.

| Platform | Közvetlen ügynök | Systems Center Operations Manager ügynök | Azure | A műveleti vezetőre van szükség? | Az Operations Manager ügynöke által küldött adatok a felügyeleti csoporton keresztül | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |naplózva |


## <a name="azure-application-gateway-analytics-solution-in-azure-monitor"></a>Az Azure Application Gateway elemzési megoldása az Azure Monitorban

![Az Azure Application Gateway Analytics szimbóluma](media/azure-networking-analytics/azure-analytics-symbol.png)

Az alkalmazásátjárók a következő naplókat támogatják:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

A következő metrikák támogatottak az alkalmazásátjárók:again


* 5 perces átmenő

### <a name="install-and-configure-the-solution"></a>A megoldás telepítése és konfigurálása
Az Azure Application Gateway elemzési megoldásának telepítéséhez és konfigurálásához kövesse az alábbi utasításokat:

1. Engedélyezze az Azure Application Gateway elemzési megoldását az [Azure piactérről,](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) vagy használja az [Azure Monitor-megoldások hozzáadása a Megoldások galériából](../../azure-monitor/insights/solutions.md)című eljárásban leírt folyamatot.
2. Engedélyezze a figyelni kívánt [alkalmazásátjárók](../../application-gateway/application-gateway-diagnostics.md) diagnosztikai naplózását.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Az Azure Application Gateway diagnosztikájának engedélyezése a portálon

1. Az Azure Portalon keresse meg az Application Gateway erőforrás figyeléséhez.
2. A következő lap megnyitásához válassza a *Diagnosztikai naplók* lehetőséget.

   ![Az Azure Application Gateway erőforrás képe](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics01.png)
3. A következő lap megnyitásához *kattintson a Diagnosztika bekapcsolása* gombra.

   ![Az Azure Application Gateway erőforrás képe](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics02.png)
4. A diagnosztika bekapcsolásához kattintson *a Be* gombra az *Állapot csoportban.*
5. Jelölje be a *Küldés a Log Analytics szolgáltatásba*jelölőnégyzetet.
6. Jelöljön ki egy meglévő Log Analytics-munkaterületet, vagy hozzon létre egy munkaterületet.
7. Jelölje be a **Napló** csoportban az egyes naplótípusok gyűjtését.
8. Kattintson a *Mentés* gombra a diagnosztika Azure Monitorba való naplózásának engedélyezéséhez.

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Az Azure hálózati diagnosztikájának engedélyezése a PowerShell használatával

A következő PowerShell-parancsfájl egy példa arra, hogyan engedélyezheti az alkalmazásátjárók erőforrás-naplózását.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzApplicationGateway -Name 'ContosoGateway'

Set-AzDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Az Azure Application Gateway elemzése
![Az Azure Application Gateway elemző csempéjének képe](media/azure-networking-analytics/log-analytics-appgateway-tile.png)

Miután az Áttekintés lapon az **Azure Application Gateway elemzési** csempéjére kattintott, megtekintheti a naplók összegzését, majd részletezheti a következő kategóriák részleteit:

* Alkalmazásátjáró-hozzáférési naplók
  * Ügyfél- és kiszolgálóhibák az Application Gateway hozzáférési naplóihoz
  * Óránkénti kérelmek az egyes alkalmazásátjárókhoz
  * Sikertelen kérelmek óránként az egyes alkalmazásátjárókhoz
  * Az alkalmazásátjárók felhasználói ügynökáltali hibái
* Az Alkalmazásátjáró teljesítménye
  * Az Alkalmazásátjáró gazdagépének állapota
  * Az Application Gateway sikertelen kérelmeinek maximális és 95%-os százaléka

![Az Azure Application Gateway elemzési irányítópultjának képe](media/azure-networking-analytics/log-analytics-appgateway01.png)

![Az Azure Application Gateway elemzési irányítópultjának képe](media/azure-networking-analytics/log-analytics-appgateway02.png)

Az **Azure Application Gateway elemzési** irányítópultján tekintse át az összefoglaló információkat az egyik panelen, majd kattintson az egyikre a naplókeresési lapon a részletes információk megtekintéséhez.

Bármelyik naplókeresési oldalon megtekintheti az eredményeket az idő, a részletes eredmények és a naplókeresési előzmények szerint. Az eredmények szűkítéséhez a kis- és nagyátaránát is szűrheti.


## <a name="azure-network-security-group-analytics-solution-in-azure-monitor"></a>Az Azure Network Security Group elemzési megoldása az Azure Monitorban

![Az Azure Network Security Group Analytics szimbóluma](media/azure-networking-analytics/azure-analytics-symbol.png)

> [!NOTE]
> A Network Security Group elemzési megoldás közösségi támogatásra vált, mivel funkcionalitását a [Traffic Analytics](../../network-watcher/traffic-analytics.md)váltotta fel.
> - A megoldás már elérhető az [Azure gyorsindítási sablonjaiban,](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/) és hamarosan nem lesz elérhető az Azure Marketplace-en.
> - Azon meglévő ügyfelek számára, akik már hozzáadták a megoldást a munkaterületükhöz, továbbra is módosítások nélkül fog működni.
> - A Microsoft továbbra is támogatja az NSG-erőforrásnaplók küldését a munkaterületre a Diagnosztikai beállítások használatával.

A hálózati biztonsági csoportok a következő naplókat támogatják:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>A megoldás telepítése és konfigurálása
Az Azure Networking Analytics-megoldás telepítéséhez és konfigurálásához kövesse az alábbi utasításokat:

1. Engedélyezze az Azure Network Security Group elemzési megoldását az [Azure piactérről,](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview) vagy használja az [Azure Monitor-megoldások hozzáadása a Megoldások galériából](../../azure-monitor/insights/solutions.md)című eljárásban leírtat.
2. Engedélyezze a diagnosztikai naplózást a figyelni kívánt [hálózati biztonsági csoport](../../virtual-network/virtual-network-nsg-manage-log.md) erőforrásaihoz.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Az Azure hálózati biztonsági csoport diagnosztikájának engedélyezése a portálon

1. Az Azure Portalon keresse meg a Hálózati biztonsági csoport erőforrást a
2. A Következő lap megnyitásához válassza *a Diagnosztikai naplók* lehetőséget.

   ![Az Azure Network Security Group erőforrásképe](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics01.png)
3. A következő lap megnyitásához kattintson *a Diagnosztika bekapcsolása gombra*

   ![Az Azure Network Security Group erőforrásképe](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics02.png)
4. A diagnosztika bekapcsolásához kattintson *a Be* gombra az *Állapot csoportban.*
5. Kattintson a *Küldés* a Log Analytics szolgáltatásba jelölőnégyzetre
6. Meglévő Log Analytics-munkaterület kiválasztása vagy munkaterület létrehozása
7. Kattintson a **napló** csoportban az egyes naplótípusok gyűjteni
8. Kattintson a *Mentés* gombra a diagnosztika logAnalytics szolgáltatásba történő naplózásának engedélyezéséhez

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Az Azure hálózati diagnosztikájának engedélyezése a PowerShell használatával

A következő PowerShell-parancsfájl bemutatja, hogyan engedélyezheti az erőforrás-naplózást a hálózati biztonsági csoportok számára
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Az Azure Network Security Group elemzéseinek használata
Miután az Áttekintés ablakban az **Azure Network Security Group elemzési** csempéjére kattintott, megtekintheti a naplók összegzését, majd részletezheti a következő kategóriák részleteit:

* A hálózati biztonsági csoport blokkolt a folyamatokat
  * Hálózati biztonsági csoport szabályai blokkolt folyamatokkal
  * BLOKKOLT folyamatokkal rendelkező MAC-címek
* A hálózati biztonsági csoport engedélyezett folyamatai
  * Hálózati biztonsági csoport szabályai engedélyezett folyamatokkal
  * MAC-címek engedélyezett folyamatokkal

![Az Azure Network Security Group elemzési irányítópultjának képe](media/azure-networking-analytics/log-analytics-nsg01.png)

![Az Azure Network Security Group elemzési irányítópultjának képe](media/azure-networking-analytics/log-analytics-nsg02.png)

Az **Azure Network Security Group elemzési** irányítópultján tekintse át az összefoglaló információkat az egyik panelen, majd kattintson az egyikre a naplókeresési lapon a részletes információk megtekintéséhez.

Bármelyik naplókeresési oldalon megtekintheti az eredményeket az idő, a részletes eredmények és a naplókeresési előzmények szerint. Az eredmények szűkítéséhez a kis- és nagyátaránát is szűrheti.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Áttelepítés a régi Networking Analytics megoldásból
2017 januárjában megváltozott a támogatott módja a naplók küldésének az Azure Application Gateways és az Azure Network Security Groups egy Log Analytics-munkaterületre. Ezek a változások a következő előnyöket biztosítják:
+ A naplók közvetlenül az Azure Monitorra kerülnek, anélkül, hogy tárfiókot kellene használniuk
+ Kevesebb késés attól az időponttól kezdve, amikor a naplók jönnek létre, hogy elérhetők az Azure Monitorban
+ Kevesebb konfigurációs lépés
+ Az Azure-diagnosztika minden típusának közös formátuma

A frissített megoldások használata:

1. [Konfigurálja a közvetlenül az Azure-alkalmazásátjárókból az Azure Monitornak küldendő diagnosztikát](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [A közvetlenül az Azure Monitornak az Azure network security csoportokból küldendő diagnosztika konfigurálása](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. Az *Azure Application Gateway Analytics* és az *Azure Network Security Group Analytics* megoldás engedélyezése az [Azure Monitor-megoldások hozzáadása a Megoldások galériából című](solutions.md) albumban ismertetett folyamat használatával
3. A mentett lekérdezések, irányítópultok és riasztások frissítése az új adattípus használatához
   + Típus az AzureDiagnostics. A ResourceType segítségével szűrheti az Azure hálózati naplók.

     | ahelyett, hogy: | Használja: |
     | --- | --- |
     | A NetworkApplicationgateways &#124; ahol az OperationName=="ApplicationGatewayAccess" | Az AzureDiagnostics &#124; ahol a ResourceType=="APPLICATIONGATEWAYS" és az OperationName=="ApplicationGatewayAccess" |
     | A NetworkApplicationgateways &#124; ahol az OperationName=="ApplicationGatewayPerformance" | Az AzureDiagnostics &#124; ahol a ResourceType=="APPLICATIONGATEWAYS" és az OperationName=="ApplicationGatewayPerformance" |
     | Hálózati biztonsági csoportok | Az AzureDiagnostics &#124; ahol A ResourceType=="NETWORKSECURITYGROUPS" |

   + Minden olyan mező esetében, amelynek \_ \_neve s, d vagy \_g utótag, módosítsa az első karaktert kisbetűsre
   + Minden olyan mező esetében, amelynek \_neve o utótag, az adatok a beágyazott mezőnevek alapján különálló mezőkre vannak felosztva.
4. Távolítsa el az *Azure Networking Analytics (Elavult)* megoldást.
   + Ha PowerShellt használ, használja a`Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

A módosítás előtt gyűjtött adatok nem láthatók az új megoldásban. Az adatok lekérdezése a régi Típus- és mezőnevek használatával folytatódhat.

## <a name="troubleshooting"></a>Hibaelhárítás
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>További lépések
* Az [Azure Monitor naplólekérdezései](../log-query/log-query-overview.md) segítségével részletes Azure diagnosztikai adatokat tekinthet meg.
