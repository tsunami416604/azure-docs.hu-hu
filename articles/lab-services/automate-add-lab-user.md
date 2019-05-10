---
title: Automatizálhatja a lab-felhasználó hozzáadása az Azure DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Ismerje meg, hogyan automatizálható a lab-felhasználó hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: 0eed874d405fcf99241a702292f8ceadae6c5a07
ms.sourcegitcommit: 1d257ad14ab837dd13145a6908bc0ed7af7f50a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65502029"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>A lab-felhasználó hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet automatizálása
Az Azure DevTest Labs lehetővé teszi az önkiszolgáló fejlesztési-tesztelési környezetek gyors létrehozását az Azure portal használatával. Azonban ha több különböző csapatok és több DevTest Labs-példánnyal rendelkezik, a létrehozási folyamat automatizálása időt takaríthat meg. [Az Azure Resource Manager-sablonok](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) hozhat létre a labs, labor virtuális gépeken, egyéni rendszerképen, képleteket, és felhasználók hozzáadása az automatizált módon zajlik. Ez a cikk kifejezetten azzal foglalkozik, a felhasználók hozzáadása a DevTest Labs-példányra.

Felhasználó hozzáadása egy laborhoz, adja hozzá a felhasználót a **DevTest Labs-felhasználó** szerepkörnek a labor létrehozása. Ez a cikk bemutatja, hogyan automatizálható egy felhasználó hozzáadása egy laborhoz, a következő módszerek egyikével:

- Azure Resource Manager-sablonok
- Azure PowerShell-parancsmagok 
- Azure CLI.

## <a name="use-azure-resource-manager-templates"></a>Használjon Azure Resource Manager-sablonokat
A következő minta Resource Manager-sablon hozzá kell adni a felhasználó megadja a **DevTest Labs-felhasználó** labor szerepe. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The objectId of the user, group, or service principal for the role."
      }
    },
    "labName": {
      "type": "string",
      "metadata": {
        "description": "The name of the lab instance to be created."
      }
    },
    "roleAssignmentGuid": {
      "type": "string",
      "metadata": {
        "description": "Guid to use as the name for the role assignment."
      }
    }
  },
  "variables": {
    "devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
    "fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]",
    "roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
  },
  "resources": [
    {
      "apiVersion": "2016-05-15",
      "type": "Microsoft.DevTestLab/labs",
      "name": "[parameters('labName')]",
      "location": "[resourceGroup().location]"
    },
    {
      "apiVersion": "2016-07-01",
      "type": "Microsoft.DevTestLab/labs/providers/roleAssignments",
      "name": "[variables('fullDevTestLabUserRoleName')]",
      "properties": {
        "roleDefinitionId": "[variables('devTestLabUserRoleId')]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('roleScope')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
      ]
    }
  ]
}

```

Ha a szerepkör, amely a labor hoz létre ugyanabban a sablonban használt hozzárendelése, ne felejtse el hozzáadni a szerepkör-hozzárendelés erőforrás és a tesztkörnyezet közötti függőség. További információkért lásd: [függőségek meghatározása az Azure Resource Manager-sablonok](/azure-resource-manager/resource-group-define-dependencies.md) cikk.

### <a name="role-assignment-resource-information"></a>Szerepkör-hozzárendelés erőforrásadatok
A szerepkör-hozzárendelés erőforrás számára meg kell adni a típusa és neve.

Az első lépésben jegyezze fel az, hogy az erőforrás típusát nem `Microsoft.Authorization/roleAssignments` , akkor a egy erőforráscsoportot.  Ehelyett az erőforrástípust a mintát követi, `{provider-namespace}/{resource-type}/providers/roleAssignments`. Ebben az esetben az erőforrástípus lesz `Microsoft.DevTestLab/labs/providers/roleAssignments`.

A szerepkör-hozzárendelés neve magának kell globálisan egyedinek kell lennie.  A mintát használ, a hozzárendelés neve `{labName}/Microsoft.Authorization/{newGuid}`. A `newGuid` egy paraméter értéke a sablont. Ez biztosítja, hogy a szerepkör-hozzárendelés neve egyedi. Mivel nincs sablonfüggvények GUID azonosítók létrehozására, létre kell hoznia egy GUID Azonosítót saját maga bármely GUID készítő eszközzel.  

A szerepkör-hozzárendelés neve a sablon által meghatározott a `fullDevTestLabUserRoleName` változó. A pontos sor a sablonból a következő:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Szerepkör-hozzárendelés erőforrás-tulajdonságok
Szerepkör-hozzárendelés maga három tulajdonságot határozza meg. Szüksége van a `roleDefinitionId`, `principalId`, és `scope`.

### <a name="role-definition"></a>Szerepkör-definíció
A szerepkör-definíció Azonosítóját a karakterlánc-azonosító, a meglévő szerepkör-definíció. A szerepkör-azonosító szerepel az űrlap `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`. 

Az előfizetés-azonosítója használatával szerezhető be `subscription().subscriptionId` sablonfüggvény.  

A szerepkör-definíció kapott a `DevTest Labs User` beépített szerepkör. A GUID azonosító beolvasásához a [DevTest Labs-felhasználó](../role-based-access-control/built-in-roles.md#devtest-labs-user) szerepkör, használhatja a [szerepkör-hozzárendelések REST API](/rest/api/authorization/roleassignments) vagy a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0) parancsmagot.

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

A szerepkör-azonosító meghatározása a változók szakaszban és nevű `devTestLabUserRoleId`. A sablon a szerepkör-azonosító értéke: 111111111-0000-0000-11111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>Résztvevő-azonosító
Résztvevő-azonosító az Active Directory-felhasználó, csoport vagy labor felhasználóként hozzá a labor kívánt szolgáltatásnév Objektumazonosítóját. Használja a sablon a `ObjectId` paraméterként.

Az ObjectId megkaphassa használatával a [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup, vagy [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) PowerShell-parancsmagokat. Ezek a parancsmagok egyetlen vagy -azonosító tulajdonságot használja, amely a szükséges objektumazonosító rendelkező Active Directory-objektumok listáját adja vissza. Az alábbi példa bemutatja, hogyan kérhet egy felhasználó Objektumazonosítója egy vállalat.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

Használhatja az Azure Active Directory PowerShell-parancsmagok tartalmazó [Get-MsolUser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0), [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0), és [Get-msolserviceprincipal parancsmag](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>Scope
Hatókör megadja az erőforrás vagy erőforráscsoport, amelyben a szerepkör-hozzárendelés érvényesíteni. Az erőforrások, a hatókör van a képernyőn: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`. Használja a sablon a `subscription().subscriptionId` függvény meg kell adni a `subscription-id` része, és a `resourceGroup().name` sablonfüggvény meg kell adni a `resource-group-name` rész. Ezek a függvények használatával, az azt jelenti, hogy a labor, amelyhez szerepkör hozzárendelése már léteznie kell az aktuális előfizetést, és ugyanahhoz az erőforráscsoporthoz tartozik, amely a sablon telepítése történik. Az utolsó szakaszban `resource-name`, a neve, a labor létrehozása. Ez az érték ebben a példában a sablonparaméter keresztül érkezik. 

