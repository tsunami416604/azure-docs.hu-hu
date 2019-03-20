---
title: Minta - naplózási, ha a megadott alkalmazások nem települnek a Linux rendszerű virtuális gépeken belül
description: Ez a minta Vendég Szabályzatkonfiguráció-kezdeményezéshez és definíciók naplózása a megadott alkalmazások nem települnek a Linux rendszerű virtuális gépeken belül.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 03/18/2019
ms.author: dacoulte
ms.openlocfilehash: bc4c54fa157f5468bbc324ebdf03dd65a47488c9
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190491"
---
# <a name="sample---audit-if-specified-applications-are-not-installed-inside-linux-vms"></a>Minta - naplózási, ha a megadott alkalmazások nem települnek a Linux rendszerű virtuális gépeken belül

A Szabályzatkonfiguráció Vendég-kezdeményezéshez naplózza, hogy a megadott alkalmazás telepítve van-e Linux rendszerű virtuális gépeken belül. A beépített kezdeményezés csomagazonosítója `/providers/Microsoft.Authorization/policySetDefinitions/c937dcb4-4398-4b39-8d63-4a6be432252e`.

> [!IMPORTANT]
> Az összes Vendég konfigurációs kezdeményezések épülnek fel, **naplózási** és **deployIfNotExists** szabályzat-definíciókat. Hozzárendelés, csak a szabályzatdefiníciók egyik Vendég konfiguráció nem megfelelő működéséhez vezethet.

Ez a minta használatával rendelheti hozzá:

- Az [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="components-of-the-initiative"></a>A kezdeményezés összetevői

Ez [Vendég konfigurációs](../concepts/guest-configuration.md) kezdeményezés a következő házirendek épül fel:

- [naplózási](#audit-definition) –, hogy az alkalmazás telepítve van-e belül Linux rendszerű virtuális gépek naplózása
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/fee5cb2b-9d9b-410e-afe3-2902d90d0004`
- [deployIfNotExists](#deployIfNotExists-definition) -üzembe helyezése Virtuálisgép-bővítményt, hogy az alkalmazás telepítve van-e belül Linux rendszerű virtuális gépek naplózása
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/4d1c04de-2172-403f-901b-90608c35c721`

### <a name="initiative-definition"></a>Kezdeményezési definíció

A kezdeményezés van létrehozva a **naplózása** és **deployIfNotExists** együtt definíciók és a [kezdeményezés paraméterek](#initiative-parameters). Ez az a JSON a definíció.

[!code-json[initiative-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/azurepolicyset.json "Initiative definition (JSON)")]

### <a name="initiative-parameters"></a>Kezdeményezési paraméterek

| Név |} Írja be. Leírás |} |}---|}---|| ---|} |} applicationName |} Karakterlánc |} Alkalmazás neve. Példa: "python", "powershell" vagy "python, powershell" például egy vesszővel tagolt lista. Használat \* helyettesítő, például a "power\*". |}

Ha PowerShell vagy Azure CLI segítségével hoz létre egy hozzárendelést, a paraméterértékek átadhatók JSON-ként akár sztring formában, akár egy `-PolicyParameter` (PowerShell) vagy `--params` (Azure CLI) elemet használó fájlban.
A PowerShell a `-PolicyParameterObject` elemet is támogatja, ehhez a parancsmagnak át kell adni egy Name/Value kivonattáblát, ahol **Name** a paraméter neve, **Value** pedig a hozzárendelés során átadott érték vagy értéktömb.

Ebben a példában a paraméterben, alkalmazások telepítése _python_ és _powershell_ ellenőrzi.

```json
{
    "applicationName": {
        "value": "python,powershell"
    }
}
```

Csak a **deployIfNotExists** szabályzat-definíció a kezdeményezési paraméterek használ.

### <a name="audit-definition"></a>definíció naplózása

A JSON-szabályok definiálása a **naplózási** szabályzat-definíció.

[!code-json[audit-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json "audit policy rules (JSON)")]

### <a name="deployifnotexists-definition"></a>deployIfNotExists definíciója

A JSON-szabályok definiálása a **deployIfNotExists** szabályzat-definíció.

[!code-json[deployIfNotExists-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json "deployIfNotExists policy rules (JSON)")]

A **deployIfNotExists** a szabályzatdefiníció határozza meg a csoportházirend érvényesítése az Azure-rendszerképek:

