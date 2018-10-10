---
title: Azure Policy-példa – Diagnosztikai beállítás naplózása
description: A mintaszabályzat naplózza, ha a diagnosztikai beállítások nincsenek engedélyezve egyes erőforrástípusokhoz.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 1a25d70962b9b93fdf11dcbeedc922b7f108cc9a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46985679"
---
# <a name="audit-diagnostic-setting"></a>Diagnosztikai beállítás naplózása

Ez a beépített szabályzat naplózza, ha a diagnosztikai beállítások nincsenek engedélyezve egyes erőforrástípusokhoz. Adjon meg egy erőforrástípus-tömböt, amelyhez ellenőrizni szeretné, hogy engedélyezve vannak-e a diagnosztikai beállítások.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Példasablon

[!code-json[main](../../../../policy-templates/samples/Monitoring/audit-diagnostic-setting/azurepolicy.json "Audit diagnostic setting")]

A sablon az [Azure Portal](#deploy-with-the-portal), a [PowerShell](#deploy-with-powershell) vagy az [Azure CLI](#deploy-with-azure-cli) használatával helyezhető üzembe. A beépített szabályzat lekéréséhez használja a következő azonosítót: `7f89b1eb-583c-429a-8828-af049802c1d9`.

## <a name="parameters"></a>Paraméterek

A paraméter átadásához használja az alábbi formátumot:

```json
{"listOfResourceTypes":{"value":["Microsoft.Cache/Redis","Microsoft.Compute/virtualmachines"]}}
```

## <a name="deploy-with-the-portal"></a>Üzembe helyezés a portállal

Szabályzat hozzárendelésekor válassza a **Diagnosztikai beállítás naplózása** lehetőséget az elérhető beépített definíciók közül.

## <a name="deploy-with-powershell"></a>Üzembe helyezés a PowerShell-lel

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

```azurepowershell-interactive
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/7f89b1eb-583c-429a-8828-af049802c1d9

New-AzureRmPolicyAssignment -name "Audit diagnostics" -PolicyDefinition $definition -PolicyParameter '{"listOfResourceTypes":{"value":["Microsoft.Cache/Redis","Microsoft.Compute/virtualmachines"]}}' -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>PowerShell-üzembehelyezés eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurepowershell-interactive
Remove-AzureRmPolicyAssignment -Name "Audit diagnostics" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure CLI-vel

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "Audit diagnostics" --policy 7f89b1eb-583c-429a-8828-af049802c1d9 --params '{"listOfResourceTypes":{"value":["Microsoft.Cache/Redis","Microsoft.Compute/virtualmachines"]}}'
```

### <a name="clean-up-azure-cli-deployment"></a>Az Azure CLI üzemelő példányának eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli-interactive
az policy assignment delete --name "Audit diagnostics" --resource-group myResourceGroup
```

## <a name="next-steps"></a>További lépések

- További minták [Az Azure Policy mintái](index.md) oldalon