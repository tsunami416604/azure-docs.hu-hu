---
title: Szabályzatok létrehozása programozott módon
description: Ez a cikk bemutatja, hogyan hozhat létre és kezelhet szabályzatokat az Azure CLI-vel, a Azure PowerShelltal és a REST APIokkal Azure Policy.
ms.date: 01/31/2019
ms.topic: how-to
ms.openlocfilehash: 08ed43a464d1dd7de8220428dbc1c61ce9fc3ad6
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78386803"
---
# <a name="programmatically-create-policies"></a>Szabályzatok létrehozása programozott módon

Ez a cikk végigvezeti programozott módon szabályzatok létrehozása és kezelése. Azure Policy definíciók különböző szabályokat és hatásokat alkalmaznak az erőforrásokra. Kényszerítési gondoskodik arról, hogy az erőforrások maradjon felelnek meg a vállalati szabványoknak és szolgáltatói szerződéseknek.

A megfelelőséggel kapcsolatos további információkért lásd a [megfelelőségi adatok beszerzése](get-compliance-data.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy a következő előfeltételek teljesülését:

1. Ha még nem tette meg, telepítse az [ARMClient](https://github.com/projectkudu/ARMClient) eszközt. Ez egy olyan segédprogram, amely HTTP-kéréseket küld az Azure Resource Manager-alapú API-khoz.

1. Frissítse Azure PowerShell modulját a legújabb verzióra. Részletes információkért lásd: [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps) . További információ a legújabb verzióról: [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

1. Regisztrálja a Azure Policy bepillantást erőforrás-szolgáltatót a Azure PowerShell használatával annak ellenőrzéséhez, hogy az előfizetés működik-e az erőforrás-szolgáltatóval. Erőforrás-szolgáltató regisztrálásához rendelkeznie kell engedéllyel az erőforrás-szolgáltató regisztrálási műveletének futtatása. Ezt a műveletet a Közreműködői és Tulajdonosi szerepkörök magukba foglalják. Az erőforrás-szolgáltató regisztrálásához futtassa az alábbi parancsot:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Az erőforrás-szolgáltatók regisztrálásával és megtekintésével kapcsolatos további információért tekintse meg az [erőforrás-szolgáltatókat és típusaikat](../../../azure-resource-manager/management/resource-providers-and-types.md) ismertető cikket.

1. Ha még nem tette, az Azure CLI telepítése. A legújabb verziót az [Azure CLI telepítése Windows](/cli/azure/install-azure-cli-windows)rendszeren végezheti el.

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

   A szabályzat-definíciók létrehozásával kapcsolatos további információkért lásd: [Azure Policy definíciós struktúra](../concepts/definition-structure.md).

1. A következő paranccsal létrehozhat egy szabályzatdefiníciót a AuditStorageAccounts.json fájllal.

   ```azurepowershell-interactive
   New-AzPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   A parancs létrehoz egy naplózási fiók nevű házirend-definíciót a _nyilvános hálózatokhoz_.
   További információ a használható egyéb paraméterekről: [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition).

   Ha a hely paramétereinek megadása nélkül hívja meg őket, `New-AzPolicyDefinition` alapértelmezett értékekkel menti a házirend-definíciót a munkamenetek környezetének kiválasztott előfizetésében. A definíció mentése más helyre, használja a következő paraméterekkel:

   - **SubscriptionId** – mentés másik előfizetésbe. _GUID_ -értéket igényel.
   - **ManagementGroupName** – mentés egy felügyeleti csoportba. _Karakterlánc_ -értéket igényel.

1. Miután létrehozta a szabályzat-definíció, létrehozhat egy szabályzat-hozzárendelést a következő parancsok futtatásával:

   ```azurepowershell-interactive
   $rg = Get-AzResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Cserélje le a _ContosoRG_ nevet a kívánt erőforráscsoport nevére.

   A `New-AzPolicyAssignment` **hatókör** -paramétere felügyeleti csoporttal, előfizetéssel, erőforráscsoporthoz vagy egyetlen erőforrással működik. A paraméter teljes erőforrás-elérési utat használ, amely a `Get-AzResourceGroup` **ResourceId** tulajdonságát adja vissza. Az egyes tárolók **hatókörének** mintája a következő. Cserélje le `{rName}`, `{rgName}`, `{subId}`és `{mgName}` értékét az erőforrás nevére, az erőforráscsoport nevére, az előfizetés-AZONOSÍTÓra és a felügyeleti csoport nevére.
   a `{rType}` helyére az **erőforrás erőforrástípus,** például egy virtuális gép `Microsoft.Compute/virtualMachines`.

   - Erőforrás-`/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Erőforráscsoport – `/subscriptions/{subId}/resourceGroups/{rgName}`
   - Előfizetés – `/subscriptions/{subId}/`
   - Felügyeleti csoport – `/providers/Microsoft.Management/managementGroups/{mgName}`

Az erőforrás-házirendek Azure Resource Manager PowerShell-modul használatával történő kezelésével kapcsolatos további információkért lásd [az az. Resources](/powershell/module/az.resources/#policies)című témakört.

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

   ```console
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
   ```

   Cserélje le az előző {subscriptionId} azonosítót az előfizetés azonosítójával vagy {managementGroupId} a [felügyeleti csoportjának](../../management-groups/overview.md)azonosítójával.

   További információ a lekérdezés struktúrájáról: [Azure Policy definíciók – létrehozás vagy frissítés](/rest/api/resources/policydefinitions/createorupdate) és [szabályzat-definíciók – létrehozás vagy frissítés a felügyeleti csoportban](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

A következő eljárással hozhat létre szabályzat-hozzárendelést, és rendelje hozzá a szabályzatdefiníciót az erőforráscsoport szintjén.

1. Másolja a következő JSON-kódrészletben hozhat létre egy JSON-szabályzat-hozzárendelés fájlt. Cserélje le például a &lt;&gt; szimbólumokat a saját értékeire.

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

   ```console
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
   ```

   Cserélje le például a &lt;&gt; szimbólumokat a saját értékeire.

   További információ a REST API HTTP-hívásáról: [Azure REST API-erőforrások](/rest/api/resources/).

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

   A szabályzat-definíciók létrehozásával kapcsolatos további információkért lásd: [Azure Policy definíciós struktúra](../concepts/definition-structure.md).

1. Futtassa a következő parancsot egy szabályzat-definíció létrehozása:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

   A parancs létrehoz egy naplózási fiók nevű házirend-definíciót a _nyilvános hálózatokhoz_.
   További információ a használható egyéb paraméterekről: [az Policy definition Create](/cli/azure/policy/definition#az-policy-definition-create).

   Ha a hely paramétereinek megadása nélkül hívja meg őket, `az policy definition creation` alapértelmezett értékekkel menti a házirend-definíciót a munkamenetek környezetének kiválasztott előfizetésében. A definíció mentése más helyre, használja a következő paraméterekkel:

   - **--előfizetés** – mentse egy másik előfizetésbe. Az előfizetés-AZONOSÍTÓhoz vagy egy _karakterlánc_ -értékhez szükséges _GUID_ -értéket igényel az előfizetés neveként.
   - **--felügyeleti** csoport – mentés egy felügyeleti csoportba. _Karakterlánc_ -értéket igényel.

1. A következő paranccsal hozzon létre egy szabályzat-hozzárendelést. Cserélje le például a &lt;&gt; szimbólumokat a saját értékeire.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   A `az policy assignment create` **hatókör** paramétere a felügyeleti csoporttal, előfizetéssel, erőforráscsoporthoz vagy egyetlen erőforrással működik. A paraméter teljes erőforrás-elérési utat használ. Az egyes tárolók **hatóköre** a következő. Cserélje le `{rName}`, `{rgName}`, `{subId}`és `{mgName}` értékét az erőforrás nevére, az erőforráscsoport nevére, az előfizetés-AZONOSÍTÓra és a felügyeleti csoport nevére. a `{rType}` helyére az **erőforrás erőforrástípus,** például egy virtuális gép `Microsoft.Compute/virtualMachines`.

   - Erőforrás-`/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Erőforráscsoport – `/subscriptions/{subID}/resourceGroups/{rgName}`
   - Előfizetés – `/subscriptions/{subID}`
   - Felügyeleti csoport – `/providers/Microsoft.Management/managementGroups/{mgName}`

A Azure Policy definíciós AZONOSÍTÓját a PowerShell használatával szerezheti be a következő paranccsal:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

A szabályzatdefiníció azonosítója számára az Ön által létrehozott szabályzat-definíció az alábbi példa kell hasonlítania:

```output
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Az erőforrás-házirendek Azure CLI-vel való kezelésével kapcsolatos további információkért lásd: [Azure CLI erőforrás-házirendek](/cli/azure/policy?view=azure-cli-latest).

## <a name="next-steps"></a>Következő lépések

Tekintse át a parancsok és lekérdezések ebben a cikkben további információt a következő cikkeket.

- [Azure REST API-erőforrások](/rest/api/resources/)
- [Azure PowerShell modulok](/powershell/module/az.resources/#policies)
- [Azure CLI-házirend parancsai](/cli/azure/policy?view=azure-cli-latest)
- [Azure Policy az erőforrás-szolgáltató REST API referenciája](/rest/api/policy-insights)
- [Erőforrások rendszerezése az Azure-beli felügyeleti csoportokkal](../../management-groups/overview.md).