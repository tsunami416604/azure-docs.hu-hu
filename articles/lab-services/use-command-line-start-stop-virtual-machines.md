---
title: A virtuális gépek indítása és leállítása parancssori eszközökkel az Azure DevTest Labs
description: Ismerje meg, hogyan indíthat el és állíthat le parancssori eszközöket az Azure DevTest Labs virtuális gépeinek indításához és leállításához.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: fd643559a09d5c75aad9be5f35c653994c8488cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169252"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Az Azure DevTest Labs virtuális gépek indításához és leállításához parancssori eszközök használata
Ez a cikk bemutatja, hogyan használhatja az Azure PowerShell vagy az Azure CLI virtuális gépek indítása vagy leállítása egy laborban az Azure DevTest Labs. PowerShell/CLI parancsfájlok létrehozása a műveletek automatizálásához. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés
Az Azure DevTest Labs segítségével gyors, egyszerű és karcsú fejlesztési és tesztelési környezeteket hozhat létre. Lehetővé teszi a költségek kezelését, a virtuális gépek gyors üzembe, valamint a hulladék minimalizálását.  Az Azure Portal beépített funkciói lehetővé teszik, hogy konfigurálja a virtuális gépeket egy tesztkörnyezetben, hogy automatikusan elinduljanak és leálljanak bizonyos időpontokban. 

Bizonyos esetekben azonban érdemes automatizálni a virtuális gépek indítását és leállítását a PowerShell/CLI-parancsfájlokból. Ez ad némi rugalmasságot a kezdő és megállás i egyes gépek bármikor helyett adott időpontokban. Íme néhány olyan helyzet, amelyben ezek a feladatok parancsfájlok használatával történő futtatása hasznos lehet.

- Ha egy háromrétegű alkalmazást használ egy tesztkörnyezet részeként, a rétegeket egymás után kell elindítani. 
- Kapcsolja ki a virtuális gép, ha egy egyéni feltételek teljesülnek, hogy pénzt takarítson meg. 
- Ci/CD-munkafolyamaton belüli feladatként használható a folyamat elején történő indításhoz, a virtuális gépek buildgépekként, tesztgépekként vagy infrastruktúraként való használatához, majd állítsa le a virtuális gépeket, amikor a folyamat befejeződött. Erre példa az Azure DevTest Labs egyéni lemezképgyára.  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> A következő parancsfájl az Azure PowerShell Az modult használja. 

A következő PowerShell-parancsfájl elindítja a virtuális gép egy laborban. [Az Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) a parancsfájl elsődleges fókusza. A **ResourceId** paraméter a laborban lévő virtuális gép teljesen minősített erőforrás-azonosítója. A **Művelet** paraméter az, ahol a **Kezdés** vagy **a Leállítás** beállítás attól függően van beállítva, hogy mire van szükség.

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


## <a name="azure-cli"></a>Azure CLI
Az [Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) egy másik módja a DevTest Labs virtuális gépek indításának és leállításának automatizálására. Az Azure CLI különböző operációs rendszerekre [telepíthető.](/cli/azure/install-azure-cli?view=azure-cli-latest) A következő parancsfájl parancsokat ad a virtuális gép indításához és leállításához egy laborban. 

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
Tekintse meg a következő cikket az Azure Portal használatával a következő műveletek: [Indítsa újra a virtuális gép.](devtest-lab-restart-vm.md)
