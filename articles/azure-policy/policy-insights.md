---
title: Programozott módon szabályzatok létrehozása és megfelelőségi adatait az Azure-házirend megtekintése
description: Ez a cikk útmutatást nyújt a programozott módon létrehozása és házirendek kezelése az Azure-házirend.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: a83402316854b23fe85bff813dc9f5665bccd1fb
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794810"
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>Programozott módon szabályzatok létrehozása és a megfelelőségi adatok megtekintése

Ez a cikk útmutatást nyújt a programozott módon létrehozása és házirendek kezelése. Azt is bemutatja, hogyan erőforrás megfelelőségi állapotok megtekintéséhez, és a házirendeket. Házirend-definíciók kényszerítése a különböző szabályok és hatások az erőforrások szerint. Érvényesítési gondoskodik arról, hogy erőforrások megfeleljenek a vállalati szabványoknak és a szolgáltatásszint-szerződések maradnak.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy a következő előfeltételek teljesülését:

1. Ha még nem tette meg, telepítse a [ARMClient](https://github.com/projectkudu/ARMClient). Olyan eszköz, amely HTTP-kérelmeket küld az Azure Resource Manager-alapú API-k.
2. Frissítse az AzureRM PowerShell-modult a legújabb verzióra. A legfrissebb verziójával kapcsolatos további információkért lásd: [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).
3. Az Azure PowerShell használatával győződjön meg arról, hogy működik-e az előfizetés az erőforrás-szolgáltató a házirend Insights erőforrás-szolgáltató regisztrálása. Egy erőforrás-szolgáltató regisztrálásához rendelkeznie kell az erőforrás-szolgáltató regisztrálási műveletének elvégzésére vonatkozó engedéllyel. Ezt a műveletet a Közreműködői és Tulajdonosi szerepkörök magukba foglalják. Az erőforrás-szolgáltató regisztrálásához futtassa az alábbi parancsot:

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  További információ a regisztráció és erőforrás-szolgáltatók megtekintése: [erőforrás-szolgáltatók és típusok](../azure-resource-manager/resource-manager-supported-services.md).
4. Ha még nem tette meg, telepítse az Azure parancssori felület. A legújabb verzió kaphat [Azure CLI 2.0 telepítése a Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Hozzon létre, és rendelje hozzá a házirend-definíció

Az első lépés jobb látható-e az erőforrások létrehozására és hozzárendelésére házirendeket az erőforrások keresztül. A következő lépésben megtudhatja, hogyan programozott módon létrehozásához, és rendelje hozzá a házirend. A példa házirend eseményeket, amelyek PowerShell, az Azure CLI és a HTTP-kérelmek használatával az összes nyilvános hálózatokon storage-fiókok.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Hozzon létre, és rendelje hozzá a házirend-definíció a PowerShell használatával

1. A következő JSON kódrészletet használja AuditStorageAccounts.json nevű JSON-fájl létrehozásához.

  ```json
  {
      "if": {
          "allOf": [{
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
              },
              {
                  "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                  "equals": "Allow"
              }
          ]
      },
      "then": {
          "effect": "audit"
      }
  }
  ```

  Szerzői házirend-definíció kapcsolatos további információkért lásd: [Azure házirend szerkezete](policy-definition.md).
2. A következő parancsot a AuditStorageAccounts.json fájllal házirend-definíció létrehozása.

  ```azurepowershell-interactive
  New-AzureRmPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
  ```

  A parancs létrehoz egy házirend-definíció nevű _naplózási tároló fiókok nyissa meg a nyilvános hálózatokhoz_. Használhatja más paraméterekkel kapcsolatos további információkért lásd: [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition).
3. A házirend-definíció létrehozása után létrehozhat egy házirend-hozzárendelést a következő parancsok futtatásával:

  ```azurepowershell-interactive
  $rg = Get-AzureRmResourceGroup -Name 'ContosoRG'
  $Policy = Get-AzureRmPolicyDefinition -Name 'AuditStorageAccounts'
  New-AzureRmPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
  ```

  Cserélje le _ContosoRG_ a megfelelő erőforráscsoport nevével.

Erőforrás-házirendek az Azure Resource Manager PowerShell-modullal kezelésével kapcsolatos további információkért lásd: [AzureRM.Resources](/powershell/module/azurerm.resources/#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Hozzon létre, és rendelje hozzá a házirend-definíció ARMClient használatával

A következő eljárással hozhat létre a házirend-definíció.

1. Másolja a következő JSON-részlet JSON-fájl létrehozásához. A fájl a következő lépésben értesítjük telefonon.

  ```json
  "properties": {
      "displayName": "Audit Storage Accounts Open to Public Networks",
      "policyType": "Custom",
      "mode": "Indexed",
      "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
      "parameters": {},
      "policyRule": {
          "if": {
              "allOf": [{
                      "field": "type",
                      "equals": "Microsoft.Storage/storageAccounts"
                  },
                  {
                      "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                      "equals": "Allow"
                  }
              ]
          },
          "then": {
              "effect": "audit"
          }
      }
  }
  ```

2. Hozza létre a házirend-definíció a következő hívásokat egyikének használatával:

  ```
  # For defining a policy in a subscription
  armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>

  # For defining a policy in a management group
  armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
  ```

  Cserélje le az előző {subscriptionId} azonosítójú, az előfizetés vagy {managementGroupId} azonosítójú, a [felügyeleti csoport](../azure-resource-manager/management-groups-overview.md).

  A lekérdezés szerkezete kapcsolatos további információkért lásd: [házirend-definíciók – létrehozás vagy frissítés](/rest/api/resources/policydefinitions/createorupdate) és [házirend-definíciók – létrehozás vagy frissítés a felügyeleti csoport](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

A következő eljárással hozzon létre egy házirend-hozzárendelést, és rendelje hozzá a házirend-definíció az erőforráscsoport szintjén.

1. Másolja a következő JSON-részlet JSON házirend-hozzárendelés fájl létrehozásához. Cserélje le a példaadatok &lt; &gt; szimbólumok saját értékekkel.

  ```json
  {
      "properties": {
          "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
          "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
          "parameters": {},
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
          "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
      }
  }
  ```

2. A következő hívást használ a házirend-hozzárendelés létrehozása:

  ```
  armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
  ```

  Cserélje le a példaadatok &lt; &gt; szimbólumok saját értékekkel.

  HTTP-hívás a REST API-val kapcsolatos további információkért lásd: [Azure REST API-erőforrások](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Hozzon létre, és rendelje hozzá a házirend-definíció Azure parancssori felülettel

Az alábbi eljárással házirend-definíció létrehozása:

1. Másolja a következő JSON-részlet JSON házirend-hozzárendelés fájl létrehozásához.

  ```json
  {
      "if": {
          "allOf": [{
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
              },
              {
                  "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                  "equals": "Allow"
              }
          ]
      },
      "then": {
          "effect": "audit"
      }
  }
  ```

2. A következő parancsot a házirend-definíció létrehozása:

  ```azurecli-interactive
az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
  ```

3. A következő paranccsal házirend-hozzárendelés létrehozása. Cserélje le a példaadatok &lt; &gt; szimbólumok saját értékekkel.

  ```azurecli-interactive
  az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
  ```

A házirend-definíció azonosítója kaphat a PowerShell használatával a következő parancsot:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

A házirend-definíció azonosítója a házirend-definíció létrehozott az alábbihoz kell hasonlítania:

```
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Hogyan kezelheti az Azure parancssori felülettel erőforrás-házirendekkel kapcsolatos további információkért lásd: [Azure CLI erőforrás-házirendek](/cli/azure/policy?view=azure-cli-latest).

## <a name="next-steps"></a>További lépések

Tekintse át a következő cikkekben további információt a parancsok és lekérdezések ebben a cikkben.

- [Az Azure REST API-erőforrások](/rest/api/resources/)
- [Az Azure erőforrás-kezelő PowerShell-modulok](/powershell/module/azurerm.resources/#policies)
- [Az Azure CLI házirend parancsok](/cli/azure/policy?view=azure-cli-latest)
- [Házirend Insights erőforrás-szolgáltató REST API-referencia](/rest/api/policy-insights)
- [Az Azure felügyeleti csoportok-erőforrások rendszerezése](../azure-resource-manager/management-groups-overview.md)