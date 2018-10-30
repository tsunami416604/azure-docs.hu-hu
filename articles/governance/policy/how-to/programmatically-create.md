---
title: Programozott módon szabályzatok létrehozása és az Azure Policyvel megfelelőségi adatok megtekintése
description: Ez a cikk végigvezeti programozott módon szabályzatok létrehozása és kezelése az Azure Policyvel.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/29/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 4a68b60df76dcc554158d6c8db4d0dfe8dd32be7
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209224"
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>Programozott módon szabályzatok létrehozása és a megfelelőségi adatok megtekintése

Ez a cikk végigvezeti programozott módon szabályzatok létrehozása és kezelése. A szabályzatdefiníciók különböző szabályokat és hatások kényszerítenek az erőforrások. Kényszerítési gondoskodik arról, hogy az erőforrások maradjon felelnek meg a vállalati szabványoknak és szolgáltatói szerződéseknek.

Megfelelőségi kapcsolatos információkért lásd: [megfelelőségi adatok](getting-compliance-data.md).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy a következő előfeltételek teljesülését:

1. Ha még nem tette meg, telepítse az [ARMClient](https://github.com/projectkudu/ARMClient) eszközt. Ez egy olyan segédprogram, amely HTTP-kéréseket küld az Azure Resource Manager-alapú API-khoz.

1. Frissítse az AzureRM PowerShell-modult a legújabb verzióra. A legújabb verzióval kapcsolatos további információkért lásd: [Azure PowerShell-lel](https://github.com/Azure/azure-powershell/releases).

1. Regisztrálja a Policy Insights erőforrás-szolgáltató, ellenőrizze, hogy működik-e az előfizetés az erőforrás-szolgáltató az Azure PowerShell-lel. Egy erőforrás-szolgáltató regisztrálásához rendelkeznie kell az erőforrás-szolgáltató regisztrálási műveletének elvégzésére vonatkozó engedéllyel. Ezt a műveletet a Közreműködői és Tulajdonosi szerepkörök magukba foglalják. Az erőforrás-szolgáltató regisztrálásához futtassa az alábbi parancsot:

   ```azurepowershell-interactive
   Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Erőforrás-szolgáltatók regisztrálásával és megtekintésével kapcsolatos további információkért lásd: [erőforrás-szolgáltatókat és típusaikat](../../../azure-resource-manager/resource-manager-supported-services.md).

1. Ha még nem tette, az Azure CLI telepítése. Megtekintheti a legújabb verzió [Azure CLI telepítése a Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Egy szabályzat-definíció létrehozása és hozzárendelése

Az erőforrások jobb rálátást biztosít az első lépését, hogy a szabályzatok létrehozása és hozzárendelése az erőforrások felett. A következő lépés, hogy programozott módon hozzon létre és rendelhet hozzá azokhoz. A példa a szabályzat a storage-fiókok, amelyek számára elérhető összes nyilvános hálózat HTTP-kérelmekre, PowerShell és Azure CLI használatával naplózza.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>A PowerShell-lel egy szabályzat-definíció létrehozása és hozzárendelése

1. Az alábbi JSON-kódrészlet használatával hozzon létre egy JSON-fájlt a nevű AuditStorageAccounts.json.

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

   Szabályzat-definíció szerzői kapcsolatos további információkért lásd: [Azure Szabályzatdefiníciók struktúrája](../concepts/definition-structure.md).

1. A következő paranccsal létrehozhat egy szabályzatdefiníciót a AuditStorageAccounts.json fájllal.

   ```azurepowershell-interactive
   New-AzureRmPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   A parancs létrehoz egy szabályzatdefiníciót nevű _naplózási Storage fiókok nyissa meg a nyilvános hálózatok_. Más paramétereket, amelyeket használhat kapcsolatos további információkért lásd: [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition).

1. Miután létrehozta a szabályzat-definíció, létrehozhat egy szabályzat-hozzárendelést a következő parancsok futtatásával:

   ```azurepowershell-interactive
   $rg = Get-AzureRmResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzureRmPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzureRmPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Cserélje le _ContosoRG_ az importálni kívánt erőforráscsoport nevét.

   A **hatókör** paraméterrel `New-AzureRmPolicyAssignment` az előfizetések és a felügyeleti csoportok is működik. A paraméter használja a teljes erőforrás-elérési útját, amely a **ResourceId** tulajdonsága `Get-AzureRmResourceGroup` adja vissza. Minta **hatókör** az egyes tárolók a következőképpen történik.  Cserélje le `{rgName}`, `{subId}`, és `{mgName}` az erőforrás-csoport nevét, az előfizetés-azonosító és a felügyeleti csoport neve, illetve.

   - Erőforráscsoport- `/subscriptions/{subId}/resourceGroups/{rgName}`
   - Előfizetés – `/subscriptions/{subId}/`
   - Felügyeleti csoport – `/providers/Microsoft.Management/managementGroups/{mgName}`

Erőforrás-szabályzatok az Azure Resource Manager PowerShell-modullal kezelésével kapcsolatos további információkért lásd: [AzureRM.Resources](/powershell/module/azurerm.resources/#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Hozzon létre és ARMClient használatával szabályzatdefiníció hozzárendelése

Az alábbi eljárás segítségével létrehozhat egy szabályzatdefiníciót.

1. Másolja a következő JSON-kódrészletben hozhat létre egy JSON-fájlt. A fájl a következő lépésben Felhívjuk.

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

1. Hozza létre a szabályzat-definíció a következő hívások egyikével:

   ```
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
   ```

   Cserélje le az előző {subscriptionId} azonosítójú, az előfizetés vagy {managementGroupId} azonosítójú, a [felügyeleti csoport](../../management-groups/overview.md).

   A lekérdezés szerkezete kapcsolatos további információkért lásd: [Szabályzatdefiníciók – létrehozás vagy frissítés](/rest/api/resources/policydefinitions/createorupdate) és [Szabályzatdefiníciók – létrehozás vagy frissítés: felügyeleti csoport](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

A következő eljárással hozhat létre szabályzat-hozzárendelést, és rendelje hozzá a szabályzatdefiníciót az erőforráscsoport szintjén.

1. Másolja a következő JSON-kódrészletben hozhat létre egy JSON-szabályzat-hozzárendelés fájlt. Cserélje le a példaadatok &lt; &gt; szimbólumok a saját értékeire.

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

1. A következő hívás segítségével a szabályzat-hozzárendelés létrehozásához:

   ```
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
   ```

   Cserélje le a példaadatok &lt; &gt; szimbólumok a saját értékeire.

   HTTP-hívások, a REST API-ra végrehajtásával kapcsolatos további információkért lásd: [Azure REST API-erőforrásokhoz](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Hozzon létre és rendelje hozzá egy szabályzatdefiníciót az Azure CLI-vel

Szabályzatdefiníció létrehozásához használja az alábbi eljárást:

1. Másolja a következő JSON-kódrészletben hozhat létre egy JSON-szabályzat-hozzárendelés fájlt.

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

1. Futtassa a következő parancsot egy szabályzat-definíció létrehozása:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

1. A következő paranccsal hozzon létre egy szabályzat-hozzárendelést. Cserélje le a példaadatok &lt; &gt; szimbólumok a saját értékeire.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

A Szabályzatdefiníció azonosítója a következő parancsot a PowerShell használatával kaphat:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

A szabályzatdefiníció azonosítója számára az Ön által létrehozott szabályzat-definíció az alábbi példa kell hasonlítania:

```
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Hogyan kezelheti az erőforrás-szabályzatok az Azure CLI-vel kapcsolatos további információkért lásd: [Azure CLI erőforrás-házirendek](/cli/azure/policy?view=azure-cli-latest).

## <a name="next-steps"></a>További lépések

Tekintse át a parancsok és lekérdezések ebben a cikkben további információt a következő cikkeket.

- [Az Azure REST API-erőforrásokhoz](/rest/api/resources/)
- [Azure RM PowerShell-modulok](/powershell/module/azurerm.resources/#policies)
- [A házirend-parancsok az Azure CLI](/cli/azure/policy?view=azure-cli-latest)
- [A házirend Insights erőforrás-szolgáltató REST API-referencia](/rest/api/policy-insights)
- [Az erőforrások rendszerezéséhez az Azure felügyeleti csoportok](../../management-groups/overview.md)