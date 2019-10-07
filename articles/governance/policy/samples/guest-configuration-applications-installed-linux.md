---
title: Minta – naplózás, ha az alkalmazások nem települnek a Linux rendszerű virtuális gépeken
description: Ez a példa a házirend vendég konfigurációs kezdeményezésére és a definíciók naplózására, ha a megadott alkalmazások nem települnek a linuxos virtuális gépeken belül.
author: DCtheGeek
ms.service: azure-policy
ms.topic: sample
ms.date: 05/02/2019
ms.author: dacoulte
ms.openlocfilehash: ef2ab4bebf2247b08cdc80ed74bbe17a67c5baae
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2019
ms.locfileid: "71977033"
---
# <a name="sample---audit-if-specified-applications-arent-installed-inside-linux-vms"></a>Minta – naplózás, ha a megadott alkalmazások nincsenek telepítve a Linux rendszerű virtuális gépeken

Ez a házirend-vendég konfigurációs kezdeményezés egy naplózási eseményt hoz létre, ha a megadott alkalmazások nem települnek a linuxos virtuális gépeken belül. A beépített kezdeményezés azonosítója `/providers/Microsoft.Authorization/policySetDefinitions/c937dcb4-4398-4b39-8d63-4a6be432252e`.

> [!IMPORTANT]
> Az összes vendég konfigurációs kezdeményezés **naplózási** és **deployIfNotExists** szabályzat-definícióból áll. Ha csak az egyik házirend-definíciót rendeli hozzá, a vendég konfigurációja nem működik megfelelően.

Ezt a mintát a következő használatával rendelheti hozzá:

