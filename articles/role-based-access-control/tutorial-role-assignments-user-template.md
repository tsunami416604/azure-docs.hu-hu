---
title: 'Oktatóanyag: felhasználói hozzáférés engedélyezése az Azure-erőforrásokhoz a RBAC és Resource Manager-sablonnal'
description: Megtudhatja, hogyan biztosíthat felhasználói hozzáférést az Azure-erőforrásokhoz szerepköralapú hozzáférés-vezérléssel (RBAC) az oktatóanyag Azure Resource Manager sablonjának használatával.
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
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138204"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>Oktatóanyag: felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz az RBAC és Resource Manager-sablonnal

A [szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) az Azure-erőforrásokhoz való hozzáférés kezelésének módja. Ebben az oktatóanyagban létrehoz egy erőforráscsoportot, és felhasználói hozzáférést biztosít a virtuális gépek létrehozásához és kezeléséhez az erőforráscsoporthoz. Ez az oktatóanyag egy Resource Manager-sablon üzembe helyezésének folyamatát ismerteti a hozzáférés biztosításához. A Resource Manager-sablonok fejlesztésével kapcsolatos további információkért tekintse meg a [Resource Manager dokumentációját](/azure/azure-resource-manager/) és a [sablonra vonatkozó referenciát](/azure/templates/microsoft.authorization/allversions
).

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Hozzáférés biztosítása egy erőforráscsoport-hatókörben lévő felhasználó számára
> * Az üzembe helyezés ellenőrzése
> * A fölöslegessé vált elemek eltávolítása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A szerepkör-hozzárendelések hozzáadásához és eltávolításához a következőket kell tennie:

* `Microsoft.Authorization/roleAssignments/write` és `Microsoft.Authorization/roleAssignments/delete` engedélyek, például a [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) vagy a [tulajdonos](built-in-roles.md#owner)

## <a name="grant-access"></a>Hozzáférés biztosítása

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/)származik. További Azure-hitelesítéssel kapcsolatos sablonok [itt](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization)találhatók.

A sablon üzembe helyezéséhez válassza a **kipróbálás** lehetőséget az Azure Cloud Shell megnyitásához, majd illessze be a következő PowerShell-szkriptet a rendszerhéj ablakába. A kód beillesztéséhez kattintson a jobb gombbal a rendszerhéj-ablakra, majd válassza a **Beillesztés**lehetőséget.

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

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. Nyissa meg az utolsó eljárásban létrehozott erőforráscsoportot. Az alapértelmezett név a projekt neve **RG** hozzáfűzéssel.
1. A bal oldali menüben válassza az **Access control (IAM)** lehetőséget.
1. Válassza a **Szerepkör-hozzárendelések** lehetőséget. 
1. A **név**mezőben adja meg az utolsó eljárásban beírt e-mail-címet. Az e-mail-címmel rendelkező felhasználónak látnia kell a **virtuális gép közreműködői** szerepkört.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Az utolsó eljárásban létrehozott erőforráscsoport eltávolításához válassza a **kipróbálás** lehetőséget az Azure Cloud Shell megnyitásához, majd illessze be a következő PowerShell-szkriptet a rendszerhéj ablakába.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz a RBAC és a Azure PowerShell használatával](tutorial-role-assignments-user-powershell.md)