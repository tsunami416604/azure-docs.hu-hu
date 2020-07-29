---
title: Azure Monitor for VMs Log Analytics munkaterület konfigurálása
description: Útmutató a Azure Monitor for VMs által használt Log Analytics munkaterület létrehozásához és konfigurálásához.
ms.subservice: ''
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 10c4f0ba4bfc88017304c228ca1afce4ba863118
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87328301"
---
# <a name="configure-log-analytics-workspace-for-azure-monitor-for-vms"></a>Azure Monitor for VMs Log Analytics munkaterület konfigurálása
Azure Monitor for VMs adatokat gyűjt a Azure Monitor egy vagy több Log Analytics munkaterületéről. Az ügynökök bevezetését megelőzően létre kell hoznia és konfigurálnia kell egy munkaterületet. Ez a cikk a munkaterület követelményeit és a Azure Monitor for VMs konfigurálását ismerteti.

## <a name="overview"></a>Áttekintés
Egy előfizetés tetszőleges számú munkaterületet használhat, attól függően, hogy a munkaterületnek csak egy támogatott helyén kell lennie, és hogy az *VMInsights* -megoldással legyen konfigurálva.

Miután konfigurálta a munkaterületet, a rendelkezésre álló lehetőségek bármelyikével telepítheti a szükséges ügynököket a virtuális gépen és a VMMS, és megadhatja, hogy az adott munkaterületet elküldi az adatküldéshez. A Azure Monitor for VMs minden konfigurált munkaterületről gyűjt adatokat az előfizetésében.

> [!NOTE]
> Ha a Azure Portal használatával engedélyezi a Azure Monitor for VMst egyetlen virtuális gépen vagy VMMS, lehetősége van egy meglévő munkaterület kiválasztására, vagy újat létrehozni. Ha még nem tette meg, akkor a *VMInsights* -megoldás erre a munkaterületre lesz telepítve. Ezt a munkaterületet más ügynökök számára is használhatja.


## <a name="create-log-analytics-workspace"></a>Log Analytics-munkaterület létrehozása

>[!NOTE]
>Az ebben a szakaszban ismertetett információk a [Service Map megoldásra](service-map.md)is érvényesek. 

A **log Analytics munkaterületek** menüjében lévő Azure Portal log Analytics munkaterületek eléréséhez.

