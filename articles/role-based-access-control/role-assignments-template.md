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
ms.date: 07/19/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e6511ff84c251577a5ff483f892387ab7d3d4d41
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360457"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>Azure-erőforrásokhoz való hozzáférés kezelése RBAC és Azure Resource Manager sablonok használatával

A [szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) az Azure-erőforrásokhoz való hozzáférés kezelésének módja. A Azure PowerShell vagy az Azure CLI használata mellett az Azure-erőforrásokhoz való hozzáférést RBAC és [Azure Resource Manager-sablonok](../azure-resource-manager/resource-group-authoring-templates.md)használatával is kezelheti. A sablonok akkor lehetnek hasznosak, ha az erőforrásokat következetesen és ismételten kell telepíteni. Ez a cikk bemutatja, hogyan kezelheti a hozzáférést a RBAC és a sablonok használatával.

## <a name="assign-role-to-resource-group-or-subscription"></a>Szerepkör társítása erőforráscsoporthoz vagy előfizetéshez

Az RBAC-ben a hozzáférés biztosítása egy szerepkör-hozzárendelés létrehozásával történik. A következő sablon a következőket mutatja be:
- Szerepkör társítása felhasználóhoz, csoporthoz vagy alkalmazáshoz az erőforráscsoport vagy az előfizetés hatókörében
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

Az alábbi példa egy olvasói szerepkör hozzárendelését mutatja be egy erőforráscsoport felhasználói számára a sablon telepítése után.

![Szerepkör-hozzárendelés sablon használatával](./media/role-assignments-template/role-assignment-template.png)

A szerepkör-hozzárendelés hatóköre a központi telepítés szintjétől függ. Ebben a cikkben az erőforráscsoport és az előfizetési szint telepítési parancsai is láthatók.

## <a name="assign-role-to-resource"></a>Szerepkör társítása az erőforráshoz

Ha egy erőforrás szintjén kell létrehoznia egy szerepkör-hozzárendelést, a szerepkör-hozzárendelés formátuma eltérő. Adja meg annak az erőforrás-szolgáltatónak a névterét és erőforrás-típusát, amelyhez hozzá szeretné rendelni a szerepkört. A szerepkör-hozzárendelés neve tartalmazza az erőforrás nevét is.

A szerepkör-hozzárendelés típusát és nevét a következő formátumban kell megadni:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

A következő sablon egy Storage-fiókot telepít, és hozzárendel egy szerepkört. Az erőforráscsoport parancsaival telepítheti azt.

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

A következő példa egy közreműködői szerepkör-hozzárendelést mutat be egy felhasználónak egy Storage-fiókhoz a sablon telepítése után.

![Szerepkör-hozzárendelés sablon használatával](./media/role-assignments-template/role-assignment-template-resource.png)

## <a name="deploy-template-using-azure-powershell"></a>Sablon üzembe helyezése Azure PowerShell használatával

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Kövesse az alábbi lépéseket az előző sablon üzembe helyezéséhez egy erőforráscsoporthoz vagy egy előfizetéshez Azure PowerShell használatával.

1. Hozzon létre egy RBAC-RG. JSON nevű új fájlt, és másolja az előző sablont.

1. Jelentkezzen be az [Azure PowerShellbe](/powershell/azure/authenticate-azureps).

1. Egy felhasználó, csoport vagy alkalmazás egyedi azonosítójának beolvasása. Használhatja például a [Get-AzADUser](/powershell/module/az.resources/get-azaduser) parancsot az Azure ad-felhasználók listázásához.

    ```azurepowershell
    $userid = (Get-AzADUser -DisplayName "{name}").id
    ```

1. A sablon létrehoz egy alapértelmezett értéket a GUID azonosítóhoz, amely a szerepkör-hozzárendelés azonosítására szolgál. Ha egy adott GUID azonosítót kell megadnia, adja át ezt az értéket a roleNameGuid paraméternek. Az azonosító formátuma:`11111111-1111-1111-1111-111111111111`

Ha egy erőforrás vagy erőforráscsoport szintjén szeretné hozzárendelni a szerepkört, kövesse az alábbi lépéseket:

1. Hozzon létre egy példa erőforráscsoportot.

    ```azurepowershell
    New-AzResourceGroup -Name ExampleGroup -Location "Central US"
    ```

