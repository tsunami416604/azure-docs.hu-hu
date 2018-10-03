---
title: Az Azure Networking Analytics megoldás a Log Analyticsben |} A Microsoft Docs
description: Az Azure Networking Analytics megoldás a Log Analytics segítségével Azure-beli hálózati biztonsági csoportok naplóinak és az Azure Application Gateway-naplók áttekintése.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: ewinner
editor: ''
ms.assetid: 66a3b8a1-6c55-4533-9538-cad60c18f28b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: richrund
ms.component: ''
ms.openlocfilehash: f1db673ad750a5d9d9d6bda9f41555d5e3affe8d
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48043090"
---
# <a name="azure-networking-monitoring-solutions-in-log-analytics"></a>A Log Analytics megoldások monitorozása az Azure-hálózatok

A log Analytics a hálózatok figyelése a következő megoldásokat kínál:
* A Network Performance Monitor (NPM)
 * A hálózat állapotának figyelése
* Az Azure Application Gateway analytics áttekintése
 * Az Azure Application Gateway-naplók
 * Az Azure Application Gateway-metrikák
* Megoldások monitorozása és naplózása a felhő-hálózatban hálózati
* [A TRAFFIC Analytics](https://docs.microsoft.com/azure/networking/network-monitoring-overview#traffic-analytics) 
* Azure Network Security Group Analytics

## <a name="network-performance-monitor-npm"></a>A Network Performance Monitor (NPM)

A [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) felügyeleti megoldás, egy Hálózatfigyelő megoldás, amely figyeli az állapot, a rendelkezésre állás és a hálózatok lemezekről.  Közötti kapcsolat figyelésére szolgál:

* Nyilvános felhő és helyi környezetekben
* Az adatközpontok és a felhasználó tartózkodási helye (fiókirodákban)
* Egy többrétegű alkalmazás különböző szinteken futtató alhálózatok.

További információkért lásd: [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview).

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Az Azure Application Gateway és a hálózati biztonsági csoport analytics
A megoldások használata:
1. Adja hozzá a felügyeleti megoldás a Log Analyticsbe, és
2. A Log Analytics-munkaterület diagnosztika közvetlen diagnosztika engedélyezése. Nem kell írni a naplókat az Azure Blob storage.

Az egyik vagy mindkét Application Gateway és a hálózati biztonsági csoportok diagnosztikai és a megfelelő megoldás engedélyezheti.

Ne engedélyezze az egyes erőforrástípusok diagnosztikai naplózását, de telepíti a megoldást, ha az irányítópult paneleket az adott erőforráshoz üres, és a egy hibaüzenetet jelenít meg.

> [!NOTE]
> A 2017 január a naplók küldésére a Log Analytics az Application Gateway átjárók és a hálózati biztonsági csoportok támogatott módon módosítani. Ha megjelenik a **Azure Networking Analytics (elavult)** megoldás, tekintse meg [a régi Networking Analytics megoldás-ről](#migrating-from-the-old-networking-analytics-solution) a lépéseket kell követnie.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Tekintse át az Azure networking adatok gyűjtése részletei
Az Azure Application Gateway analytics és a hálózati biztonsági csoport analytics felügyeleti megoldás a diagnosztikai naplók összegyűjtése közvetlenül az Azure Application Gateway átjárók és a hálózati biztonsági csoportokat. Nem szükséges a naplók írni az Azure Blob storage és nem az ügynök nem szükséges az adatgyűjtés.

Az alábbi táblázat adatgyűjtési módszerek és egyéb hogyan adatgyűjtés az Azure Application Gateway analytics és a hálózati biztonsági csoport analytics részleteit.

| Platform | Közvetlen ügynök | Systems Center Operations Manager-ügynök | Azure | Az Operations Manager szükséges? | A felügyeleti csoport Operations Manager-ügynök adatok küldött | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |bejelentkezve |


## <a name="azure-application-gateway-analytics-solution-in-log-analytics"></a>Az Azure Application Gateway analytics megoldás a Log Analyticsben

![Az Azure Application Gateway Analytics szimbólum](./media/log-analytics-azure-networking/azure-analytics-symbol.png)

Az Application Gateway átjárók támogatottak a következő naplók kapcsolódnak:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

A következő metrikákat az Application Gateway átjárók támogatottak: újra


* 5 perces átviteli sebesség

### <a name="install-and-configure-the-solution"></a>Telepítse és konfigurálja a megoldást
Az alábbi utasítások segítségével telepítheti és konfigurálhatja az Azure Application Gateway analytics megoldás:

1. Engedélyezze az Azure Application Gateway analytics megoldás a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) vagy leírt folyamatot követve [adja hozzá a Log Analytics solutions kövesse a megoldástárban](log-analytics-add-solutions.md).
2. Naplózás a diagnosztika engedélyezése a [az Application Gateway átjárók](../application-gateway/application-gateway-diagnostics.md) figyelni szeretné.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>A portálon az Azure Application Gateway-diagnosztika engedélyezése

1. Az Azure Portalon keresse meg az Application Gateway erőforrás figyelése
2. Válassza ki *diagnosztikai naplók* nyissa meg a következő

   ![az Azure Application Gateway-erőforrásának képe](./media/log-analytics-azure-networking/log-analytics-appgateway-enable-diagnostics01.png)
3. Kattintson a *diagnosztika bekapcsolása* nyissa meg a következő

   ![az Azure Application Gateway-erőforrásának képe](./media/log-analytics-azure-networking/log-analytics-appgateway-enable-diagnostics02.png)
4. Engedélyezze a diagnosztikát, kattintson a *a* alatt *állapota*
5. Kattintson a jelölőnégyzetre a *Küldés a Log Analyticsnek*
6. Válasszon ki egy meglévő Log Analytics-munkaterületet, vagy hozzon létre egy munkaterületet
7. Kattintson a jelölőnégyzetre alatt **Log** minden gyűjtése a log típusok
8. Kattintson a *mentése* a Log Analytics diagnosztikai naplózás engedélyezése

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>PowerShell-lel az Azure hálózati diagnosztika engedélyezése

A következő PowerShell-parancsfájl azt szemlélteti, hogyan engedélyezheti a diagnosztikai naplózás azoknál az alkalmazásátjáróknál.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzureRmApplicationGateway -Name 'ContosoGateway'

Set-AzureRmDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Az Azure Application Gateway analytics használata
![az Azure Application Gateway analytics csempére képe](./media/log-analytics-azure-networking/log-analytics-appgateway-tile.png)

Miután rákattintott a **Azure Application Gateway analytics** csempére az Áttekintés a a naplók összegzéseinek megtekintése és majd jelenítse meg a részleteket az alábbi kategóriákban:

* Application Gateway hozzáférési naplók
  * Ügyfél és kiszolgáló hibák az Application Gateway-naplók elérése
  * Kérelmek száma óránként egyes Application Gateway számára
  * Sikertelen kérelmek száma óránként egyes Application Gateway számára
  * Hibák az Application Gateway átjárók a felhasználói ügynök szerint
* Application Gateway – teljesítmény
  * Számítógép állapota az Application Gateway számára
  * Az Application Gateway számára maximális és a 95. percentilis sikertelen kérelmek

![az Azure Application Gateway analytics-irányítópult képe](./media/log-analytics-azure-networking/log-analytics-appgateway01.png)

![az Azure Application Gateway analytics-irányítópult képe](./media/log-analytics-azure-networking/log-analytics-appgateway02.png)

Az a **Azure Application Gateway analytics** irányítópult, tekintse át az összefoglaló adatokat az egyik a paneleket, és kattintson az egyik, a keresési oldalon található részletes információk megtekintéséhez.

Bármelyik, log search, az eredmények megtekintéséhez idő, a részletes eredmények és a napló keresési előzmények. Értékkorlátozással szűkítheti az eredmények alapján is szűrheti.


## <a name="azure-network-security-group-analytics-solution-in-log-analytics"></a>Azure-beli hálózati biztonsági csoport analytics megoldás a Log Analyticsben

![Azure-beli hálózati biztonsági csoport Analytics szimbólum](./media/log-analytics-azure-networking/azure-analytics-symbol.png)

> [!NOTE]
> A hálózati biztonsági csoport analytics megoldás áll át közösségi támogatás, mivel szolgáltatásait váltotta [Traffic Analytics](../network-watcher/traffic-analytics.md).
> - A megoldás már elérhető a [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/) és hamarosan már az Azure piactéren nem érhető el.
> - A meglévő ügyfeleknek, akik már hozzáadta a megoldás a munkaterület továbbra is működik, nem kell módosítania.
> - A Microsoft továbbra is támogatja a küldő NSG diagnosztikai naplók a munkaterülethez, a diagnosztikai beállítások használatával.

Hálózati biztonsági csoportok támogatottak a következő naplók kapcsolódnak:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Telepítse és konfigurálja a megoldást
Az alábbi utasítások segítségével telepítheti és konfigurálhatja az Azure Networking Analytics megoldás:

1. Engedélyezze az Azure-beli hálózati biztonsági csoport analytics megoldás a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview) vagy leírt folyamatot követve [adja hozzá a Log Analytics solutions kövesse a megoldástárban](log-analytics-add-solutions.md).
2. Naplózás a diagnosztika engedélyezése a [hálózati biztonsági csoport](../virtual-network/virtual-network-nsg-manage-log.md) figyelni kívánt erőforrásokat.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Engedélyezze az Azure-beli hálózati biztonsági csoport diagnosztikai a portálon