[![Anlytics-munkaterületek naplózása](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

Az alábbi módszerek bármelyikével létrehozhat egy új Log Analytics munkaterületet. A környezetében használt munkaterületek számának meghatározásához, valamint a hozzáférési stratégia kialakításához tekintse meg a [Azure monitor naplók üzembe helyezésének megtervezése](../platform/design-logs-deployment.md) című témakört.


* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

## <a name="supported-regions"></a>Támogatott régiók

A Azure Monitor for VMs Log Analytics munkaterületeket támogat a következő régiókban, de a virtuális gépek bármelyik régióban figyelhetők. Maguk a virtuális gépek nem korlátozódnak a Log Analytics munkaterület által támogatott régiókra.

- USA nyugati középső régiója
- USA nyugati régiója
- USA 2. nyugati régiója
- USA déli középső régiója
- USA keleti régiója
- USA 2. keleti régiója
- USA középső régiója
- USA északi középső régiója
- US Gov az
- US Gov VA
- Közép-Kanada
- Az Egyesült Királyság déli régiója
- Észak-Európa
- Nyugat-Európa
- Kelet-Ázsia
- Délkelet-Ázsia
- Közép-India
- Kelet-Japán
- Kelet-Ausztrália
- Délkelet-Ausztrália

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés
A Azure Monitor for VMs szolgáltatásainak engedélyezéséhez és eléréséhez a munkaterületen a [log Analytics közreműködő szerepkörrel](../platform/manage-access.md#manage-access-using-azure-permissions) kell rendelkeznie. A teljesítmény-, állapot-és leképezési adat megtekintéséhez az Azure-beli virtuális gép [figyelési olvasójának szerepkörrel](../platform/roles-permissions-security.md#built-in-monitoring-roles) kell rendelkeznie. A Log Analytics munkaterület elérésének szabályozásáról a [munkaterületek kezelése](../platform/manage-access.md)című témakörben olvashat bővebben.

## <a name="add-vminsights-solution-to-workspace"></a>VMInsights-megoldás hozzáadása a munkaterülethez
Ahhoz, hogy egy Log Analytics munkaterület használható legyen a Azure Monitor for VMsval, telepíteni kell a *VMInsights* -megoldást. A munkaterület konfigurálásának módszereit az alábbi szakaszokban ismertetjük.

> [!NOTE]
> Amikor hozzáadja a *VMInsights* -megoldást a munkaterülethez, a munkaterülethez csatlakozó összes meglévő virtuális gép elkezdi az adatküldést a InsightsMetrics. A többi adattípussal kapcsolatos adatok gyűjtése addig nem történik meg, amíg hozzá nem adja a Dependency Agent a munkaterülethez csatlakozó meglévő virtuális gépekhez.

### <a name="azure-portal"></a>Azure Portal
Három lehetőség áll rendelkezésre a Azure Portal meglévő munkaterületének konfigurálására.

Egyetlen munkaterület konfigurálásához válassza a többi előkészítési **lehetőséget** , majd konfigurálja a **munkaterületet**. Válasszon egy előfizetést és egy munkaterületet, majd kattintson a **Konfigurálás**elemre.

[![Munkaterület konfigurálása](media/vminsights-enable-at-scale-policy/configure-workspace.png)](media/vminsights-enable-at-scale-policy/configure-workspace.png#lightbox)

Több munkaterület konfigurálásához válassza a Azure Portal **figyelés** menüjének **Virtual Machines** menüjének **munkaterület konfigurációja** lapját. Állítsa be a szűrési értékeket a meglévő munkaterületek listájának megjelenítéséhez. Jelölje be az egyes munkaterületek melletti jelölőnégyzetet az engedélyezéshez, majd kattintson a **kijelölt konfigurálása** elemre.

[![Munkaterület konfigurációja](media/vminsights-enable-at-scale-policy/workspace-configuration.png)](media/vminsights-enable-at-scale-policy/workspace-configuration.png#lightbox)


Ha a Azure Portal használatával engedélyezi a Azure Monitor for VMst egyetlen virtuális gépen vagy VMMS, lehetősége van egy meglévő munkaterület kiválasztására, vagy újat létrehozni. Ha még nem tette meg, akkor a *VMInsights* -megoldás erre a munkaterületre lesz telepítve. Ezt a munkaterületet más ügynökök számára is használhatja.

[![Egyetlen virtuális gép engedélyezése a portálon](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>Resource Manager-sablon
A Azure Monitor for VMs Azure Resource Manager sablonjai a GitHub-tárházból [letölthető](https://aka.ms/VmInsightsARMTemplates)archív fájlban (. zip) vannak megadva. Ez magában foglalja a **ConfigureWorkspace** nevű sablont, amely egy log Analytics munkaterületet konfigurál Azure monitor for VMshoz. Ezt a sablont a szabványos módszerek bármelyikével központilag telepítheti, beleértve az alábbi minta PowerShell-és CLI-parancsokat: 

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli
az deployment group create --name ConfigureWorkspace --resource-group my-resource-group --template-file CreateWorkspace.json  --parameters workspaceResourceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace' workspaceLocation='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzResourceGroupDeployment -Name ConfigureWorkspace -ResourceGroupName my-resource-group -TemplateFile ConfigureWorkspace.json -workspaceResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace -location eastus
```

---



## <a name="next-steps"></a>További lépések
- Az ügynökök Azure Monitor for VMshoz való összekapcsolásához tekintse [meg az ügynökök Azure monitor for VMS](vminsights-enable-overview.md) .
- A megoldásból a munkaterületre továbbított adatok mennyiségének korlátozásához tekintse meg [Azure monitor (előzetes verzió) figyelési megoldásait](solution-targeting.md) .