---
title: "Az Azure házirend json minta - hez egy erőforráscsoportból egyéni Virtuálisgép-lemezkép |} Microsoft Docs"
description: "A json-mintát házirend szükséges, hogy az egyéni lemezképek származnia egy jóváhagyott erőforráscsoportot."
services: azure-policy
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 
ms.service: azure-policy
ms.devlang: 
ms.topic: sample
ms.tgt_pltfrm: 
ms.workload: 
ms.date: 10/30/2017
ms.author: banders
ms.custom: mvc
ms.openlocfilehash: 3da9a459f7e3008fc6a75473cc1a239d9cd5749b
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2017
---
# <a name="allow-custom-vm-image-from-a-resource-group"></a>Az erőforráscsoport egyéni Virtuálisgép-lemezkép engedélyezése

A json-mintát házirend szükséges, hogy az egyéni lemezképek származnia egy jóváhagyott erőforráscsoportot. Megadhatja a jóváhagyott erőforráscsoport nevét.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Minta sablon

[!code-json[main](../../../policy-templates/samples/compute/custom-image-from-rg/azurepolicy.json "Allow custom VM image from a Resource Group")]


A sablon használatával telepíthető a [Azure-portálon](#deploy-with-the-portal), a [PowerShell](#deploy-with-powershell) vagy a [Azure CLI](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Üzembe helyezés a portállal

[![Üzembe helyezés az Azure-ban](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fcustom-image-from-rg%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Üzembe helyezés a PowerShell-lel

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

```powershell
$definition = New-AzureRmPolicyDefinition -Name "custom-image-from-rg" -DisplayName "Allow custom VM image from a Resource Group" -description "This policy allows only usage of images from a resource group" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/custom-image-from-rg/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/custom-image-from-rg/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope>  -resourceGroupName <Resource Group Name> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>PowerShell központi telepítés tisztítása

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure parancssori felületén keresztül

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'custom-image-from-rg' --display-name 'Allow custom VM image from a Resource Group' --description 'This policy allows only usage of images from a resource group' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/custom-image-from-rg/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/custom-image-from-rg/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "custom-image-from-rg"
```

### <a name="clean-up-azure-cli-deployment"></a>Az Azure parancssori felület központi telepítés tisztítása

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Következő lépések

Az Azure házirend dokumentációjában található további Azure csoportházirend-sablon minták
