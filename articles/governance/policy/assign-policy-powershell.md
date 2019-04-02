---
title: A nem megfelelő erőforrások szabályzat létrehozása az Azure PowerShell használatával
description: Azure PowerShell használatával hozzon létre egy Azure szabályzat-hozzárendelést a nem megfelelő erőforrások azonosításához.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/11/2019
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 7f743ee99516200c1fb046460c261605e7b3b4e0
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58801203"
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Azure PowerShell-lel a nem megfelelő erőforrások azonosítására a szabályzat-hozzárendelés létrehozása

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása. Ebben a rövid útmutatóban egy felügyelt lemezeket nem használó virtuális gépek azonosítására szolgáló szabályzat-hozzárendelést hoz létre. Amikor végzett, azonosítania kell a virtuális gépek, amelyek *nem megfelelő*.

Az Azure PowerShell-modul az Azure-erőforrások kezelhetők a parancssorból vagy szkriptekkel szolgál.
Ez az útmutató ismerteti, hogyan hozhat létre szabályzat-hozzárendelés Az modul.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Mielőtt elkezdené, győződjön meg arról, hogy telepítve van-e az Azure PowerShell legújabb verzióját. Lásd: [Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) részletes információkat.
- Regisztrálja a Policy Insights erőforrás-szolgáltatót az Azure PowerShell használatával. Az erőforrás-szolgáltató regisztrálásával biztosítja, hogy az előfizetése működni fog vele. Erőforrás-szolgáltató regisztrálásához rendelkeznie kell engedéllyel az erőforrás-szolgáltató regisztrálása művelet. Ezt a műveletet a Közreműködői és Tulajdonosi szerepkörök magukba foglalják. Az erőforrás-szolgáltató regisztrálásához futtassa az alábbi parancsot:

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Az erőforrás-szolgáltatók regisztrálásával és megtekintésével kapcsolatos további információért tekintse meg az [erőforrás-szolgáltatókat és típusaikat](../../azure-resource-manager/resource-manager-supported-services.md) ismertető cikket.

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben a rövid útmutatóban létrehozhat egy szabályzat-hozzárendelést a a *felügyelt lemezek nélküli virtuális gépek naplózása* definíciója. Ez a szabályzat-definíció azonosítja a felügyelt lemezeket nem használó virtuális gépeket.

Futtassa a következő parancsokat egy új szabályzat-hozzárendelés létrehozásához:

```azurepowershell-interactive
# Get a reference to the resource group that will be the scope of the assignment
$rg = Get-AzResourceGroup -Name '<resourceGroupName>'

# Get a reference to the built-in policy definition that will be assigned
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }

# Create the policy assignment with the built-in definition against your resource group
New-AzPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

Az előző parancsok a következő információkat használják:

- **Name** – A hozzárendelés tényleges neve. A fenti példában az *audit-vm-manageddisks* nevet használtuk.
- **DisplayName** – A szabályzat-hozzárendelés megjelenített neve. Ebben az esetben használ *hozzárendelés felügyelt lemezek nélküli virtuális gépek naplózása*.
- **Definíció** – A szabályzatdefiníció, amely alapján létre fogja hozni a hozzárendelést. Ebben az esetben a szabályzatdefiníció azonosítója *felügyelt lemezeket nem használó virtuális gépek naplózása*.
- **Hatókör** – A hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrások vagy erőforráscsoportok esetében lesz kényszerítve. Ez egyetlen előfizetéstől teljes erőforráscsoportokig terjedhet. Győződjön meg arról, hogy a &lt;scope&gt; helyett az erőforráscsoport neve szerepel.

Most már készen áll a nem megfelelő erőforrások azonosítására a környezet megfelelési állapotának megismerése céljából.

## <a name="identify-non-compliant-resources"></a>Nem megfelelő erőforrások azonosítása

A következő információkkal azonosíthatja a létrehozott szabályzat-hozzárendelés szempontjából nem megfelelő erőforrásokat. Futtassa az alábbi parancsot:

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

Szabályzat állapota első kapcsolatos további információkért lásd: [Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState).

Az eredmények a következő példához hasonlók:

```output
Timestamp                   : 3/9/19 9:21:29 PM
ResourceId                  : /subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmId}
PolicyAssignmentId          : /subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/audit-vm-manageddisks
PolicyDefinitionId          : /providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d
IsCompliant                 : False
SubscriptionId              : {subscriptionId}
ResourceType                : /Microsoft.Compute/virtualMachines
ResourceTags                : tbd
PolicyAssignmentName        : audit-vm-manageddisks
PolicyAssignmentOwner       : tbd
PolicyAssignmentScope       : /subscriptions/{subscriptionId}
PolicyDefinitionName        : 06a78e20-9358-41c9-923c-fb736d382a4d
PolicyDefinitionAction      : audit
PolicyDefinitionCategory    : Compute
ManagementGroupIds          : {managementGroupId}
```

A Keresés eredménye megfelel, megjelenik a **erőforrás megfelelőségi** lapján az Azure portal nézetében a szabályzat-hozzárendelés.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott hozzárendelést eltávolításához használja a következő parancsot:

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban hozzárendelt egy szabályzatdefiníciót az Azure-környezetben megtalálható, nem megfelelő erőforrások azonosítása céljából.

További információ a szabályzatok ellenőrzése, hogy az új erőforrások megfelelnek hozzárendeléséről, folytassa a következő oktatóanyagban:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./tutorials/create-and-manage.md)