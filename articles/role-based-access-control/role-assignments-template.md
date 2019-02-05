---
title: Való hozzáférés RBAC- és Azure Resource Manager-sablonok kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti a felhasználók, csoportok és alkalmazások szerepköralapú hozzáférés-vezérlés (RBAC) és az Azure Resource Manager-sablonok használatával hozzáférését.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b8c6ac78447a4e4db79ed75100222eee8d528b58
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696897"
---
# <a name="manage-access-using-rbac-and-azure-resource-manager-templates"></a>Való hozzáférés RBAC- és Azure Resource Manager-sablonok kezelése

A [szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) az erőforrásokhoz való hozzáférés kezelésének a módja az Azure-ban. Mellett az Azure PowerShell vagy az Azure parancssori felület használata esetén az RBAC használatával Azure-erőforrásokhoz való hozzáférést kezelheti és [Azure Resource Manager-sablonok](../azure-resource-manager/resource-group-authoring-templates.md). Sablonok akkor lehet hasznos, ha erőforrások telepítése konzisztens és ismétlődő van szüksége. Ez a cikk bemutatja, hogyan kezelheti a hozzáférés RBAC és sablonok használatával.

## <a name="example-template-to-create-a-role-assignment"></a>Példasablon szerepkör-hozzárendelés létrehozása

Az RBAC-ben a hozzáférés biztosítása egy szerepkör-hozzárendelés létrehozásával történik. Az alábbi sablont mutat be:
- Egy felhasználó, csoport vagy alkalmazás erőforrás-csoportot a csoporthatókörben, a szerepkör hozzárendelése
- A tulajdonos, közreműködő és olvasó szerepkörök megadása paraméterként

A sablon használatához meg kell adnia az alábbi ráfordítások:
- Az erőforráscsoport nevét
- Egy felhasználó, csoport vagy a szerepkör hozzárendelése alkalmazás egyedi azonosítója
- A szerepkör hozzárendelése
- A szerepkör-hozzárendelés esetében használt egyedi azonosító

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
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    }
  },
  "variables": {
    "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
    "scope": "[resourceGroup().id]"
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2017-05-01",
      "name": "[parameters('roleNameGuid')]",
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('scope')]"
      }
    }
  ]
}
```

Az alábbiakban látható egy példa egy olvasó szerepkör-hozzárendelést egy felhasználóhoz a sablon üzembe helyezése után.

![Szerepkör-hozzárendelés egy sablon használatával](./media/role-assignments-template/role-assignment-template.png)

## <a name="deploy-template-using-azure-powershell"></a>Azure PowerShell-lel sablon üzembe helyezése

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Az Azure PowerShell-lel előző sablon üzembe helyezéséhez kövesse az alábbi lépéseket.

1. Hozzon létre egy új rbac-rg.json fájlt, és másolja ki az előző sablon.

1. Jelentkezzen be az [Azure PowerShellbe](/powershell/azure/authenticate-azureps).

1. Get-felhasználó, csoport vagy alkalmazás egyedi azonosítója. Használhatja például a [Get-AzADUser](/powershell/module/az.resources/get-azaduser) paranccsal listát készíthet az Azure AD-felhasználók.

    ```azurepowershell
    Get-AzADUser
    ```

1. Egy GUID eszköz segítségével hozza létre, amely jelzi a szerepkör-hozzárendelés egyedi azonosítója. Az azonosító formátuma: `11111111-1111-1111-1111-111111111111`

1. Hozzon létre egy példa erőforráscsoportot.

    ```azurepowershell
    New-AzResourceGroup -Name ExampleGroup -Location "Central US"
    ```

1. Használja a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) parancsot az üzembe helyezés elindításához.

    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json
    ```

    A rendszer felkéri a szükséges paramétereket adja meg. Az alábbiakban látható egy példa a kimenetre.

    ```Output
    PS /home/user> New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json
    
    cmdlet New-AzResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    principalId: 22222222-2222-2222-2222-222222222222
    builtInRoleType: Reader
    roleNameGuid: 11111111-1111-1111-1111-111111111111
    
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

## <a name="deploy-template-using-the-azure-cli"></a>Helyezze üzembe a sablont az Azure CLI használatával

Az Azure CLI használatával az előző sablon üzembe helyezéséhez kövesse az alábbi lépéseket.

1. Hozzon létre egy új rbac-rg.json fájlt, és másolja ki az előző sablon.

1. Jelentkezzen be a [az Azure CLI](/cli/azure/authenticate-azure-cli).

1. Get-felhasználó, csoport vagy alkalmazás egyedi azonosítója. Használhatja például a [az ad felhasználó-lista](/cli/azure/ad/user#az-ad-user-list) paranccsal listát készíthet az Azure AD-felhasználók.

    ```azurecli
    az ad user list
    ```

1. Egy GUID eszköz segítségével hozza létre, amely jelzi a szerepkör-hozzárendelés egyedi azonosítója. Az azonosító formátuma: `11111111-1111-1111-1111-111111111111`

1. Hozzon létre egy példa erőforráscsoportot.

    ```azurecli
    az group create --name ExampleGroup --location "Central US"
    ```

1. Használja a [az csoport központi telepítésének létrehozása](/cli/azure/group/deployment#az-group-deployment-create) parancsot az üzembe helyezés elindításához.

    ```azurecli
    az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json
    ```

    A rendszer felkéri a szükséges paramétereket adja meg. Az alábbiakban látható egy példa a kimenetre.

    ```Output
    C:\Azure\Templates>az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json
    Please provide string value for 'principalId' (? for help): 22222222-2222-2222-2222-222222222222
    Please provide string value for 'builtInRoleType' (? for help):
     [1] Owner
     [2] Contributor
     [3] Reader
    Please enter a choice [1]: 3
    Please provide string value for 'roleNameGuid' (? for help): 11111111-1111-1111-1111-111111111111
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
    
## <a name="next-steps"></a>További lépések

- [Létre és helyezhet üzembe az első Azure Resource Manager-sablon](../azure-resource-manager/resource-manager-create-first-template.md)
- [Megismerheti a szerkezetének és szintaxisának az Azure Resource Manager-sablonok](../azure-resource-manager/resource-group-authoring-templates.md)
- [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/?term=rbac)
