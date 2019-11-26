---
title: Sample - Billing tags policy initiative
description: This sample policy initiative (policy set) requires specified tag values defined in parameters for cost center and product name.
ms.date: 01/23/2019
ms.topic: sample
ms.openlocfilehash: 449909b160c2b811c62cd8c6592e74dd0f714e41
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463389"
---
# <a name="sample---billing-tags-policy-initiative"></a>Sample - Billing tags policy initiative

Ehhez a szabályzathoz a költséghely és a terméknév címkének a megadott értékekkel kell rendelkeznie. A példa beépített szabályzatokat használ a szükséges címkék hozzáadásához és előírásához. A címkék szükséges értékeit Ön adja meg.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Példasablon

[!code-json[main](../../../../policy-templates/samples/PolicyInitiatives/multiple-billing-tags/azurepolicyset.json "Billing Tags Policy Initiative")]

You can deploy this template with [PowerShell](#deploy-with-powershell).

## <a name="deploy-with-powershell"></a>Üzembe helyezés a PowerShell-lel

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$policydefinitions = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/multiple-billing-tags/azurepolicyset.definitions.json"
$policysetparameters = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/multiple-billing-tags/azurepolicyset.parameters.json"

$policyset= New-AzPolicySetDefinition -Name "multiple-billing-tags" -DisplayName "Billing Tags Policy Initiative" -Description "Specify cost Center tag and product name tag" -PolicyDefinition $policydefinitions -Parameter $policysetparameters

New-AzPolicyAssignment -PolicySetDefinition $policyset -Name <assignmentname> -Scope <scope>  -costCenterValue <required value for Cost Center tag> -productNameValue <required value for product Name tag>
```

### <a name="clean-up-powershell-deployment"></a>PowerShell-üzembehelyezés eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="apply-tags-to-existing-resources"></a>Címkék hozzáadása meglévő erőforrásokhoz

A szabályzatok hozzárendelését követően aktiválhat egy frissítést, amely az összes létező erőforráson alkalmazza a hozzáadott címkeszabályzatokat. A következő szkript minden egyéb címkét megőriz az erőforrásokon:

```azurepowershell-interactive
$resources = Get-AzResource -ResourceGroupName 'ExampleGroup'

foreach ($r in $resources) {
    try {
        Set-AzResource -Tags ($a = if ($r.Tags -eq $NULL) { @{} } else {$r.Tags}) -ResourceId $r.ResourceId -Force -UsePatchSemantics
    }
    catch {
        Write-Host $r.ResourceId + "can't be updated"
    }
}
```

## <a name="next-steps"></a>Következő lépések

- További példákat [Az Azure Policy-minták](index.md) oldalon talál.