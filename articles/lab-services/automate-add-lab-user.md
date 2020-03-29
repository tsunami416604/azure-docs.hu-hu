---
title: Laborfelhasználó automatikus hozzáadása az Azure DevTest Labs ben | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan automatizálhatja egy felhasználó hozzáadása egy laborban az Azure DevTest Labs az Azure Resource Manager-sablonok, PowerShell és CLI használatával.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 70a6359923734c83590d4677bb2c93966c925d14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718139"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Laborfelhasználó automatikus hozzáadása laborkörnyezethez az Azure DevTest Labsben
Az Azure DevTest Labs lehetővé teszi, hogy gyorsan hozzon létre önkiszolgáló fejlesztési-tesztelési környezeteket az Azure Portal használatával. Ha azonban több csapattal és több DevTest Labs-példával rendelkezik, a létrehozási folyamat automatizálása időt takaríthat meg. [Az Azure Resource Manager-sablonok](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) lehetővé teszik, hogy laborokat, laborvirtuális gépeket, egyéni lemezképeket, képleteket hozzon létre, és automatizált módon adjon hozzá felhasználókat. Ez a cikk kifejezetten a felhasználók hozzáadása a DevTest Labs-példány.

Ha hozzá szeretne adni egy felhasználót egy tesztkörnyezetbe, adja hozzá a felhasználót a **devtest labs felhasználói** szerepköréhez a laborban. Ez a cikk bemutatja, hogyan automatizálhatja egy felhasználó hozzáadása a laborhoz az alábbi módok on:

- Azure Resource Manager-sablonok
- Azure PowerShell-parancsmagok 
- Azure CLI.

## <a name="use-azure-resource-manager-templates"></a>Használjon Azure Resource Manager-sablonokat
A következő minta Erőforrás-kezelő sablon egy felhasználót ad hozzá a **tesztkörnyezet DevTest Labs felhasználói** szerepköréhez. 

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

Ha a szerepkört ugyanabban a sablonban rendeli hozzá, amely a labort hozza létre, ne felejtse el függőséget hozzáadni a szerepkör-hozzárendelési erőforrás és a labor között. További információ: [Függőségek definiálása az Azure Resource Manager-sablonok cikkben.](../azure-resource-manager/templates/define-resource-dependency.md)

### <a name="role-assignment-resource-information"></a>Szerepkör-hozzárendelés erőforrás adatai
A szerepkör-hozzárendelési erőforrásnak meg kell adnia a típust és a nevet.

Az első dolog, hogy vegye figyelembe, hogy `Microsoft.Authorization/roleAssignments` a típus az erőforrás nem olyan, mint lenne egy erőforráscsoport.  Ehelyett az erőforrástípus követi `{provider-namespace}/{resource-type}/providers/roleAssignments`a mintát. Ebben az esetben az erőforrás `Microsoft.DevTestLab/labs/providers/roleAssignments`típusa a .

Magának a szerepkör-hozzárendelési névnek globálisan egyedinek kell lennie.  A hozzárendelés neve a `{labName}/Microsoft.Authorization/{newGuid}`mintát használja. A `newGuid` a sablon paraméterértéke. Biztosítja, hogy a szerepkör-hozzárendelés neve egyedi legyen. Mivel nincsenek sablonfüggvények a GUID-ok létrehozásához, saját maga kell létrehoznia egy GUID azonosítót bármely GUID generátor eszközzel.  

A sablonban a szerepkör-hozzárendelés nevét a `fullDevTestLabUserRoleName` változó határozza meg. A sablon pontos sora a következő:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Szerepkör-hozzárendelési erőforrás tulajdonságai
Maga a szerepkör-hozzárendelés három tulajdonságot határoz meg. Szüksége van `roleDefinitionId` `principalId`a `scope`, és.

### <a name="role-definition"></a>Szerepkör-definíció
A szerepkör-definícióazonosító a meglévő szerepkör-definíció karakterlánc-azonosítója. A szerepkör-azonosító a `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`képernyőn található. 

Az előfizetés-azonosító t a `subscription().subscriptionId` sablonfüggvény használatával kapjuk meg.  

