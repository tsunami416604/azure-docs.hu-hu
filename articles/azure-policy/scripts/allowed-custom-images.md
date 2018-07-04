---
title: Azure Policy-minta – Jóváhagyott VM-rendszerképek
description: Ez a szabályzat előírja, hogy csak jóváhagyott lemezképek legyenek telepítve a környezetben.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 06/03/2018
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 408968723ab9dc14a1db1321c4db3b3d8518d511
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319299"
---
# <a name="approved-vm-images"></a>Jóváhagyott VM-lemezképek

Ez a szabályzat előírja, hogy csak jóváhagyott lemezképek legyenek telepítve a környezetben. Meg kell adnia egy jóváhagyott lemezazonosítókat tartalmazó tömböt.

Ennek a minta szabályzatnak az üzembe helyezéséhez a következőre lesz szükség:

- Az [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure CLI](#azure-cli)
- [REST API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Minta szabályzat

### <a name="policy-definition"></a>Szabályzatdefiníció

A REST API által, az „Üzembe helyezés az Azure-ban” gombok által, valamint a portálon manuálisan használt teljes összeállított JSON szabályzatdefiníció.

[!code-json[full](../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.json "Complete policy definition (JSON)")]

> [!NOTE]
> Ha manuálisan hoz létre a portálon szabályzatot, használja a fenti **properties.parameters** és **properties.policyRule** részeket. A két szakaszt kapcsos zárójelekkel `{}` összekapcsolva érvényes JSON-t hozhat létre.

### <a name="policy-rules"></a>Szabályzat szabályai

A szabályzat szabályait Azure CLI és Azure PowerShell segítségével meghatározó JSON.

[!code-json[rule](../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Szabályzatparaméterek

A szabályzat paramétereit Azure CLI és Azure PowerShell segítségével meghatározó JSON.

[!code-json[parameters](../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.parameters.json "Policy parameters (JSON)")]

## <a name="parameters"></a>Paraméterek

|Name (Név) |Típus |Mező |Leírás |
|---|---|---|---|
|imageIds |Tömb |Microsoft.Compute/imageIds |A jóváhagyott VM-rendszerképek listája|

Ha PowerShell vagy Azure CLI segítségével hoz létre egy hozzárendelést, a paraméterértékek átadhatók JSON-ként akár sztring formában, akár egy `-PolicyParameter` (PowerShell) vagy `--params` (Azure CLI) elemet használó fájlban.
A PowerShell a `-PolicyParameterObject` elemet is támogatja, ehhez a parancsmagnak át kell adni egy Name/Value kivonattáblát, ahol **Name** a paraméter neve, **Value** pedig a hozzárendelés során átadott érték vagy értéktömb.

Ebben a példa paraméterben csak a _YourResourceGroup_ erőforráscsoportban található _ContosoStdImage_ vagy az USA középső régiójában található Windows Server 2016 Datacenter 2018 májusi rendszerképverziója engedélyezett.

```json
{
    "imageIds": {
        "value": [
            "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage",
            "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510"
        ]
    }
}
```

## <a name="azure-portal"></a>Azure Portal

[![Üzembe helyezés az Azure-ban](../media/deploy/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fallowed-custom-images%2Fazurepolicy.json)
[![Üzembe helyezés az Azure Governmentben](../media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fallowed-custom-images%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

### <a name="deploy-with-azure-powershell"></a>Üzembe helyezés az Azure PowerShell-lel

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzureRmPolicyDefinition -Name 'allowed-custom-images' -DisplayName 'Approved VM images' -description 'This policy governs the approved VM images' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a subscription or management group
$scope = Get-AzureRmResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyparam = '{ "imageIds": { "value": [ "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage", "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510" ] } }'

# Create the Policy Assignment
$assignment = New-AzureRmPolicyAssignment -Name 'allowed-custom-images-assignment' -DisplayName 'Approved VM images Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyparam
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

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure CLI-vel

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'allowed-custom-images' --display-name 'Approved VM images' --description 'This policy governs the approved VM images' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.parameters.json' --mode All)

# Set the scope to a resource group; may also be a subscription or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyparam='{ "imageIds": { "value": [ "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage", "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510" ] } }'

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'allowed-custom-images-assignment' --display-name 'Approved VM images Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
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

Számos eszköz alkalmas a Resource Manager REST API-val való kommunikációra, például az [ARMClient](https://github.com/projectkudu/ARMClient) vagy a PowerShell. A REST API PowerShellből való meghívására a **Szabályzatdefiníciók struktúrája** [Aliasok](../policy-definition.md#aliases) szakaszában találhat példát.

### <a name="deploy-with-rest-api"></a>Üzembe helyezés a REST API-val

- Hozza létre a szabályzat definícióját (Előfizetési hatókör). A kérelem törzséhez használja a [szabályzatdefiníció](#policy-definition) JSON-t.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images?api-version=2016-12-01
  ```

- Hozza létre a szabályzat hozzárendelését (Erőforráscsoporti hatókör)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/allowed-custom-images-assignment?api-version=2017-06-01-preview
  ```

  A kérelem törzséhez használja az alábbi JSON-mintát:

  ```json
  {
      "properties": {
          "displayName": "Approved VM images Assignment",
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images",
          "parameters": {
              "imageIds": {
                  "value": [
                      "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage",
                      "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510"
                  ]
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Eltávolítás a REST API-val

- A szabályzat-hozzárendelés eltávolítása

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/allowed-custom-images-assignment?api-version=2017-06-01-preview
  ```

- A szabályzatdefiníció eltávolítása

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images?api-version=2016-12-01
  ```

### <a name="rest-api-explanation"></a>REST API magyarázat

| Szolgáltatás | Csoport | Művelet | Megjegyzések |
|---|---|---|---|
| Erőforrás-kezelés | Szabályzatdefiníciók | [Létrehozás](/rest/api/resources/policydefinitions/createorupdate) | Létrehoz egy új Azure Policy definíciót egy előfizetésnél. Alternatíva: [Létrehozás felügyeleti csoportnál](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| Erőforrás-kezelés | Szabályzat-hozzárendelések | [Létrehozás](/rest/api/resources/policyassignments/create) | Létrehoz egy új Azure Policy-hozzárendelést. Ebben a példában adunk hozzá egy definíciót, de használhat egy kezdeményezést is. |
| Erőforrás-kezelés | Szabályzat-hozzárendelések | [Törlés](/rest/api/resources/policyassignments/delete) | Eltávolít egy létező Azure Policy-hozzárendelést. |
| Erőforrás-kezelés | Szabályzatdefiníciók | [Törlés](/rest/api/resources/policydefinitions/delete) | Eltávolít egy létező Azure Policy-definíciót. Alternatíva: [Eltávolítás felügyeleti csoportnál](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>További lépések

- További [Azure Policy-minták](../json-samples.md) áttekintése
- Az [Azure szabályzatdefiníciók struktúrája](../policy-definition.md) szakasz áttekintése
- A virtuális gépekre vonatkozó további Azure Policy-példákat talál a [Szabályzatok alkalmazása Windows rendszerű virtuális gépekre](../../virtual-machines/windows/policy.md) témakörben