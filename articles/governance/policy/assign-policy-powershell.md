---
title: 'Rövid útmutató: Új házirend-hozzárendelés a PowerShell-lel'
description: Ebben a rövid útmutatóban az Azure PowerShell segítségével hozzon létre egy Azure Policy-hozzárendelést a nem megfelelő erőforrások azonosításához.
ms.date: 11/25/2019
ms.topic: quickstart
ms.openlocfilehash: 3fd6060d1f38c523ccf22e80807f6220bfdf3cbc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75978311"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Rövid útmutató: Hozzon létre egy szabályzat-hozzárendelést a nem megfelelő erőforrások azonosítására az Azure PowerShell használatával

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása. Ebben a rövid útmutatóban létrehoz egy házirend-hozzárendelést a felügyelt lemezeket nem használó virtuális gépek azonosítására. Ha elkészült, azonosíthatja a _nem megfelelő_virtuális gépeket.

Az Azure PowerShell-modul az Azure-erőforrások kezelésére szolgál a parancssorból vagy parancsfájlokban.
Ez az útmutató bemutatja, hogyan hozhat létre házirend-hozzárendelést az Az modul.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

- Mielőtt elkezdené, győződjön meg arról, hogy az Azure PowerShell legújabb verziója telepítve van. Részletes információt az [Azure PowerShell-modul telepítése](/powershell/azure/install-az-ps) című témakörben talál.

- Regisztrálja az Azure Policy Insights erőforrás-szolgáltatót az Azure PowerShell használatával. Az erőforrás-szolgáltató regisztrálásával biztosítja, hogy az előfizetése működni fog vele. Erőforrás-szolgáltató regisztrálásához engedéllyel kell rendelkeznie az erőforrás-szolgáltató regisztrálásához. Ezt a műveletet a Közreműködői és Tulajdonosi szerepkörök magukba foglalják. Az erőforrás-szolgáltató regisztrálásához futtassa az alábbi parancsot:

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Az erőforrás-szolgáltatók regisztrálásával és megtekintésével kapcsolatos további információért tekintse meg az [erőforrás-szolgáltatókat és típusaikat](../../azure-resource-manager/management/resource-providers-and-types.md) ismertető cikket.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben a rövid útmutatóban hozzon létre egy házirend-hozzárendelést a _felügyelt lemezek definíciója nélküli virtuális gépek naplózásához._ Ez a házirend-definíció azonosítja a nem felügyelt lemezeket használó virtuális gépeket.

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

- **Name** – A hozzárendelés tényleges neve. A fenti példában az _audit-vm-manageddisks_ nevet használtuk.
- **DisplayName** – A szabályzat-hozzárendelés megjelenített neve. Ebben az esetben felügyelt _lemezek hozzárendelés nélküli naplózó virtuális gépeket_használ.
- **Definíció** – A szabályzatdefiníció, amely alapján létre fogja hozni a hozzárendelést. Ebben az esetben a felügyelt _lemezeket nem használó házirend-definíciónaplózási virtuális gépek azonosítója._
- **Hatókör** – A hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrások vagy erőforráscsoportok esetében lesz kényszerítve. Ez egyetlen előfizetéstől teljes erőforráscsoportokig terjedhet. Győződjön meg arról, hogy a &lt;scope&gt; helyett az erőforráscsoport neve szerepel.

Most már készen áll a nem megfelelő erőforrások azonosítására a környezet megfelelőségi állapotának megértéséhez.

## <a name="identify-non-compliant-resources"></a>Nem megfelelő erőforrások azonosítása

A következő információkkal azonosíthatja a létrehozott szabályzat-hozzárendelés szempontjából nem megfelelő erőforrásokat. Futtassa az alábbi parancsot:

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

A házirend állapotának beszerzéséről a [Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState)című témakörben talál további információt.

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

Az eredmények megegyeznek az Azure Portal nézetben lévő szabályzat-hozzárendelés **Erőforrás-megfelelőség** lapján láthatóeredményekkel.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott hozzárendelés eltávolításához használja a következő parancsot:

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban hozzárendelt egy szabályzatdefiníciót az Azure-környezetben megtalálható, nem megfelelő erőforrások azonosítása céljából.

Ha többet szeretne megtudni arról, hogy milyen házirendeket szeretne hozzárendelni az új erőforrások megfelelősének ellenőrzéséhez, folytassa az oktatóanyaggal:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./tutorials/create-and-manage.md)