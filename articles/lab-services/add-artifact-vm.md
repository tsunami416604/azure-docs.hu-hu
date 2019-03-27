---
title: Az összetevő hozzáadása egy virtuális géphez az Azure DevTest Labs |} A Microsoft Docs
description: Ismerje meg, hogyan egy összetevő hozzáadása egy virtuális géphez az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet
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
ms.openlocfilehash: 5e6a7cbc070d81de33fac07a89dabf2b469bd355
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58450089"
---
# <a name="add-an-artifact-to-a-vm"></a>Az összetevő hozzáadása egy virtuális géphez
Virtuális gép létrehozásakor hozzáadhat meglévő összetevőkkel rá. Ezek az összetevők lehetnek, vagy a [nyilvános DevTest Labs Git-tárház](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) vagy a saját Git-adattárból. Ez a cikk bemutatja, hogyan adhat hozzá összetevőket az Azure Portalon, és az Azure PowerShell-lel. 

Az Azure DevTest Labs *összetevők* segítségével megadhatja, hogy *műveletek* , ha a virtuális gép ki van építve, például a Windows PowerShell-parancsfájlok futtatásakor, a Bash-parancsok futtatása és a szoftverek telepítése történik. Összetevő *paraméterek* segítségével testre szabhatja az adott forgatókönyvhöz az összetevőben.

Egyéni összetevők létrehozásával kapcsolatos további tudnivalókért tekintse meg a cikket: [Egyéni összetevők létrehozása](devtest-lab-artifact-author.md).

## <a name="use-azure-portal"></a>Az Azure Portal használata 
1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
1. Tesztkörnyezetek listájában jelölje ki a labor létrehozása a virtuális Gépet, amely a használni kívánt tartalmazó.  
1. Válassza ki **a virtual machines**.
1. Válassza ki a kívánt virtuális Gépet.
1. Válassza ki **az összetevők kezelése**. 
1. Válassza ki **összetevők alkalmazása**.
1. Az a **összetevők alkalmazása** ablaktáblán válassza ki a virtuális géphez hozzá kívánja az összetevőben.
1. Az a **Hozzáadás összetevő** panelen adja meg a szükséges paraméterértékeket, és opcionális paramétereket kell.  
1. Válassza ki **Hozzáadás** összetevő hozzáadása, és térjen vissza a **összetevők alkalmazása** ablaktáblán.
1. Továbbra is összetevők hozzáadása a virtuális gép igény szerint.
1. Miután hozzáadta az összetevőkhöz, is [, amelyben futnak, az összetevők sorrendjének módosítása](#change-the-order-in-which-artifacts-are-run). Is léphet vissza [megtekintése vagy módosítása egy összetevő](#view-or-modify-an-artifact).
1. Hozzáadása összetevők elkészült, válassza ki a **alkalmaz**

### <a name="change-the-order-in-which-artifacts-are-run"></a>Az összetevők futnak, amelyben sorrend módosítása
Alapértelmezés szerint az összetevők végrehajtás a sorrendben, amelyben a virtuális gép kerül. A következő lépések bemutatják, hogyan módosíthatja a sorrendben, amelyben az összetevők futnak.

1. Felső részén a **összetevők alkalmazása** ablaktáblán jelölje ki a kapcsolatot a virtuális géphez hozzáadott összetevők számát jelző.
   
    ![Virtuális géphez hozzáadott összetevők száma](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Az a **kiválasztott összetevők** ablaktáblán áthúzása az összetevők a kívánt sorrendben be. Ha problémája van az összetevőt húzással, ügyeljen arra, hogy a lehívandó összetevő bal oldalán húzza. 
1. Kattintson az **OK** gombra, amikor végzett.  

### <a name="view-or-modify-an-artifact"></a>Megtekintése vagy módosítása egy összetevő
A következő lépések bemutatják, hogyan lehet megtekinteni, vagy módosítsa a paramétereket egy lehívandó összetevő:

1. Felső részén a **összetevők alkalmazása** ablaktáblán jelölje ki a kapcsolatot a virtuális géphez hozzáadott összetevők számát jelző.
   
    ![Virtuális géphez hozzáadott összetevők száma](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Az a **kiválasztott összetevők** ablaktáblán válassza ki a megtekinteni vagy szerkeszteni kívánt összetevő.  
1. Az a **Hozzáadás összetevő** panelen kattintson a jobb tétele bármely szükséges módosításokat, és válassza a **OK** gombra kattintva zárja be a **Hozzáadás összetevő** ablaktáblán.
1. Válassza ki **OK** gombra kattintva zárja be a **kiválasztott összetevők** ablaktáblán.

## <a name="use-powershell"></a>A PowerShell használata
A következő parancsfájl a megadott virtuális gép a megadott összetevő vonatkozik. A [Invoke-AzureRmResourceAction](/powershell/module/azurerm.resources/invoke-azurermresourceaction?view=azurermps-6.13.0) parancs, amely végrehajtja a műveletet.  

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

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
Set-AzureRmContext -SubscriptionId $SubscriptionId | Out-Null
 
# Get the lab resource group name
$resourceGroupName = (Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
if ($resourceGroupName -eq $null) { throw "Unable to find lab $DevTestLabName in subscription $SubscriptionId." }

# Get the internal repo name
$repository = Get-AzureRmResource -ResourceGroupName $resourceGroupName `
                    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
                    -ResourceName $DevTestLabName `
                    -ApiVersion 2016-05-15 `
                    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
                    | Select-Object -First 1

if ($repository -eq $null) { "Unable to find repository $RepositoryName in lab $DevTestLabName." }

# Get the internal artifact name
$template = Get-AzureRmResource -ResourceGroupName $resourceGroupName `
                -ResourceType "Microsoft.DevTestLab/labs/artifactSources/artifacts" `
                -ResourceName "$DevTestLabName/$($repository.Name)" `
                -ApiVersion 2016-05-15 `
                | Where-Object { $ArtifactName -in ($_.Name, $_.Properties.title) } `
                | Select-Object -First 1

if ($template -eq $null) { throw "Unable to find template $ArtifactName in lab $DevTestLabName." }

# Find the virtual machine in Azure
$FullVMId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                /providers/Microsoft.DevTestLab/labs/$DevTestLabName/virtualmachines/$virtualMachineName"

$virtualMachine = Get-AzureRmResource -ResourceId $FullVMId

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
   $status = Invoke-AzureRmResourceAction -Parameters $prop -ResourceId $virtualMachine.ResourceId -Action "applyArtifacts" -ApiVersion 2016-05-15 -Force
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
Az összetevők a következő cikkekben talál:

- [Adja meg a kötelező összetevők a tesztkörnyezethez](devtest-lab-mandatory-artifacts.md)
- [Egyéni összetevők létrehozása](devtest-lab-artifact-author.md)
- [Az összetevőtárban hozzáadása egy laborhoz](devtest-lab-artifact-author.md)
- [Az összetevők hibáinak diagnosztizálása](devtest-lab-troubleshoot-artifact-failure.md)