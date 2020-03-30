---
title: Az Azure Monitor engedélyezése virtuális gépekhez PowerShellnel vagy sablonokkal
description: Ez a cikk azt ismerteti, hogyan engedélyezi az Azure Monitor virtuális gépekhez egy vagy több Azure virtuális gépek vagy virtuálisgép-méretezési csoportok segítségével az Azure PowerShell vagy az Azure Resource Manager-sablonok használatával.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/14/2019
ms.openlocfilehash: 75d5203e7c475a44b6a00dbf9286f43114b7b54f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480844"
---
# <a name="enable-azure-monitor-for-vms-using-azure-powershell-or-resource-manager-templates"></a>Az Azure Monitor engedélyezése virtuális gépekhez az Azure PowerShell- vagy Erőforrás-kezelő-sablonok használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a cikk bemutatja, hogyan engedélyezheti az Azure Monitor virtuális gépekhez vagy virtuálisgép-méretezési csoportok hoz az Azure PowerShell vagy az Azure Resource Manager-sablonok használatával. A folyamat végén sikeresen megkezdte az összes virtuális gép figyelését, és megtudhatja, hogy teljesítmény- vagy rendelkezésre állási problémák at tapasztal-e.

## <a name="set-up-a-log-analytics-workspace"></a>Log Analytics-munkaterület beállítása

Ha nem rendelkezik Log Analytics-munkaterülettel, létre kell hoznia egyet. Tekintse át az Előfeltételek szakaszban javasolt [módszereket,](vminsights-enable-overview.md#log-analytics) mielőtt folytatnák a konfigurálásának lépéseit. Ezután befejezheti az Azure Monitor virtuális gépekhez az Azure Resource Manager sablonmódszer használatával üzembe helyezését.

### <a name="install-the-vminsights-solution"></a>A VMInsights-megoldás telepítése

Ez a módszer tartalmaz egy JSON-sablont, amely meghatározza a megoldás-összetevők a Log Analytics-munkaterületen engedélyezéséhez szükséges konfigurációt.

Ha nem tudja, hogyan helyezheti üzembe az erőforrásokat sablon használatával, olvassa el az:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)
* [Erőforrások üzembe helyezése erőforrás-kezelői sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)

Az Azure CLI használatához először telepítenie kell és használnia kell a CLI-t helyileg. Az Azure CLI 2.0.27-es vagy újabb verzióját kell futtatnia. A verzió azonosításához `az --version`futtassa a futtassa a futtassa a futtassa a futtassa Az Azure CLI telepítéséhez vagy frissítéséhez olvassa [el az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli)című témakört.

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
                        "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'VMInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Mentse ezt a fájlt *installsolutionsforvminsights.json* fájlba egy helyi mappába.

1. Rögzítse a *WorkspaceName*, *ResourceGroupName*és *WorkspaceLocation*értékét. A *WorkspaceName* értéke a Log Analytics-munkaterület neve. A *WorkspaceLocation* értéke az a terület, amelyben a munkaterület definiálva van.

