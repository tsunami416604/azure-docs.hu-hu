---
title: Szerepkör-hozzárendelések hozzáadása RBAC- és Azure Resource Manager-sablonokkal
description: Ismerje meg, hogyan adhat hozzáférést az Azure-erőforrásokhoz felhasználók, csoportok, egyszerű szolgáltatástagok vagy felügyelt identitások számára az Azure szerepköralapú hozzáférés-vezérlés (RBAC) és az Azure Resource Manager-sablonok használatával.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9f817880f938f5d03024e3aacd9b84817a5ac721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77138289"
---
# <a name="add-role-assignments-using-azure-rbac-and-azure-resource-manager-templates"></a>Szerepkör-hozzárendelések hozzáadása Azure RBAC és Azure Resource Manager-sablonok használatával

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Az Azure PowerShell vagy az Azure CLI használata mellett szerepköröket is hozzárendelhet az [Azure Resource Manager-sablonok](../azure-resource-manager/templates/template-syntax.md)használatával. A sablonok akkor lehetnek hasznosak, ha konzisztensen és ismételten kell telepítenie az erőforrásokat. Ez a cikk azt ismerteti, hogyan rendelhet szerepköröket sablonok használatával.

## <a name="get-object-ids"></a>Objektumazonosítók beszerezése

Szerepkör hozzárendeléséhez meg kell adnia annak a felhasználónak, csoportnak vagy alkalmazásnak az azonosítóját, amelyhez a szerepkört hozzá kívánja rendelni. Az azonosító formátuma: `11111111-1111-1111-1111-111111111111`. Az azure-portál, az Azure PowerShell vagy az Azure CLI használatával kaphatja le az azonosítót.

### <a name="user"></a>Felhasználó