A szerepkör hatóköre a sablonban: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>A sablon telepítése
Először is hozzon létre egy paraméterfájlt (például: azuredeploy.parameters.json), amely a Resource Manager-sablon paramétereihez tartozó értékek adja át. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "value": "11111111-1111-1111-1111-111111111111"
    },
    "labName": {
      "value": "MyLab"
    },
    "roleAssignmentGuid": {
      "value": "22222222-2222-2222-2222-222222222222"
    }
  }
}
```

Ezután használja a [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) PowerShell-parancsmag használatával a Resource Manager-sablon üzembe helyezéséhez. A következő példaparancs egy személy, vagy az egyszerű szolgáltatás rendel a DevTest Labs-felhasználó szerepkör tesztkörnyezet.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

Fontos megjegyezni, hogy a csoport központi telepítés nevét és a szerepkör-hozzárendelés GUID kell egyedinek lennie. Ha megpróbál telepíteni egy nem egyedi GUID Azonosítóval rendelkező erőforrás-hozzárendelés, akkor kap egy `RoleAssignmentUpdateNotPermitted` hiba.

Ha azt tervezi, több Active Directory-objektumok hozzáadása a DevTest Labs-felhasználó szerepkörnek a labor is több alkalommal a sablont használja, érdemes dinamikus objektumok a PowerShell-parancsot. Az alábbi példában a [New-Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) parancsmag használatával dinamikusan adjon meg az erőforrás csoport központi telepítés nevét és a szerepkör-hozzárendelés GUID.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Azure PowerShell használatával
A bevezető írtaknak hoz létre egy új Azure szerepkör-hozzárendelés hozzáadása a felhasználót, hogy a **DevTest Labs-felhasználó** szerepkörnek a labor létrehozása. A PowerShellben úgy teheti meg, hogy a [New-AzureRMRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) parancsmagot. Ez a parancsmag rugalmasság érdekében számos választható paraméterekkel rendelkezik. A `ObjectId`, `SigninName`, vagy `ServicePrincipalName` az objektum alatt engedélyeket adhat meg.  

Íme egy minta Azure PowerShell-parancsot, amely a felhasználó hozzáadja a megadott labor létrehozása a DevTest Labs-felhasználó szerepkör.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Adja meg az erőforrást, amelyhez engedélyeket a rendszer éppen nyújtott megadott kombinációja a `ResourceName`, `ResourceType`, `ResourceGroup` vagy a `scope` paraméter. Paraméterek bármilyen kombinációját használja, a parancsmag az Active Directory-objektum (felhasználó, csoport vagy szolgáltatásnév), a hatókör (erőforráscsoportra vagy erőforrásra) és a szerepkör-definíció egyedi azonosításához elegendő információt tartalmaznak.

## <a name="use-azure-command-line-interface-cli"></a>Az Azure parancssori felület (CLI) használata
Az Azure CLI-ben, egy labs-felhasználó hozzáadása egy laborhoz segítségével történik a `az role assignment create` parancsot. További információ az Azure CLI-parancsmagok: [RBAC és az Azure CLI használatával Azure-erőforrásokhoz való hozzáférés kezelése](../role-based-access-control/role-assignments-cli.md).

A hozzáféréssel rendelkező objektum is megadható a `objectId`, `signInName`, `spn` paramétereket. Úgy azonosítható a tesztkörnyezetben, amelyhez az objektum alatt hozzáférést kap a `scope` URL-cím vagy kombinációja a `resource-name`, `resource-type`, és `resource-group` paraméterek.

Az alábbi Azure CLI-példa bemutatja, hogyan a megadott labor hozzáadása egy személy a DevTest Labs-felhasználó szerepkörhöz.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Hozzon létre és kezelhet virtuális gépeket a DevTest Labs szolgáltatással az Azure CLI használatával](devtest-lab-vmcli.md)
- [Virtuális gép létrehozása a DevTest Labs szolgáltatással az Azure PowerShell-lel](devtest-lab-vm-powershell.md)
- [Parancssori eszközök segítségével elindíthatja és leállíthatja a virtuális gépek Azure DevTest Labs szolgáltatásban](use-command-line-start-stop-virtual-machines.md)