1. A [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) parancs használatával indítsa el a központi telepítést.

    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
    ```

    Az alábbi ábrán egy példa látható a kimenetre.

    ```Output
    PS /home/user> New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
    
    DeploymentName          : rbac-rg
    ResourceGroupName       : ExampleGroup
    ProvisioningState       : Succeeded
    Timestamp               : 7/17/2018 7:46:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              principalId      String                     22222222-2222-2222-2222-222222222222
                              builtInRoleType  String                     Reader
                              roleNameGuid     String                     11111111-1111-1111-1111-111111111111
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

Ha a szerepkört egy előfizetés szintjén szeretné hozzárendelni, használja a [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) parancsot, és adjon meg egy helyet a központi telepítéshez.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
```

Hasonló kimenettel rendelkezik az erőforráscsoportok üzembe helyezési parancsához.

## <a name="deploy-template-using-the-azure-cli"></a>Sablon üzembe helyezése az Azure CLI használatával

Az előző sablon az Azure CLI-vel való üzembe helyezéséhez válasszon egy erőforráscsoportot vagy egy előfizetést, majd kövesse az alábbi lépéseket.

1. Hozzon létre egy RBAC-RG. JSON nevű új fájlt, és másolja az előző sablont.

1. Jelentkezzen be az [Azure CLI](/cli/azure/authenticate-azure-cli)-be.

1. Egy felhasználó, csoport vagy alkalmazás egyedi azonosítójának beolvasása. Az az [ad User show](/cli/azure/ad/user#az-ad-user-show) parancs használatával például megjelenítheti az Azure ad-felhasználót.

    ```azurecli
    userid=$(az ad user show --upn-or-object-id "{email}" --query objectId --output tsv)
    ```

1. A sablon létrehoz egy alapértelmezett értéket a GUID azonosítóhoz, amely a szerepkör-hozzárendelés azonosítására szolgál. Ha egy adott GUID azonosítót kell megadnia, adja át ezt az értéket a roleNameGuid paraméternek. Az azonosító formátuma:`11111111-1111-1111-1111-111111111111`

Ha egy erőforrás vagy erőforráscsoport szintjén szeretné hozzárendelni a szerepkört, kövesse az alábbi lépéseket:

1. Hozzon létre egy példa erőforráscsoportot.

    ```azurecli
    az group create --name ExampleGroup --location "Central US"
    ```

1. A telepítés elindításához használja az az [Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) parancsot.

    ```azurecli
    az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
    ```

    Az alábbi ábrán egy példa látható a kimenetre.

    ```Output
    C:\Azure\Templates>az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
    
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/rbac-rg",
      "name": "rbac-rg",
      "properties": {
        "additionalProperties": {
          "duration": "PT9.5323924S",
          "outputResources": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
              "resourceGroup": "ExampleGroup"
            }
          ],
          "templateHash": "0000000000000000000"
        },
        "correlationId": "33333333-3333-3333-3333-333333333333",
        "debugSetting": null,
        "dependencies": [],
        "mode": "Incremental",
        "outputs": null,
        "parameters": {
          "builtInRoleType": {
            "type": "String",
            "value": "Reader"
          },
          "principalId": {
            "type": "String",
            "value": "22222222-2222-2222-2222-222222222222"
          },
          "roleNameGuid": {
            "type": "String",
            "value": "11111111-1111-1111-1111-111111111111"
          }
        },
        "parametersLink": null,
        "providers": [
          {
            "id": null,
            "namespace": "Microsoft.Authorization",
            "registrationState": null,
            "resourceTypes": [
              {
                "aliases": null,
                "apiVersions": null,
                "locations": [
                  null
                ],
                "properties": null,
                "resourceType": "roleAssignments"
              }
            ]
          }
        ],
        "provisioningState": "Succeeded",
        "template": null,
        "templateLink": null,
        "timestamp": "2018-07-17T19:00:31.830904+00:00"
      },
      "resourceGroup": "ExampleGroup"
    }
    ```

Ha egy előfizetés szintjén szeretné hozzárendelni a szerepkört, használja az az [Deployment Create](/cli/azure/deployment#az-deployment-create) parancsot, és adjon meg egy helyet a központi telepítéshez.

```azurecli
az deployment create --location centralus --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
```

Hasonló kimenettel rendelkezik az erőforráscsoportok üzembe helyezési parancsához.

## <a name="next-steps"></a>További lépések

- [Rövid útmutató: Azure Resource Manager-sablonok létrehozása és üzembe helyezése a Azure Portal használatával](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [Az Azure Resource Manager-sablonok struktúrája és szintaxisa](../azure-resource-manager/resource-group-authoring-templates.md)
- [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/?term=rbac)