1. Az Azure Portalon keresse meg a hálózati biztonsági csoport erőforrást figyelése
2. Válassza ki *diagnosztikai naplók* nyissa meg a következő

   ![Azure-beli hálózati biztonsági csoport erőforrás képe](./media/log-analytics-azure-networking/log-analytics-nsg-enable-diagnostics01.png)
3. Kattintson a *diagnosztika bekapcsolása* nyissa meg a következő

   ![Azure-beli hálózati biztonsági csoport erőforrás képe](./media/log-analytics-azure-networking/log-analytics-nsg-enable-diagnostics02.png)
4. Engedélyezze a diagnosztikát, kattintson a *a* alatt *állapota*
5. Kattintson a jelölőnégyzetre a *Küldés a Log Analyticsnek*
6. Válasszon ki egy meglévő Log Analytics-munkaterületet, vagy hozzon létre egy munkaterületet
7. Kattintson a jelölőnégyzetre alatt **Log** minden gyűjtése a log típusok
8. Kattintson a *mentése* a Log Analytics diagnosztikai naplózás engedélyezése

### <a name="enable-azure-network-diagnostics-using-powershell"></a>PowerShell-lel az Azure hálózati diagnosztika engedélyezése

A következő PowerShell-parancsprogram azt szemlélteti, diagnosztikai hálózati biztonsági csoportok naplózásának engedélyezése
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzureRmNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzureRmDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Elemzések használata Azure-beli hálózati biztonsági csoport
Miután rákattintott a **Azure-beli hálózati biztonsági csoport analytics** csempére az Áttekintés a a naplók összegzéseinek megtekintése és majd jelenítse meg a részleteket az alábbi kategóriákban:

