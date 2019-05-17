---
title: Oktatóanyag – a felhasználó rbac-RÓL és a Resource Manager-sablon használatával Azure-erőforrásokhoz való hozzáférés biztosítása |} A Microsoft Docs
description: Megtudhatja, hogyan felhasználói szerepkörön alapuló hozzáférés-vezérlés (RBAC) használatával az Azure Resource Manager-sablon használatával Azure-erőforrásokhoz való hozzáférést.
services: role-based-access-control,azure-resource-manager
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control,azure-resource-manager
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/15/2019
ms.author: rolyon
ms.openlocfilehash: e99a9d2cfa38c9b2ea74f9075b18f81006b34881
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791524"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>Oktatóanyag: Egy felhasználó hozzáférést biztosítani Azure-erőforrások rbac-RÓL és a Resource Manager-sablon használatával

[Szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) van az Azure-erőforrásokhoz való hozzáférés kezelése ugyanúgy. Ebben az oktatóanyagban hozzon létre egy erőforráscsoportot, és hozzáférést egy felhasználó az erőforráscsoportban lévő virtuális gépek létrehozása és kezelése. Ebben az oktatóanyagban a hozzáférést egy Resource Manager-sablon üzembe helyezését tárgyalja. A Resource Manager-sablonok fejlesztéséhez további információkért lásd: [Resource Manager dokumentációja](/azure/azure-resource-manager/) és a [sablonreferenciája](/azure/templates/microsoft.authorization/allversions
).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzáférés engedélyezése egy felhasználó egy erőforrás-csoport hatóköre:
> * Az üzembe helyezés ellenőrzése
> * A fölöslegessé vált elemek eltávolítása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Adja hozzá, és távolítsa el a szerepkör-hozzárendeléseket, kell rendelkeznie:

* `Microsoft.Authorization/roleAssignments/write` és `Microsoft.Authorization/roleAssignments/delete` engedélyek, mint például [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) vagy [tulajdonosa](built-in-roles.md#owner)

## <a name="grant-access"></a>Hozzáférés biztosítása

A rendszer a rövid útmutatóban használt sablon [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/). További Azure engedélyezési kapcsolódó sablonok találhatók [Itt](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization).

A sablon üzembe helyezéséhez válassza **kipróbálás** nyissa meg az Azure Cloud shellt, és illessze be a következő PowerShell-parancsfájlt a shell ablakába. Illessze be a kódot, kattintson a jobb gombbal a rendszerhéj ablakát, és jelölje ki **illessze be**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$emailAddress = Read-Host -Prompt "Enter your email address that is associated with your Azure subscription (used to find the principal ID)"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

$resourceGroupName = "${projectName}rg"
$roleAssignmentName = New-Guid
$principalId = (Get-AzAdUser -Mail $emailAddress).id
$roleDefinitionId = (Get-AzRoleDefinition -name "Virtual Machine Contributor").id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
```

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Nyissa meg az előző eljárásban létrehozott erőforráscsoportot. Alapértelmezés szerint ez a projekt nevére a **rg** hozzáfűzve.
1. A bal oldali menüben válassza az **Access control (IAM)** lehetőséget.
1. Válassza ki **szerepkör-hozzárendelések**. 
1. A **neve**, adja meg az e-mail-címét írta be az utolsó eljárást. Meg kell jelennie az e-mail-címmel a felhasználó rendelkezik a **virtuális gépek Közreműködője** szerepkör.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Az utolsó eljárás során létrehozott erőforráscsoportot eltávolításához jelölje ki **kipróbálás** nyissa meg az Azure Cloud shellt, és illessze be a következő PowerShell-parancsfájlt a shell ablakába.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Egy felhasználó hozzáférést biztosítani Azure-erőforrások RBAC és az Azure PowerShell használatával](tutorial-role-assignments-user-powershell.md)