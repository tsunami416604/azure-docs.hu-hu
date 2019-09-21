---
title: Azure-erőforrásokhoz való hozzáférés kezelése RBAC és Azure Resource Manager sablonok használatával | Microsoft Docs
description: Ismerje meg, hogyan kezelheti az Azure-erőforrásokhoz való hozzáférést a felhasználók, csoportok és alkalmazások számára szerepköralapú hozzáférés-vezérlés (RBAC) és Azure Resource Manager-sablonok használatával.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/06/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: d9ec2fc76fb1cc33898c331a79167f9291fc8c63
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172970"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>Azure-erőforrásokhoz való hozzáférés kezelése RBAC és Azure Resource Manager sablonok használatával

A [szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) az Azure-erőforrásokhoz való hozzáférés kezelésének módja. A Azure PowerShell vagy az Azure CLI használata mellett az Azure-erőforrásokhoz való hozzáférést [Azure Resource Manager sablonok](../azure-resource-manager/resource-group-authoring-templates.md)használatával is kezelheti. A sablonok akkor lehetnek hasznosak, ha az erőforrásokat következetesen és ismételten kell telepíteni. Ez a cikk bemutatja, hogyan kezelheti a hozzáférést a RBAC és a sablonok használatával.

## <a name="create-a-role-assignment-at-a-resource-group-scope-without-parameters"></a>Szerepkör-hozzárendelés létrehozása erőforráscsoport-hatókörben (paraméterek nélkül)

Az RBAC-ben a hozzáférés biztosítása egy szerepkör-hozzárendelés létrehozásával történik. A következő sablon alapszintű módszert mutat a szerepkör-hozzárendelés létrehozásához. Néhány érték a sablonban van megadva. A következő sablon a következőket mutatja be:

-  Az [olvasó](built-in-roles.md#reader) szerepkör társítása egy felhasználóhoz, csoporthoz vagy alkalmazáshoz erőforráscsoport-hatókörben

A sablon használatához a következőket kell tennie:

- Új JSON-fájl létrehozása és a sablon másolása
- A `<your-principal-id>` helyére írja be annak a felhasználónak, csoportnak vagy alkalmazásnak az egyedi azonosítóját, amelyhez hozzá szeretné rendelni a szerepkört. Az azonosító formátuma:`11111111-1111-1111-1111-111111111111`

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

Az alábbi példa a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) és az [az Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) parancsokat mutatja be a telepítés elindításához egy ExampleGroup nevű erőforráscsoporthoz.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

Az alábbi példa az olvasói szerepkör hozzárendelését mutatja be egy erőforráscsoport felhasználói számára a sablon telepítése után.

![Szerepkör-hozzárendelés erőforrás-csoport hatókörében](./media/role-assignments-template/role-assignment-template.png)

## <a name="create-a-role-assignment-at-a-resource-group-or-subscription-scope"></a>Szerepkör-hozzárendelés létrehozása erőforráscsoport vagy előfizetés hatókörében

Az előző sablon nem túl rugalmas. A következő sablon paramétereket használ, és különböző hatókörökben használható. A következő sablon a következőket mutatja be:

- Szerepkör társítása felhasználóhoz, csoporthoz vagy alkalmazáshoz erőforráscsoport vagy előfizetés hatókörében
- Tulajdonos, közreműködő és olvasó szerepkörök meghatározása paraméterként

A sablon használatához a következő bemeneteket kell megadnia:

- Annak a felhasználónak, csoportnak vagy alkalmazásnak az egyedi azonosítója, amelyhez hozzá szeretné rendelni a szerepkört
- A hozzárendelni kívánt szerepkör
- A szerepkör-hozzárendeléshez használt egyedi azonosító, vagy használhatja az alapértelmezett azonosítót is.

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

Ahhoz, hogy egy felhasználó egyedi azonosítója legyen hozzárendelve a szerepkörhöz, a [Get-AzADUser](/powershell/module/az.resources/get-azaduser) vagy [az ad User show](/cli/azure/ad/user#az-ad-user-show) parancsot használhatja.

```azurepowershell
$userid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
userid=$(az ad user show --upn-or-object-id "{email}" --query objectId --output tsv)
```

A szerepkör-hozzárendelés hatóköre a központi telepítés szintjétől függ. Az alábbi példa a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) és az [az Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) parancsokat mutatja be a telepítés elindításához egy erőforráscsoport-hatókörben.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Reader
```

Az alábbi példa a [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) és az [Deployment Create](/cli/azure/deployment#az-deployment-create) parancsait mutatja be a központi telepítés előfizetési hatókörben való elindításához és a hely megadásához.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Reader
```

