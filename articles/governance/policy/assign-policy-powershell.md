---
title: 'Rövid útmutató: új szabályzat-hozzárendelés a PowerShell-lel'
description: Ebben a rövid útmutatóban a Azure PowerShell használatával hozhat létre egy Azure Policy-hozzárendelést a nem megfelelő erőforrások azonosításához.
ms.date: 11/25/2019
ms.topic: quickstart
ms.openlocfilehash: 3ce823a7abfe16e4433128dcdfe073dfcfaeba50
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482394"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Gyors útmutató: szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához Azure PowerShell használatával

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása. Ebben a rövid útmutatóban egy felügyelt lemezeket nem használó virtuális gépek azonosítására szolgáló szabályzat-hozzárendelést hoz létre. Ha elkészült, azonosíthatja azokat a virtuális gépeket, amelyek _nem megfelelőek_.

A Azure PowerShell modul az Azure-erőforrások parancssorból vagy parancsfájlokból való kezelésére szolgál.
Ez az útmutató ismerteti, hogyan használható az az modul egy szabályzat-hozzárendelés létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

- Mielőtt elkezdené, győződjön meg arról, hogy a Azure PowerShell legújabb verziója van telepítve. Részletes információkért lásd: [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps) .

- Regisztrálja a Azure Policy bepillantást erőforrás-szolgáltatót a Azure PowerShell használatával. Az erőforrás-szolgáltató regisztrálásával biztosítja, hogy az előfizetése működni fog vele. Erőforrás-szolgáltató regisztrálásához rendelkeznie kell engedéllyel az erőforrás-szolgáltató regisztrálása művelet. Ezt a műveletet a Közreműködői és Tulajdonosi szerepkörök magukba foglalják. Az erőforrás-szolgáltató regisztrálásához futtassa az alábbi parancsot:

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Az erőforrás-szolgáltatók regisztrálásával és megtekintésével kapcsolatos további információért tekintse meg az [erőforrás-szolgáltatókat és típusaikat](../../azure-resource-manager/resource-manager-supported-services.md) ismertető cikket.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben a rövid útmutatóban egy szabályzat-hozzárendelést hoz létre a _felügyelt lemezek definíciója nélküli naplózási virtuális gépek_ számára. Ez a házirend-definíció a felügyelt lemezeket nem használó virtuális gépeket azonosítja.

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
- **DisplayName** – A szabályzat-hozzárendelés megjelenített neve. Ebben az esetben a _virtuális gépek naplózása felügyelt lemezek hozzárendelése nélkül_történik.
- **Definíció** – A szabályzatdefiníció, amely alapján létre fogja hozni a hozzárendelést. Ebben az esetben ez a házirend-definíciós _virtuális gépek azonosítója, amelyek nem használnak felügyelt lemezeket_.
- **Hatókör** – A hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrások vagy erőforráscsoportok esetében lesz kényszerítve. Ez egyetlen előfizetéstől teljes erőforráscsoportokig terjedhet. Győződjön meg arról, hogy a &lt;scope&gt; helyett az erőforráscsoport neve szerepel.

Most már készen áll a nem megfelelő erőforrások azonosítására a környezet megfelelőségi állapotának megismerése érdekében.

## <a name="identify-non-compliant-resources"></a>Nem megfelelő erőforrások azonosítása

A következő információkkal azonosíthatja a létrehozott szabályzat-hozzárendelés szempontjából nem megfelelő erőforrásokat. Futtassa az alábbi parancsot:

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

A házirend állapotának beolvasásával kapcsolatos további információkért lásd: [Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState).

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

Az eredmények megegyeznek a szabályzat-hozzárendelés **erőforrás-megfelelőség** lapján látható Azure Portal nézetben.

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