1. Most már készen áll a sablon üzembe helyezésére.

    * Használja a következő PowerShell-parancsokat a sablont tartalmazó mappában:

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        A konfiguráció módosítása néhány percet is igénybe vehet. Amikor elkészült, egy üzenet a következőhöz hasonló üzenet jelenik meg, és tartalmazza az eredményt:

        ```output
        provisioningState       : Succeeded
        ```

    * A következő parancs futtatása az Azure CLI használatával:

        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        A konfiguráció módosítása néhány percet is igénybe vehet. Amikor elkészült, egy üzenet jelenik meg, amely hasonló a következőhöz, és tartalmazza az eredményt:

        ```output
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Engedélyezés az Azure Resource Manager-sablonokkal

Példát hoztunk létre az Azure Resource Manager-sablonokhoz a virtuális gépek és a virtuálisgép-méretezési készletek bevezetéséhez. Ezek a sablonok olyan forgatókönyveket tartalmaznak, amelyek segítségével engedélyezheti a figyelést egy meglévő erőforráson, és létrehozhat egy új erőforrást, amelynek engedélyezve van a figyelés.

>[!NOTE]
>A sablont ugyanabban az erőforráscsoportban kell telepíteni, mint a fedélzetre hozandó erőforrást.

Ha nem tudja, hogyan helyezheti üzembe az erőforrásokat sablon használatával, olvassa el az:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)
* [Erőforrások üzembe helyezése erőforrás-kezelői sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)

Az Azure CLI használatához először telepítenie kell és használnia kell a CLI-t helyileg. Az Azure CLI 2.0.27-es vagy újabb verzióját kell futtatnia. A verzió azonosításához `az --version`futtassa a futtassa a futtassa a futtassa a futtassa Az Azure CLI telepítéséhez vagy frissítéséhez olvassa [el az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli)című témakört.

### <a name="download-templates"></a>Sablonok letöltése

Az Azure Resource Manager-sablonok egy archív fájlban (.zip) találhatók, amelyet a GitHub-tárházban [tölthet le.](https://aka.ms/VmInsightsARMTemplates) A fájl tartalma olyan mappákat tartalmaz, amelyek sablonnal és paraméterfájllal jelölik az egyes telepítési eseteket. Mielőtt futtatná őket, módosítsa a paraméterfájlt, és adja meg a szükséges értékeket. Csak akkor módosítsa a sablonfájlt, ha az adott követelmények hez testre kell szabnia. A paraméterfájl módosítása után a cikk későbbi részében ismertetett alábbi módszerekkel telepítheti azt.

A letöltési fájl a következő sablonokat tartalmazza a különböző forgatókönyvekhez:

- **A ExistingVmOnboarding** sablon engedélyezi az Azure Monitor virtuális gépekhez, ha a virtuális gép már létezik.
- **A NewVmOnboarding** sablon létrehoz egy virtuális gépet, és lehetővé teszi, hogy az Azure Monitor a virtuális gépek figyelheti azt.
- **A ExistingVmssOnboarding** sablon engedélyezi az Azure Monitor virtuális gépekhez, ha a virtuálisgép-méretezési készlet már létezik.
- **A NewVmssOnboarding** sablon virtuálisgép-méretezési készleteket hoz létre, és lehetővé teszi, hogy az Azure Monitor a virtuális gépek hez figyelhesse őket.
- **ConfigureWorkspace** sablon konfigurálja a Log Analytics munkaterületet, hogy támogassa az Azure Monitor virtuális gépek, lehetővé téve a linuxos és windowsos operációs rendszer teljesítményszámlálók.

>[!NOTE]
>Ha a virtuálisgép-méretezési csoportok már jelen voltak, és a frissítési szabályzat **beállítása Kézi,** az Azure Monitor szolgáltatás virtuális gépek hez alapértelmezés szerint nem lesz engedélyezve a **ExistingVmssOnboarding** Azure Resource Manager sablon futtatása után. A példányokat manuálisan kell frissítenie.

### <a name="deploy-by-using-azure-powershell"></a>Üzembe helyezés az Azure PowerShell-lel

A következő lépés lehetővé teszi a figyelés t az Azure PowerShell használatával.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
A konfiguráció módosítása néhány percet is igénybe vehet. Amikor elkészült, egy üzenet a következőhöz hasonló üzenet jelenik meg, és tartalmazza az eredményt:

```output
provisioningState       : Succeeded
```

### <a name="deploy-by-using-the-azure-cli"></a>Üzembe helyezés az Azure CLI használatával

A következő lépés lehetővé teszi a figyelés az Azure CLI használatával.

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

A kimenet a következőhöz hasonlít:

```output
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Engedélyezés a PowerShell használatával

Ha több virtuális gép vagy virtuálisgép-méretezési csoport számára szeretné engedélyezni az Azure Monitor virtuális gépekhez vagy virtuálisgép-méretezési csoportokhoz, használja az [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0)PowerShell-parancsfájlt. Az Azure PowerShell-galériából érhető el. Ez a szkript végighalad:

- Az előfizetésben beállított összes virtuális gép és virtuálisgép-méretezés.
- A ResourceGroup által megadott hatókörrel ellátott *erőforráscsoport.*
- A *Név*által megadott egyetlen virtuális gép- vagy virtuálisgép-méretezési készlet.

Minden virtuális gép vagy virtuálisgép méretezési csoport, a parancsfájl ellenőrzi, hogy a virtuális gép bővítmény már telepítve van. Ha a virtuális gép bővítmény telepítve van, a parancsfájl megpróbálja újratelepíteni. Ha a virtuálisgép-bővítmény nincs telepítve, a parancsfájl telepíti a Log Analytics és a függőségi ügynök virtuálisgép-bővítményeket.

Ellenőrizze, hogy az Azure PowerShell-modul T 1.0.0-s vagy újabb verzióját használja-e, ha `Enable-AzureRM` a kompatibilitási aliasok engedélyezve vannak. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](https://docs.microsoft.com/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, `Connect-AzAccount` az Azure-ral való kapcsolat létrehozásához is futnia kell.

A parancsfájl argumentumrészleteinek és a példahasználatnak `Get-Help`a listájának leírásához futtassa a futtassa a futtassa a futtassa a futtassa a futtassa a futtassa a futtassa a futtassa a

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

A következő példa bemutatja a PowerShell-parancsok használatát a mappában a virtuális gépek Azure Monitorszolgáltatásának engedélyezéséhez és a várt kimenet megértéséhez:

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

## <a name="next-steps"></a>További lépések

Most, hogy a figyelés engedélyezve van a virtuális gépek, ez az információ az Azure Monitor virtuális gépek hez érhető el.

- A felderített alkalmazásfüggőségek megtekintéséhez olvassa el [az Azure-figyelő megtekintése virtuális gépekleképezéséhez.](vminsights-maps.md)

- A szűk keresztmetszetek és a virtuális gép teljesítményének általános kihasználásának azonosításáról az [Azure virtuális gép teljesítményének megtekintése](vminsights-performance.md)című témakörben nyújt elő.
