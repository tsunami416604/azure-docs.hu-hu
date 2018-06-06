---
title: Hálózati biztonsági csoport eseménye és a szabály a teljesítményszámláló az Azure diagnosztikai naplók |} Microsoft Docs
description: Megtudhatja, hogyan engedélyezése esemény és szabály számláló diagnosztikai naplók az Azure hálózati biztonsági csoport számára.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: jdial
ms.openlocfilehash: c3f4a64c9e11d17899987bbe818506f61c415e3f
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757059"
---
# <a name="diagnostic-logging-for-a-network-security-group"></a>A hálózati biztonsági csoport diagnosztikai naplózás

A hálózati biztonsági csoport (NSG) tartalmaz, szabályok, amelyek a virtuális hálózati alhálózat, a hálózati adapter, vagy mindkét adatforgalom engedélyezéséhez vagy letiltásához. Ha engedélyezte a diagnosztikai egy NSG naplózását, bejelentkezhet a következő kategóriákba tartozó információkat:

* **Esemény:** kerülnek naplózásra bejegyzések mely NSG-t a virtuális gépek, a MAC-címe alapján szabályok érvényesek. Ezek a szabályok állapota 60 másodpercenként gyűjti.
* **A szabály számláló:** Contains bejegyzéseket hányszor minden NSG-szabályok forgalom engedélyezése vagy megtagadása vonatkozik.