A felhasználó azonosítójának lekért menüjele a [Get-AzADUser](/powershell/module/az.resources/get-azaduser) vagy az [az ad user show](/cli/azure/ad/user#az-ad-user-show) parancsokat használhatja.

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Csoport

Egy csoport azonosítójának lekért menüje a [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) vagy az az ad group show parancsokat [használhatja.](/cli/azure/ad/group#az-ad-group-show)

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="application"></a>Alkalmazás

Egy egyszerű szolgáltatásnév (egy alkalmazás által használt identitás identitás) azonosítójának lekért és használhatja a [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) vagy az [ad sp list](/cli/azure/ad/sp#az-ad-sp-list) parancsokat. Egyszerű szolgáltatás esetén az objektumazonosítót használja, **ne** az alkalmazásazonosítót.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="add-a-role-assignment"></a>Szerepkör-hozzárendelés hozzáadása

Az RBAC-ban a hozzáférés engedélyezéséhez egy szerepkör-hozzárendelést kell hozzáadnia.

### <a name="resource-group-without-parameters"></a>Erőforráscsoport (paraméterek nélkül)

A következő sablon bemutatja a szerepkör-hozzárendelés hozzáadásának alapvető módját. Néhány érték a sablonon belül van megadva. A következő sablon bemutatja:

-  [Az Olvasó](built-in-roles.md#reader) szerepkör hozzárendelése egy felhasználóhoz, csoporthoz vagy alkalmazáshoz egy erőforráscsoport hatókörén

A sablon használatához a következőket kell tennie:

- Új JSON-fájl létrehozása és a sablon másolása
- Cserélje `<your-principal-id>` le egy felhasználó, csoport vagy alkalmazás azonosítójára, hogy a szerepkört hozzárendelje

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(resourceGroup().id)]",
            "properties": {
                "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
                "principalId": "<your-principal-id>"
            }
        }
    ]
}
```

Íme a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) és [az az csoport központi telepítésének létrehozása](/cli/azure/group/deployment#az-group-deployment-create) parancsokat, hogyan indíthatja el a központi telepítést egy ExampleGroup nevű erőforráscsoportban.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

Az alábbi példa az Olvasó szerepkör-hozzárendelésegy felhasználó egy erőforráscsoport telepítése után a sablont.

![Szerepkör-hozzárendelés az erőforráscsoport hatókörén](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription"></a>Erőforráscsoport vagy -előfizetés

Az előző sablon nem túl rugalmas. A következő sablon paramétereket használ, és különböző hatókörökben használható. A következő sablon bemutatja:

- Szerepkör hozzárendelése egy felhasználóhoz, csoporthoz vagy alkalmazáshoz erőforráscsoportban vagy előfizetési hatókörben
- A tulajdonosi, közreműködői és olvasói szerepkörök paraméterként való megadása

A sablon használatához a következő bemeneteket kell megadnia:

- Egy felhasználó, csoport vagy alkalmazás azonosítója, amelyhez hozzá rendelheti a szerepkört
- Egyedi azonosító, amelyet a szerepkör-hozzárendeléshez használni fog, vagy használhatja az alapértelmezett azonosítót

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

> [!NOTE]
> Ez a sablon nem idempotens, kivéve, ha ugyanazt `roleNameGuid` az értéket a sablon egyes központi telepítéséhez paraméterként adják meg. Ha `roleNameGuid` nem szerepel, alapértelmezés szerint minden központi telepítéskor új GUID jön `Conflict: RoleAssignmentExists` létre, és a következő telepítések hiba esetén sikertelenek lesznek.

A szerepkör-hozzárendelés hatóköre a központi telepítés szintje alapján történik. Íme a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) és [az az csoport központi telepítésének létrehozása](/cli/azure/group/deployment#az-group-deployment-create) parancsokat, hogyan indíthatja el a központi telepítést egy erőforráscsoport hatókörén.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Íme a [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) és az az deployment telepítési parancsok [létrehozása,](/cli/azure/deployment#az-deployment-create) hogyan indíthatja el a központi telepítést egy előfizetési hatókörben, és adja meg a helyet.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource"></a>Erőforrás

Ha egy erőforrás szintjén szerepkör-hozzárendelést kell hozzáadnia, a szerepkör-hozzárendelés formátuma eltérő. Megadja annak az erőforrásnak az erőforrás névterét és erőforrástípusát, amelyhez hozzá rendeli a szerepkört. Az erőforrás nevét is feltünteti a szerepkör-hozzárendelés nevében.

A szerepkör-hozzárendelés típusához és nevéhez a következő formátumot használja:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

A következő sablon bemutatja:

- Új tárfiók létrehozása
- Szerepkör hozzárendelése egy felhasználóhoz, csoporthoz vagy alkalmazáshoz a tárfiók hatókörén
- A tulajdonosi, közreműködői és olvasói szerepkörök paraméterként való megadása

A sablon használatához a következő bemeneteket kell megadnia:

- Egy felhasználó, csoport vagy alkalmazás azonosítója, amelyhez hozzá rendelheti a szerepkört

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        },
        {
            "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', guid(uniqueString(variables('storageName'))))]",
            "dependsOn": [
                "[variables('storageName')]"
            ],
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Az előző sablon központi telepítéséhez használja az erőforráscsoport-parancsokat. Íme a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) és [az az csoport központi telepítésének létrehozása](/cli/azure/group/deployment#az-group-deployment-create) parancsokat, hogyan indíthatja el a központi telepítést egy erőforrás-hatókörben.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

Az alábbi példa a közreműködői szerepkör-hozzárendelés egy felhasználó egy tárfiók telepítése után a sablont.

![Szerepkör-hozzárendelés az erőforrás hatókörén](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Új szolgáltatásnév

Ha létrehoz egy új egyszerű szolgáltatást, és azonnal megpróbál szerepkört hozzárendelni az egyszerű szolgáltatáshoz, a szerepkör-hozzárendelés bizonyos esetekben sikertelen lehet. Ha például létrehoz egy új felügyelt identitást, majd megpróbál szerepkört hozzárendelni az adott szolgáltatásnévhez ugyanabban az Azure Resource Manager-sablonban, a szerepkör-hozzárendelés sikertelen lehet. A hiba oka valószínűleg replikációs késleltetés. A szolgáltatásnév egy régióban jön létre; azonban a szerepkör-hozzárendelés előfordulhat egy másik régióban, amely még nem replikálta a szolgáltatás egyszerű még. A forgatókönyv megoldásához a `principalType` tulajdonságot `ServicePrincipal` a szerepkör-hozzárendelés létrehozásakor kell beállítania.

A következő sablon bemutatja:

- Új felügyelt identitásszolgáltatás-egyszerű szolgáltatás létrehozása
- Hogyan adja meg a`principalType`
- A közreműködői szerepkör hozzárendelése az adott szolgáltatásnévhez egy erőforráscsoport hatókörén

A sablon használatához a következő bemeneteket kell megadnia:

- A felügyelt identitás alapneve, vagy használhatja az alapértelmezett karakterláncot

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "baseName": {
            "type": "string",
            "defaultValue": "msi-test"
        }
    },
    "variables": {
        "identityName": "[concat(parameters('baseName'), '-bootstrap')]",
        "bootstrapRoleAssignmentId": "[guid(concat(resourceGroup().id, 'contributor'))]",
        "contributorRoleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
            "name": "[variables('identityName')]",
            "apiVersion": "2018-11-30",
            "location": "[resourceGroup().location]"
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[variables('bootstrapRoleAssignmentId')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
            ],
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionId')]",
                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName')), '2018-11-30').principalId]",
                "scope": "[resourceGroup().id]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

Íme a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) és [az az csoport központi telepítésének létrehozása](/cli/azure/group/deployment#az-group-deployment-create) parancsokat, hogyan indíthatja el a központi telepítést egy erőforráscsoport hatókörén.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Az alábbi példa a közreműködői szerepkör-hozzárendelés egy új felügyelt identitás egyszerű a sablon üzembe helyezése után.

![Szerepkör-hozzárendelés egy új felügyelt identitásegyszerű szolgáltatáshoz](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>További lépések

- [Rövid útmutató: Azure Resource Manager-sablon létrehozása és üzembe helyezése az Azure Portalon](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Az Azure Resource Manager-sablonok struktúrája és szintaxisa](../azure-resource-manager/templates/template-syntax.md)
- [Erőforráscsoportok és -erőforrások létrehozása előfizetési szinten](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/?term=rbac)