- Az [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="components-of-the-initiative"></a>A kezdeményezés összetevői

A [vendég konfigurációs](../concepts/guest-configuration.md) kezdeményezés a következő szabályzatokból áll:

- [naplózás](#audit-definition) – naplózás, ha az alkalmazások nem települnek a Linux rendszerű virtuális gépekre
  - AZONOSÍTÓ: @NO__T – 0
- [deployIfNotExists](#deployIfNotExists-definition) – virtuálisgép-bővítmény üzembe helyezése naplózásra, ha az alkalmazások nem települnek a Linux rendszerű virtuális gépeken
  - AZONOSÍTÓ: @NO__T – 0

### <a name="initiative-definition"></a>Kezdeményezési definíció

A kezdeményezést úgy hozza létre, hogy összekapcsolja a **naplózási** és a **deployIfNotExists** -definíciókat, valamint a [kezdeményezés paramétereit](#initiative-parameters). Ez a definíció JSON-értéke.

[!code-json[initiative-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/azurepolicyset.json "Initiative definition (JSON)")]

### <a name="initiative-parameters"></a>Kezdeményezési paraméterek

|Name (Név) |Típus |Leírás |
|---|---|---|
|applicationName |Sztring |Az alkalmazások nevei. Például: "Python", "PowerShell" vagy vesszővel tagolt lista, például "Python, PowerShell". Használjon \* karaktert a helyettesítő karakterekkel való egyezéshez, például: "Power @ no__t-1". |

Ha PowerShell vagy Azure CLI segítségével hoz létre egy hozzárendelést, a paraméterértékek átadhatók JSON-ként akár sztring formában, akár egy `-PolicyParameter` (PowerShell) vagy `--params` (Azure CLI) elemet használó fájlban.
A PowerShell a `-PolicyParameterObject` elemet is támogatja, ehhez a parancsmagnak át kell adni egy Name/Value kivonattáblát, ahol **Name** a paraméter neve, **Value** pedig a hozzárendelés során átadott érték vagy értéktömb.

Ebben a példában a _Python_ és a _PowerShell_ alkalmazások telepítése naplózva van.

```json
{
    "applicationName": {
        "value": "python,powershell"
    }
}
```

Csak a **deployIfNotExists** szabályzat definíciója használja a kezdeményezés paramétereit.

### <a name="audit-definition"></a>naplózási definíció

A **naplózási** házirend definíciójának szabályait definiáló JSON.

[!code-json[audit-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json "audit policy rules (JSON)")]

### <a name="deployifnotexists-definition"></a>deployIfNotExists-definíció

A JSON a **deployIfNotExists** szabályzat definíciójának szabályait határozza meg.

[!code-json[deployIfNotExists-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json "deployIfNotExists policy rules (JSON)")]

A **deployIfNotExists** szabályzat definíciója meghatározza azokat az Azure-lemezképeket, amelyeken a házirend ellenőrzése megtörtént:

|Kiadó |Ajánlat |SKU |
|-|-|-|
|OpenLogic |CentOS @ no__t – 0 |Mind a 6 @ no__t-0 kivételével |
|RedHat |RHEL |Mind a 6 @ no__t-0 kivételével |
|RedHat |OSA | Összes |
|credativ |Debian | Mind a 7 @ no__t-0 kivételével |
|SUSE |SLES @ NO__T – 0 |Mind a 11 @ no__t-0 kivételével |
|Canonical| UbuntuServer |Mind a 12 @ no__t-0 kivételével |
|Microsoft – dsvm |linux-data-science-vm-ubuntu |Összes |
|Microsoft – dsvm |azureml |Összes |
|cloudera |cloudera-centos-os |Mind a 6 @ no__t-0 kivételével |
|cloudera |cloudera-altus-centos-os |Összes |
|microsoft-ads |linux\* |Összes |
|microsoft-aks |Összes |Összes |
|AzureDatabricks |Összes |Összes |
|qubole-Inc |Összes |Összes |
|datastax |Összes |Összes |
|couchbase |Összes |Összes |
|scalegrid |Összes |Összes |
|Checkpoint |Összes |Összes |
|paloaltonetworks |Összes |Összes |

A szabály **központi telepítési** része a _installedApplication_ paramétert átadja a virtuális gép vendég konfigurációs ügynökének. Ez a konfiguráció lehetővé teszi az ügynök számára, hogy a **naplózási** házirend definícióján keresztül visszaállítsa a megfelelőségi és jelentési megfelelőségi műveleteket.

## <a name="azure-portal"></a>Azure Portal

Miután a rendszer létrehozta a **naplózási** és **deployIfNotExists** -definíciókat a portálon, azt javasoljuk, hogy csoportosítsa azokat egy hozzárendelési [kezdeményezésre](../concepts/definition-structure.md#initiatives) .

### <a name="create-copy-of-audit-definition"></a>A naplózási definíció másolatának létrehozása

[@no__t – 1Deploy a szabályzatot az azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)
[![Deploy az Azure gov-hoz](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)

Ha ezeket a gombokat a portálon keresztül telepíti, a a **naplózási** házirend definíciójának másolatát hozza létre.
A párosított **deployIfNotExists** házirend-definíció nélkül a vendég konfigurációja nem fog megfelelően működni.

### <a name="create-copy-of-deployifnotexists-definition"></a>DeployIfNotExists-definíció másolatának létrehozása

[@no__t – 1Deploy a szabályzatot az azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)
[![Deploy az Azure gov-hoz](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)

Ha ezeket a gombokat használja a portálon keresztül történő üzembe helyezéshez, a létrehoz egy másolatot a **deployIfNotExists** házirend-definícióról. A párosított **naplózási** házirend meghatározása nélkül a vendég konfigurációja nem fog megfelelően működni.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Üzembe helyezés az Azure PowerShell-lel

#### <a name="copy-and-assign-the-initiative"></a>A kezdeményezés másolása és kiosztása

Ezek a lépések létrehozzák a kezdeményezés egy példányát, amely magában foglalja a **naplózási** és **deployIfNotExists** beépített szabályzatait, és hozzárendeli a kezdeményezést egy erőforráscsoporthoz.

```azurepowershell-interactive
# Create the policy initiative (Subscription scope)
$initDef = New-AzPolicySetDefinition -Name 'guestconfig-installed-application-linux' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This initiative will both deploy the policy requirements and audit that the specified application is installed inside Linux virtual machines.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.definitions.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the initiative parameter (JSON format)
$initParam = '{ "applicationName": { "value": "python,powershell" } }'

# Create the initiative assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicySetDefinition $initDef -PolicyParameter $initParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

Az előző hozzárendelés és definíció eltávolításához futtassa az alábbi parancsokat:

```azurepowershell-interactive
# Remove the initiative assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the initiative definition
Remove-AzPolicySetDefinition -Id $initDef
```

#### <a name="copy-and-assign-the-audit-definition"></a>A naplózási definíció másolása és kiosztása

Ezek a lépések létrehozzák a **naplózási** definíció másolatát, és hozzárendelik azt egy erőforráscsoporthoz. Ez a definíció nem fog megfelelően működni a párosított **deployIfNotExists** -definíció hozzárendelése nélkül is.

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-audit' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This policy audits that the specified application is installed inside Linux virtual machines. This policy should only be used along with its corresponding deploy policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-audit-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicyDefinition $definition
```

Az előző hozzárendelés és definíció eltávolításához futtassa az alábbi parancsokat:

```azurepowershell-interactive
# Remove the policy definition
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

#### <a name="copy-and-assign-the-deployifnotexists-definition"></a>A deployIfNotExists-definíció másolása és kiosztása

Ezek a lépések létrehozzák a **deployIfNotExists** definíciójának másolatát, és hozzárendelik azt egy erőforráscsoporthoz.
Ez a definíció nem fog megfelelően működni a párosított **naplózási** definíció hozzárendelése nélkül is.

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-deployIfNotExists' -DisplayName 'GuestConfig - Deploy VM extension to audit that an application is installed inside Linux VMs' -description 'Include this rule to deploy the VM extension for Microsoft Guest Configuration, the VM extension for Microsoft Azure Managed Service Identity, and the content required to audit that an application is installed inside Linux virtual machines. This policy should only be used along with its corresponding audit policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the definition parameter (JSON format)
$policyParam  = '{ "applicationName": { "value": "python,powershell" } }'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-deployIfNotExists-assignment' -DisplayName 'GuestConfig - Deploy VM extension to audit that Python and PowerShell are installed inside Linux VMs' -Scope $scope.ResourceID -PolicyDefinition $definition -PolicyParameter $policyParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

Az előző hozzárendelés és definíció eltávolításához futtassa az alábbi parancsokat:

```azurepowershell-interactive
# Remove the policy assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

### <a name="azure-powershell-explanation"></a>Azure PowerShell magyarázat

Az üzembe helyezési és eltávolítási szkriptek a következő parancsokat használják. Az alábbi táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik:

| Parancs | Megjegyzések |
|---|---|
| [New-AzPolicySetDefinition](/powershell/module/az.resources/New-AzPolicySetDefinition) | Létrehoz egy Azure Policy kezdeményezést. |
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-AzPolicyDefinition) | Létrehoz egy Azure Policy definíciót. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup) | Egyetlen erőforráscsoportot kér le. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-AzPolicyAssignment) | Új Azure Policy-hozzárendelést hoz létre egy kezdeményezéshez vagy definícióhoz. |
| [New-AzRoleAssignment](/powershell/module/az.resources/New-AzRoleAssignment) | Egy meglévő szerepkör-hozzárendelést ad az adott rendszerbiztonsági tag számára. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-AzPolicyAssignment) | Eltávolít egy létező Azure Policy-hozzárendelést. |
| [Remove-AzPolicySetDefinition](/powershell/module/az.resources/Remove-AzPolicySetDefinition) | Egy kezdeményezés eltávolítása. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-AzPolicyDefinition) | Eltávolít egy definíciót. |

## <a name="next-steps"></a>További lépések

- Tekintse át a további [Azure Policy mintákat](index.md).
- További információ a [Azure Policy vendég konfigurációról](../concepts/guest-configuration.md).
- Tekintse át [Azure Policy definíciós struktúráját](../concepts/definition-structure.md).
