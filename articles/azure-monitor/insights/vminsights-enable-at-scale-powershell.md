---
title: Azure Monitor for VMs (előzetes verzió) engedélyezése Azure PowerShell vagy Resource Manager-sablonok használatával | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan engedélyezhető Azure Monitor for VMs egy vagy több Azure-beli virtuális gép vagy virtuálisgép-méretezési csoport számára Azure PowerShell vagy Azure Resource Manager sablonok használatával.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/14/2019
ms.openlocfilehash: 25e9848124b84822749d3be4d1ead7b86e6e584e
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091774"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-powershell-or-resource-manager-templates"></a>Azure Monitor for VMs (előzetes verzió) engedélyezése Azure PowerShell vagy Resource Manager-sablonok használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a cikk azt ismerteti, hogyan engedélyezhető a Azure Monitor for VMs (előzetes verzió) Azure-beli virtuális gépekhez vagy virtuálisgép-méretezési csoportokhoz Azure PowerShell vagy Azure Resource Manager sablonok használatával. A folyamat végén sikeresen megkezdődött az összes virtuális gép figyelése, és megtudhatja, hogy vannak-e teljesítmény-vagy rendelkezésre állási problémák.

## <a name="set-up-a-log-analytics-workspace"></a>Log Analytics-munkaterület beállítása 

Ha nem rendelkezik Log Analytics munkaterülettel, létre kell hoznia egyet. Tekintse át az [Előfeltételek](vminsights-enable-overview.md#log-analytics) szakaszban javasolt módszereket, mielőtt folytatná a konfigurálásához szükséges lépéseket. Ezután befejezheti Azure Monitor for VMs telepítését a Azure Resource Manager sablon metódus használatával.

### <a name="enable-performance-counters"></a>Teljesítményszámlálók engedélyezése

Ha a Log Analytics-munkaterületet a megoldás által hivatkozott már nincs konfigurálva a megoldás által igényelt a teljesítményszámlálók adatainak összegyűjtése, meg kell engedélyezheti őket. Ezt kétféleképpen teheti meg:
* Leírtak szerint manuálisan [a Log Analytics Windows és Linux rendszerű teljesítmény adatforrások](../../azure-monitor/platform/data-sources-performance-counters.md)
* A [Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1) -katalógusból elérhető PowerShell-szkript letöltésével és futtatásával

### <a name="install-the-servicemap-solution"></a>A ServiceMap-megoldás telepítése

Ez a módszer egy JSON-sablon, amely a megoldás-összetevőket a Log Analytics-munkaterület engedélyezése konfigurációját tartalmazza.

Ha nem tudja, hogyan helyezhet üzembe erőforrásokat sablon használatával, tekintse meg a következőt:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/resource-group-template-deploy.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Az Azure CLI használatához először telepítenie és használnia kell a CLI-t helyileg. Kell futnia az Azure CLI 2.0.27-es vagy újabb. A verzió azonosításához futtassa `az --version`. Az Azure CLI telepítéséhez vagy frissítéséhez tekintse meg [Az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli)ismertető témakört.

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
                    }
                ]
            }
        ]
    }
    ```

1. Mentse a fájlt *installsolutionsforvminsights.json* egy helyi mappába.

1. Rögzítse a *WorkspaceName*, a *ResourceGroupName*és a *WorkspaceLocation*értékeit. A *WorkspaceName* értéke a log Analytics munkaterület neve. Az érték *WorkspaceLocation* a régió, a munkaterület van definiálva.

1. Most már készen áll a sablon üzembe helyezésére.
 
    * Használja a következő PowerShell-parancsokat a sablont tartalmazó mappában:

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        A konfiguráció módosítása több percet is igénybe vehet. Ha elkészült, egy üzenet jelenik meg, amely a következőhöz hasonló, és az eredményt tartalmazza:

        ```powershell
        provisioningState       : Succeeded
        ```

    * A következő parancs futtatása az Azure CLI-vel:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        A konfiguráció módosítása több percet is igénybe vehet. Ha elkészült, egy üzenet jelenik meg, amely a következőhöz hasonló, és az eredményt tartalmazza:

        ```azurecli
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Engedélyezés Azure Resource Manager-sablonokkal

Létrehoztuk például Azure Resource Manager sablonokat a virtuális gépek és a virtuálisgép-méretezési csoportok bevezetéséhez. Ezek a sablonok olyan forgatókönyveket tartalmaznak, amelyekkel engedélyezheti a figyelést egy meglévő erőforráson, és létrehozhat egy olyan új erőforrást, amelyen engedélyezve van a figyelés.

>[!NOTE]
>A sablonnak ugyanabban az erőforráscsoporthoz kell lennie, mint a fedélzeten üzembe helyezni kívánt erőforrásnak.

Ha nem tudja, hogyan helyezhet üzembe erőforrásokat sablon használatával, tekintse meg a következőt:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/resource-group-template-deploy.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Az Azure CLI használatához először telepítenie és használnia kell a CLI-t helyileg. Kell futnia az Azure CLI 2.0.27-es vagy újabb. A verzió azonosításához futtassa `az --version`. Az Azure CLI telepítéséhez vagy frissítéséhez tekintse meg [Az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli)ismertető témakört.

### <a name="download-templates"></a>Sablonok letöltése

