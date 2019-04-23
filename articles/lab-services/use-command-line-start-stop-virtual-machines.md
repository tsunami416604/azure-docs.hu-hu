---
title: Parancssori eszközök segítségével elindíthatja és leállíthatja a virtuális gépek Azure DevTest Labs |} A Microsoft Docs
description: Ismerje meg, hogyan parancssori eszközök használatával elindíthatja és leállíthatja a virtuális gépek az Azure DevTest Labs szolgáltatásban.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 51c45fdb0c96e84d3f37f485279aa805361f3818
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798929"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Parancssori eszközök segítségével elindíthatja és leállíthatja a virtuális gépek Azure DevTest Labs szolgáltatásban
Ez a cikk bemutatja, hogyan elindítani vagy leállítani a virtuális gépek az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet Azure PowerShell vagy az Azure CLI használatával. Ezek a műveletek automatizálása a PowerShell vagy a parancssori felületen parancsfájlok hozhat létre. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés
Az Azure DevTest Labs szolgáltatás gyors, egyszerű és lean fejlesztési-tesztelési környezeteket hozhat létre. Lehetővé teszi, hogy a költségek kezelése, gyorsan üzembe virtuális gépeket és minimalizálja a selejt.  Nincsenek a beépített funkciók az Azure Portalon, amelyek lehetővé teszik, hogy a virtuális gép konfigurálása automatikusan elindíthatja és leállíthatja a megadott időpontban történő tesztkörnyezetben. 

Azonban bizonyos esetekben előfordulhat, hogy automatizálni szeretné indítása és leállítása, virtuális gépek a PowerShell és CLI-szkriptek. Biztosít néhány lehetőség a indítása és leállítása folyamatban van az egyes gépek megadott időpontban helyett bármikor. Íme néhány a helyzetekben, mely fut az ezeket a feladatokat a parancsfájlok segítségével hasznos lenne.

- Egy tesztkörnyezetben részeként egy 3 szintű alkalmazás használatakor a rétegek kell elindítani, a feladatütemezés. 
- Kikapcsolja a virtuális Gépet, pénzt takaríthat meg egy egyéni feltétel teljesülése esetén. 
- Használhatja a CI/CD-munkafolyamaton belül feladatként indítsa el a folyamat elején, használja a virtuális gépek, a gépek hozhat létre, tesztelje a gépeket vagy infrastruktúrát, majd állítsa le a virtuális gépek, ha a folyamat befejeződött. Ilyen például az egyéni rendszerkép-előállító az Azure DevTest Labs lenne.  

## <a name="azure-powershell"></a>Azure PowerShell
A következő PowerShell-parancsfájl egy tesztkörnyezetben egy virtuális gép elindul. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azurermps-6.13.0) referenciamodelljének a szkript van. A **ResourceId** paraméter értéke a lab-ben a virtuális gép teljesen minősített erőforrás-azonosító. A **művelet** paraméter, ahol a **Start** vagy **leállítása** beállításainak függően van szükség.

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab information
if ($(Get-Module -Name AzureRM).Version.Major -eq 6) {
    $devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -Name $devTestLabName
} else {
    $devTestLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $devTestLabName
}

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzResourceAction `
                    -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                    -Action $vmAction `
                    -Force

if ($returnStatus.Status -eq 'Succeeded') {
    Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
}
else {
    Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
}
```


## <a name="azure-cli"></a>Azure CLI
A [Azure CLI-vel](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) másik módja, indítását és leállítását DevTest Labs virtuális gépek automatizálásához. Az Azure CLI lehet [telepített](/cli/azure/install-azure-cli?view=azure-cli-latest) különböző operációs rendszereken. Az alábbi parancsfájlt és a labor virtuális gép leállítási parancsok biztosítja. 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>További lépések
Ezek a műveletek elvégzésére az Azure portal használatával a következő cikkben talál: [Indítsa újra a virtuális gép](devtest-lab-restart-vm.md).
