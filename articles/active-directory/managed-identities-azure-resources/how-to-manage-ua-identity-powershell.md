---
title: A felhasználó által kijelölt felügyelt identitás létrehozása, listázása & törlése az Azure PowerShell használatával – Azure AD
description: Lépésenkénti útmutató a felhasználó által hozzárendelt felügyelt identitás okán az Azure PowerShell használatával.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: c512a867685b4480c7b31ac582e2cee069ee2447
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547396"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása, listázása vagy törlése az Azure PowerShell használatával

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Az Azure-erőforrások felügyelt identitásai felügyelt identitást biztosítaz Azure-szolgáltatások felügyelt identitással az Azure Active Directoryban. Ezzel az identitással hitelesítheti az Azure AD-hitelesítést támogató szolgáltatások, anélkül, hogy hitelesítő adatokat a kódban. 

Ebben a cikkben megtudhatja, hogyan hozhat létre, listázhat és törölhet egy felhasználó által hozzárendelt felügyelt identitást az Azure PowerShell használatával.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [áttekintő szakaszt.](overview.md) **Mindenképpen tekintse át a [rendszerhez rendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget.](overview.md#how-does-the-managed-identities-for-azure-resources-work)**
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Telepítse [az Azure PowerShell legújabb verzióját,](/powershell/azure/install-az-ps) ha még nem tette meg.
- Ha helyileg futtatja a PowerShellt, akkor emellett a következőket kell tennie: 
    - Futtassa a `Connect-AzAccount` parancsot, hogy kapcsolatot hozzon létre az Azure-ral.
    - Telepítse a [PowerShellGet legújabb verzióját](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Futtassa a következőt: `Install-Module -Name PowerShellGet -AllowPrerelease` a `PowerShellGet` modul kiadás előtti verziójának eléréséhez (előfordulhat, hogy a parancs futtatása után ki kell lépnie (`Exit`) az aktuális PowerShell-munkamenetből, hogy telepíteni tudja az `Az.ManagedServiceIdentity` modult).
    - Futtassa `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` a `Az.ManagedServiceIdentity` modul előzetes verziójának telepítéséhez a felhasználó által hozzárendelt felügyelt identitásműveletek végrehajtásához ebben a cikkben.

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Felhasználó által hozzárendelt felügyelt identitás létrehozásához a fióknak szüksége van a [felügyelt identitás közreműködőszerepkör-hozzárendelésre.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Felhasználó által hozzárendelt felügyelt identitás létrehozásához `New-AzUserAssignedIdentity` használja a parancsot. A `ResourceGroupName` paraméter azt az erőforráscsoportot határozza meg, ahol a felhasználó `-Name` által hozzárendelt felügyelt identitást létre kell hozni, és a paraméter megadja a nevét. Cserélje `<RESOURCE GROUP>` le `<USER ASSIGNED IDENTITY NAME>` a és a paraméterértékeket a saját értékeire:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Felhasználó által hozzárendelt felügyelt identitások listázása

A felhasználó által hozzárendelt felügyelt identitás okának listázásához/olvasásához a fióknak szüksége van a [Felügyelt identitásoperátorra](/azure/role-based-access-control/built-in-roles#managed-identity-operator) vagy a [Felügyelt identitásközreműködő](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelésre.

A felhasználó által hozzárendelt felügyelt identitások listázásához használja a [Get-AzUserAssigned] parancsot.  A `-ResourceGroupName` paraméter azt az erőforráscsoportot adja meg, amelyben a felhasználó által hozzárendelt felügyelt identitás létrejött. Cserélje `<RESOURCE GROUP>` ki a saját értékére:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
A válaszban a felhasználó által hozzárendelt felügyelt identitások értéke vissza a `"Microsoft.ManagedIdentity/userAssignedIdentities"` kulcs, `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás törlése

A felhasználó által hozzárendelt felügyelt identitás törléséhez a fióknak szüksége van a [Felügyelt identitás közreműködő](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelésre.

A felhasználó által hozzárendelt felügyelt identitás `Remove-AzUserAssignedIdentity` törléséhez használja a parancsot.  A `-ResourceGroupName` paraméter azt az erőforráscsoportot adja meg, amelyben a `-Name` felhasználó által hozzárendelt identitás t, a paraméter pedig a nevét adja meg. Cserélje `<RESOURCE GROUP>` le `<USER ASSIGNED IDENTITY NAME>` a és a paraméterek értékeit a saját értékeire:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> A felhasználó által hozzárendelt felügyelt identitás törlése nem távolítja el a hivatkozást egyetlen olyan erőforrásról sem, amelyhez hozzá volt rendelve. Az identitás-hozzárendeléseket külön kell eltávolítani.

## <a name="next-steps"></a>További lépések

Az Azure-erőforrások parancsainak teljes listáját és az Azure PowerShell által felügyelt identitások további részleteit az [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity)című témakörben találja.
