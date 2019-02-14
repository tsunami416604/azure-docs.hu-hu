---
title: Minta – címke és címke értékének az erőforráscsoportokkal kényszerítése
description: Ez a mintaszabályzat előírja egy címke és egy érték használatát egy erőforráscsoporthoz.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/31/2019
ms.author: dacoulte
ms.openlocfilehash: 8fd9ba2e6e5eae43476102c8928c67d6015ec2eb
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245660"
---
# <a name="enforce-tag-and-its-value-on-resource-groups"></a>Címke és címkeérték kényszerített használata erőforráscsoportokra

Ez a szabályzat előírja egy címke és egy érték használatát egy erőforráscsoporthoz. Ön adja meg a kötelező címkenevet és -értéket.

Ennek a minta szabályzatnak az üzembe helyezéséhez a következőre lesz szükség:

- Az [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure CLI](#azure-cli)
- [REST API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Minta szabályzat

### <a name="policy-definition"></a>Szabályzatdefiníció

A REST API által, az „Üzembe helyezés az Azure-ban” gombok által, valamint a portálon manuálisan használt teljes összeállított JSON szabályzatdefiníció.

[!code-json[main](../../../../policy-templates/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.json "Enforce tag and its value on resource groups")]

> [!NOTE]
> Ha manuálisan hoz létre a portálon szabályzatot, használja a fenti **properties.parameters** és **properties.policyRule** részeket. A két szakaszt kapcsos zárójelekkel `{}` összekapcsolva érvényes JSON-t hozhat létre.

### <a name="policy-rules"></a>Szabályzat szabályai

A szabályzat szabályait Azure CLI és Azure PowerShell segítségével meghatározó JSON.

[!code-json[rule](../../../../policy-templates/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Szabályzatparaméterek

A szabályzat paramétereit Azure CLI és Azure PowerShell segítségével meghatározó JSON.

[!code-json[parameters](../../../../policy-templates/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.parameters.json "Policy parameters (JSON)")]

|Name (Név) |Typo |Mező |Leírás |
|---|---|---|---|
|tagName |String |tags |A címke neve, például costCenter|
|tagValue |String |tags |A címke értéke, például headquarter|

Ha PowerShell vagy Azure CLI segítségével hoz létre egy hozzárendelést, a paraméterértékek átadhatók JSON-ként akár sztring formában, akár egy `-PolicyParameter` (PowerShell) vagy `--params` (Azure CLI) elemet használó fájlban.
A PowerShell a `-PolicyParameterObject` elemet is támogatja, ehhez a parancsmagnak át kell adni egy Name/Value kivonattáblát, ahol **Name** a paraméter neve, **Value** pedig a hozzárendelés során átadott érték vagy értéktömb.

Ebben a példaparaméterben a _tagName_ a **costCenter**, a _tagValue_ pedig a **headquarter**.

```json
{
    "tagName": {
        "value": "costCenter"
    },
    "tagValue": {
        "value": "headquarter"
    }
}
```

## <a name="azure-portal"></a>Azure Portal

[![Üzembe helyezés az Azure-ban](../media/deploy/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FResourceGroup%2Fenforce-resourceGroup-tags%2Fazurepolicy.json)
[![Üzembe helyezés az Azure Governmentben](../media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FResourceGroup%2Fenforce-resourceGroup-tags%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Üzembe helyezés az Azure PowerShell-lel

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'enforce-resourceGroup-tags' -DisplayName 'Enforce tag and its value on resource groups' -description 'Enforces a required tag and its value on resource groups.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyParam = '{ "tagName": { "value": "costCenter" }, "tagValue": { "value": "headquarter" } }'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'enforce-resourceGroup-tags-assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyParam
```

### <a name="remove-with-azure-powershell"></a>Eltávolítás az Azure PowerShell-lel

Az előző hozzárendelés és definíció eltávolításához futtassa az alábbi parancsokat:

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Azure PowerShell magyarázat

Az üzembe helyezési és eltávolítási szkriptek a következő parancsokat használják. Az alábbi táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik:

| Parancs | Megjegyzések |
|---|---|
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-Azpolicydefinition) | Létrehoz egy új Azure Policy-definíciót. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-Azresourcegroup) | Egyetlen erőforráscsoportot kér le. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) | Létrehoz egy új Azure Policy-hozzárendelést. Ebben a példában adunk hozzá egy definíciót, de használhat egy kezdeményezést is. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-Azpolicyassignment) | Eltávolít egy létező Azure Policy-hozzárendelést. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-Azpolicydefinition) | Eltávolít egy létező Azure Policy-definíciót. |

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure CLI-vel

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'enforce-resourceGroup-tags' --display-name 'Enforce tag and its value on resource groups' --description 'Enforces a required tag and its value on resource groups.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.parameters.json' --mode All)

# Set the scope to a resource group; may also be a resource, subscription, or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyParam='{ "tagName": { "value": "costCenter" }, "tagValue": { "value": "headquarter" } }'

# Create the Policy Assignment
assignment=$(
az policy assignment create --name 'enforce-resourceGroup-tags-assignment' --display-name 'Enforce tag and its value on resource groups'  --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
```

### <a name="remove-with-azure-cli"></a>Eltávolítás az Azure CLI segítségével

Az előző hozzárendelés és definíció eltávolításához futtassa az alábbi parancsokat:

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Azure CLI magyarázat

| Parancs | Megjegyzések |
|---|---|
| [az policy definition create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | Létrehoz egy új Azure Policy-definíciót. |
| [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show) | Egyetlen erőforráscsoportot kér le. |
| [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | Létrehoz egy új Azure Policy-hozzárendelést. Ebben a példában adunk hozzá egy definíciót, de használhat egy kezdeményezést is. |
| [az policy assignment delete](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | Eltávolít egy létező Azure Policy-hozzárendelést. |
| [az policy definition delete](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | Eltávolít egy létező Azure Policy-definíciót. |

Számos eszköz alkalmas a Resource Manager REST API-val való kommunikációra, például az [ARMClient](https://github.com/projectkudu/ARMClient) vagy a PowerShell. A REST API PowerShellből való meghívására a **Szabályzatdefiníciók struktúrája** [Aliasok](../concepts/definition-structure.md#aliases) szakaszában találhat példát.

## <a name="rest-api"></a>REST API

### <a name="deploy-with-rest-api"></a>Üzembe helyezés a REST API-val

- Hozza létre a szabályzat definícióját (Előfizetési hatókör). A kérelem törzséhez használja a [szabályzatdefiníció](#policy-definition) JSON-t.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/enforce-resourceGroup-tags?api-version=2016-12-01
  ```

- Hozza létre a szabályzat hozzárendelését (Erőforráscsoporti hatókör)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/enforce-resourceGroup-tags-assignment?api-version=2017-06-01-preview
  ```

  A kérelem törzséhez használja az alábbi JSON-mintát:

```json
  {
      "properties": {
          "displayName": "Enforce tag and its value Assignment",
          "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/enforce-resourceGroup-tags",
          "parameters": {
              "tagName": {
                  "value": "costCenter"
              },
              "tagValue": {
                  "value": "headquarter"
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Eltávolítás a REST API-val

- A szabályzat-hozzárendelés eltávolítása

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/enforce-resourceGroup-tags-assignment?api-version=2017-06-01-preview
  ```

- A szabályzatdefiníció eltávolítása

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/enforce-resourceGroup-tags?api-version=2016-12-01
  ```

### <a name="rest-api-explanation"></a>REST API magyarázat

| Szolgáltatás | Csoport | Művelet | Megjegyzések |
|---|---|---|---|
| Erőforrás-kezelés | Szabályzatdefiníciók | [Létrehozás](/rest/api/resources/policydefinitions/createorupdate) | Létrehoz egy új Azure Policy definíciót egy előfizetésnél. Alternatív: [Hozzon létre, amikor a felügyeleti csoport](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| Erőforrás-kezelés | Szabályzat-hozzárendelések | [Létrehozás](/rest/api/resources/policyassignments/create) | Létrehoz egy új Azure Policy-hozzárendelést. Ebben a példában adunk hozzá egy definíciót, de használhat egy kezdeményezést is. |
| Erőforrás-kezelés | Szabályzat-hozzárendelések | [Törlés](/rest/api/resources/policyassignments/delete) | Eltávolít egy létező Azure Policy-hozzárendelést. |
| Erőforrás-kezelés | Szabályzatdefiníciók | [Törlés](/rest/api/resources/policydefinitions/delete) | Eltávolít egy létező Azure Policy-definíciót. Alternatív: [A felügyeleti csoport törlése](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>További lépések

- További [Azure Policy-minták](index.md) áttekintése
- Az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakasz áttekintése