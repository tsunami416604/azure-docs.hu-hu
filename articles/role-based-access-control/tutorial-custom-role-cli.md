---
title: 'Oktatóanyag: egyéni szerepkör létrehozása Azure-erőforrásokhoz az Azure CLI-vel'
description: Első lépésként hozzon létre egy egyéni szerepkört az Azure-erőforrásokhoz az oktatóanyagban az Azure CLI használatával.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.openlocfilehash: f1a6e72e1444a26b675379d9948217a7773c4a1c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394514"
---
# <a name="tutorial-create-a-custom-role-for-azure-resources-using-azure-cli"></a>Oktatóanyag: egyéni szerepkör létrehozása Azure-erőforrásokhoz az Azure CLI használatával

Ha az [Azure-erőforrások beépített szerepkörei](built-in-roles.md) nem felelnek meg a szervezet konkrét igényeinek, létrehozhat saját egyéni szerepköröket is. Ebben az oktatóanyagban egy Reader Support Tickets (Olvasó – Támogatási jegyek) nevű egyéni szerepkört fog létrehozni az Azure CLI-vel. Az egyéni szerepkör lehetővé teszi a felhasználó számára, hogy megtekintse az előfizetés felügyeleti síkja összes adatát, valamint a támogatási jegyek megnyitását is.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Egyéni szerepkör létrehozása
> * Egyéni szerepkörök listázása
> * Egyéni szerepkörök frissítése
> * Egyéni szerepkörök törlése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre van szükség:

