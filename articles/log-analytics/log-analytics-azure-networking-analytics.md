---
title: A Naplóelemzési Azure Networking elemzési megoldások |} Microsoft Docs
description: Log Analytics az Azure-hálózat elemzési megoldás használatával nézze át az Azure hálózati biztonsági csoport naplók és Azure Application Gateway naplókat.
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
ms.topic: article
ms.date: 03/20/2018
ms.author: richrund
ms.openlocfilehash: 12172e81ed6b4d79ee200ee1ca79803ad58d6d19
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="azure-networking-monitoring-solutions-in-log-analytics"></a>Figyelési megoldásoknak a Naplóelemzési Azure hálózatkezelés

Naplóelemzési a hálózatok figyeléséhez a következő megoldásokat nyújtja:
* Hálózati teljesítmény figyelő (NPM)
 * A hálózat állapotának figyelésére
* Az Azure Application Gateway analytics áttekintése
 * Az Azure alkalmazás-átjáró naplói
 * Az Azure Application Gateway metrikák
* Figyelheti és naplózási megoldások hálózati tevékenységet a felhő hálózaton
* [Traffic Analytics](https://docs.microsoft.com/azure/networking/network-monitoring-overview#traffic-analytics) 
* Azure hálózati biztonsági csoport elemzés

## <a name="network-performance-monitor-npm"></a>Hálózati teljesítmény figyelése (NPM)

A [hálózati Teljesítményfigyelő](https://docs.microsoft.com/azure/networking/network-monitoring-overview) felügyeleti megoldás egy olyan hálózati felügyeleti megoldás, amely az állapot, a rendelkezésre állás és a hálózatok reachability figyeli.  Közötti kapcsolat figyelésére szolgál:

* Nyilvános felhő és a helyszíni
* Adatközpontok és a felhasználó helye (fiókirodákban)
* Egy többrétegű alkalmazást a különböző rétegeket tartalmazó alhálózat.

További információkért lásd: [hálózati Teljesítményfigyelő](https://docs.microsoft.com/azure/networking/network-monitoring-overview).

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Az Azure Application Gateway és a hálózati biztonsági csoport elemzés
A megoldás használata:
1. Adja hozzá a felügyeleti megoldás szolgáltatáshoz, és
2. A Naplóelemzési munkaterület diagnosztika közvetlen diagnosztika engedélyezése. Nincs szükség a naplók írni az Azure Blob Storage tárolóban.

Diagnosztika és a megfelelő megoldás az egyik vagy mindkét Application Gateway és a hálózati biztonsági csoportok használatával engedélyezheti.

Ha nem engedélyezi a diagnosztikai naplózást egy adott erőforrástípusra, de a megoldás telepítéséhez, az irányítópult paneleket az adott erőforrás üres, és egy hibaüzenetet jeleníti meg.

> [!NOTE]
> 2017. január, az elküldött naplók Alkalmazásátjárót és a hálózati biztonsági csoportok Naplóelemzési támogatott módon módosítani. Ha megjelenik a **(elavult az) Azure hálózatkezelési elemzés** megoldás, tekintse meg [áttelepítése a régi hálózati elemzési megoldások](#migrating-from-the-old-networking-analytics-solution) lépéseit kövesse.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Tekintse át az Azure-hálózat az gyűjtemény adatait
Az Azure Application Gateway elemzés és a hálózati biztonsági csoport analytics kezelési megoldásai gyűjteni a diagnosztika közvetlenül Azure Alkalmazásátjárót és a hálózati biztonsági csoportok. Nincs szükség a naplók az Azure Blob storage írni, és nincs ügynök szükséges adatok gyűjtését.

A következő táblázat adatgyűjtési módszerek és egyéb hogyan adatgyűjtés Azure Application Gateway-analitikához és a hálózati biztonsági csoport analytics részleteit.

| Platform | Közvetlen ügynök | Systems Center Operations Manager-ügynök | Azure | Az Operations Manager szükséges? | Az Operations Manager ügynök adatait a felügyeleti csoport keresztül küldött | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |Amikor bejelentkezik |


## <a name="azure-application-gateway-analytics-solution-in-log-analytics"></a>A Naplóelemzési Azure Application Gateway elemzési megoldások

![Az Azure alkalmazás átjáró Analytics szimbólum](./media/log-analytics-azure-networking/azure-analytics-symbol.png)

A következő naplók kapcsolódnak Alkalmazásátjárót támogatja:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

A következő mérőszámokat Alkalmazásátjárót támogatja:

* 5 perc átviteli sebesség

### <a name="install-and-configure-the-solution"></a>Telepítse és konfigurálja a megoldást
A következő utasításokat követve telepítse és konfigurálja az Azure Application Gateway elemzési megoldások:

1. Engedélyezze az Azure Application Gateway elemzési megoldások a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) vagy ismertetett folyamatot követve [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md).
2. Engedélyezze a belépést a diagnosztika a [Alkalmazásátjárót](../application-gateway/application-gateway-diagnostics.md) kívánja figyelni.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>A portál Azure Application Gateway diagnosztika engedélyezése

1. Az Azure-portálon lépjen az Alkalmazásátjáró erőforrás figyelése
2. Válassza ki *diagnosztikai naplók* a következő lap megnyitása

   ![Azure Application Gateway erőforrás képe](./media/log-analytics-azure-networking/log-analytics-appgateway-enable-diagnostics01.png)
3. Kattintson a *a diagnosztika bekapcsolásához* a következő lap megnyitása

   ![Azure Application Gateway erőforrás képe](./media/log-analytics-azure-networking/log-analytics-appgateway-enable-diagnostics02.png)
4. A diagnosztika bekapcsolásához kattintson *a* alatt *állapota*
5. Kattintson a jelölőnégyzetbe *Naplóelemzési küldése*
6. Válasszon ki egy meglévő Naplóelemzési munkaterület, vagy egy munkaterület létrehozása
7. A jelölőnégyzet alatti **napló** a napló típusuk gyűjtéséhez
8. Kattintson a *mentése* szolgáltatáshoz diagnosztikai naplózás engedélyezése

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Engedélyezze a PowerShell használatával az Azure hálózati diagnosztika

A következő PowerShell-parancsfájl egy diagnosztikai alkalmazásátjárót naplózásának engedélyezése példát.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzureRmApplicationGateway -Name 'ContosoGateway'

Set-AzureRmDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Azure Application Gateway analytics használata
![Azure Application Gateway analytics csempe képe](./media/log-analytics-azure-networking/log-analytics-appgateway-tile.png)

Miután rákattintott a **Azure Application Gateway analytics** csempére az Áttekintés a a naplók összegzéseinek megtekintése és majd adatpontra az alábbi kategóriákban:

* Alkalmazás átjáró naplói
  * Alkalmazásátjáró belépési naplók az ügyfél és kiszolgáló hibák
  * Az egyes Alkalmazásátjáró óránként kérelmek
  * Sikertelen kérelmek óránként az egyes Alkalmazásátjáró
  * Felhasználói ügynök alkalmazás átjárók hibák
* Alkalmazásteljesítmény-átjáró
  * Az Alkalmazásátjáró állomás állapota
  * Az Alkalmazásátjáró maximális és 95. percentilis sikertelen kérelmek

![Azure Application Gateway elemzések irányítópultján képe](./media/log-analytics-azure-networking/log-analytics-appgateway01.png)

![Azure Application Gateway elemzések irányítópultján képe](./media/log-analytics-azure-networking/log-analytics-appgateway02.png)

Az a **Azure Application Gateway analytics** irányítópult, ellenőrizze az összefoglaló információkat a paneleken valamelyikével, és kattintson a napló lapon részletes információk megtekintéséhez egy.

A naplófájl-keresési lapok egyikén tekintheti eredmények idő, illetve részletes leírást és a keresési korábbi naplók. Az eredmények szűkítéséhez értékkorlátozással is szűrhet.


## <a name="azure-network-security-group-analytics-solution-in-log-analytics"></a>A Naplóelemzési Azure hálózati biztonsági csoport elemzési megoldások

![Azure hálózati biztonsági csoport Analytics szimbólum](./media/log-analytics-azure-networking/azure-analytics-symbol.png)

Hálózati biztonsági csoportok támogatja a következő naplók kapcsolódnak:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Telepítse és konfigurálja a megoldást
A következő utasításokat követve telepítse és konfigurálja az Azure-hálózat elemzési megoldások:

1. Engedélyezze az Azure hálózati biztonsági csoport elemzési megoldások a [Azure piactér](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview) vagy ismertetett folyamatot követve [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md).
2. Engedélyezze a belépést a diagnosztika a [hálózati biztonsági csoport](../virtual-network/virtual-network-nsg-manage-log.md) figyelni kívánt erőforrásokat.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Engedélyezze az Azure hálózati biztonsági csoport diagnosztikai a portálon

1. Az Azure portálon keresse meg a hálózati biztonsági csoport erőforrás figyelése
2. Válassza ki *diagnosztikai naplók* a következő lap megnyitása

   ![Azure hálózati biztonsági csoport erőforrás képe](./media/log-analytics-azure-networking/log-analytics-nsg-enable-diagnostics01.png)
3. Kattintson a *a diagnosztika bekapcsolásához* a következő lap megnyitása

   ![Azure hálózati biztonsági csoport erőforrás képe](./media/log-analytics-azure-networking/log-analytics-nsg-enable-diagnostics02.png)
4. A diagnosztika bekapcsolásához kattintson *a* alatt *állapota*
5. Kattintson a jelölőnégyzetbe *Naplóelemzési küldése*
6. Válasszon ki egy meglévő Naplóelemzési munkaterület, vagy egy munkaterület létrehozása
7. A jelölőnégyzet alatti **napló** a napló típusuk gyűjtéséhez
8. Kattintson a *mentése* szolgáltatáshoz diagnosztikai naplózás engedélyezése

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Engedélyezze a PowerShell használatával az Azure hálózati diagnosztika

A következő PowerShell-parancsfájl egy példát diagnosztikai hálózati biztonsági csoportok naplózásának engedélyezése
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzureRmNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzureRmDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Hálózati biztonsági csoport használata Azure elemzés
Miután rákattintott a **Azure hálózati biztonsági csoport analytics** csempére az Áttekintés a a naplók összegzéseinek megtekintése és majd adatpontra az alábbi kategóriákban:

* Hálózati biztonsági csoport blokkolva adatfolyamok
  * Hálózati biztonsági csoportszabályok letiltott folyamatokkal
  * Letiltott adatfolyamok MAC-címek
* Hálózati biztonsági csoport engedélyezett adatfolyamok
  * Hálózati biztonsági csoportszabályok engedélyezett folyamatokkal
  * MAC-címek megengedett adatfolyamok

![Azure hálózati biztonsági csoport elemzések irányítópultján képe](./media/log-analytics-azure-networking/log-analytics-nsg01.png)

![Azure hálózati biztonsági csoport elemzések irányítópultján képe](./media/log-analytics-azure-networking/log-analytics-nsg02.png)

Az a **Azure hálózati biztonsági csoport analytics** irányítópult, ellenőrizze az összefoglaló információkat a paneleken valamelyikével, és kattintson a napló lapon részletes információk megtekintéséhez egy.

A naplófájl-keresési lapok egyikén tekintheti eredmények idő, illetve részletes leírást és a keresési korábbi naplók. Az eredmények szűkítéséhez értékkorlátozással is szűrhet.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>A régi hálózati elemzési megoldások áttelepítése
2017. január, az elküldött naplók az Azure alkalmazás átjárók és az Azure hálózati biztonsági csoportok Naplóelemzési támogatott módon módosítani. Ezeket a változásokat a következő előnyöket biztosítja:
+ Írja a naplókat közvetlenül Naplóelemzési használja a storage-fiók nélkül
+ A naplók hozzájuk legyenek elérhetők a Naplóelemzési létrehozásának óta kevesebb késés
+ Kevesebb konfigurációs lépések
+ Az összes Azure diagnostics közös formátum

A frissített megoldások használata:

1. [Azure Alkalmazásátjárót közvetlenül a Log Analyticshez való küldésének diagnosztika konfigurálása](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Diagnosztika küldendő közvetlenül Naplóelemzési az Azure hálózati biztonsági csoportok konfigurálása](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. Engedélyezze a *Azure Application Gateway Analytics* és a *Azure hálózati biztonsági csoport Analytics* ismertetett folyamatot követve megoldás [hozzáadni a Naplóelemzési megoldásokat az a Megoldások gyűjteménye](log-analytics-add-solutions.md)
3. Bármely lekérdezések, irányítópultok vagy használni az új adattípusra riasztások frissítése
  + A AzureDiagnostics típus. A ResourceType használhatja az Azure hálózati naplók szűrése.

    | ahelyett, hogy: | Használata: |
    | --- | --- |
    | NetworkApplicationgateways &#124; ahol OperationName == "ApplicationGatewayAccess" | AzureDiagnostics &#124; where ResourceType="APPLICATIONGATEWAYS" and OperationName=="ApplicationGatewayAccess" |
    | NetworkApplicationgateways &#124; where OperationName=="ApplicationGatewayPerformance" | AzureDiagnostics &#124; where ResourceType=="APPLICATIONGATEWAYS" and OperationName=ApplicationGatewayPerformance |
    | NetworkSecuritygroups | AzureDiagnostics &#124; ahol ResourceType == "Biztonsági csoportok" |

   + Bármely mezőhöz, amely rendelkezik egy utótagja \_s, \_d, vagy \_nevét, a g kisbetű módosítsa az első karakter
   + Bármely mezőhöz, amely rendelkezik egy utótagja \_o a neve, az adatok egy egyéni mezők beágyazott mező neve alapján van osztva.
4. Távolítsa el a *Azure hálózatkezelési elemzés (elavult)* megoldás.
  + Ha a PowerShell használata esetén `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Mielőtt a változás nem jelenik meg az új megoldás összegyűjtött adatok. Továbbra is a régi típusú és mezőnevek ezeket az adatokat lekérdezni.

## <a name="troubleshooting"></a>Hibaelhárítás
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>További lépések
* Használjon [Log Analytics-e jelentkezni a keresések](log-analytics-log-searches.md) részletes Azure diagnosztikai adatainak megtekintése.
