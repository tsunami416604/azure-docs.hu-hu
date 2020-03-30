---
title: Szabályzatok létrehozása programozott módon
description: Ez a cikk végigvezeti az Azure CLI, az Azure PowerShell és a REST API használatával az Azure Policy szabályzatok programozással történő létrehozásán és kezelésén.
ms.date: 01/31/2019
ms.topic: how-to
ms.openlocfilehash: 08ed43a464d1dd7de8220428dbc1c61ce9fc3ad6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264543"
---
# <a name="programmatically-create-policies"></a>Szabályzatok létrehozása programozott módon

Ez a cikk végigvezeti a szabályzatok programozott létrehozásán és kezelésén. Az Azure Policy-definíciók különböző szabályokat és hatásokat kényszerítenek ki az erőforrásokra. A kényszerítés biztosítja, hogy az erőforrások megfeleljenek a vállalati szabványoknak és a szolgáltatásiszint-szerződéseknek.

A megfelelőségről a [Megfelelőségi adatok beszerzése](get-compliance-data.md)című témakörben talál további információt.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy az alábbi előfeltételek teljesülnek:

1. Ha még nem tette meg, telepítse az [ARMClient](https://github.com/projectkudu/ARMClient) eszközt. Ez egy olyan segédprogram, amely HTTP-kéréseket küld az Azure Resource Manager-alapú API-khoz.

1. Frissítse az Azure PowerShell-modult a legújabb verzióra. Részletes információt az [Azure PowerShell-modul telepítése](/powershell/azure/install-az-ps) című témakörben talál. A legújabb verzióról az [Azure PowerShell című témakörben](https://github.com/Azure/azure-powershell/releases)talál további információt.

1. Regisztrálja az Azure Policy Insights erőforrás-szolgáltatót az Azure PowerShell használatával annak ellenőrzéséhez, hogy az előfizetés e-kiszolgálóval működik-e. Erőforrás-szolgáltató regisztrálásához engedéllyel kell rendelkeznie az erőforrás-szolgáltató regiszterműveletének futtatásához. Ezt a műveletet a Közreműködői és Tulajdonosi szerepkörök magukba foglalják. Az erőforrás-szolgáltató regisztrálásához futtassa az alábbi parancsot:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Az erőforrás-szolgáltatók regisztrálásával és megtekintésével kapcsolatos további információért tekintse meg az [erőforrás-szolgáltatókat és típusaikat](../../../azure-resource-manager/management/resource-providers-and-types.md) ismertető cikket.

1. Ha még nem tette meg, telepítse az Azure CLI-t. A legújabb verziót az [Azure CLI telepítése Windows rendszeren](/cli/azure/install-azure-cli-windows)című témakörben szerezheti be.

## <a name="create-and-assign-a-policy-definition"></a>Házirend-definíció létrehozása és hozzárendelése

Az erőforrások jobb láthatósága felé tett első lépés a házirendek létrehozása és hozzárendelése az erőforrásokhoz. A következő lépés a házirend programozott létrehozásának és hozzárendelésének megismerése. A példaházirend a PowerShell, az Azure CLI és a HTTP-kérelmek használatával minden nyilvános hálózat számára nyitott tárfiókokat naplózza.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Házirend-definíció létrehozása és hozzárendelése a PowerShell használatával

1. Az alábbi JSON-kódrészlet segítségével hozzon létre egy AuditStorageAccounts.json nevű JSON-fájlt.

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

   A szabályzatdefiníciók szerzőiről az [Azure Policy Definition Structure című témakörben](../concepts/definition-structure.md)talál további információt.

1. A következő paranccsal hozzon létre egy házirend-definíciót az AuditStorageAccounts.json fájl használatával.

   ```azurepowershell-interactive
   New-AzPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   A parancs létrehoz egy házirend-definíciót, _amelynek_neve Audit Storage Accounts Open to Public Networks .
   A többi használható paraméterről a [New-AzPolicyDefinition című](/powershell/module/az.resources/new-azpolicydefinition)témakörben talál további információt.

   Ha helyparaméterek nélkül `New-AzPolicyDefinition` hívják meg, alapértelmezés szerint a házirend-definíciót a munkamenetek kijelölt előfizetésébe menti. Ha a definíciót egy másik helyre szeretné menteni, használja a következő paramétereket:

   - **SubscriptionId** – Mentés másik előfizetésre. GUID _GUID_ értéket igényel.
   - **ManagementGroupName** - Mentés felügyeleti csoportba. _Karakterlánc-érték_ szükséges.

1. A házirend-definíció létrehozása után a következő parancsok futtatásával hozhat létre házirend-hozzárendelést:

   ```azurepowershell-interactive
   $rg = Get-AzResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Cserélje le a _ContosoRG-t_ a tervezett erőforráscsoport nevére.

   A **Scope** `New-AzPolicyAssignment` paraméter felügyeleti csoporttal, előfizetéssel, erőforráscsoporttal vagy egyetlen erőforrással működik. A paraméter egy teljes erőforráselérési utat használ, `Get-AzResourceGroup` amelyet a **ResourceId** tulajdonság visszáruban. Az egyes **tárolók hatókörének** mintája a következő. Cserélje `{rName}` `{rgName}`le `{subId}`a `{mgName}` , , és az erőforrás nevét, erőforráscsoport nevét, az előfizetés-azonosítót és a felügyeleti csoport nevét.
   `{rType}`lecserélné az erőforrás **erőforrás-típusa,** például `Microsoft.Compute/virtualMachines` egy virtuális gép.

   - Erőforrás -`/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Erőforráscsoport -`/subscriptions/{subId}/resourceGroups/{rgName}`
   - Előfizetés -`/subscriptions/{subId}/`
   - Vezetői csoport -`/providers/Microsoft.Management/managementGroups/{mgName}`

Az Erőforrás-házirendek Azure Resource Manager PowerShell modullal történő kezeléséről az [Az.Resources](/powershell/module/az.resources/#policies)című témakörben talál további információt.

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Házirend-definíció létrehozása és hozzárendelése az ARMClient használatával

Házirend-definíció létrehozásához kövesse az alábbi eljárást.

1. JSON-fájl létrehozásához másolja a következő JSON-kódrészletet. A következő lépésben meghívja a fájlt.

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

1. Hozza létre a házirend-definíciót az alábbi hívások egyikével:

   ```console
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
   ```

   Cserélje le az előző {subscriptionId} azonosítót az előfizetés ére vagy a (managementGroupId} azonosítót) a [felügyeleti csoport azonosítójára.](../../management-groups/overview.md)

   A lekérdezés szerkezetéről további információt az [Azure-szabályzatdefiníciók – létrehozás vagy frissítés](/rest/api/resources/policydefinitions/createorupdate) és [házirend-definíciók – Létrehozás vagy frissítés a felügyeleti csoportban című](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) témakörben talál.

Az alábbi eljárással hozzon létre egy házirend-hozzárendelést, és rendelje hozzá a házirend-definíciót az erőforráscsoport szintjén.

1. JSON-házirend-hozzárendelési fájl létrehozásához másolja a következő JSON-kódrészletet. A szimbólumokban &lt; &gt; lévő példaadatok cseréje a saját értékeire.

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

1. Hozza létre a házirend-hozzárendelést a következő hívással:

   ```console
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
   ```

   A szimbólumokban &lt; &gt; lévő példaadatok cseréje a saját értékeire.

   A REST API HTTP-hívásairól az [Azure REST API-erőforrások című témakörben](/rest/api/resources/)talál további információt.

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Házirend-definíció létrehozása és hozzárendelése az Azure CLI-vel

Házirend-definíció létrehozásához kövesse az alábbi eljárást:

1. JSON-házirend-hozzárendelési fájl létrehozásához másolja a következő JSON-kódrészletet.

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

   A szabályzatdefiníciók szerzőiről az [Azure Policy Definition Structure című témakörben](../concepts/definition-structure.md)talál további információt.

1. Házirend-definíció létrehozásához futtassa a következő parancsot:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

   A parancs létrehoz egy házirend-definíciót, _amelynek_neve Audit Storage Accounts Open to Public Networks .
   A többi használható paraméterről az az [házirend-definíció létrehozása című](/cli/azure/policy/definition#az-policy-definition-create)témakörben talál további információt.

   Ha helyparaméterek nélkül `az policy definition creation` hívják meg, alapértelmezés szerint a házirend-definíciót a munkamenetek kijelölt előfizetésébe menti. Ha a definíciót egy másik helyre szeretné menteni, használja a következő paramétereket:

   - **--subscription** - Mentés másik előfizetésre. Az előfizetés azonosítójának _GUID-értékét_ vagy az előfizetés nevéhez _karakterlánc-értéket_ igényel.
   - **--management-group** - Mentés egy felügyeleti csoportba. _Karakterlánc-érték_ szükséges.

1. Házirend-hozzárendelés létrehozásához használja a következő parancsot. A szimbólumokban &lt; &gt; lévő példaadatok cseréje a saját értékeire.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   A **--scope** `az policy assignment create` paraméter felügyeleti csoporttal, előfizetéssel, erőforráscsoporttal vagy egyetlen erőforrással működik. A paraméter teljes erőforráselérési utat használ. A **minta --hatókör** minden tároló a következő. Cserélje `{rName}` `{rgName}`le `{subId}`a `{mgName}` , , és az erőforrás nevét, erőforráscsoport nevét, az előfizetés-azonosítót és a felügyeleti csoport nevét. `{rType}`lecserélné az erőforrás **erőforrás-típusa,** például `Microsoft.Compute/virtualMachines` egy virtuális gép.

   - Erőforrás -`/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Erőforráscsoport -`/subscriptions/{subID}/resourceGroups/{rgName}`
   - Előfizetés -`/subscriptions/{subID}`
   - Vezetői csoport -`/providers/Microsoft.Management/managementGroups/{mgName}`

Az Azure Policy Definition ID a PowerShell használatával a következő paranccsal szerezheti be:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

A létrehozott házirend-definíció házirend-definícióazonosítójának a következő példához kell hasonlítania:

```output
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Az Azure CLI használatával az erőforrás-szabályzatok kezeléséről az [Azure CLI erőforrás-szabályzatai](/cli/azure/policy?view=azure-cli-latest)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

A cikkben található parancsokkal és lekérdezésekkel kapcsolatos további információkért tekintse át az alábbi cikkeket.

- [Azure REST API-erőforrások](/rest/api/resources/)
- [Azure PowerShell-modulok](/powershell/module/az.resources/#policies)
- [Azure CLI-házirendparancsok](/cli/azure/policy?view=azure-cli-latest)
- [Az Azure Policy Insights erőforrás-szolgáltató REST API-hivatkozása](/rest/api/policy-insights)
- [Az Azure felügyeleti csoportokkal rendszerezheti erőforrásait.](../../management-groups/overview.md)