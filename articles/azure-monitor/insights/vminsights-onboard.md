---
title: Virtuális gépek (előzetes verzió) üzembe helyezése az Azure Monitor |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan telepíti, és az Azure Monitor konfigurálása a virtuális gépek, így ismertetése, hogyan működik az elosztott alkalmazás és milyen állapotbeli problémák azonosított.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2019
ms.author: magoedte
ms.openlocfilehash: 1a4bfae22477e345176971bd40b0afa91c8867fb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885825"
---
# <a name="deploy-azure-monitor-for-vms-preview"></a>Üzembe helyezése az Azure Monitor-beli virtuális gépek (előzetes verzió)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a cikk ismerteti, hogyan állítható be az Azure Monitor-beli virtuális gépek. A szolgáltatás figyeli az Azure-beli virtuális gépek (VM) és a virtuálisgép-méretezési csoportok és a környezetében a virtuális gépek operációs rendszer állapotát. A monitorozás tartalmaz, a felderítés és az alkalmazás függőségekkel is üzemelhet őket leképezése. 

Engedélyezi az Azure Monitor-beli virtuális gépek a következő módszerek egyikével:

* Egy Azure virtuális gépen engedélyezze kiválasztásával **Insights (előzetes verzió)** közvetlenül a virtuális gépről.
* Két vagy több Azure virtuális gépek az Azure Policy használatával engedélyezheti. Ezzel a módszerrel a szükséges függőségek a meglévő és új virtuális gép telepítve van és megfelelően konfigurálva. Nem megfelelő virtuális gépek jelenti, hogy lehetősége engedélyezheti őket, és elháríthatja a módját.
* Engedélyezzen két vagy több Azure virtuális gépek vagy virtuálisgép-méretezési csoportok között egy adott előfizetésen vagy erőforráscsoporton PowerShell használatával.

További információ az egyes módszerek van megadva a cikk későbbi részében.

## <a name="prerequisites"></a>Előfeltételek
A Kezdés előtt győződjön meg arról, hogy tisztában van-e az alábbi szakaszokban található információk.

### <a name="log-analytics"></a>Log Analytics

A virtuális gépek az Azure Monitor Log Analytics-munkaterület az alábbi régiókban támogatja:

- USA nyugati középső régiója
- USA keleti régiója
- Közép-Kanada<sup>1</sup>
- Egyesült Királyság déli régiója<sup>1</sup>
- Nyugat-Európa
- Délkelet-Ázsia<sup>1</sup>

<sup>1</sup> ebben a régióban jelenleg nem támogatja a Azure monitor az állapotfigyelő szolgáltatás virtuális gépek számára.

>[!NOTE]
>Az Azure virtual machines-régióból kezdeményezett telepíthetők, és nem csak a támogatott régiók, a Log Analytics-munkaterületen.
>