|Közzétevő |Ajánlat |SKU |
|-|-|-|
|OpenLogic |CentOS\* |Mindenhol, kivéve 6\* |
|RedHat |RHEL |Mindenhol, kivéve 6\* |
|RedHat |osa | Összes |
|credativ |Debian | Mindenhol, kivéve 7\* |
|SUSE |SLES\* |Mindenhol, kivéve 11\* |
|Canonical| UbuntuServer |Mindenhol, kivéve 12\* |
|microsoft-dsvm |linux-data-science-vm-ubuntu |Összes |
|microsoft-dsvm |azureml |Összes |
|cloudera |cloudera-centos-os |Mindenhol, kivéve 6\* |
|cloudera |cloudera-altus-centos-os |Összes |
|microsoft-ads |linux\* |Összes |
|microsoft-aks |Összes |Összes |
|AzureDatabricks |Összes |Összes |
|qubole-Inc vállalattól |Összes |Összes |
|datastax |Összes |Összes |
|A Couchbase |Összes |Összes |
|scalegrid |Összes |Összes |
|Ellenőrzőpont |Összes |Összes |
|paloaltonetworks |Összes |Összes |

A **üzembe helyezési** a szabály részét adja át a _installedApplication_ paramétert a virtuális gépen Vendég konfigurációja ügynökhöz. Ez a konfiguráció lehetővé teszi, hogy az ügynök az ellenőrzések elvégzéséhez és a jelentés megfelelőség biztonsági keresztül a **naplózási** szabályzat-definíció.

## <a name="azure-portal"></a>Azure Portal

Után az **naplózási** és **deployIfNotExists** definíciók jönnek létre a portálon, azt javasoljuk, hogy be őket egy [kezdeményezés](../concepts/definition-structure.md#initiatives) hozzárendelés.

### <a name="create-copy-of-audit-definition"></a>Naplózási definíció másolatának létrehozása

[![Üzembe helyezés az Azure-ban](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)
[![Üzembe helyezés az Azure Governmentben](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)

A gombok segítségével üzembe helyezése a portálon keresztül másolatot készít a **naplózási** szabályzat-definíció.
Anélkül a párosított **deployIfNotExists** szabályzatdefiníciót, a Vendég-konfiguráció nem fog megfelelően működni.

### <a name="create-copy-of-deployifnotexists-definition"></a>Másolat deployIfNotExists-definíció létrehozása

[![Üzembe helyezés az Azure-ban](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)
[![Üzembe helyezés az Azure Governmentben](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)

A gombok segítségével üzembe helyezése a portálon keresztül másolatot készít a **deployIfNotExists** szabályzat-definíció. Anélkül a párosított **naplózási** szabályzatdefiníciót, a Vendég-konfiguráció nem fog megfelelően működni.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Üzembe helyezés az Azure PowerShell-lel

#### <a name="copy-and-assign-the-initiative"></a>Másolja, majd a kezdeményezés hozzárendelése

Ezeket a lépéseket, hozzon létre egy másolatot a kezdeményezés, amely tartalmazza a beépített szabályzatokat is **naplózási** és **deployIfNotExists** és a kezdeményező rendel egy erőforráscsoportot.

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

#### <a name="copy-and-assign-the-audit-definition"></a>Másolja ki és a naplózási definíció hozzárendelése

Ezeket a lépéseket, hozzon létre egy példányát a **naplózási** definícióját, és rendelje hozzá egy erőforráscsoportot. Ez a definíció nem fog megfelelően működni anélkül a párosított **deployIfNotExists** definícióját is hozzárendeli.

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

#### <a name="copy-and-assign-the-deployifnotexists-definition"></a>Másolja, majd a deployIfNotExists-definíció hozzárendelése

Ezeket a lépéseket, hozzon létre egy példányát a **deployIfNotExists** definícióját, és rendelje hozzá egy erőforráscsoportot.
Ez a definíció nem fog megfelelően működni anélkül a párosított **naplózási** definícióját is hozzárendeli.

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
| [New-AzPolicySetDefinition](/powershell/module/az.resources/New-AzPolicySetDefinition) | Létrehoz egy Azure szabályzat-kezdeményezéshez. |
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-AzPolicyDefinition) | Létrehoz egy Azure szabályzat-definíció. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup) | Egyetlen erőforráscsoportot kér le. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-AzPolicyAssignment) | Létrehoz egy új Azure szabályzat-hozzárendelés-kezdeményezéshez vagy definíciója. |
| [New-AzRoleAssignment](/powershell/module/az.resources/New-AzRoleAssignment) | A megadott egyszerű hozzáférést biztosít egy meglévő szerepkör-hozzárendelés. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-AzPolicyAssignment) | Eltávolít egy létező Azure Policy-hozzárendelést. |
| [Remove-AzPolicySetDefinition](/powershell/module/az.resources/Remove-AzPolicySetDefinition) | Eltávolítja a kezdeményezések. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-AzPolicyDefinition) | Eltávolít egy definíciót. |

## <a name="next-steps"></a>További lépések

- Tekintse át további [Azure Policy minták](index.md).
- Tudjon meg többet [Azure Vendég Szabályzatkonfiguráció](../concepts/guest-configuration.md).
- Felülvizsgálat [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md).