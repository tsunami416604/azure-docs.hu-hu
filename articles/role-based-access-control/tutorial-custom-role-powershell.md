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
ms.date: 06/12/2018
ms.author: rolyon
ms.openlocfilehash: 1e3043a6053625f8363e9035e59b53275714a827
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43300609"
---
# <a name="tutorial-create-a-custom-role-using-azure-powershell"></a>Oktatóanyag: Egyéni szerepkör létrehozása az Azure PowerShell használatával

Ha a [beépített szerepkörök](built-in-roles.md) nem felelnek meg a cég vagy intézmény igényeinek, saját egyéni szerepköröket is létrehozhat. Ebben az oktatóanyagban egy Reader Support Tickets (Olvasó – Támogatási jegyek) nevű egyéni szerepkört fog létrehozni az Azure PowerShell-lel. Ez az egyéni szerepkör lehetővé teszi a felhasználóknak, hogy az előfizetéshez tartozó minden adatot és tartalmat megtekinthessenek, valamint támogatási jegyeket nyithassanak.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egyéni szerepkör létrehozása
> * Egyéni szerepkörök listázása
> * Egyéni szerepkörök frissítése
> * Egyéni szerepkörök törlése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre van szükség:

- Egyéni szerepkörök létrehozására vonatkozó engedélyre, amely lehet például [Tulajdonos](built-in-roles.md#owner) vagy [Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator)
- Helyileg telepített [Azure PowerShellre](/powershell/azure/install-azurerm-ps)

## <a name="sign-in-to-azure-powershell"></a>Bejelentkezés az Azure PowerShellbe

Jelentkezzen be az [Azure PowerShellbe](/powershell/azure/authenticate-azureps).

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

Legegyszerűbben úgy hozhat létre egyéni szerepkört, ha egy beépített szerepkört szerkeszt át.

1. A PowerShellben a [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) paranccsal kérheti le a Microsoft.Support erőforrás-szolgáltató műveleteinek listáját. Érdemes megismerkedni az engedélyek létrehozására szolgáló műveletekkel. Az összes műveletet tartalmazó listát az [Azure Resource Manager erőforrás-szolgáltatói műveletek](resource-provider-operations.md#microsoftsupport) témakörében is megtekintheti.

    ```azurepowershell
    Get-AzureRMProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize
    ```
    
    ```Output
    Operation                              Description
    ---------                              -----------
    Microsoft.Support/register/action      Registers to Support Resource Provider
    Microsoft.Support/supportTickets/read  Gets Support Ticket details (including status, severity, contact ...
    Microsoft.Support/supportTickets/write Creates or Updates a Support Ticket. You can create a Support Tic...
    ```

1. A [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) paranccsal hozza létre a [Reader](built-in-roles.md#reader) (Olvasó) szerepkört JSON formátumban.

    ```azurepowershell
    Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json
    ```

1. Nyissa meg a **ReaderSupportRole.json** fájlt egy tetszőleges szövegszerkesztőben.

    Az alábbiakban a JSON kimenete látható. További információkat a különböző tulajdonságokról az [egyéni szerepkörökkel](custom-roles.md) foglalkozó cikkben talál.

    ```json
    {
        "Name":  "Reader",
        "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "IsCustom":  false,
        "Description":  "Lets you view everything, but not make any changes.",
        "Actions":  [
                        "*/read"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/"
                             ]
    }
    ```
    
1. A JSON-fájl szerkesztésével adja hozzá a `"Microsoft.Support/*"` műveletet az `Actions` tulajdonsághoz. Ügyeljen arra, hogy az olvasási művelet után mindenképpen használjon vesszőt. Ez a művelet lehetővé teszi a felhasználónak a támogatási jegyek létrehozását.

1. Kérje le az előfizetése azonosítóját a [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) paranccsal.

    ```azurepowershell
    Get-AzureRmSubscription
    ```

1. Az `AssignableScopes` elemnél adja meg az előfizetés azonosítóját a következő formátumban: `"/subscriptions/00000000-0000-0000-0000-000000000000"`

    Explicit előfizetési azonosítókat kell megadnia, különben nem importálhatja a szerepkört az előfizetésébe.

1. Törölje az `Id` tulajdonságsort, és módosítsa az `IsCustom` tulajdonságot `true` értékre.

1. Módosítsa a `Name` és a `Description` tulajdonságot „Reader Support Tickets” és „View everything in the subscription and also open support tickets” (Mindent megtekinthet az előfizetésben, valamint támogatási jegyeket nyithat) értékre.

    A JSON-fájlnak a következőhöz kell hasonlítania:

    ```json
    {
        "Name":  "Reader Support Tickets",
        "IsCustom":  true,
        "Description":  "View everything in the subscription and also open support tickets.",
        "Actions":  [
                        "*/read",
                        "Microsoft.Support/*"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/subscriptions/00000000-0000-0000-0000-000000000000"
                             ]
    }
    ```
    
1. Az új egyéni szerepkör létrehozásához használja a [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) parancsot, és adja meg a JSON szerepkör-definíciós fájlt.

    ```azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole.json"
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

- A [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) paranccsal minden egyéni szerepkört listázhat.

    ```azurepowershell
    Get-AzureRmRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
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

1. A JSON-fájl frissítéséhez a [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) paranccsal adja ki az egyéni szerepkört JSON formátumban.

    ```azurepowershell
    Get-AzureRmRoleDefinition -Name "Reader Support Tickets" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole2.json
    ```

1. Nyissa meg a fájlt egy tetszőleges szövegszerkesztőben.

1. Az `Actions` elemnél adja hozzá a műveletet az erőforráscsoportok `"Microsoft.Resources/deployments/*"` üzemelő példányainak létrehozásához és kezeléséhez.

    A frissített JSON-fájlnak a következőhöz kell hasonlítania:

    ```json
    {
        "Name":  "Reader Support Tickets",
        "Id":  "22222222-2222-2222-2222-222222222222",
        "IsCustom":  true,
        "Description":  "View everything in the subscription and also open support tickets.",
        "Actions":  [
                        "*/read",
                        "Microsoft.Support/*",
                        "Microsoft.Resources/deployments/*"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/subscriptions/00000000-0000-0000-0000-000000000000"
                             ]
    }
    ```
        
1. Az egyéni szerepkör frissítéséhez használja a [Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) parancsot, és adja meg a frissített JSON-fájlt.

    ```azurepowershell
    Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole2.json"
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

1. Ahhoz, hogy a `PSRoleDefintion` objektum használatával frissítse az egyéni szerepkört, először használja a [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) parancsot a szerepkör lekéréséhez.

    ```azurepowershell
    $role = Get-AzureRmRoleDefinition "Reader Support Tickets"
    ```
    
1. Hívja meg az `Add` metódust, hogy hozzáadja a műveletet a diagnosztikai beállítások olvasásához.

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. A [Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) paranccsal frissítse a szerepkört.

    ```azurepowershell
    Set-AzureRmRoleDefinition -Role $role
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

1. A [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) paranccsal kérje le az egyéni szerepkör azonosítóját.

    ```azurepowershell
    Get-AzureRmRoleDefinition "Reader Support Tickets"
    ```

1. Használja a [Remove-AzureRmRoleDefinition](/powershell/module/azurerm.resources/remove-azurermroledefinition) parancsot, és adja meg a szerepkör azonosítóját az egyéni szerepkör törléséhez.

    ```azurepowershell
    Remove-AzureRmRoleDefinition -Id "22222222-2222-2222-2222-222222222222"
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
