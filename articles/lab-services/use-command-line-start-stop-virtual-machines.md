---
title: A virtuális gépek elindításához és leállításához használható parancssori eszközök használata Azure DevTest Labs | Microsoft Docs
description: Megtudhatja, hogyan indíthatja el és állíthatja le a virtuális gépeket a Azure DevTest Labsban a parancssori eszközök használatával.
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
ms.openlocfilehash: 8e00de295a7f41bf0ff768c4f948a667bc188616
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456947"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Azure DevTest Labs virtuális gépek indítása és leállítása parancssori eszközök használatával
Ez a cikk bemutatja, hogyan indíthatja el vagy állíthatja le a virtuális gépeket a Azure PowerShell vagy az Azure CLI használatával a Azure DevTest Labs tesztkörnyezetben. A műveletek automatizálásához létrehozhat PowerShell-vagy CLI-parancsfájlokat is. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés
Azure DevTest Labs gyors, könnyen használható és karcsú fejlesztési és tesztelési környezeteket hozhat létre. Lehetővé teszi a díjak kezelését, a virtuális gépek gyors üzembe helyezését és a hulladék minimalizálását.  A Azure Portal beépített funkciói lehetővé teszik, hogy a virtuális gépeket egy laborban konfigurálja, hogy automatikusan induljon el és álljon le adott időpontokban. 

Bizonyos esetekben azonban érdemes lehet automatizálni a virtuális gépek indítását és leállítását a PowerShell-vagy CLI-parancsfájlokból. Némi rugalmasságot biztosít az egyes gépek elindításához és leállításához, a megadott időpontok helyett. Íme néhány olyan helyzet, amelyben a feladatok parancsfájlok használatával történő futtatása hasznos lehet.

- Ha egy tesztelési környezet részeként 3 rétegbeli alkalmazást használ, a rétegeket egy sorba kell kezdeni. 
- Kapcsolja ki a virtuális gépet, ha az egyéni feltételek teljesülnek a pénz megtakarítása érdekében. 
- Egy CI/CD-munkafolyamaton belül feladatként használhatja a folyamat elejére, a virtuális gépeket Build Machines, test Machines vagy Infrastructure néven használhatja, majd leállíthatja a virtuális gépeket, amikor a folyamat befejeződött. Erre példa az egyéni rendszerkép-előállító Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> A következő szkript a Azure PowerShell az modult használja. 

A következő PowerShell-parancsfájl egy virtuális gépet indít el egy laborban. A [meghívó-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) a parancsfájl elsődleges fókusza. A **ResourceId** paraméter a virtuális gép teljes erőforrás-azonosítója a laborban. A **műveleti** paraméter az a hely, ahol a szükséges **indítási** vagy **leállítási** beállítások vannak megadva.

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
$devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName

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


## <a name="azure-cli"></a>Azure parancssori felület (CLI)
Az [Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) egy másik módszer a DevTest Labs-beli virtuális gépek indításának és leállításának automatizálására. Az Azure CLI különböző operációs rendszerekre [telepíthető](/cli/azure/install-azure-cli?view=azure-cli-latest) . A következő szkript egy virtuális gép elindítására és leállítására szolgáló parancsokat biztosít a laborban. 

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


## <a name="next-steps"></a>Következő lépések
Az alábbi cikkből megtudhatja, hogyan használhatja a Azure Portal a következő műveletek elvégzéséhez: [indítsa újra a virtuális gépet](devtest-lab-restart-vm.md).