## <a name="create-a-role-assignment-at-a-resource-scope"></a>Szerepkör-hozzárendelés létrehozása erőforrás-hatókörben

Ha egy erőforrás szintjén kell létrehoznia egy szerepkör-hozzárendelést, a szerepkör-hozzárendelés formátuma eltérő. Adja meg annak az erőforrás-szolgáltatónak a névterét és erőforrás-típusát, amelyhez hozzá szeretné rendelni a szerepkört. A szerepkör-hozzárendelés neve tartalmazza az erőforrás nevét is.

A szerepkör-hozzárendelés típusát és nevét a következő formátumban kell megadni:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

A következő sablon a következőket mutatja be:

- Új tárfiók létrehozása
- Szerepkör társítása felhasználóhoz, csoporthoz vagy alkalmazáshoz a Storage-fiók hatókörében
- Tulajdonos, közreműködő és olvasó szerepkörök meghatározása paraméterként

A sablon használatához a következő bemeneteket kell megadnia:

- Annak a felhasználónak, csoportnak vagy alkalmazásnak az egyedi azonosítója, amelyhez hozzá szeretné rendelni a szerepkört
- A hozzárendelni kívánt szerepkör
- A szerepkör-hozzárendeléshez használt egyedi azonosító, vagy használhatja az alapértelmezett azonosítót is.


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
            "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleNameGuid'))]",
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

Az előző sablon üzembe helyezéséhez használja az erőforráscsoport-parancsokat. Az alábbi példa a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) és az [az Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) parancsokat mutatja be a telepítés elindításához egy erőforrás-hatókörben.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Contributor
```

A következő példa a közreműködői szerepkör hozzárendelését mutatja be egy felhasználónak egy Storage-fiókhoz a sablon telepítése után.

![Szerepkör-hozzárendelés erőforrás-hatókörben](./media/role-assignments-template/role-assignment-template-resource.png)

## <a name="create-a-role-assignment-for-a-new-service-principal"></a>Szerepkör-hozzárendelés létrehozása egy új egyszerű szolgáltatáshoz

Ha létrehoz egy új szolgáltatásnevet, és azonnal megpróbál hozzárendelni egy szerepkört az egyszerű szolgáltatáshoz, a szerepkör-hozzárendelés bizonyos esetekben sikertelen lehet. Ha például létrehoz egy új felügyelt identitást, majd megpróbál hozzárendelni egy szerepkört az adott szolgáltatáshoz ugyanahhoz a Azure Resource Manager-sablonhoz, előfordulhat, hogy a szerepkör-hozzárendelés sikertelen lesz. A hiba oka valószínűleg a replikálás késése. Az egyszerű szolgáltatás egy régióban jön létre; a szerepkör-hozzárendelés azonban egy másik régióban is előfordulhat, amely még nem replikálta a szolgáltatásnevet. Ennek a forgatókönyvnek a megoldásához a `principalType` tulajdonságot a szerepkör- `ServicePrincipal` hozzárendelés létrehozásakor kell beállítania.

A következő sablon a következőket mutatja be:

- Új felügyelt identitási szolgáltatásnév létrehozása
- A`principalType`
- A közreműködői szerepkör társítása az adott egyszerű szolgáltatáshoz erőforráscsoport-hatókörben

A sablon használatához a következő bemeneteket kell megadnia:

- A felügyelt identitás alapneve, vagy használhatja az alapértelmezett karakterláncot is.

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

Az alábbi példa a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) és az [az Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) parancsokat mutatja be a telepítés elindításához egy erőforráscsoport-hatókörben.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Az alábbi példa a közreműködői szerepkör hozzárendelését mutatja be egy új felügyelt identitási szolgáltatásnév számára a sablon telepítése után.

![Szerepkör-hozzárendelés egy új felügyelt identitás egyszerű szolgáltatásnév számára](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>További lépések

- [Rövid útmutató: Azure Resource Manager-sablonok létrehozása és üzembe helyezése a Azure Portal használatával](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [Az Azure Resource Manager-sablonok struktúrája és szintaxisa](../azure-resource-manager/resource-group-authoring-templates.md)
- [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/?term=rbac)
