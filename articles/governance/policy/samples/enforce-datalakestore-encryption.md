---
title: Azure Policy-példa – Titkosítás megkövetelése a Data Lake Store-hoz
description: Ez a mintaszabályzat megköveteli a titkosítást a Data Lake Store-hoz.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 278b81fc193851d8cfc9576777818d9dda6f2497
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46970787"
---
# <a name="require-data-lake-store-encryption"></a>Data Lake Store-titkosítás megkövetelése

Ez a beépített szabályzat elutasít minden olyan Data Lake Store-fiókot, ahol nincs engedélyezve a titkosítás.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Példasablon

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.DataLakeStore/accounts"
      },
      {
        "field": "Microsoft.DataLakeStore/accounts/encryptionState",
        "equals": "Disabled"
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

A sablon az [Azure Portal](#deploy-with-the-portal), a [PowerShell](#deploy-with-powershell) vagy az [Azure CLI](#deploy-with-azure-cli) használatával helyezhető üzembe. A beépített szabályzat lekéréséhez használja a következő azonosítót: `a7ff3161-0087-490a-9ad9-ad6217f4f43a`.

## <a name="deploy-with-the-portal"></a>Üzembe helyezés a portállal

Szabályzat hozzárendelésekor válassza a **Titkosítás kényszerítése Data Lake Store-fiókokban** lehetőséget az elérhető beépített definíciók közül.

## <a name="deploy-with-powershell"></a>Üzembe helyezés a PowerShell-lel

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

```azurepowershell-interactive
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/a7ff3161-0087-490a-9ad9-ad6217f4f43a

New-AzureRmPolicyAssignment -name "Data Lake Store encryption" -PolicyDefinition $definition -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>PowerShell-üzembehelyezés eltávolítása

A szabályzat-hozzárendelés törléséhez futtassa a következő parancsot.

```azurepowershell-interactive
Remove-AzureRmPolicyAssignment -Name "Data Lake Store encryption" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure CLI-vel

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "Data Lake Store encryption" --policy a7ff3161-0087-490a-9ad9-ad6217f4f43a
```

### <a name="clean-up-azure-cli-deployment"></a>Az Azure CLI üzemelő példányának eltávolítása

A szabályzat-hozzárendelés törléséhez futtassa a következő parancsot.

```azurecli-interactive
az policy assignment delete --name "Data Lake Store encryption" --resource-group myResourceGroup
```

## <a name="next-steps"></a>További lépések

- További minták [Az Azure Policy mintái](index.md) oldalon