---
title: "Az Azure házirend json-mintát – nem engedélyezett típusú erőforrások |} Microsoft Docs"
description: "Json minta meggátolja, hogy a megadott erőforrástípus telepítését."
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
ms.openlocfilehash: 5dc39dbf06335fb7c12d473e44fdce2be4960c85
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="not-allowed-resource-types"></a>Nem engedélyezett típusú erőforrások

Ez a házirend tiltja, hogy a központi telepítés megadott erőforrás típusa. Megadhat letilthatja az erőforrástípusok tömbjét.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Minta sablon

[!code-json[main](../../../policy-templates/samples/built-in-policy/not-allowed-resourcetypes/azurepolicy.json "Not allowed resource types")]


A sablon használatával telepíthető a [Azure-portálon](#deploy-with-the-portal), a [PowerShell](#deploy-with-powershell) vagy a [Azure CLI](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Üzembe helyezés a portállal

[![Üzembe helyezés az Azure-ban](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade)

## <a name="deploy-with-powershell"></a>Üzembe helyezés a PowerShell-lel

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]


````powershell
$definition = New-AzureRmPolicyDefinition -Name "not-allowed-resourcetypes" -DisplayName "Not allowed resource types" -description "This policy enables you to specify the resource types that your organization cannot deploy." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/not-allowed-resourcetypes/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/not-allowed-resourcetypes/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope> -PolicyDefinition $definition
$assignment
````

### <a name="clean-up-powershell-deployment"></a>PowerShell központi telepítés tisztítása

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```


## <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure parancssori felületén keresztül

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

````cli

az policy definition create --name 'not-allowed-resourcetypes' --display-name 'Not allowed resource types' --description 'This policy enables you to specify the resource types that your organization cannot deploy.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/not-allowed-resourcetypes/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/not-allowed-resourcetypes/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "not-allowed-resourcetypes"

````

### <a name="clean-up-azure-cli-deployment"></a>Az Azure parancssori felület központi telepítés tisztítása

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Következő lépések

- További Azure csoportházirend-sablon minták erővel [sablonok Azure házirend](../json-samples.md).