- Egyéni szerepkörök létrehozására vonatkozó engedélyre, amely lehet például [Tulajdonos](built-in-roles.md#owner) vagy [Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) vagy [Azure CLI](/cli/azure/install-azure-cli)

## <a name="sign-in-to-azure-cli"></a>Bejelentkezés az Azure CLI-be

Jelentkezzen be az [Azure CLI](/cli/azure/authenticate-azure-cli)-be.

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

Legegyszerűbben úgy hozhat létre egyéni szerepkört, ha egy JSON-sablonnal kezd, amelyhez hozzáadja a módosításait, majd létrehozza az új szerepkört.

1. Tekintse át a [Microsoft.Support erőforrás-szolgáltató](resource-provider-operations.md#microsoftsupport) műveleteinek listáját. Érdemes megismerkedni az engedélyek létrehozására szolgáló műveletekkel.

    | Művelet | Leírás |
    | --- | --- |
    | Microsoft.Support/register/action | Regisztrálás a támogatási erőforrás-szolgáltatóra. |
    | Microsoft.Support/supportTickets/read | Támogatási jegy részleteinek lekérése (beleértve az állapotot, súlyosságot, kapcsolattartási adatokat és kommunikációkat) vagy az összes előfizetés támogatási jegyeinek lekérése. |
    | Microsoft.Support/supportTickets/write | Támogatási jegy létrehozása vagy frissítése. Támogatási jegyet technikai, számlázási, kvótákkal kapcsolatos vagy előfizetés-kezelési problémákkal kapcsolatban hozhat létre. A meglévő támogatási jegyeknek frissítheti a súlyosságát, kapcsolattartási adatait és kommunikációit. |
    
1. Hozzon létre egy új fájlt **ReaderSupportRole.json** néven.

1. Nyissa meg a ReaderSupportRole.json fájlt egy szövegszerkesztőben, és adja hozzá a következő JSON-t.

    További információ a különböző tulajdonságokkal kapcsolatban: [Egyéni szerepkörök az Azure-erőforrásokhoz](custom-roles.md).

    ```json
    {
      "Name": "",
      "IsCustom": true,
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId1}"
      ]
    }
    ```
    
1. Adja hozzá a következő műveleteket az `Actions` tulajdonsághoz. Ezek a műveletek lehetővé teszik a felhasználóknak, hogy az előfizetéshez tartozó minden adatot és tartalmat megtekinthessenek, valamint támogatási jegyeket hozhassanak létre.

    ```
    "*/read",
    "Microsoft.Support/*"
    ```

1. Kérje le az előfizetése azonosítóját az [az account list](/cli/azure/account#az-account-list) paranccsal.

    ```azurecli
    az account list --output table
    ```

1. Az `AssignableScopes` felületen cserélje le a `{subscriptionId1}` értéket az előfizetése azonosítójára.

    Explicit előfizetési azonosítókat kell megadnia, különben nem importálhatja a szerepkört az előfizetésébe.

1. Módosítsa a `Name` és a `Description` tulajdonságot „Reader Support Tickets” és „View everything in the subscription and also open support tickets” (Mindent megtekinthet az előfizetésben, valamint támogatási jegyeket nyithat) értékre.

    A JSON-fájlnak a következőhöz kell hasonlítania:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    
1. Az új egyéni szerepkör létrehozásához használja az [az role definition create](/cli/azure/role/definition#az-role-definition-create) parancsot, és adja meg a JSON szerepkör-definíciós fájlt.

    ```azurecli
    az role definition create --role-definition "~/CustomRoles/ReaderSupportRole.json"
    ```

    ```Output
    {
      "additionalProperties": {},
      "assignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ],
      "description": "View everything in the subscription and also open support tickets.",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
      "name": "22222222-2222-2222-2222-222222222222",
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Support/*"
          ],
          "additionalProperties": {},
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": "Reader Support Tickets",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

    Az új egyéni szerepkör mostantól elérhető, és a beépített szerepkörökhöz hasonlóan hozzárendelhető felhasználókhoz, csoportokhoz vagy szolgáltatásnevekhez.

## <a name="list-custom-roles"></a>Egyéni szerepkörök listázása

- Az összes egyéni szerepkör listázásához használja az [az role definition list](/cli/azure/role/definition#az-role-definition-list) parancsot a `--custom-role-only` paraméterrel.

    ```azurecli
    az role definition list --custom-role-only true
    ```
    
    ```Output
    [
      {
        "additionalProperties": {},
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ],
        "description": "View everything in the subscription and also open support tickets.",
        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
        "name": "22222222-2222-2222-2222-222222222222",
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Support/*",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Insights/diagnosticSettings/*/read"
            ],
            "additionalProperties": {},
            "dataActions": [],
            "notActions": [],
            "notDataActions": []
          }
        ],
        "roleName": "Reader Support Tickets",
        "roleType": "CustomRole",
        "type": "Microsoft.Authorization/roleDefinitions"
      }
    ]
    ```
    
    Az egyéni szerepköröket az Azure Portalon is megtekintheti.

    ![Képernyőkép az importált egyéni szerepkörről az Azure Portalon](./media/tutorial-custom-role-cli/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>Egyéni szerepkörök frissítése

Egyéni szerepkör frissítéséhez módosítsa a JSON-fájlt, majd frissítse az egyéni szerepkört.

1. Nyissa meg a ReaderSupportRole.json fájlt.

1. Az `Actions` elemnél adja hozzá a műveletet az erőforráscsoportok `"Microsoft.Resources/deployments/*"` üzemelő példányainak létrehozásához és kezeléséhez. Ügyeljen arra, hogy az előző művelet után mindenképpen használjon vesszőt.

    A frissített JSON-fájlnak a következőhöz kell hasonlítania:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*",
        "Microsoft.Resources/deployments/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
        
1. Az egyéni szerepkör frissítéséhez használja az [az role definition update](/cli/azure/role/definition#az-role-definition-update) parancsot, és adja meg a frissített JSON-fájlt.

    ```azurecli
    az role definition update --role-definition "~/CustomRoles/ReaderSupportRole.json"
    ```

    ```Output
    {
      "additionalProperties": {},
      "assignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ],
      "description": "View everything in the subscription and also open support tickets.",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
      "name": "22222222-2222-2222-2222-222222222222",
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Support/*",
            "Microsoft.Resources/deployments/*"
          ],
          "additionalProperties": {},
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": "Reader Support Tickets",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```
    
## <a name="delete-a-custom-role"></a>Egyéni szerepkörök törlése

- Használja az [az role definition delete](/cli/azure/role/definition#az-role-definition-delete) parancsot, és adja meg a szerepkör nevét vagy azonosítóját az egyéni szerepkör törléséhez.

    ```azurecli
    az role definition delete --name "Reader Support Tickets"
    ```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyéni szerepkörök létrehozása Azure-erőforrásokhoz az Azure CLI használatával](custom-roles-cli.md)