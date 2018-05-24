---
title: Azure Policy JSON-minta – Engedélyezett termékváltozatok SQL-adatbázisokhoz | Microsoft Docs
description: Ez a JSON-mintaszabályzat megköveteli egy jóváhagyott termékváltozat használatát az SQL-adatbázisokon.
services: azure-policy
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-policy
ms.devlang: ''
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 10/30/2017
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 6e72816204d5d811715d8ef24e9d473e22a7c31c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="allowed-sql-db-skus"></a>Engedélyezett termékváltozatok SQL-adatbázisokhoz

Ez a szabályzat megköveteli egy jóváhagyott termékváltozat használatát az SQL-adatbázisokon. Meg kell adnia egy engedélyezett termékváltozat-azonosítókat tartalmazó tömböt vagy egy engedélyezett termékváltozat-neveket tartalmazó tömböt.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Példasablon

[!code-json[main](../../../policy-templates/samples/SQL/sql-db-skus/azurepolicy.json "Allowed SQL DB SKUs")]

A sablon az [Azure Portal](#deploy-with-the-portal), a [PowerShell](#deploy-with-powershell) vagy az [Azure CLI](#deploy-with-azure-cli) használatával helyezhető üzembe.

## <a name="deploy-with-the-portal"></a>Üzembe helyezés a portállal

[![Üzembe helyezés az Azure-ban](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FSQL%2Fsql-db-skus%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Üzembe helyezés a PowerShell-lel

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]


```powershell
$definition = New-AzureRmPolicyDefinition -Name "sql-db-skus" -DisplayName "Allowed SQL DB SKUs" -description "This policy enables you to specify a set of SQL DB SKUs" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/sql-db-skus/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/sql-db-skus/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope>  -listOfSKUId <Allowed SKU IDs> -listOfSKUName <Allowed SKU Names> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>PowerShell-üzembehelyezés eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure CLI-n keresztül

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'sql-db-skus' --display-name 'Allowed SQL DB SKUs' --description 'This policy enables you to specify a set of SQL DB SKUs' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/sql-db-skus/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/sql-db-skus/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "sql-db-skus"
```

### <a name="clean-up-azure-cli-deployment"></a>Az Azure CLI-n keresztüli üzembe helyezés eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

- További Azure Policy-példasablonokért lásd az [Azure Policy-sablonok](../json-samples.md) témakörét.