Be kell szereznie a `DevTest Labs User` beépített szerepkör szerepkör-definícióját. A [DevTest Labs felhasználói](../role-based-access-control/built-in-roles.md#devtest-labs-user) szerepkör GUID-jának lekérnie használhatja a [Szerepkör-hozzárendelések REST API-t](/rest/api/authorization/roleassignments) vagy a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0) parancsmagot.

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

A szerepkörazonosító a változók szakaszban van `devTestLabUserRoleId`definiálva, és neve . A sablonban a szerepkör-azonosító beállítása: 111111111-0000-0000-111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>Elsődleges azonosító
Az egyszerű azonosító az Active Directory-felhasználó, -csoport vagy egyszerű szolgáltatás objektumazonosítója, amelyet tesztkörnyezet-felhasználóként szeretne hozzáadni a laborhoz. A sablon `ObjectId` a paramétert használja.

Az ObjectId a [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup vagy [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) PowerShell-parancsmagokkal kaphatja le. Ezek a parancsmagok egy vagy olyan Active Directory-objektumok listáját adják vissza, amelyek azonosító tulajdonsággal rendelkeznek, és ez az objektumazonosító, amelyre szüksége van. A következő példa bemutatja, hogyan szerezheti be egyetlen felhasználó objektumazonosítóját egy vállalatnál.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

Az Azure Active Directory PowerShell-parancsmagjai közé tartozik a [Get-MsolUser,](/powershell/module/msonline/get-msoluser?view=azureadps-1.0) [a Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)és a [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>Hatókör
A hatókör azt az erőforrást vagy erőforráscsoportot határozza meg, amelyre a szerepkör-hozzárendelést alkalmazni kell. Az erőforrások esetében a hatókör `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`a következő formában található: . A sablon `subscription().subscriptionId` a függvényt `subscription-id` használja az `resourceGroup().name` alkatrész kitöltéséhez, `resource-group-name` a sablon függvényt pedig az alkatrész kitöltéséhez. Ezeket a függvényeket azt jelenti, hogy a labor, amelyhez szerepkört rendel, léteznie kell az aktuális előfizetésben, és ugyanazt az erőforráscsoportot, amelyhez a sablon központi telepítése történik. Az utolsó `resource-name`rész, a labor neve. Ez az érték ebben a példában a sablon paraméteren keresztül érkezik. 

A szerepkör hatóköre a sablonban: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>A sablon telepítése
Először hozzon létre egy paraméterfájlt (például: azuredeploy.parameters.json), amely átadja az Erőforrás-kezelő sablon paramétereinek értékeit. 

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

Ezután az [Új-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) PowerShell-parancsmag használatával telepítse az Erőforrás-kezelő sablont. A következő példaparancs egy személyt, csoportot vagy egy egyszerű szolgáltatást rendel egy labor DevTest Labs felhasználói szerepköréhez.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

Fontos megjegyezni, hogy a csoport központi telepítési nevét és szerepkör-hozzárendelés GUID egyedinek kell lennie. Ha nem egyedi GUID azonosítóval próbál meg központi telepíteni egy `RoleAssignmentUpdateNotPermitted` erőforrás-hozzárendelést, hibaüzenetet kap.

Ha azt tervezi, hogy a sablon támlájára többször is több Active Directory-objektumot ad hozzá a labor DevTest Labs felhasználói szerepköréhez, fontolja meg a dinamikus objektumok használatát a PowerShell-parancsban. A következő példa az [Új guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) parancsmag segítségével adja meg dinamikusan az erőforráscsoport központi telepítési nevét és szerepkör-hozzárendelésGUID azonosítóját.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Azure PowerShell használatával
Abevezetésben tárgyalt a létrehozott egy új Azure-szerepkör-hozzárendelés t a felhasználó hozzáadása a **DevTest Labs felhasználói** szerepkör a laborban. A PowerShellben ezt a [New-AzureRMRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) parancsmag használatával. Ez a parancsmag számos választható paraméterrel rendelkezik a rugalmasság érdekében. A `ObjectId` `SigninName`, `ServicePrincipalName` vagy megadható az engedélyekkel rendelkező objektumként.  

Az alábbiakban egy minta Azure PowerShell-parancs, amely hozzáadja a felhasználót a DevTest Labs felhasználói szerepkör a megadott laborban.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Annak az erőforrásnak a megadásához, amelyhez az `ResourceName`engedélyeket megadják, `ResourceType`a , `ResourceGroup` vagy a `scope` paraméter kombinációjával adható meg. Bármilyen paraméterkombinációt is használ, adjon meg elegendő információt a parancsmagnak ahhoz, hogy egyedileg azonosíthassa az Active Directory-objektumot (felhasználó, csoport vagy egyszerű szolgáltatás), a hatókört (erőforráscsoportot vagy erőforrást) és a szerepkör-definíciót.

## <a name="use-azure-command-line-interface-cli"></a>Az Azure parancssori felületének (CLI) használata
Az Azure CLI-ben egy labs-felhasználó hozzáadása `az role assignment create` a laborhoz a parancs használatával történik. Az Azure CLI-parancsmagokról az [Azure-erőforrásokhoz való hozzáférés kezelése az RBAC és az Azure CLI használatával című](../role-based-access-control/role-assignments-cli.md)témakörben talál további információt.

A hozzáférést kapó objektumot a `objectId`, `signInName`, `spn` paraméterek határozzák meg. A labor, amelyhez az objektum hozzáférést kap, a url-cím `resource-type`vagy `resource-group` a `scope` , és a `resource-name`paraméterek kombinációja alapján azonosítható.

A következő Azure CLI-példa bemutatja, hogyan adhat hozzá egy személyt a devtest labs felhasználói szerepköra a megadott laborban.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Virtuális gépek létrehozása és kezelése a DevTest Labs segítségével az Azure CLI használatával](devtest-lab-vmcli.md)
- [Hozzon létre egy virtuális gépet a DevTest Labs segítségével az Azure PowerShell használatával](devtest-lab-vm-powershell.md)
- [Az Azure DevTest Labs virtuális gépek indításához és leállításához parancssori eszközök használata](use-command-line-start-stop-virtual-machines.md)