Diagnosztikai naplók esetén csak az Azure Resource Manager deployment használatával telepített NSG-k érhetők el. Az NSG-ket a klasszikus üzembe helyezési modellben telepített diagnosztikai naplózás nem engedélyezhető. Szeretné jobban megismerni a két modell, lásd: [ismertetése Azure üzembe helyezési modellel](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Diagnosztikai naplózás külön engedélyezve van az *minden* szeretne gyűjteni a diagnosztikai adatok NSG. Ha érdekli a működési, vagy a tevékenység, hanem naplózza, tekintse meg az Azure [naplózása](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enable-logging"></a>Naplózás engedélyezése

Használhatja a [Azure Portal](#azure-portal), vagy [PowerShell](#powershell)diagnosztikai naplózás engedélyezéséhez.

### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be a [portal](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás**, majd írja be a *hálózati biztonsági csoportok*. Ha **hálózati biztonsági csoportok** jelennek meg a keresési eredmények között, válassza ki azt.
3. Válassza ki a naplózás engedélyezése kívánt NSG.
4. A **figyelés**, jelölje be **diagnosztikai naplók**, majd válassza ki **a diagnosztika bekapcsolásához**, az alábbi ábrán látható módon:
 
    ![Diagnosztika bekapcsolása](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. A **diagnosztikai beállítások**, adja meg, vagy válassza ki a következő adatokat, majd **mentése**:

    | Beállítás                                                                                     | Érték                                                          |
    | ---------                                                                                   |---------                                                       |
    | Name (Név)                                                                                        | A név.  Például: *myNsgDiagnostics*      |
    | **Archív tárfiókba**, **adatfolyam egy eseményközpontba**, és **Naplóelemzési küldése** | Választhat, amikor a lehető legtöbb célhelyre. Minden egyes kapcsolatos további információkért lásd: [naplózni a célok](#log-destinations).                                                                                                                                           |
    | NAPLÓ                                                                                         | Válassza ki az egyik vagy mindkét naplózási kategóriák. A különböző kategóriájú naplózott adatok kapcsolatos további információkért lásd: [kategóriák jelentkezzen](#log-categories).                                                                                                                                             |
6. Tekintheti meg és elemezheti a naplókat. További információkért lásd: [megtekintése és elemzése a naplók](#view-and-analyze-logs).

### <a name="powershell"></a>PowerShell

A parancsok futtatása a [Azure Cloud rendszerhéj](https://shell.azure.com/powershell), vagy futtassa a PowerShell a számítógépről. Az Azure-felhő rendszerhéj a szabad interaktív rendszerhéjat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha PowerShell a számítógépen futtatja, akkor a *AzureRM* PowerShell-modult, 6.1.1 verzió vagy újabb. Futtatás `Get-Module -ListAvailable AzureRM` a számítógépen, a telepített verzió található. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtat PowerShell, is futtatásához szükséges `Login-AzureRmAccount` Azure bejelentkezni egy olyan fiókkal, amely rendelkezik a [szükséges engedélyek](virtual-network-network-interface.md#permissions)].

Diagnosztikai naplózás engedélyezéséhez szüksége van egy meglévő NSG azonosítóját. Ha még nem rendelkezik egy meglévő NSG, létrehozhat egyet a [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup).

Engedélyezni szeretné a diagnosztikai naplózás a hálózati biztonsági csoport lekérése [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup). Például egy NSG beolvasása nevű *myNsg* nevű erőforráscsoportban található *myResourceGroup*, adja meg a következő parancsot:

```azurepowershell-interactive
$Nsg=Get-AzureRmNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Diagnosztikai naplók háromféle cél írhat. További információkért lásd: [naplózni a célok](#log-destinations). Ez a cikk naplók kell küldeni a *Naplóelemzési* cél példaként. Egy meglévő Naplóelemzési munkaterület beolvasása [Get-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightsworkspace). Például egy meglévő munkaterület beolvasása nevű *myLaWorkspace* erőforráscsoportban nevű *LaWorkspaces*, adja meg a következő parancsot:

```azurepowershell-interactive
$Oms=Get-AzureRmOperationalInsightsWorkspace `
  -ResourceGroupName LaWorkspaces `
  -Name myLaWorkspace
```

Ha még nem rendelkezik egy meglévő munkaterülettel, létrehozhat egy- [New-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace).

Engedélyezheti a naplókat a további naplózás két kategóriába sorolhatók. További információkért lásd: [kategóriák jelentkezzen](#log-categories). Az NSG diagnosztikai naplózás engedélyezése [Set-AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting). A következő példa a munkaterület esemény és a számláló kategória-adatokat egy NSG-t az azonosítók használata az NSG-t és a korábban lekért munkaterület jelentkezik:

```azurepowershell-interactive
Set-AzureRmDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Ha csak egy kategóriát vagy a másik ahelyett, hogy mindkét adatokat naplózhatnak, vegye fel a `-Categories` az előző parancsot, utána pedig a beállítással *NetworkSecurityGroupEvent* vagy *NetworkSecurityGroupRuleCounter*. Ha azt szeretné, hogy egy másik bejelentkezési [cél](#log-destinations) mint a Naplóelemzési munkaterület használata az Azure a megfelelő paramétereket [tárfiók](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Eseményközpont](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Tekintheti meg és elemezheti a naplókat. További információkért lásd: [megtekintése és elemzése a naplók](#view-and-analyze-logs).

## <a name="log-destinations"></a>Napló célok

Diagnosztikai adatok lehetnek:
- [Egy Azure Storage-fiókot írt](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), naplózási vagy manuális ellenőrzést. Megadhatja, hogy a megőrzési időt (napokban) erőforrás diagnosztikai beállításait.
- [Az Eseményközpontok felé adatátvitel](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) egy külső szolgáltatás, vagy az egyéni elemzési megoldások, például a Power bi szempontjából.
- [Az Azure Naplóelemzés írt](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-diagnostics-direct-to-log-analytics).

## <a name="log-categories"></a>Naplókategóriák

JSON-formátumú adatot ír a következő naplózási kategóriákban:

### <a name="event"></a>Esemény

Az Eseménynapló tartalmaz információkat, a virtuális gépekhez, a MAC-címe alapján alkalmazott NSG-szabályok. A következő példa adatokat az egyes eseményekhez jegyzi fel:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY RULE NAME]",
        "direction":"In",
        "priority":1000,
        "type":"allow",
        "conditions":{
            "protocols":"6",
            "destinationPortRange":"[PORT RANGE]",
            "sourcePortRange":"0-65535",
            "sourceIP":"0.0.0.0/0",
            "destinationIP":"0.0.0.0/0"
            }
        }
}
```

### <a name="rule-counter"></a>Szabályszámlálója

A szabály a számláló minden egyes szabály erőforrásokra alkalmazzák vonatkozó információkat tartalmaz. A következő példa adatokat a rendszer naplózza minden alkalommal, amikor egy szabály alkalmazásakor:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY RULE NAME]",
        "direction":"In",
        "type":"allow",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> Az IP-forráscím a kommunikáció nincs bejelentkezve. Engedélyezheti a [NSG folyamata naplózási](../network-watcher/network-watcher-nsg-flow-logging-portal.md) egy NSG-t a azonban, amely naplózza a szabály teljesítményszámláló adatai, valamint az IP-forráscím, a kommunikációt kezdeményezett összes. A rendszer az NSG-folyamatnapló adatait egy Azure Storage-fiókba fogja írni. Elemezheti az adatokat a [analytics forgalom](../network-watcher/traffic-analytics.md) Azure hálózati figyelőt képességét.

## <a name="view-and-analyze-logs"></a>Megtekintése és elemzése a naplókat

Diagnosztikai naplóadatok megtekintéséhez, lásd: [Azure diagnosztikai naplók áttekintése](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ha diagnosztikai adatokat küldi el:
- **Naplófájl Analytics**: használhatja a [hálózati biztonsági csoport analytics](../log-analytics/log-analytics-azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-log-analytics
) továbbfejlesztett insights-megoldást. A megoldás az NSG-szabályok, amelyek engedélyezik vagy megtagadják a forgalmat, MAC-címét, a hálózati illesztő a virtuális gép képi megjelenítések biztosít.
- **Az Azure Storage-fiók**: adatot ír egy PT1H.json fájlt. Megtalálhatja az:
    - Eseménynapló a következő elérési úton: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
    - A szabály számláló napló a következő elérési úton: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>További lépések

- További információ [naplózása](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), korábban ismert naplózási vagy műveleti naplókat. Naplózása alapértelmezés szerint engedélyezve van a az NSG-k vagy az Azure-telepítés modell használatával létrehozott. Annak megállapításához, hogy mely műveletek a műveletnaplóban az NSG-ket befejeződött, keresse meg a következő erőforrás-típusokat tartalmazó bejegyzéseket:
    - Microsoft.ClassicNetwork/networkSecurityGroups
    - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
    - Microsoft.Network/networkSecurityGroups
    - Microsoft.Network/networkSecurityGroups/securityRules
- Diagnosztikai adatok, hogy tartalmazzák az IP-forráscím mindenhol, lásd: [NSG folyamata naplózási](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).