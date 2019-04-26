---
title: Minta - diagnosztikai naplózás beállítása
description: A szabályzatdefiníció-minta naplózza, ha a diagnosztikai beállítások nincs engedélyezve a megadott erőforrás-típus.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
origin.date: 04/27/2018
ms.date: 03/11/2019
ms.author: v-biyu
ms.openlocfilehash: 66c9c1c21cad7fb4058a91be826a50059691877c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60545639"
---
# <a name="sample---audit-diagnostic-setting"></a>Minta - diagnosztikai naplózás beállítása

Ez a beépített szabályzat naplózza, ha a diagnosztikai beállítások nincsenek engedélyezve egyes erőforrástípusokhoz. Adjon meg egy erőforrástípus-tömböt, amelyhez ellenőrizni szeretné, hogy engedélyezve vannak-e a diagnosztikai beállítások.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Példasablon
```json
{
    "name": "audit-diagnostic-setting",
    "properties": {
        "displayName": "Audit diagnostic setting",
        "description": "Audit diagnostic setting for selected resource types",
        "mode": "all",
        "parameters": {
            "listOfResourceTypes": {
                "type": "Array",
                "metadata": {
                    "displayName": "Resource Types",
                    "strongType": "resourceTypes"
                }
            }
        },
        "policyRule": {
            "if": {
                "field": "type",
                "in": "[parameters('listOfResourceTypes')]"
            },
            "then": {
                "effect": "auditIfNotExists",
                "details": {
                    "type": "Microsoft.Insights/diagnosticSettings",
                    "existenceCondition": {
                        "allOf": [
                            {
                                "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
                                "equals": "true"
                            },
                            {
                                "field": "Microsoft.Insights/diagnosticSettings/metrics.enabled",
                                "equals": "true"
                            }
                        ]
                    }
                }
            }
        }
    }
}
```
A sablon az [Azure Portal](#deploy-with-the-portal), a [PowerShell](#deploy-with-powershell) vagy az [Azure CLI](#deploy-with-azure-cli) használatával helyezhető üzembe. A beépített szabályzat lekéréséhez használja a következő azonosítót: `7f89b1eb-583c-429a-8828-af049802c1d9`.

## <a name="parameters"></a>Paraméterek

A paraméter átadásához használja az alábbi formátumot:

```json
{"listOfResourceTypes":{"value":["Microsoft.Cache/Redis","Microsoft.Compute/virtualmachines"]}}
```

## <a name="deploy-with-the-portal"></a>Üzembe helyezés a portállal

Szabályzat hozzárendelésekor válassza a **Diagnosztikai beállítás naplózása** lehetőséget az elérhető beépített definíciók közül.

## <a name="deploy-with-powershell"></a>Üzembe helyezés a PowerShell-lel

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```powershell
$definition = Get-AzPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/7f89b1eb-583c-429a-8828-af049802c1d9

New-AzPolicyAssignment -name "Audit diagnostics" -PolicyDefinition $definition -PolicyParameter '{"listOfResourceTypes":{"value":["Microsoft.Cache/Redis","Microsoft.Compute/virtualmachines"]}}' -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>PowerShell-üzembehelyezés eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```powershell
Remove-AzPolicyAssignment -Name "Audit diagnostics" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure CLI-vel

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```cli
az policy assignment create --scope <scope> --name "Audit diagnostics" --policy 7f89b1eb-583c-429a-8828-af049802c1d9 --params '{"listOfResourceTypes":{"value":["Microsoft.Cache/Redis","Microsoft.Compute/virtualmachines"]}}'
```

### <a name="clean-up-azure-cli-deployment"></a>Az Azure CLI üzemelő példányának eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```cli
az policy assignment delete --name "Audit diagnostics" --resource-group myResourceGroup
```

## <a name="next-steps"></a>További lépések

- További példákat [Az Azure Policy-minták](index.md) oldalon talál.
