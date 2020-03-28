---
title: 'Oktatóanyag: Felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz az RBAC és a Resource Manager sablonnal'
description: Megtudhatja, hogyan adhat hozzáférést egy felhasználónak az Azure-erőforrásokhoz szerepköralapú hozzáférés-vezérlés (RBAC) használatával az oktatóanyagban található Azure Resource Manager-sablon használatával.
services: role-based-access-control,azure-resource-manager
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/15/2019
ms.author: rolyon
ms.openlocfilehash: 96ca4f65d2def5f5004388c533410f09cc2a71fa
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77138204"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>Oktatóanyag: Felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz az RBAC és a Resource Manager sablon használatával

[Szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) az Azure-erőforrásokhoz való hozzáférés kezelése. Ebben az oktatóanyagban létrehoz egy erőforráscsoportot, és hozzáférést biztosít a felhasználónak az erőforráscsoportban lévő virtuális gépek létrehozásához és kezeléséhez. Ez az oktatóanyag az Erőforrás-kezelő sablon üzembe helyezésének folyamatára összpontosít a hozzáférés biztosításához. Az Erőforrás-kezelő sablonjainak fejlesztéséről az [Erőforrás-kezelő dokumentációja](/azure/azure-resource-manager/) és a sablon hivatkozása című [témakörben olvashat bővebben.](/azure/templates/microsoft.authorization/allversions
)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzáférés megadása egy felhasználószámára egy erőforráscsoport hatókörén
> * Az üzembe helyezés ellenőrzése
> * A fölöslegessé vált elemek eltávolítása

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Szerepkör-hozzárendelések hozzáadásához és eltávolításához a következőkre van szükség:

* `Microsoft.Authorization/roleAssignments/write`és `Microsoft.Authorization/roleAssignments/delete` engedélyek, például [a Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) vagy [a tulajdonos](built-in-roles.md#owner)

## <a name="grant-access"></a>Hozzáférés biztosítása

A rövid útmutatóban használt sablon az [Azure rövidútmutatós sablonjaiból származik.](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/) További Azure-engedélyezési sablonok [itt](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization)találhatók.

A sablon üzembe helyezéséhez válassza a **Próbálja ki** az Azure Cloud rendszerhéj megnyitásához, majd illessze be a következő PowerShell-parancsfájlt a rendszerhéj ablakába. A kód beillesztéséhez kattintson a jobb gombbal a rendszerhéj ablakára, majd válassza a **Beillesztés parancsot.**

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

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Nyissa meg az utolsó eljárásban létrehozott erőforráscsoportot. Az alapértelmezett név a projekt neve **rg** csatolt.
1. A bal oldali menüben válassza az **Access control (IAM)** lehetőséget.
1. Válassza a **Szerepkör-hozzárendelések** lehetőséget. 
1. A **Név mezőbe**írja be az utolsó eljárásban beírt e-mail címet. Látnia kell, hogy az e-mail-címmel rendelkező felhasználó rendelkezik a **Virtuálisgép közreműködői** szerepkörrel.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Az utolsó eljárásban létrehozott erőforráscsoport eltávolításához válassza a **Próbálja ki** az Azure Cloud rendszerhéj megnyitásához lehetőséget, majd illessze be a következő PowerShell-parancsfájlt a rendszerhéj ablakába.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz az RBAC és az Azure PowerShell használatával](tutorial-role-assignments-user-powershell.md)