Ha nem rendelkezik egy munkaterületet, létrehozhat egyet az alábbi módszerek egyikével:
* [Az Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Ha engedélyezi az Azure Portalon egy Azure virtuális gép figyelése, létrehozhat egy munkaterületet, a folyamat során.

Az ipari méretekben a forgatókönyvben a megoldás engedélyezéséhez előbb konfigurálja a következőket a Log Analytics-munkaterület:

* Telepítse a ServiceMap és InfrastructureInsights megoldásokat. A telepítést csak ebben a cikkben megadott egy Azure Resource Manager-sablon használatával.
* Konfigurálja a teljesítményszámlálók adatainak összegyűjtése a Log Analytics-munkaterületet.

A munkaterület az ipari méretekben a forgatókönyvhöz, lásd: Set ipari méretekben üzembe helyezésének Log Analytics-munkaterületet.

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

Az alábbi táblázat a virtuális gépek az Azure monitorban támogatott Windows- és Linux operációs rendszerek listája. Ebben a szakaszban később nyújtott teljes listáját, amelyek a fő- és alverzió Linux operációsrendszer-kiadás részleteit, és a kernel verziója támogatott.

|Operációs rendszer verziója |Teljesítmény |Maps |Állapot |
|-----------|------------|-----|-------|
|A Windows Server 2019 | X | X | |
|A Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| X |
|Ubuntu 14.04, 16.04, 18.04 | X | X | X |
|CentOS Linux 6, 7 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 11, 12 | X | X | X |
|Debian 8, 9.4 | X<sup>1</sup> | | X |

<sup>1</sup> a teljesítmény funkció az Azure Monitor-beli virtuális gépek csak az Azure Monitor érhető el. Nem érhető el, közvetlenül a bal oldali ablaktáblán az Azure virtuális gépek használatakor.

>[!NOTE]
>Az alábbi adatokat a következőkre vonatkozik a Linux operációs rendszer támogatja:
> - Csak az alapértelmezett és az SMP Linux kernelű kiadások támogatottak.
> - Nestandardní kernel kiadások, mint például a Xen, és a fizikai cím bővítmény (fizikai) nem támogatottak az minden olyan Linux-disztribúció. A kiadási karakterláncot, például egy rendszer *2.6.16.21-0.8-xen* nem támogatott.
> - Egyéni kernelekkel, többek között a standard szintű kernelekkel, újrafordítások nem támogatottak.
> - CentOSPlus kernel használata támogatott.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

### <a name="centosplus"></a>CentOSPlus
| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15. * |
| Ubuntu 16.04.3 | kernel 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 nagyvállalati Server

| Operációs rendszer verziója | Kernel verziója
|:--|:--|
|11 SP4 | 3.0.* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 vállalati kiszolgáló

| Operációs rendszer verziója | Kernel verziója
|:--|:--|
|12 SP2 | 4.4. * |
|12 SP3 | 4.4. * |

### <a name="the-microsoft-dependency-agent"></a>A Microsoft Dependency agent
Virtuális gépek térkép funkció az Azure Monitor az adatok lekérése a Microsoft Dependency agent. A függőségi ügynök a Log Analytics-ügynököket a Log Analytics-kapcsolat támaszkodik. Ezért a rendszer a Log Analytics-ügynököket telepíteni és konfigurálni a függőségi ügynök kell rendelkeznie.

Engedélyezi az Azure Monitor-beli virtuális gépek egy Azure virtuális Gépen, vagy az ipari méretekben üzembe helyezési módszert használja, a felhasználói élményt részeként az ügynök telepítése az Azure virtuális gép függőségi ügynök bővítmény használatára van szükség.

Hibrid környezetben töltse le, és a függőségi ügynök telepítése a két módszer egyikével: Manuálisan, vagy egy automatikus telepítési módszer használatával a virtuális gépek, amelyek Azure-on kívülről üzemelnek.

A következő táblázat ismerteti a térkép funkció támogatja a hibrid környezetben összekapcsolt forrásokról.

| Csatlakoztatott forrás | Támogatott | Leírás |
|:--|:--|:--|
| Windows-ügynökök | Igen | Mellett a [Log Analytics-ügynököket for Windows](../../azure-monitor/platform/log-analytics-agent.md), Windows-ügynökök a Microsoft Dependency agent szükséges. Operációs rendszerek teljes listáját lásd: [támogatott operációs rendszerek](#supported-operating-systems). |
| Linux-ügynökök | Igen | Mellett a [Linuxhoz készült Log Analytics-ügynök](../../azure-monitor/platform/log-analytics-agent.md), Linux-ügynökök a Microsoft Dependency agent szükséges. Operációs rendszerek teljes listáját lásd: [támogatott operációs rendszerek](#supported-operating-systems). |
| System Center Operations Manage felügyeleti csoport | Nem | |

A függőségi ügynök letölthető a következő helyekről:

| Fájl | Operációs rendszer | Verzió | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés
Engedélyezze, és az Azure monitorban funkcióhoz férhet hozzá a virtuális gépek kell a következő hozzáférési szerepköröket hozzárendelni:

- A megoldás engedélyezéséhez rendelkeznie kell a *Log Analytics-közreműködő* szerepkör.

- Teljesítmény, egészségügyi, megtekintése, és adatokat, rendelkeznie kell a *Monitoring Reader* az Azure virtuális gép szerepkör. A Log Analytics-munkaterület-beli virtuális gépek az Azure Monitor kell konfigurálni.

A Log Analytics-munkaterülethez való hozzáférésének kapcsolatos további információkért lásd: [munkaterületeinek kezeléséhez](../../azure-monitor/platform/manage-access.md).

## <a name="enable-monitoring-in-the-azure-portal"></a>Engedélyezze a monitorozást az Azure Portalon
Ha engedélyezni szeretné az Azure Portalon az Azure Virtuálisgép-monitorozási, tegye a következőket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki **virtuális gépek**.

1. Válasszon ki egy virtuális gépet a listából.

1. A virtuális gép lapon található a **figyelés** szakaszban jelölje be **Insights (előzetes verzió)**.

1. Az a **Insights (előzetes verzió)** lapon jelölje be **kipróbálása**.

    ![A virtuális gépek az Azure Monitor engedélyezése a virtuális gép](./media/vminsights-onboard/enable-vminsights-vm-portal-01.png)
1. Az a **Azure Monitor Insights felvételi** lapon, ha rendelkezik egy meglévő Log Analytics munkaterület ugyanabban az előfizetésben, válassza a legördülő listában.  
    A lista preselects az alapértelmezett munkaterületre és a helyre, amely a virtuális gép telepítve van az előfizetésben. 

    >[!NOTE]
    >Ha azt szeretné, a virtuális gépről a monitorozási adatok tárolására szolgáló új Log Analytics-munkaterület létrehozásához kövesse a [hozzon létre egy Log Analytics-munkaterület](../../azure-monitor/learn/quick-create-workspace.md) a támogatott régiók egyikében korábban felsorolt.

Miután engedélyezte a figyelés, előtt megtekintheti a mérőszámok a virtuális gép körülbelül 10 percet vehet igénybe.

![Az Azure Monitor engedélyezése a virtuális gépek figyelése a központi telepítés feldolgozása](./media/vminsights-onboard/onboard-vminsights-vm-portal-status.png)


## <a name="deploy-at-scale"></a>Üzembe helyezés
Ebben a szakaszban, üzembe helyezés az Azure Monitor-beli virtuális gépek vagy az Azure Policy, vagy az Azure PowerShell használatával.

A virtuális gépek üzembe helyezése, előtt előre konfigurálhatja a Log Analytics-munkaterület az alábbiak szerint:

1. Ha még nem rendelkezik egy munkaterületet, hozzon létre egyet az Azure Monitor által támogatott virtuális gépek számára.  
    Mielőtt folytatná, tekintse meg a [munkaterületeinek kezeléséhez](../../log-analytics/log-analytics-manage-access.md?toc=/azure/azure-monitor/toc.json) költség, felügyeleti és megfelelőségi szempontok megismerése.

1. Hozzon létre egy új munkaterületen, ha egy még nem létezik, amely használható az Azure Monitor virtuális gépek támogatásához. Felülvizsgálat [munkaterületeinek kezeléséhez](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json) egy új munkaterületet, a költség, felügyeleti és megfelelőségi szempontokat a folytatás előtt létrehozása előtt.

1. Engedélyezze a teljesítményszámlálók a munkaterületen, a Linux és Windows virtuális gépeken a gyűjteményhez.

1. Telepítse, és engedélyezze a ServiceMap és InfrastructureInsights megoldást a munkaterületén.

### <a name="set-up-a-log-analytics-workspace"></a>Log Analytics-munkaterület beállítása
Ha a Log Analytics-munkaterület nincs, hozzon létre egyet a módszereket, a javasolt áttekintésével a ["Előfeltételek"](#log-analytics) szakaszban.

#### <a name="enable-performance-counters"></a>Teljesítményszámlálók engedélyezése
Ha a Log Analytics-munkaterületet a megoldás által hivatkozott már nincs konfigurálva a megoldás által igényelt a teljesítményszámlálók adatainak összegyűjtése, meg kell engedélyezheti őket. Ezt két módon teheti meg:
* Leírtak szerint manuálisan [a Log Analytics Windows és Linux rendszerű teljesítmény adatforrások](../../azure-monitor/platform/data-sources-performance-counters.md)
* Letöltésével és futtatásával egy PowerShell-parancsprogram, amely elérhető a [Azure PowerShell-galéria](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

#### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Telepítse a ServiceMap és InfrastructureInsights megoldásokat
Ez a módszer egy JSON-sablon, amely a megoldás-összetevőket a Log Analytics-munkaterület engedélyezése konfigurációját tartalmazza.

Ha még nem ismeri a sablon segítségével üzembe helyezni erőforrásokat, lásd:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/resource-group-template-deploy.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Ha az Azure CLI-vel, akkor először helyi telepítése és használata a parancssori felület. Kell futnia az Azure CLI 2.0.27-es vagy újabb. A verzió azonosításához futtassa `az --version`. Ha telepíteni vagy frissíteni szeretné az Azure CLI, lásd: kell [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Másolja és illessze be a következő JSON-szintaxist a létrehozott fájlba:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Mentse a fájlt *installsolutionsforvminsights.json* egy helyi mappába.

1. Rögzítheti a tartozó értékeket *WorkspaceName*, *ResourceGroupName*, és *WorkspaceLocation*. Az érték *WorkspaceName* Log Analytics-munkaterület neve. Az érték *WorkspaceLocation* a régió, a munkaterület van definiálva.

1. Most már készen áll a sablon üzembe helyezésére.
 
    * Használja a következő PowerShell-parancsokat a sablont tartalmazó mappában:

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Ha elkészült, megjelenik egy üzenet, amely a következő példához hasonló, és az eredmény tartalmazza:

        ```powershell
        provisioningState       : Succeeded
        ```

    * A következő parancs futtatása az Azure CLI-vel:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Ha elkészült, megjelenik egy üzenet, amely a következő példához hasonló, és az eredmény tartalmazza:

        ```azurecli
        provisioningState       : Succeeded

### Enable by using Azure Policy
To enable Azure Monitor for VMs at scale in a way that helps ensure consistent compliance and the automatic enabling of the newly provisioned VMs, we recommend [Azure Policy](../../governance/policy/overview.md). These policies:

* Deploy the Log Analytics agent and the Dependency agent.
* Report on compliance results.
* Remediate for non-compliant VMs.

To enable Azure Monitor for VMs by using Azure Policy in your tenant:

- Assign the initiative to a scope: management group, subscription, or resource group
- Review and remediate compliance results

For more information about assigning Azure Policy, see [Azure Policy overview](../../governance/policy/overview.md#policy-assignment) and review the [overview of management groups](../../governance/management-groups/index.md) before you continue.

The policy definitions are listed in the following table:

|Name |Description |Type |
|-----|------------|-----|
|[Preview]: Enable Azure Monitor for VMs |Enable Azure Monitor for the Virtual Machines (VMs) in the specified scope (management group, subscription, or resource group). Takes Log Analytics workspace as a parameter. |Initiative |
|[Preview]: Audit Dependency Agent Deployment – VM Image (OS) unlisted |Reports VMs as non-compliant if the VM Image (OS) isn't defined in the list and the agent isn't installed. |Policy |
|[Preview]: Audit Log Analytics Agent Deployment – VM Image (OS) unlisted |Reports VMs as non-compliant if the VM Image (OS) isn't defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Dependency Agent for Linux VMs |Deploy Dependency Agent for Linux VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Dependency Agent for Windows VMs |Deploy Dependency Agent for Windows VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Log Analytics Agent for Linux VMs |Deploy Log Analytics Agent for Linux VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Log Analytics Agent for Windows VMs |Deploy Log Analytics Agent for Windows VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |

Standalone policy (not included with the initiative) is described here:

|Name |Description |Type |
|-----|------------|-----|
|[Preview]: Audit Log Analytics Workspace for VM - Report Mismatch |Report VMs as non-compliant if they aren't logging to the Log Analytics workspace specified in the policy/initiative assignment. |Policy |

#### Assign the Azure Monitor initiative
With this initial release, you can create the policy assignment only in the Azure portal. To understand how to complete these steps, see [Create a policy assignment from the Azure portal](../../governance/policy/assign-policy-portal.md).

1. To launch the Azure Policy service in the Azure portal, select **All services**, and then search for and select **Policy**.

1. In the left pane of the Azure Policy page, select **Assignments**.  
    An assignment is a policy that has been assigned to take place within a specific scope.
    
1. At the top of the **Policy - Assignments** page, select **Assign Initiative**.

1. On the **Assign Initiative** page, select the **Scope** by clicking the ellipsis (...), and select a management group or subscription.  
    In our example, a scope limits the policy assignment to a grouping of virtual machines for enforcement.
    
1. At the bottom of the **Scope** page, save your changes by selecting **Select**.

1. (Optional) To remove one or more resources from the scope, select **Exclusions**.

1. Select the **Initiative definition** ellipsis (...) to display the list of available definitions, select **[Preview] Enable Azure Monitor for VMs**, and then select **Select**.  
    The **Assignment name** box is automatically populated with the initiative name you selected, but you can change it. You can also add an optional description. The **Assigned by** box is automatically populated based on who is logged in, and this value is optional.
    
1. In the **Log Analytics workspace** drop-down list for the supported region, select a workspace.

   > [!NOTE]
   > If the workspace is beyond the scope of the assignment, grant *Log Analytics Contributor* permissions to the policy assignment's Principal ID. If you don't do this, you might see a deployment failure such as: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`
   > To grant access, review [how to manually configure the managed identity](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  The **Managed Identity** check box is selected, because the initiative being assigned includes a policy with the *deployIfNotExists* effect.
    
1. In the **Manage Identity location** drop-down list, select the appropriate region.

1. Select **Assign**.

#### Review and remediate the compliance results

You can learn how to review compliance results by reading [identify non-compliance results](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). In the left pane, select **Compliance**, and then locate the **[Preview] Enable Azure Monitor for VMs** initiative for VMs that aren't compliant according to the assignment you created.

![Policy compliance for Azure VMs](./media/vminsights-onboard/policy-view-compliance-01.png)

Based on the results of the policies included with the initiative, VMs are reported as non-compliant in the following scenarios:

* Log Analytics or the Dependency agent isn't deployed.  
    This scenario is typical for a scope with existing VMs. To mitigate it, deploy the required agents by [creating remediation tasks](../../governance/policy/how-to/remediate-resources.md) on a non-compliant policy.  
    - [Preview]: Deploy Dependency Agent for Linux VMs
    - [Preview]: Deploy Dependency Agent for Windows VMs
    - [Preview]: Deploy Log Analytics Agent for Linux VMs
    - [Preview]: Deploy Log Analytics Agent for Windows VMs

* VM Image (OS) isn't identified in the policy definition.  
    The criteria of the deployment policy include only VMs that are deployed from well-known Azure VM images. Check the documentation to see whether the VM OS is supported. If it isn't supported, duplicate the deployment policy and update or modify it to make the image compliant.  
    - [Preview]: Audit Dependency Agent Deployment – VM Image (OS) unlisted
    - [Preview]: Audit Log Analytics Agent Deployment – VM Image (OS) unlisted

* VMs aren't logging in to the specified Log Analytics workspace.  
    It's possible that some VMs in the initiative scope are logging in to a Log Analytics workspace other than the one that's specified in the policy assignment. This policy is a tool to identify which VMs are reporting to a non-compliant workspace.  
    - [Preview]: Audit Log Analytics Workspace for VM - Report Mismatch

### Enable with PowerShell
To enable Azure Monitor for VMs for multiple VMs or virtual machine scale sets, you can use the PowerShell script [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0), available from the Azure PowerShell Gallery. This script iterates through every virtual machine and virtual machine scale set in your subscription, in the scoped resource group that's specified by *ResourceGroup*, or to a single VM or virtual machine scale set that's specified by *Name*. For each VM or virtual machine scale set, the script verifies whether the VM extension is already installed. If the VM extension is not installed, the script tries to reinstall it. If the VM extension is installed, the script installs the Log Analytics and Dependency agent VM extensions.

This script requires Azure PowerShell module Az version 1.0.0 or later. Run `Get-Module -ListAvailable Az` to find the version. If you need to upgrade, see [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps). If you're running PowerShell locally, you also need to run `Connect-AzAccount` to create a connection with Azure.

To get a list of the script's argument details and example usage, run `Get-Help`.

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VMs and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VMs/VM Scale Sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed will not install again.
    Use -ReInstall switch if you need to for example update the workspace.

    Use -WhatIf if you would like to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VMs in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to reinstall (move to a new workspace)
```

A következő példa bemutatja a mappában található a PowerShell-parancsok segítségével engedélyezése az Azure Monitor-beli virtuális gépek és a várt kimeneti megismerése:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or VM ScaleSets matching criteria specified

VMs or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on above 2 VMs or VM Scale Sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example to update workspace

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already Onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="enable-for-a-hybrid-environment"></a>A hibrid környezet engedélyezése
Ez a szakasz azt ismerteti, hogyan helyezhet üzembe virtuális gépeket vagy fizikai számítógépeket, amelyek az adatközpontban vagy egyéb felhőalapú környezetek figyelés által üzemeltetett Azure Monitor-beli virtuális gépek.

Az Azure Monitor, virtuális gépek térkép függőségi ügynök maga adatokat nem továbbít, és nincs szükség tűzfalak és a portok módosítása. A térképadatok mindig továbbítása a Log Analytics-ügynököket, az Azure Monitor szolgáltatásba, vagy közvetlenül vagy keresztül a [OMS-átjáró](../../azure-monitor/platform/gateway.md) , ha az informatikai biztonsági szabályzatok nem engedélyezése a számítógépek a hálózat csatlakozik az internethez.

Tekintse át a követelményeket és a központi telepítési módszerek a [Log Analytics Linux és Windows-ügynök](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

A lépések a következőképpen lehet összefoglalni:

1. Telepítse a Log Analytics-ügynököket Windows vagy Linux.

1. Töltse le és telepítse az Azure Monitor, a virtuális gépek térkép függőségi ügynök [Windows](https://aka.ms/dependencyagentwindows) vagy [Linux](https://aka.ms/dependencyagentlinux).

1. A teljesítményszámlálók gyűjtésének engedélyezéséhez.

1. Az Azure Monitor-beli virtuális gépek üzembe helyezése.

### <a name="install-the-dependency-agent-on-windows"></a>Telepítse a függőségi ügynököt Windows
Telepítheti a függőségi ügynök manuálisan a Windows-számítógépeken futó `InstallDependencyAgent-Windows.exe`. Ha a végrehajtható fájl kapcsolók nélkül futtatja, a telepítővarázsló, amelyekkel az ügynök telepítéséhez interaktív módon elindítja.

>[!NOTE]
>*Rendszergazdai* jogosultság szükséges telepíteni, vagy távolítsa el az ügynököt.

Az alábbi táblázat a paramétereket, az ügynök a parancssorból a telepítő által támogatott emeli ki.

| Paraméter | Leírás |
|:--|:--|
| /? | A parancssori kapcsolók listáját adja vissza. |
| /S | Felhasználói beavatkozás nélkül csendes telepítést hajt végre. |

Ha például az a telepítőprogram futtatása a `/?` írja be a paraméter **InstallDependencyAgent-Windows.exe /?**.

A Windows a függőségi ügynök fájlok vannak telepítve a *C:\Program Files\Microsoft függőségi ügynök* alapértelmezés szerint. Ha a függőségi ügynök nem a telepítés befejezése után indul el, ellenőrizze a hibával kapcsolatos részletes információk a naplókat. A naplózási könyvtár *%Programfiles%\Microsoft függőségi Agent\logs*.

### <a name="install-the-dependency-agent-on-linux"></a>A függőségi ügynök telepítése Linux rendszeren
A függőségi ügynök telepítve van a Linux-kiszolgálókon *InstallDependencyAgent-Linux64.bin*, azt a héjparancsfájlt, egy önkicsomagoló bináris. A fájl használatával futtathatja `sh` vagy adjon hozzá végrehajtási engedélyeket magát a fájlt.

>[!NOTE]
> Az ügynök telepítéséhez vagy eltávolításához gyökérszintű hozzáférés szükséges.
>

| Paraméter | Leírás |
|:--|:--|
| – Súgó | A parancssori kapcsolók listájának lekérése. |
| -s | Beavatkozás nélküli telepítés a felhasználónak szóló üzenetek nélkül. |
| --ellenőrzése | Ellenőrizze a jogosultságokat és az operációs rendszer, de ne telepítse az ügynököt. |

Ha például az a telepítőprogram futtatása a `-help` paraméter, írja be **InstallDependencyAgent-Linux64.bin-súgó**.

A Linux függőségi ügynök telepítése a rendszergazdaként a következő parancs futtatásával `sh InstallDependencyAgent-Linux64.bin`.

Ha a függőségi ügynök nem indul el, ellenőrizze a hibával kapcsolatos részletes információk a naplókat. A Linux-ügynökök, a naplózási könyvtár van */var/opt/microsoft/dependency-agent/log*.

A függőségi ügynök fájlok kerülnek a következő könyvtárak:

| Fájlok | Hely |
|:--|:--|
| Alapvető fájlok | /opt/microsoft/dependency-agent |
| Naplófájlok | /var/opt/microsoft/dependency-agent/log |
| Konfigurációs fájlok | /etc/opt/microsoft/dependency-agent/config |
| Szolgáltatás végrehajtható fájljai | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Bináris tárolófájlok | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>Teljesítményszámlálók engedélyezése
Ha a Log Analytics-munkaterületet a megoldás által hivatkozott már nincs konfigurálva a megoldás által igényelt a teljesítményszámlálók adatainak összegyűjtése, szüksége lesz az engedélyezésükhöz. Ezt két módon teheti meg:
* Leírtak szerint manuálisan [a Log Analytics Windows és Linux rendszerű teljesítmény adatforrások](../../azure-monitor/platform/data-sources-performance-counters.md)
* Letöltésével és futtatásával egy PowerShell-parancsprogram, amely elérhető a [Azure PowerShell-galéria](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

### <a name="deploy-azure-monitor-for-vms"></a>Az Azure Monitor-beli virtuális gépek üzembe helyezése
Ez a módszer egy JSON-sablon, amely a megoldás-összetevőket a Log Analytics-munkaterület engedélyezése konfigurációját tartalmazza.

Ha még nem ismeri a sablon segítségével üzembe helyezni erőforrásokat, lásd:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/resource-group-template-deploy.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Ha az Azure CLI-vel, akkor először helyi telepítése és használata a parancssori felület. Kell futnia az Azure CLI 2.0.27-es vagy újabb. A verzió azonosításához futtassa `az --version`. Ha telepíteni vagy frissíteni szeretné az Azure CLI, lásd: kell [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

#### <a name="create-and-execute-a-template"></a>Hozzon létre, és hajtsa végre a sablon

1. Másolja és illessze be a következő JSON-szintaxist a létrehozott fájlba:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Mentse a fájlt *installsolutionsforvminsights.json* egy helyi mappába.

1. Értékeinek szerkesztéséhez *WorkspaceName*, *ResourceGroupName*, és *WorkspaceLocation*. Az érték *WorkspaceName* a Log Analytics-munkaterületet, amely tartalmazza a következő munkaterület nevének teljes erőforrás-Azonosítójára van. Az érték *WorkspaceLocation* a régió, a munkaterület van definiálva.

1. Készen áll a sablon üzembe helyezése a következő PowerShell-parancs használatával:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Ha elkészült, megjelenik egy üzenet, amely a következő példához hasonló, és az eredmény tartalmazza:

    ```powershell
    provisioningState       : Succeeded
    ```
   Miután engedélyezte a figyelés, az állapot és a hibrid számítógép metrikák megtekintéséhez nagyjából 10 percet vehet igénybe.

## <a name="performance-counters-enabled"></a>A teljesítményszámlálók engedélyezve
A virtuális gépek az Azure Monitor konfigurálása a Log Analytics-munkaterületet a megoldás által használt a teljesítményszámlálók adatainak összegyűjtése. A következő táblázat felsorolja azokat az objektumokat és a megoldás által konfigurált számlálói gyűjtött minden 60 másodpercben.

### <a name="windows-performance-counters"></a>Windows-teljesítményszámlálók

|Objektumnév |Számláló neve |
|------------|-------------|
|Logikai lemez |% Szabad terület |
|Logikai lemez |Átl. Lemez mp/Olvasás |
|Logikai lemez |Átl. Lemez mp/átvitel |
|Logikai lemez |Átl. Lemez mp/írás |
|Logikai lemez |Bájt/mp |
|Logikai lemez |Lemezolvasási sebesség (bájt/s) |
|Logikai lemez |Lemezolvasások/mp |
|Logikai lemez |Átvitel/mp |
|Logikai lemez |Lemezírási sebesség (bájt/s) |
|Logikai lemez |Lemezírások/mp |
|Logikai lemez |Szabad hely MB-ban |
|Memory (Memória) |Rendelkezésre álló memória |
|Hálózati Adapter |Fogadott bájtok/mp |
|Hálózati Adapter |Küldött bájtok/s |
|Processzor |Processzoridő |

### <a name="linux-performance-counters"></a>Linux-teljesítményszámlálók

|Objektumnév |Számláló neve |
|------------|-------------|
|Logikai lemez |Foglalt hely % |
|Logikai lemez |Lemezolvasási sebesség (bájt/s) |
|Logikai lemez |Lemezolvasások/mp |
|Logikai lemez |Átvitel/mp |
|Logikai lemez |Lemezírási sebesség (bájt/s) |
|Logikai lemez |Lemezírások/mp |
|Logikai lemez |Szabad hely MB-ban |
|Logikai lemez |Logikai lemez bájt/mp |
|Memory (Memória) |Rendelkezésre álló memória |
|Network (Hálózat) |Fogadott bájtok teljes száma |
|Network (Hálózat) |Küldött bájtok száma összesen |
|Processzor |Processzoridő |

## <a name="diagnostic-and-usage-data"></a>Diagnosztika és használati adatok
A Microsoft automatikusan gyűjt keresztül az Azure Monitor szolgáltatás használatának és teljesítményének adatait. A Microsoft ezeket az adatokat adja meg, és a minőségének, biztonságának és integritásának a szolgáltatás javítására használja. Pontos és hatékony hibaelhárítás képességeket biztosít, a térkép funkció adatait tartalmazza a szoftverek, például az operációs rendszer és verzió, IP-cím, DNS-nevet és munkaállomás neve konfigurációjával kapcsolatos információkat. A Microsoft nem gyűjt, neveket, címeket és egyéb kapcsolattartási adatait.

Az adatok gyűjtésével és használatával kapcsolatos további információkért tekintse meg a [Microsoft Online Services adatvédelmi nyilatkozata](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]
## <a name="next-steps"></a>További lépések

Most, hogy a figyelés engedélyezve van a virtuális gépet, ezeket az adatokat és az Azure Monitor-beli virtuális gépek elemzési érhető el. Az állapotfigyelő szolgáltatás használatával kapcsolatban lásd: [a virtuális gépek állapotának megtekintése az Azure Monitor](vminsights-health.md). Felderített alkalmazások függőségeinek megtekintése: [megtekintése az Azure Monitor virtuális gépeket a térképen](vminsights-maps.md).