* Hálózati biztonsági csoport letiltott forgalma
  * Hálózati biztonsági csoport forgalomletiltást tartalmazó szabályok
  * MAC-címek letiltott forgalommal
* Hálózati biztonsági csoport engedélyezett forgalma
  * Hálózati biztonsági csoportszabályok és engedélyezett forgalom
  * MAC-címek és engedélyezett forgalom

![Azure-beli hálózati biztonsági csoport analytics-irányítópult képe](./media/log-analytics-azure-networking/log-analytics-nsg01.png)

![Azure-beli hálózati biztonsági csoport analytics-irányítópult képe](./media/log-analytics-azure-networking/log-analytics-nsg02.png)

Az a **Azure-beli hálózati biztonsági csoport analytics** irányítópult, tekintse át az összefoglaló adatokat az egyik a paneleket, és kattintson az egyik, a keresési oldalon található részletes információk megtekintéséhez.

Bármelyik, log search, az eredmények megtekintéséhez idő, a részletes eredmények és a napló keresési előzmények. Értékkorlátozással szűkítheti az eredmények alapján is szűrheti.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>A régi Networking Analytics megoldás-ről
A 2017 január a naplók küldésére a Log Analytics az Azure Application Gateway átjárók és az Azure hálózati biztonsági csoportok támogatott módon módosítani. Ezek a módosítások a következő előnyöket biztosítják:
+ Naplók írt közvetlenül a Log Analytics használata a storage-fiók nélkül
+ Az idő, amikor jönnek létre számukra legyenek elérhetők a Log Analytics naplók a kisebb késés
+ Kevesebb konfigurációs lépéssel
+ Az Azure diagnostics minden alkalmazástípus esetében egy gyakran alkalmazott formátum

A frissített megoldások használata:

1. [Diagnosztika az Azure Application Gateway átjárókon közvetlenül a Log Analyticshez való küldésének konfigurálása](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Diagnosztika az Azure hálózati biztonsági csoportok közvetlenül a Log Analyticshez való küldésének konfigurálása](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. Engedélyezze a *Azure Application Gateway Analytics* és a *Azure hálózati biztonsági csoport Analytics* leírt folyamatot követve megoldás [adja hozzá a Log Analytics-megoldások a a Megoldástárából](log-analytics-add-solutions.md)
3. Bármely mentett lekérdezések, az irányítópultok vagy a riasztások az új adattípus használandó frissítése
  + Típus: AzureDiagnostics való. Az erőforrástípus használhatja az Azure hálózati naplók szűrése.

    | ahelyett, hogy: | Használat: |
    | --- | --- |
    | NetworkApplicationgateways &#124; ahol OperationName == "ApplicationGatewayAccess" | AzureDiagnostics &#124; ahol ResourceType = "APPLICATIONGATEWAYS" és az OperationName == "ApplicationGatewayAccess" |
    | NetworkApplicationgateways &#124; ahol OperationName == "ApplicationGatewayPerformance" | AzureDiagnostics &#124; ahol ResourceType == "APPLICATIONGATEWAYS" és az OperationName = ApplicationGatewayPerformance |
    | NetworkSecuritygroups | AzureDiagnostics &#124; ahol ResourceType == "NETWORKSECURITYGROUPS" |

   + Bármely mező, amely rendelkezik, amik utótagja \_s, \_d, vagy \_a nevét, a g módosítása az első karakter kisbetűsre
   + Bármely mező, amely rendelkezik, amik utótagja \_o a neve, az adatok az egyes mezők a beágyazott mezők neve alapján van felosztva.
4. Távolítsa el a *Azure Networking Analytics (elavult)* megoldás.
  + Ha a Powershellt használ, használja a `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Összegyűjtött adatok, mielőtt a változás nem jelenik meg az új megoldásba. Továbbra is a régi típusú és a mezőnevek ezen adatok lekérdezéséhez.

## <a name="troubleshooting"></a>Hibaelhárítás
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>További lépések
* Használat [Log Analytics naplóbeli kereséseivel](log-analytics-log-searches.md) részletes Azure diagnostics-adatok megtekintéséhez.