A Azure Resource Manager-sablonok a GitHub-tárházból [letölthető](https://aka.ms/VmInsightsARMTemplates) archív fájlban (. zip) vannak megadva. A fájl tartalma olyan mappákat tartalmaz, amelyek az egyes telepítési forgatókönyveket egy sablon és egy paraméter fájlja alapján jelölik. A futtatása előtt módosítsa a paramétereket tartalmazó fájlt, és adja meg a szükséges értékeket. Csak akkor módosítsa a sablonfájlt, ha testre szeretné szabni az adott követelmények támogatásához. A paraméterérték módosítása után a cikk későbbi részében ismertetett módszerek használatával telepítheti azt. 

A letöltési fájl a következő sablonokat tartalmazza különböző forgatókönyvekhez:

- A **ExistingVmOnboarding** -sablon lehetővé teszi, hogy Azure monitor for VMS, ha a virtuális gép már létezik.
- A **NewVmOnboarding** sablon egy virtuális gépet hoz létre, és lehetővé teszi Azure monitor for VMS számára a figyelését.
- A **ExistingVmssOnboarding** -sablon lehetővé teszi, hogy Azure monitor for VMS, ha a virtuálisgép-méretezési csoport már létezik.
- A **NewVmssOnboarding** sablon virtuálisgép-méretezési csoportokat hoz létre, és lehetővé teszi a Azure monitor for VMS számára a figyelését.
- A **ConfigureWorkspace** -sablon úgy konfigurálja a log Analytics munkaterületet, hogy támogassa a Azure monitor for VMS a Linux és a Windows operációs rendszer teljesítményszámlálói által kínált megoldások és gyűjtemények engedélyezésével.

>[!NOTE]
>Ha a virtuálisgép-méretezési csoportok már jelen voltak, és a frissítési szabályzat **manuálisra**van állítva, akkor a **ExistingVmssOnboarding** Azure Resource Manager-sablon futtatása után a rendszer alapértelmezés szerint nem engedélyezi a példányok számára a Azure monitor for VMS. A példányokat manuálisan kell frissítenie.

### <a name="deploy-by-using-azure-powershell"></a>Üzembe helyezés az Azure PowerShell-lel

A következő lépés lehetővé teszi a figyelést Azure PowerShell használatával.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
A konfiguráció módosítása több percet is igénybe vehet. Ha elkészült, egy üzenet jelenik meg, amely a következőhöz hasonló, és az eredményt tartalmazza:

```powershell
provisioningState       : Succeeded
```

### <a name="deploy-by-using-the-azure-cli"></a>Üzembe helyezés az Azure CLI használatával

A következő lépés lehetővé teszi a figyelést az Azure CLI használatával.

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

A kimenet a következőhöz hasonló:

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Engedélyezze a PowerShell-lel

Több virtuális gép vagy virtuálisgép-méretezési csoport Azure Monitor for VMsának engedélyezéséhez használja a [install-VMInsights. Ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0)PowerShell-szkriptet. A Azure PowerShell katalógusból érhető el. Ez a szkript a következő lépésekből áll:

- Minden virtuális gép és virtuálisgép-méretezési csoport az előfizetésben.
- A *ResourceGroup*által megadott hatókörön belüli erőforráscsoport. 
- Egyetlen virtuális gép vagy virtuálisgép-méretezési csoport, amelyet a *név*határoz meg.

Minden virtuális gép vagy a virtuális gép méretezési csoport esetében a szkript ellenőrzi a Virtuálisgép-bővítmény telepítve van-e már. Ha a virtuálisgép-bővítmény telepítve van, a parancsfájl megpróbálja újratelepíteni. Ha a virtuálisgép-bővítmény nincs telepítve, a parancsfájl telepíti a Log Analytics és a függőségi ügynök virtuálisgép-bővítményeit.

Győződjön meg arról, hogy a Azure PowerShell-modult használja az az Version 1.0.0 vagy újabb verzióval, `Enable-AzureRM` kompatibilitási aliasok engedélyezve vannak. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](https://docs.microsoft.com/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja PowerShell, is futtatni szeretné `Connect-AzAccount` kapcsolat létrehozása az Azure-ral.

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
    This script installs or reconfigures the following on VMs and virtual machine scale sets:
    - Log Analytics VM extension configured to supplied Log Analytics workspace
    - Dependency agent VM extension

    Can be applied to:
    - Subscription
    - Resource group in a subscription
    - Specific VM or virtual machine scale set
    - Compliance results of a policy for a VM or VM extension

    Script will show you a list of VMs or virtual machine scale sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed, they will not install again.
    Use -ReInstall switch if you need to, for example, update the workspace.

    Use -WhatIf if you want to see what would happen in terms of installs, what workspace configured to, and status of the extension.


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

    Install for all VMs in a resource group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example, to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source and to reinstall (move to a new workspace)
```

A következő példa bemutatja a mappában található a PowerShell-parancsok segítségével engedélyezése az Azure Monitor-beli virtuális gépek és a várt kimeneti megismerése:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or virtual machine scale sets matching criteria specified

VMs or virtual machine scale sets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on the previous two VMs or virtual machine scale sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example, to update workspace.

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

Already onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="next-steps"></a>Következő lépések

Most, hogy a figyelés engedélyezve van a virtuális gépek számára, ezek az információk a Azure Monitor for VMssal való elemzéshez érhetők el.
 
- Felderített alkalmazások függőségeinek megtekintése: [megtekintése az Azure Monitor virtuális gépeket a térképen](vminsights-maps.md). 

- Az Azure-beli [virtuális gépek teljesítményének megtekintése](vminsights-performance.md)a szűk keresztmetszetek és a virtuális gépek teljesítményének teljes kihasználtsága alapján:. 
