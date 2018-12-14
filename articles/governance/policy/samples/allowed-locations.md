---
title: Minta - engedélyezett helyek
description: Ez a mintaszabályzat megköveteli, hogy az összes erőforrás a jóváhagyott helyekre legyen telepítve.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 12/12/2018
ms.author: dacoulte
ms.openlocfilehash: aa67133b9f95715d84e9680e1ea45019d722609e
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53386553"
---
# <a name="allowed-locations"></a>Engedélyezett helyek

Ez a szabályzat lehetővé teszi, hogy korlátozni azon helyeket, a szervezet megadhat az erőforrások üzembe helyezésekor. Használja a földrajzi megfelelőségi követelmények kényszerítésére. Nem tartalmazza az erőforráscsoportok Microsoft.AzureActiveDirectory/b2cDirectories és erőforrásokat, a "global" régiót használni. Megadhatja az engedélyezett helyek tömbjét.

Ennek a minta szabályzatnak az üzembe helyezéséhez a következőre lesz szükség:

- Az [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure CLI](#azure-cli)
- [REST API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Minta szabályzat

### <a name="policy-definition"></a>Szabályzatdefiníció

A REST API által, az „Üzembe helyezés az Azure-ban” gombok által, valamint a portálon manuálisan használt teljes összeállított JSON szabályzatdefiníció.

[!code-json[full](../../../../policy-templates/samples/built-in-policy/allowed-locations/azurepolicy.json "Allowed locations")]

> [!NOTE]
> Ha manuálisan hoz létre a portálon szabályzatot, használja a fenti **properties.parameters** és **properties.policyRule** részeket. A két szakaszt kapcsos zárójelekkel `{}` összekapcsolva érvényes JSON-t hozhat létre.

### <a name="policy-rules"></a>Szabályzat szabályai

A szabályzat szabályait Azure CLI és Azure PowerShell segítségével meghatározó JSON.

[!code-json[rule](../../../../policy-templates/samples/built-in-policy/allowed-locations/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Szabályzatparaméterek

A szabályzat paramétereit Azure CLI és Azure PowerShell segítségével meghatározó JSON.

[!code-json[parameters](../../../../policy-templates/samples/built-in-policy/allowed-locations/azurepolicy.parameters.json "Policy parameters (JSON)")]

## <a name="parameters"></a>Paraméterek

|Name (Név) |Típus |Mező |Leírás |
|---|---|---|---|
|listOfAllowedLocations |Tömb |helyek |Engedélyezett helyek listája|

Ha PowerShell vagy Azure CLI segítségével hoz létre egy hozzárendelést, a paraméterértékek átadhatók JSON-ként akár sztring formában, akár egy `-PolicyParameter` (PowerShell) vagy `--params` (Azure CLI) elemet használó fájlban.
A PowerShell a `-PolicyParameterObject` elemet is támogatja, ehhez a parancsmagnak át kell adni egy Name/Value kivonattáblát, ahol **Name** a paraméter neve, **Value** pedig a hozzárendelés során átadott érték vagy értéktömb.

Az ebben a példában paraméter csak a _eastus2_ vagy _westus_ helyeken engedélyezett lesz.

```json
{
    "listOfAllowedLocations": {
        "value": [
            "eastus2",
            "westus"
        ]
    }
}
```

## <a name="azure-portal"></a>Azure Portal

[![Üzembe helyezés az Azure-ban](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fallowed-locations%2Fazurepolicy.json)
[![Üzembe helyezés az Azure Governmentben](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fallowed-locations%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

### <a name="deploy-with-azure-powershell"></a>Üzembe helyezés az Azure PowerShell-lel

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzureRmPolicyDefinition -Name "allowed-locations" -DisplayName "Allowed locations" -description "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements. Excludes resource groups, Microsoft.AzureActiveDirectory/b2cDirectories, and resources that use the 'global' region." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.parameters.json' -Mode Indexed

# Set the scope to a resource group; may also be a subscription or management group
$scope = Get-AzureRmResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyparam = '{ "listOfAllowedLocations": { "value": [ "eastus2", "westus" ] } }'

# Create the Policy Assignment
$assignment = New-AzureRmPolicyAssignment -Name 'allowed-locations-assignment' -DisplayName 'Allowed locations Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyparam
```

### <a name="remove-with-azure-powershell"></a>Eltávolítás az Azure PowerShell-lel

Az előző hozzárendelés és definíció eltávolításához futtassa az alábbi parancsokat:

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzureRmPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzureRmPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Azure PowerShell magyarázat

Az üzembe helyezési és eltávolítási szkriptek a következő parancsokat használják. Az alábbi táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik:

| Parancs | Megjegyzések |
|---|---|
| [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) | Létrehoz egy új Azure Policy-definíciót. |
| [Get-AzureRmResourceGroup](/powershell/module/azurerm.resources/get-azurermresourcegroup) | Egyetlen erőforráscsoportot kér le. |
| [New-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) | Létrehoz egy új Azure Policy-hozzárendelést. Ebben a példában adunk hozzá egy definíciót, de használhat egy kezdeményezést is. |
| [Remove-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/remove-azurermpolicyassignment) | Eltávolít egy létező Azure Policy-hozzárendelést. |
| [Remove-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/remove-azurermpolicydefinition) | Eltávolít egy létező Azure Policy-definíciót. |

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure CLI-vel

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'allowed-locations' --display-name 'Allowed locations' --description 'This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements. Excludes resource groups, Microsoft.AzureActiveDirectory/b2cDirectories, and resources that use the 'global' region.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.parameters.json' --mode Indexed)

# Set the scope to a resource group; may also be a subscription or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyparam='{ "listOfAllowedLocations": { "value": [ "eastus2", "westus" ] } }'

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'allowed-locations-assignment' --display-name 'Allowed locations Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
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

## <a name="rest-api"></a>REST API

Számos eszköz alkalmas a Resource Manager REST API-val való kommunikációra, például az [ARMClient](https://github.com/projectkudu/ARMClient) vagy a PowerShell.

### <a name="deploy-with-rest-api"></a>Üzembe helyezés a REST API-val

- Hozza létre a szabályzat definícióját (Előfizetési hatókör). A kérelem törzséhez használja a [szabályzatdefiníció](#policy-definition) JSON-t.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-locations?api-version=2018-05-01
  ```

- Hozza létre a szabályzat hozzárendelését (Erőforráscsoporti hatókör)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/allowed-locations-assignment?api-version=2018-05-01
  ```

  A kérelem törzséhez használja az alábbi JSON-mintát:

  ```json
  {
      "properties": {
          "displayName": "Allowed locations Assignment",
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/allowed-locations",
          "parameters": {
              "listOfAllowedLocations": {
                  "value": [
                      "eastus2",
                      "westus"
                  ]
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Eltávolítás a REST API-val

- A szabályzat-hozzárendelés eltávolítása

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/allowed-locations-assignment?api-version=2018-05-01
  ```

- A szabályzatdefiníció eltávolítása

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-locations?api-version=2018-05-01
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