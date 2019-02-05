---
title: Oktatóanyag – Egyéni szerepkör létrehozása az Azure PowerShell használatával | Microsoft Docs
description: Bevezetés az egyéni szerepkörök létrehozásába az Azure PowerShell használatával.
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
ms.date: 02/02/2019
ms.author: rolyon
ms.openlocfilehash: 7ea9ce47b82dd4ad31caf935fd10e04daa07faba
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55699978"
---
# <a name="tutorial-create-a-custom-role-using-azure-powershell"></a>Oktatóanyag: Azure PowerShell-lel egyéni szerepkör létrehozása

Ha a [beépített szerepkörök](built-in-roles.md) nem felelnek meg a cég vagy intézmény igényeinek, saját egyéni szerepköröket is létrehozhat. Ebben az oktatóanyagban egy Reader Support Tickets (Olvasó – Támogatási jegyek) nevű egyéni szerepkört fog létrehozni az Azure PowerShell-lel. Ez az egyéni szerepkör lehetővé teszi a felhasználóknak, hogy az előfizetéshez tartozó minden adatot és tartalmat megtekinthessenek, valamint támogatási jegyeket nyithassanak.

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
- Helyileg telepített [Azure PowerShellre](/powershell/azure/install-az-ps)

## <a name="sign-in-to-azure-powershell"></a>Bejelentkezés az Azure PowerShellbe

Jelentkezzen be az [Azure PowerShellbe](/powershell/azure/authenticate-azureps).

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

Legegyszerűbben úgy hozhat létre egyéni szerepkört, ha egy beépített szerepkört szerkeszt át.

1. A PowerShellben használja a [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) parancs használatával beszerezheti a Microsoft.Support erőforrás-szolgáltató műveleteinek listáját. Érdemes megismerkedni az engedélyek létrehozására szolgáló műveletekkel. Az összes műveletet tartalmazó listát az [Azure Resource Manager erőforrás-szolgáltatói műveletek](resource-provider-operations.md#microsoftsupport) témakörében is megtekintheti.

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

1. Használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) parancsot a kimenet a [olvasó](built-in-roles.md#reader) szerepkör JSON formátumban.

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

1. Az előfizetés használatával Azonosítójának lekéréséhez a [Get-AzSubscription](/powershell/module/az.profile/get-azsubscription) parancsot.

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
    
1. Az új egyéni szerepkör létrehozásához használja a [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) parancsot, majd adja meg a JSON-szerepkör-definíció fájl.

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

- Az egyéni szerepkörök listájában, használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) parancsot.

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

1. A JSON-fájl frissítéséhez használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) parancs kimenete az egyéni szerepkör JSON formátumban.

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
        
1. Az egyéni szerepkör frissítéséhez használja a [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) parancsot, majd adja meg a frissített JSON-fájlt.

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

1. Használatához a `PSRoleDefintion` objektum az egyéni szerepkör frissítése, először használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) parancs használatával beszerezheti a szerepkört.

    ```azurepowershell
    $role = Get-AzRoleDefinition "Reader Support Tickets"
    ```
    
1. Hívja meg az `Add` metódust, hogy hozzáadja a műveletet a diagnosztikai beállítások olvasásához.

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. Használja a [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) frissíteni a szerepkör.

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

1. Használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) parancs használatával beszerezheti az egyéni szerepkör azonosítója.

    ```azurepowershell
    Get-AzRoleDefinition "Reader Support Tickets"
    ```

1. Használja a [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) parancsot, majd adja meg az egyéni szerepkör törlése a szerepkör-azonosító.

    ```azurepowershell
    Remove-AzRoleDefinition -Id "22222222-2222-2222-2222-222222222222"
    ```

    ```Output
    Confirm
    Are you sure you want to remove role definition with id '22222222-2222-2222-2222-222222222222'.
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```

1. Ha rendszer megerősítést kér, írja be a következőt: **Y**.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni szerepkörök létrehozása a PowerShell használatával](custom-roles-powershell.md)
