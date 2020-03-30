---
title: Műtermék hozzáadása virtuális géphez az Azure DevTest Labsben | Microsoft dokumentumok
description: Megtudhatja, hogyan adhat hozzá egy összetevőt egy virtuális géphez egy tesztkörnyezetben az Azure DevTest Labs-ben
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 27fec279582d845972b87ac635c87c16c239924e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73901316"
---
# <a name="add-an-artifact-to-a-vm"></a>Műtermék hozzáadása virtuális géphez
Virtuális gép létrehozása közben meglévő összetevőket adhat hozzá. Ezek a összetevők lehetnek a [nyilvános DevTest Labs Git tárház](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) vagy a saját Git-tárház. Ez a cikk bemutatja, hogyan adhat hozzá összetevőket az Azure Portalon, és az Azure PowerShell használatával. 

Az Azure DevTest Labs összetevői lehetővé *teszik* a virtuális gép kiépítésekor végrehajtott *műveletek,* például a Windows PowerShell-parancsfájlok futtatása, a Bash-parancsok futtatása és a szoftverek telepítése. A *műtermék-paraméterek* lehetővé teszik a műtermék testreszabását az adott forgatókönyvhöz.

Az egyéni összetevők létrehozásáról a következő cikkben olvashat: [Egyéni összetevők létrehozása](devtest-lab-artifact-author.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-azure-portal"></a>Az Azure Portal használata 
1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)
1. Válassza az **Összes szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.
1. A laborok listájából válassza ki azt a tesztkörnyezetet, amely a kívánt virtuális gép kívánt dolgozni.  
1. Válassza **a Saját virtuális gépek**lehetőséget .
1. Válassza ki a kívánt virtuális gép.
1. Válassza **az Összetevők kezelése**lehetőséget. 
1. Válassza **az Összetevők alkalmazása**lehetőséget.
1. Az **Összetevők alkalmazása** ablaktáblán jelölje ki a virtuális géphez hozzáadni kívánt összetevőt.
1. A **Műtermék hozzáadása** ablaktáblán adja meg a szükséges paraméterértékeket és a szükséges választható paramétereket.  
1. Válassza a **Hozzáadás** lehetőséget a műtermék hozzáadásához és a **Műtermékek alkalmazása** ablaktáblához való visszatéréshez.
1. Folytassa az összetevők hozzáadását a virtuális géphez szükség szerint.
1. Miután hozzáadta az összetevőket, [módosíthatja az összetevők futtatásának sorrendjét.](#change-the-order-in-which-artifacts-are-run) Visszatérhet az összetevő [megtekintéséhez vagy módosításához](#view-or-modify-an-artifact)is.
1. Ha végzett az összetevők hozzáadásával, válassza **az Alkalmaz lehetőséget.**

### <a name="change-the-order-in-which-artifacts-are-run"></a>Az összetevők futtatásának sorrendjének módosítása
Alapértelmezés szerint az összetevők műveletek végrehajtása abban a sorrendben, amelyben hozzáadódnak a virtuális géphez. A következő lépések bemutatják, hogyan módosíthatja az összetevők futtatásának sorrendjét.

1. Az **Összetevők alkalmazása** ablaktábla tetején jelölje ki a virtuális géphez hozzáadott összetevők számát jelző hivatkozást.
   
    ![A virtuális géphez hozzáadott összetevők száma](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. A **Kijelölt összetevők** ablaktáblán húzza az összetevőket a kívánt sorrendbe. Ha nem tudja áthúzni a műtárgyat, győződjön meg arról, hogy a műtermék bal oldaláról húzza. 
1. Kattintson az **OK** gombra, amikor végzett.  

### <a name="view-or-modify-an-artifact"></a>Műtermék megtekintése vagy módosítása
A következő lépések bemutatják, hogyan tekintheti meg vagy módosíthatja egy műtermék paramétereit:

1. Az **Összetevők alkalmazása** ablaktábla tetején jelölje ki a virtuális géphez hozzáadott összetevők számát jelző hivatkozást.
   
    ![A virtuális géphez hozzáadott összetevők száma](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. A **Kijelölt összetevők** ablaktáblán jelölje ki a megtekinteni vagy szerkesztni kívánt műterméket.  
1. A **Műtermék hozzáadása** ablaktáblán hajtsa végre a szükséges módosításokat, és az **OK** gombra a **Műtermék hozzáadása** ablaktábla bezárásához.
1. A Kijelölt összetevők ablaktábla bezárásához válassza az **OK** **gombot.**

## <a name="use-powershell"></a>A PowerShell használata
A következő parancsfájl a megadott műtermék a megadott virtuális gépre alkalmazza. Az [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) parancs hajtja végre a műveletet.  

```powershell
#Requires -Module Az.Resources

param
(
[Parameter(Mandatory=$true, HelpMessage="The ID of the subscription that contains the lab")]
   [string] $SubscriptionId,
[Parameter(Mandatory=$true, HelpMessage="The name of the lab containing the virtual machine")]
   [string] $DevTestLabName,
[Parameter(Mandatory=$true, HelpMessage="The name of the virtual machine")]
   [string] $VirtualMachineName,
[Parameter(Mandatory=$true, HelpMessage="The repository where the artifact is stored")]
   [string] $RepositoryName,
[Parameter(Mandatory=$true, HelpMessage="The artifact to apply to the virtual machine")]
   [string] $ArtifactName,
[Parameter(ValueFromRemainingArguments=$true)]
   $Params
)

# Set the appropriate subscription
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null
 
# Get the lab resource group name
$resourceGroupName = (Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
if ($resourceGroupName -eq $null) { throw "Unable to find lab $DevTestLabName in subscription $SubscriptionId." }

# Get the internal repo name
$repository = Get-AzResource -ResourceGroupName $resourceGroupName `
                    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
                    -ResourceName $DevTestLabName `
                    -ApiVersion 2016-05-15 `
                    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
                    | Select-Object -First 1

if ($repository -eq $null) { "Unable to find repository $RepositoryName in lab $DevTestLabName." }

# Get the internal artifact name
$template = Get-AzResource -ResourceGroupName $resourceGroupName `
                -ResourceType "Microsoft.DevTestLab/labs/artifactSources/artifacts" `
                -ResourceName "$DevTestLabName/$($repository.Name)" `
                -ApiVersion 2016-05-15 `
                | Where-Object { $ArtifactName -in ($_.Name, $_.Properties.title) } `
                | Select-Object -First 1

if ($template -eq $null) { throw "Unable to find template $ArtifactName in lab $DevTestLabName." }

# Find the virtual machine in Azure
$FullVMId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                /providers/Microsoft.DevTestLab/labs/$DevTestLabName/virtualmachines/$virtualMachineName"

$virtualMachine = Get-AzResource -ResourceId $FullVMId

# Generate the artifact id
$FullArtifactId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                        /providers/Microsoft.DevTestLab/labs/$DevTestLabName/artifactSources/$($repository.Name)`
                        /artifacts/$($template.Name)"

# Handle the inputted parameters to pass through
$artifactParameters = @()

# Fill artifact parameter with the additional -param_ data and strip off the -param_
$Params | ForEach-Object {
   if ($_ -match '^-param_(.*)') {
      $name = $_.TrimStart('^-param_')
   } elseif ( $name ) {
      $artifactParameters += @{ "name" = "$name"; "value" = "$_" }
      $name = $null #reset name variable
   }
}

# Create structure for the artifact data to be passed to the action

$prop = @{
artifacts = @(
    @{
        artifactId = $FullArtifactId
        parameters = $artifactParameters
    }
    )
}

# Check the VM
if ($virtualMachine -ne $null) {
   # Apply the artifact by name to the virtual machine
   $status = Invoke-AzResourceAction -Parameters $prop -ResourceId $virtualMachine.ResourceId -Action "applyArtifacts" -ApiVersion 2016-05-15 -Force
   if ($status.Status -eq 'Succeeded') {
      Write-Output "##[section] Successfully applied artifact: $ArtifactName to $VirtualMachineName"
   } else {
      Write-Error "##[error]Failed to apply artifact: $ArtifactName to $VirtualMachineName"
   }
} else {
   Write-Error "##[error]$VirtualMachine was not found in the DevTest Lab, unable to apply the artifact"
}

```

## <a name="next-steps"></a>További lépések
Lásd a műtermékekről szóló alábbi cikkeket:

- [Kötelező összetevők megadása a laborhoz](devtest-lab-mandatory-artifacts.md)
- [Egyéni összetevők létrehozása](devtest-lab-artifact-author.md)
- [Összetevő-adattár hozzáadása egy laborhoz](devtest-lab-artifact-author.md)
- [Az összetevők hibáinak diagnosztizálása](devtest-lab-troubleshoot-artifact-failure.md)
