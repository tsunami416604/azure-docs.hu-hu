---
title: Az Azure Monitor engedélyezése a virtuális gépek (előzetes verzió) az Azure PowerShell vagy a Resource Manager-sablon használatával |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan engedélyezi az Azure Monitor-beli virtuális gépek egy vagy több Azure-beli virtuális gépek vagy virtuálisgép-méretezési csoportok Azure PowerShell vagy az Azure Resource Manager-sablonok használatával.
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
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: a22bc88fb066d9b845f7fdf1592e2194a03915bc
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524130"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-powershell-or-resource-manager-template"></a>Az Azure Monitor engedélyezése a virtuális gépek (előzetes verzió) az Azure PowerShell vagy a Resource Manager-sablon használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a cikk ismerteti az Azure Monitor engedélyezése (előzetes verzió) virtuális gépek Azure-beli virtuális gépek vagy virtuálisgép-méretezési csoportok Azure PowerShell vagy az Azure Resource Manager-sablonok használatával. Akkor lesz sikeresen megkezdődött a folyamat végén figyelés, a virtuális gépek számát, és ismerje meg, ha bármelyik tapasztalt teljesítmény vagy a rendelkezésre állási problémák. 

## <a name="set-up-a-log-analytics-workspace"></a>Log Analytics-munkaterület beállítása 

