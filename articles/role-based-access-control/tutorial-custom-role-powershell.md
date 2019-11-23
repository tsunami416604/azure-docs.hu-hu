---
title: Tutorial - Create a custom role for Azure resources using Azure PowerShell
description: Get started creating a custom role for Azure resources using Azure PowerShell in this tutorial.
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
ms.openlocfilehash: 176e465163d92156308eda64c4187467cc10ee15
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74419745"
---
# <a name="tutorial-create-a-custom-role-for-azure-resources-using-azure-powershell"></a>Tutorial: Create a custom role for Azure resources using Azure PowerShell

If the [built-in roles for Azure resources](built-in-roles.md) don't meet the specific needs of your organization, you can create your own custom roles. Ebben az oktatóanyagban egy Reader Support Tickets (Olvasó – Támogatási jegyek) nevű egyéni szerepkört fog létrehozni az Azure PowerShell-lel. The custom role allows the user to view everything in the management plane of a subscription and also open support tickets.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egyéni szerepkör létrehozása
> * Egyéni szerepkörök listázása
> * Egyéni szerepkörök frissítése
> * Egyéni szerepkörök törlése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre van szükség:

- Egyéni szerepkörök létrehozására vonatkozó engedélyre, amely lehet például [Tulajdonos](built-in-roles.md#owner) vagy [Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) or [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="sign-in-to-azure-powershell"></a>Bejelentkezés az Azure PowerShellbe

Jelentkezzen be az [Azure PowerShellbe](/powershell/azure/authenticate-azureps).

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

Legegyszerűbben úgy hozhat létre egyéni szerepkört, ha egy beépített szerepkört szerkeszt át.

1. In PowerShell, use the [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) command to get the list of operations for the Microsoft.Support resource provider. Érdemes megismerkedni az engedélyek létrehozására szolgáló műveletekkel. Az összes műveletet tartalmazó listát az [Azure Resource Manager erőforrás-szolgáltatói műveletek](resource-provider-operations.md#microsoftsupport) témakörében is megtekintheti.

    ```azurepowershell
    Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize
    ```
    
    ```Output
    Operation                              Description
    ---------                              -----------
    Microsoft.Support/register/action      Registers to Support Resource Provider
    Microsoft.Support/supportTickets/read  Gets Support Ticket details (including status, severity, contact ...
    Microsoft.Support/supportTickets/write Creates or Updates a Support Ticket. You can create a Support Tic...
    ```

1. Use the [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) command to output the [Reader](built-in-roles.md#reader) role in JSON format.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json
    ```

1. Nyissa meg a **ReaderSupportRole.json** fájlt egy tetszőleges szövegszerkesztőben.

    Az alábbiakban a JSON kimenete látható. További információkat a különböző tulajdonságokról az [egyéni szerepkörökkel](custom-roles.md) foglalkozó cikkben talál.

    ```json
    {
      "Name": "Reader",
      "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
      "IsCustom": false,
      "Description": "Lets you view everything, but not make any changes.",
      "Actions": [
        "*/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/"
      ]
    }
    ```
    
1. A JSON-fájl szerkesztésével adja hozzá a `"Microsoft.Support/*"` műveletet az `Actions` tulajdonsághoz. Ügyeljen arra, hogy az olvasási művelet után mindenképpen használjon vesszőt. Ez a művelet lehetővé teszi a felhasználónak a támogatási jegyek létrehozását.

1. Get the ID of your subscription using the [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) command.

    ```azurepowershell
    Get-AzSubscription
    ```

1. Az `AssignableScopes` elemnél adja meg az előfizetés azonosítóját a következő formátumban: `"/subscriptions/00000000-0000-0000-0000-000000000000"`

    Explicit előfizetési azonosítókat kell megadnia, különben nem importálhatja a szerepkört az előfizetésébe.

1. Törölje az `Id` tulajdonságsort, és módosítsa az `IsCustom` tulajdonságot `true` értékre.

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
    
1. To create the new custom role, use the [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) command and specify the JSON role definition file.

    ```azurepowershell
    New-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
        
    Az új egyéni szerepkör mostantól elérhető az Azure Portalon, és a beépített szerepkörökhöz hasonlóan hozzárendelhető felhasználókhoz, csoportokhoz vagy szolgáltatásnevekhez.

## <a name="list-custom-roles"></a>Egyéni szerepkörök listázása

- To list all your custom roles, use the [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) command.

    ```azurepowershell
    Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
    ```

    ```Output
    Name                   IsCustom
    ----                   --------
    Reader Support Tickets     True
    ```
    
    Az egyéni szerepköröket az Azure Portalon is megtekintheti.

    ![Képernyőkép az importált egyéni szerepkörről az Azure Portalon](./media/tutorial-custom-role-powershell/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>Egyéni szerepkörök frissítése

Egyéni szerepkör frissítéséhez módosítsa a JSON-fájlt, vagy használja a `PSRoleDefinition` objektumot.

1. To update the JSON file, use the [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) command to output the custom role in JSON format.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader Support Tickets" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole2.json
    ```

1. Nyissa meg a fájlt egy tetszőleges szövegszerkesztőben.

1. Az `Actions` elemnél adja hozzá a műveletet az erőforráscsoportok `"Microsoft.Resources/deployments/*"` üzemelő példányainak létrehozásához és kezeléséhez.

    A frissített JSON-fájlnak a következőhöz kell hasonlítania:

    ```json
    {
      "Name": "Reader Support Tickets",
      "Id": "22222222-2222-2222-2222-222222222222",
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
        
1. To update the custom role, use the [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) command and specify the updated JSON file.

    ```azurepowershell
    Set-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole2.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```

1. To use the `PSRoleDefintion` object to update your custom role, first use the [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) command to get the role.

    ```azurepowershell
    $role = Get-AzRoleDefinition "Reader Support Tickets"
    ```
    
1. Hívja meg az `Add` metódust, hogy hozzáadja a műveletet a diagnosztikai beállítások olvasásához.

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. Use the [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) to update the role.

    ```azurepowershell
    Set-AzRoleDefinition -Role $role
    ```
    
    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*,
                       Microsoft.Insights/diagnosticSettings/*/read}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
    
## <a name="delete-a-custom-role"></a>Egyéni szerepkörök törlése

1. Use the [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) command to get the ID of the custom role.

    ```azurepowershell
    Get-AzRoleDefinition "Reader Support Tickets"
    ```

1. Use the [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) command and specify the role ID to delete the custom role.

    ```azurepowershell
    Remove-AzRoleDefinition -Id "22222222-2222-2222-2222-222222222222"
    ```

    ```Output
    Confirm
    Are you sure you want to remove role definition with id '22222222-2222-2222-2222-222222222222'.
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```

1. Ha rendszer megerősítést kér, írja be a következőt: **Y**.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyéni szerepkörök létrehozása Azure-erőforrásokhoz az Azure PowerShell használatával](custom-roles-powershell.md)
