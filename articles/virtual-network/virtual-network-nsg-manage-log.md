---
title: Hálózati biztonsági csoport diagnosztikai naplózása
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogyan engedélyezheti az Azure hálózati biztonsági csoport esemény- és szabályszámláló-diagnosztikai naplóit.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: kumud
ms.openlocfilehash: 9829e713f19ab9755e9dc79d676446c8048e09b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751179"
---
# <a name="diagnostic-logging-for-a-network-security-group"></a>Hálózati biztonsági csoport diagnosztikai naplózása

A hálózati biztonsági csoport (NSG) olyan szabályokat tartalmaz, amelyek engedélyezik vagy megtagadják a virtuális hálózati alhálózat, a hálózati csatoló vagy mindkettő forgalmát. Ha engedélyezi az NSG diagnosztikai naplózását, a következő információkategóriákat naplózhatja:

* **Esemény:** A rendszer azokat a bejegyzéseket naplózza, amelyekre a MAC-cím alapján NSG-szabályok vonatkoznak a virtuális gépekre.
* **Szabályszámláló:** Az egyes NSG-szabályok megtagadására vagy engedélyezésére alkalmazott bejegyzéseket tartalmazza. A szabályok állapotát 60 másodpercenként gyűjti a rendszer.

Diagnosztikai naplók csak az Azure Resource Manager üzembe helyezési modellen keresztül üzembe helyezett NSG-k számára érhetők el. A klasszikus üzembe helyezési modellen keresztül üzembe helyezett NSG-k diagnosztikai naplózása nem engedélyezhető. A két modell jobb megértését az [Azure üzembe helyezési modelljeinek ismertetése](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakörben talál.

A diagnosztikai naplózás külön-külön engedélyezve van *minden olyan* NSG-hez, amelyhez diagnosztikai adatokat szeretne gyűjteni. Ha érdekli a működési vagy tevékenység, naplók helyett, lásd: [Azure-tevékenység naplózása.](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="enable-logging"></a>Naplózás engedélyezése

Használhatja az [Azure Portalon](#azure-portal), [PowerShell,](#powershell)vagy az [Azure CLI](#azure-cli) diagnosztikai naplózás engedélyezéséhez.

### <a name="azure-portal"></a>Azure portál

1. Jelentkezzen be a [portálra.](https://portal.azure.com)
2. Válassza a **Minden szolgáltatás**lehetőséget, majd írja be a hálózati *biztonsági csoportokat.* Amikor **hálózati biztonsági csoportok** jelennek meg a keresési eredmények között, jelölje ki azt.
3. Válassza ki azt az NSG-t, amelyhez engedélyezni szeretné a naplózást.
4. A **FIGYELÉS**csoportban válassza **a Diagnosztikai naplók**lehetőséget, majd a **Diagnosztika bekapcsolása**lehetőséget az alábbi képen látható módon:

   ![Diagnosztika bekapcsolása](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. A **Diagnosztikai beállítások csoportban**adja meg vagy jelölje ki a következő adatokat, majd kattintson a **Mentés gombra:**

    | Beállítás                                                                                     | Érték                                                          |
    | ---------                                                                                   |---------                                                       |
    | Név                                                                                        | Egy általad választott név.  Például: *myNsgDiagnostics*      |
    | **Archiválás tárfiókba**, **Streamelés eseményközpontba**és **Küldés a Log Analytics szolgáltatásba** | Annyi úti célt választhat ki, amennyit csak akar. Ha többet szeretne megtudni az egyes ekről, olvassa el [az Úti célok naplózása (Helyek naplózása) (Úti célok naplózása) (Úti célok naplózása) (Úti célok](#log-destinations)                                                                                                                                           |
    | LOG                                                                                         | Válassza ki az egyiket vagy mindkét naplókategóriát. Ha többet szeretne megtudni az egyes kategóriákhoz naplózott adatokról, olvassa el a Kategóriák naplózása című [témakört.](#log-categories)                                                                                                                                             |
6. Naplók megtekintése és elemzése. További információt a Naplók megtekintése és elemzése című [témakörben](#view-and-analyze-logs)talál.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Futtathatja az Azure Cloud [Shellben](https://shell.azure.com/powershell)követett parancsokat, vagy a PowerShell t a számítógépről futtathatja. Az Azure Cloud Shell egy ingyenes interaktív rendszerhéj. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha a PowerShell t a számítógépről futtatja, szüksége van az Azure PowerShell-modul 1.0.0-s vagy újabb verzióra. Futtassa `Get-Module -ListAvailable Az` a számítógépen, hogy megtalálja a telepített verziót. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, `Connect-AzAccount` akkor is futnia kell, hogy olyan fiókkal jelentkezzen be az Azure-ba, amely rendelkezik a [szükséges engedélyekkel.](virtual-network-network-interface.md#permissions)

A diagnosztikai naplózás engedélyezéséhez egy meglévő NSG azonosítójára van szükség. Ha nem rendelkezik meglévő NSG-vel, létrehozhat egyet a [New-AzNetworkSecurityGroup csoporttal.](/powershell/module/az.network/new-aznetworksecuritygroup)

A [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)segítségével a diagnosztikai naplózást engedélyezni kívánt hálózati biztonsági csoport beolvasása. Ha például egy *myResourceGroup*nevű erőforráscsoportban létező *myNsg* nevű NSG-t szeretne beolvasni, írja be a következő parancsot:

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Diagnosztikai naplók at három céltípusba írhat. További információ: [Úti célok naplózása](#log-destinations). Ebben a cikkben a naplókat a *log analytics* cél, példaként. Meglévő Log Analytics-munkaterület lekérése a [Get-AzOperationalInsightsWorkspace segítségével.](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) Ha például egy *myWorkspace* nevű meglévő munkaterületet szeretne beolvasni egy *MyWorkspaces*nevű erőforráscsoportban, írja be a következő parancsot:

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Ha nem rendelkezik meglévő munkaterülettel, létrehozhat egyet a [New-AzOperationalInsightsWorkspace segítségével.](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace)

A naplózásnak két kategóriája van, amelyekhez engedélyezheti a naplókat. További információ: [Kategóriák naplózása](#log-categories). Engedélyezze a diagnosztikai naplózást az NSG számára a [Set-AzDiagnosticSetting segítségével.](/powershell/module/az.monitor/set-azdiagnosticsetting) A következő példa naplózza az esemény- és számlálókategória-adatokat egy NSG munkaterületére, a korábban beolvasott NSG és munkaterület azonosítóinak használatával:

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Ha csak az egyik vagy a másik kategória adatait szeretné `-Categories` naplózni, nem pedig mindkettőt, adja hozzá a lehetőséget az előző parancshoz, majd a *NetworkSecurityGroupEvent* vagy *a NetworkSecurityGroupRuleCounter*. Ha a Log Analytics-munkaterülettől eltérő [helyre](#log-destinations) szeretne bejelentkezni, használja az Azure [Storage-fiók](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy az Event Hub megfelelő [paramétereit.](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

Naplók megtekintése és elemzése. További információt a Naplók megtekintése és elemzése című [témakörben](#view-and-analyze-logs)talál.

### <a name="azure-cli"></a>Azure CLI

Futtathatja az Azure Cloud [Shellben](https://shell.azure.com/bash)követett parancsokat, vagy az Azure CLI-t a számítógépről. Az Azure Cloud Shell egy ingyenes interaktív rendszerhéj. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha a CLI-t a számítógépről futtatja, a 2.0.38-as vagy újabb verzióra van szükség. Futtassa `az --version` a számítógépen, hogy megtalálja a telepített verziót. Ha frissítenie kell, olvassa el [az Azure CLI telepítése](/cli/azure/install-azure-cli?view=azure-cli-latest)című témakört. Ha helyileg futtatja a CLI-t, `az login` akkor is futnia kell, hogy olyan fiókkal jelentkezzen be az Azure-ba, amely rendelkezik a [szükséges engedélyekkel.](virtual-network-network-interface.md#permissions)

A diagnosztikai naplózás engedélyezéséhez egy meglévő NSG azonosítójára van szükség. Ha nem rendelkezik meglévő NSG-vel, létrehozhat egyet [az hálózati nsg create](/cli/azure/network/nsg#az-network-nsg-create).

Annak a hálózati biztonsági csoportnak a beolvasása, amelynek diagnosztikai naplózását engedélyezni szeretné az [az network nsg show](/cli/azure/network/nsg#az-network-nsg-show)segítségével. Ha például egy *myResourceGroup*nevű erőforráscsoportban létező *myNsg* nevű NSG-t szeretne beolvasni, írja be a következő parancsot:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

Diagnosztikai naplók at három céltípusba írhat. További információ: [Úti célok naplózása](#log-destinations). Ebben a cikkben a naplókat a *log analytics* cél, példaként. További információ: [Kategóriák naplózása](#log-categories).

Engedélyezze a diagnosztikai naplózást az NSG számára az [az monitor diagnosztikai beállításainak létrehozása érdekében.](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) A következő példa az esemény- és számlálókategória-adatokat is naplózza egy *myWorkspace*nevű meglévő munkaterületre, amely a *myWorkspaces*nevű erőforráscsoportban és a korábban beolvasott NSG azonosítójában található:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Ha nem rendelkezik meglévő munkaterülettel, létrehozhat egyet az [Azure Portalon](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy a [PowerShell](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace)használatával. A naplózásnak két kategóriája van, amelyekhez engedélyezheti a naplókat.

Ha csak az egyik vagy a másik kategória adatait szeretné naplózni, távolítsa el azt a kategóriát, amelyhez az előző parancsban nem kíván adatokat naplózni. Ha a Log Analytics-munkaterülettől eltérő [helyre](#log-destinations) szeretne bejelentkezni, használja az Azure [Storage-fiók](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy az Event Hub megfelelő [paramétereit.](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

Naplók megtekintése és elemzése. További információt a Naplók megtekintése és elemzése című [témakörben](#view-and-analyze-logs)talál.

## <a name="log-destinations"></a>Úti célok naplózása

A diagnosztikai adatok a következők lehetnek:
- [Egy Azure Storage-fiókba írt,](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)naplózáshoz vagy manuális ellenőrzéshez. Megadhatja a megőrzési idő (napokban) erőforrás diagnosztikai beállítások használatával.
- Egy külső szolgáltatás vagy egyéni elemzési megoldás, például a PowerBI által betöltés céljából [egy eseményközpontba továbbítja](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a betöltéshez.
- [Az Azure Monitor naplóiba írásban.](../azure-monitor/platform/resource-logs-collect-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="log-categories"></a>Naplókategóriák

A JSON formátumú adatok a következő naplókategóriákhoz íródnak:

### <a name="event"></a>Esemény

Az eseménynapló információkat tartalmaz arról, hogy mely NSG-szabályok vonatkoznak a virtuális gépekre a MAC-cím alapján. A következő adatokat naplózza az egyes események. A következő példában az adatok naplózva egy virtuális gép IP-címe 192.168.1.4 és a MAC-cím 00-0D-3A-92-6A-7C:

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
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "priority":"[PRIORITY-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "conditions":{
            "protocols":"[PROTOCOLS-SPECIFIED-IN-RULE]",
            "destinationPortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourcePortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourceIP":"[SOURCE-IP-OR-RANGE-SPECIFIED-IN-RULE]",
            "destinationIP":"[DESTINATION-IP-OR-RANGE-SPECIFIED-IN-RULE]"
            }
        }
}
```

### <a name="rule-counter"></a>Szabályszámláló

A szabályszámláló naplója az erőforrásokra alkalmazott szabályok adatait tartalmazza. A következő példaadatok naplózása minden alkalommal, amikor egy szabály alkalmazása. A következő példában az adatok naplózva egy virtuális gép IP-címe 192.168.1.4 és a MAC-cím 00-0D-3A-92-6A-7C:

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
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> A kommunikáció forrás IP-címe nem kerül naplózásra. Engedélyezheti azonban [az NSG-folyamatnaplózást](../network-watcher/network-watcher-nsg-flow-logging-portal.md) egy NSG-hez, amely naplózza az összes szabályszámláló-információt, valamint a kommunikációt kezdeményező forrás IP-címet. A rendszer az NSG-folyamatnapló adatait egy Azure Storage-fiókba fogja írni. Elemezheti az adatokat az Azure Network Watcher [forgalomelemzési](../network-watcher/traffic-analytics.md) képességével.

## <a name="view-and-analyze-logs"></a>Naplók megtekintése és elemzése

A diagnosztikai naplóadatok megtekintéséről az [Azure diagnosztikai naplók áttekintése című témakörben](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)olvashat. Ha diagnosztikai adatokat küld a következő kreált adatoknak:
- **Azure Monitor naplók: Használhatja**a [hálózati biztonsági csoport elemzési](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-azure-monitor
) megoldás továbbfejlesztett betekintést. A megoldás vizualizációkat biztosít az NSG-szabályokhoz, amelyek engedélyezik vagy megtagadják a virtuális gép hálózati adapterének MAC-címenkénti forgalmát.
- **Azure Storage-fiók:** Az adatok pt1H.json fájlba íródnak. A következőket találja:
  - Eseménynapló a következő elérési úton:`insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - A szabályszámláló naplója a következő elérési úton található:`insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>További lépések

- További információ [a tevékenységnaplózásról,](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)amelyet korábban naplózásnak vagy működési naplóknak neveznek. A tevékenységnaplózás alapértelmezés szerint engedélyezve van az Azure-telepítési modellen keresztül létrehozott NSG-k esetében. Annak megállapításához, hogy mely műveletek et hajtottavégre az NSG-k a tevékenységnaplóban, keresse meg a következő erőforrástípusokat tartalmazó bejegyzéseket:
  - Microsoft.ClassicNetwork/networkSecurityGroups
  - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
  - Microsoft.Network/networkSecurityGroups
  - Microsoft.Network/networkSecurityGroups/securityRules
- A diagnosztikai adatok naplózásáról az egyes folyamatok forrás IP-címének felvételéhez olvassa el az [NSG-folyamatnaplózás című témakört.](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