Ha a Log Analytics-munkaterület nincs, hozzon létre egyet a módszereket, a javasolt áttekintésével a [Előfeltételek](vminsights-enable-overview.md#log-analytics) szakasz lépéseit, annak érdekében, hogy az Azure Monitor az összes folytatása előtt A virtuális gépek az Azure Resource Manager-sablon módszerrel.

### <a name="enable-performance-counters"></a>Teljesítményszámlálók engedélyezése

Ha a Log Analytics-munkaterületet a megoldás által hivatkozott már nincs konfigurálva a megoldás által igényelt a teljesítményszámlálók adatainak összegyűjtése, meg kell engedélyezheti őket. Ezt két módon teheti meg:
* Leírtak szerint manuálisan [a Log Analytics Windows és Linux rendszerű teljesítmény adatforrások](../../azure-monitor/platform/data-sources-performance-counters.md)
* Letöltésével és futtatásával egy PowerShell-parancsprogram, amely elérhető a [Azure PowerShell-galéria](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Telepítse a ServiceMap és InfrastructureInsights megoldásokat
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
```

## <a name="enable-with-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon engedélyezése
A példában az Azure Resource Manager-sablonok bevezetése hoztunk létre a virtuális gépek és virtuálisgép-méretezési csoportok. Ezek a sablonok közé tartoznak a forgatókönyvek a meglévő erőforrás figyelés engedélyezése és a figyelés engedélyezve lesz rendelkező új erőforrás létrehozása.

>[!NOTE]
>A sablonhoz telepíteni szeretné ugyanabban az erőforráscsoportban és az erőforrásnak az előkészíteni.

Ha ismeri a sablon segítségével üzembe helyezni erőforrásokat fogalmát, lásd:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/resource-group-template-deploy.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Ha az Azure CLI-vel, akkor először helyi telepítése és használata a parancssori felület. Kell futnia az Azure CLI 2.0.27-es vagy újabb. A verzió azonosításához futtassa `az --version`. Ha telepíteni vagy frissíteni szeretné az Azure CLI, lásd: kell [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="download-templates"></a>Sablonok letöltése

Az Azure Resource Manager-sablonok az archív fájl (.zip) Ön által biztosított [letöltése](https://aka.ms/VmInsightsARMTemplates) a GitHub-adattárból. A fájl tartalmát az egyes üzembe helyezési forgatókönyvekben képviselő sablonnal és paraméterfájlokkal fájllal megfelelő mappák belefoglalása. Előtt fut, módosítsa a paramétereket tartalmazó fájlt, és adja meg a szükséges értékeket. Ne módosítsa a sablonfájlt, kivéve, ha testre kell szabnia azt igényeknek. A paraméterfájl módosítása után telepítheti a következő módszerekkel a cikk későbbi részében leírt. 

A letöltési fájl az alábbi sablonok a különböző helyzetekhez tartalmazza:

- **ExistingVmOnboarding** sablon lehetővé teszi az Azure Monitor-beli virtuális gépek, ha már létezik a virtuális gépek.
- **NewVmOnboarding** a sablon létrehoz egy virtuális gépet, és lehetővé teszi, hogy a virtuális gépek figyelése, az Azure Monitor.
- **ExistingVmssOnboarding** sablon lehetővé teszi az Azure Monitor-beli virtuális gépek, ha a virtuálisgép-méretezési csoportot már létezik.
- **NewVmssOnboarding** sablont hoz létre egy virtuálisgép-méretezési csoportok, és megfigyelheti őket a virtuális gépek az Azure Monitor lehetővé teszi.
- **ConfigureWorksapce*** sablon konfigurálja a virtuális gépek az Azure Monitor támogatásához engedélyezni kell a megoldások és a Linux és Windows operációs rendszer teljesítményszámlálók gyűjtése a Log Analytics-munkaterületet.

>[!NOTE]
>Ha a virtual machine scale sets már jelen volt, és a frissítési szabályzat lesz beállítva, **manuális**, az Azure Monitor-beli virtuális gépek nem lesz engedélyezve a példány futtatása után alapértelmezés szerint a **ExistingVmssOnboarding** Az Azure Resource Manager-sablon. A példányokat manuálisan frissíteni kell.

### <a name="deploy-using-azure-powershell"></a>Üzembe helyezés az Azure PowerShell-lel

A következő lépés lehetővé teszi, hogy a figyelést az Azure PowerShell használatával.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Ha elkészült, megjelenik egy üzenet, amely a következő példához hasonló, és az eredmény tartalmazza:

```powershell
provisioningState       : Succeeded
```
### <a name="deploy-using-azure-cli"></a>Üzembe helyezés az Azure CLI használatával

A következő lépés lehetővé teszi, hogy a figyelést az Azure CLI használatával.   

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

A kimenet az alábbihoz hasonló lesz:

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Engedélyezze a PowerShell-lel

Engedélyezi az Azure Monitor-beli virtuális gépek több virtuális gép vagy virtuálisgép-méretezési csoportok, használhatja a PowerShell-parancsfájl [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0), az Azure PowerShell-galériából érhető el. Ez a szkript minden virtuális gép és a virtuális gép méretezési csoportot az előfizetésében, a hatókörön belüli erőforráscsoport által meghatározott végighalad *ResourceGroup*, vagy egy egyetlen virtuális gép vagy virtuálisgép-méretezési csoporthoz által megadott *Neve*. Minden virtuális gép vagy a virtuális gép méretezési csoport esetében a szkript ellenőrzi a Virtuálisgép-bővítmény telepítve van-e már. Ha a Virtuálisgép-bővítmény nincs telepítve, a parancsfájl megpróbálja újra kell telepíteni. Ha a Virtuálisgép-bővítmény telepítve van, a parancsfájl telepíti a Log Analytics és a függőségi ügynök Virtuálisgép-bővítmények.

A szkriptnek szüksége van az Azure PowerShell-modul Az verziójára 1.0.0 vagy újabb. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](https://docs.microsoft.com/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja PowerShell, is futtatni szeretné `Connect-AzAccount` kapcsolat létrehozása az Azure-ral.

A parancsfájl argumentum részletek és a példa használati listájának lekéréséhez futtassa `Get-Help`.

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

## <a name="next-steps"></a>További lépések

Most, hogy a virtuális gépek számára engedélyezve van a figyelés és az Azure Monitor-beli virtuális gépek elemzési érhető el ezt az információt. Az állapotfigyelő szolgáltatás használatával kapcsolatban lásd: [a virtuális gépek állapotának megtekintése az Azure Monitor](vminsights-health.md). Felderített alkalmazások függőségeinek megtekintése: [megtekintése az Azure Monitor virtuális gépeket a térképen](vminsights-maps.md). Azonosíthatja a szűk keresztmetszeteket és a virtuális gépek teljesítményét a teljes kihasználtság, lásd: [megtekintése az Azure virtuális gép teljesítménye](vminsights-performance.md), vagy a felderített alkalmazások függőségeinek megtekintése: [megtekintése az Azure Monitor virtuális gépeket a térképen](vminsights-maps.md).