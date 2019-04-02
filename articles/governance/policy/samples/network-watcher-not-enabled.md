---
title: Minta - naplózási, ha a régió nem engedélyezett a Network Watcher
description: A szabályzatdefiníció-minta naplózza, ha a network watcher nincs engedélyezve egy meghatározott régióban
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: 841f1ad3e22c40e05489bb590caee64ec50466b8
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58800941"
---
# <a name="sample---audit-if-network-watcher-is-not-enabled-for-region"></a>Minta - naplózási, ha a régió nem engedélyezett a Network Watcher

Ez a szabályzat naplót készít, ha a Network Watcher nincs engedélyezve egy adott régióban. Megadhatja a régió nevét, és ellenőrizheti, hogy engedélyezve van-e a Network Watcher.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Példasablon

[!code-json[main](../../../../policy-templates/samples/Network/audit-network-watcher-existence/azurepolicy.json "Audit if Network Watcher is not enabled for region")]

A sablon az [Azure Portal](#deploy-with-the-portal), a [PowerShell](#deploy-with-powershell) vagy az [Azure CLI](#deploy-with-azure-cli) használatával helyezhető üzembe.

## <a name="deploy-with-the-portal"></a>Üzembe helyezés a portállal

[![Az Azure-bA a szabályzat minta üzembe helyezése](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FNetwork%2Faudit-network-watcher-existence%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Üzembe helyezés a PowerShell-lel

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "audit-network-watcher-existence" -DisplayName "Audit if Network Watcher is not enabled for region" -description "This policy audits if Network Watcher is not enabled for a selected region." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/audit-network-watcher-existence/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/audit-network-watcher-existence/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -location <Audit if Network Watcher is not enabled for region> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>PowerShell-üzembehelyezés eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure CLI-vel

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'audit-network-watcher-existence' --display-name 'Audit if Network Watcher is not enabled for region' --description 'This policy audits if Network Watcher is not enabled for a selected region.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/audit-network-watcher-existence/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/audit-network-watcher-existence/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "audit-network-watcher-existence"
```

### <a name="clean-up-azure-cli-deployment"></a>Az Azure CLI üzemelő példányának eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

- További példákat [Az Azure Policy-minták](index.md) oldalon talál.