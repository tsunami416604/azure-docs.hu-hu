---
title: Felhasználó által hozzárendelt felügyelt identitás létrehozása, listázása & Azure PowerShell-Azure AD használatával
description: Részletes útmutató a felhasználó által hozzárendelt felügyelt identitás Azure PowerShell használatával történő létrehozásához, listázásához és törléséhez.
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
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547396"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása, listázása vagy törlése Azure PowerShell használatával

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Az Azure-erőforrások felügyelt identitásai Azure-szolgáltatásokat biztosítanak a Azure Active Directory felügyelt identitásával. Ezt az identitást hitelesítheti az Azure AD-hitelesítést támogató szolgáltatásokban anélkül, hogy hitelesítő adatokat kellene használnia a kódban. 

Ebből a cikkből megtudhatja, hogyan hozhat létre, listázhat és törölhet egy felhasználó által hozzárendelt felügyelt identitást Azure PowerShell használatával.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md). **Mindenképpen tekintse át a [rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ha még nem tette meg [, telepítse a Azure PowerShell legújabb verzióját](/powershell/azure/install-az-ps) .
- Ha helyileg futtatja a PowerShellt, akkor emellett a következőket kell tennie: 
    - Futtassa a `Connect-AzAccount` parancsot, hogy kapcsolatot hozzon létre az Azure-ral.
    - Telepítse a [PowerShellGet legújabb verzióját](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Futtassa a következőt: `Install-Module -Name PowerShellGet -AllowPrerelease` a `PowerShellGet` modul kiadás előtti verziójának eléréséhez (előfordulhat, hogy a parancs futtatása után ki kell lépnie (`Exit`) az aktuális PowerShell-munkamenetből, hogy telepíteni tudja az `Az.ManagedServiceIdentity` modult).
    - Futtassa `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` a `Az.ManagedServiceIdentity` modul előzetes verziójának telepítéséhez a felhasználó által hozzárendelt felügyelt identitási műveletek elvégzéséhez ebben a cikkben.

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Felhasználó által hozzárendelt felügyelt identitás létrehozásához a fióknak rendelkeznie kell a [felügyelt identitás közreműködői](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelésével.

Felhasználó által hozzárendelt felügyelt identitás létrehozásához használja a `New-AzUserAssignedIdentity` parancsot. A `ResourceGroupName` paraméter határozza meg azt az erőforráscsoportot, amelyben létre kell hozni a felhasználó által hozzárendelt felügyelt identitást, és a `-Name` paraméter adja meg a nevét. Cserélje le a `<RESOURCE GROUP>` és a `<USER ASSIGNED IDENTITY NAME>` paraméter értékét a saját értékeire:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Felhasználó által hozzárendelt felügyelt identitások listázása

Felhasználó által hozzárendelt felügyelt identitás listázásához/olvasásához a fióknak a [felügyelt identitás-kezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) vagy a [felügyelt identitás közreműködői](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelésre van szüksége.

A felhasználó által hozzárendelt felügyelt identitások listázásához használja a [Get-AzUserAssigned] parancsot.  A `-ResourceGroupName` paraméter határozza meg azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt felügyelt identitás létrejött. Cserélje le a `<RESOURCE GROUP>`t a saját értékére:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
A válaszban a felhasználó által hozzárendelt felügyelt identitások `"Microsoft.ManagedIdentity/userAssignedIdentities"` értéket adtak vissza a kulcshoz, `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás törlése

Felhasználó által hozzárendelt felügyelt identitás törléséhez a fióknak rendelkeznie kell a [felügyelt identitás közreműködői](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelésével.

Felhasználó által hozzárendelt felügyelt identitás törléséhez használja a `Remove-AzUserAssignedIdentity` parancsot.  A `-ResourceGroupName` paraméter határozza meg azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt identitás létrejött, és a `-Name` paraméter a nevét adja meg. Cserélje le a `<RESOURCE GROUP>` és a `<USER ASSIGNED IDENTITY NAME>` paraméterek értékét a saját értékeire:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> A felhasználó által hozzárendelt felügyelt identitás törlése nem távolítja el a hivatkozást, a hozzá rendelt összes erőforrásból. Az identitás-hozzárendeléseket külön kell eltávolítani.

## <a name="next-steps"></a>Következő lépések

A teljes listát és az Azure-erőforrások parancsainak Azure PowerShell felügyelt identitások részletes ismertetését az [az. ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity)című részben tekintheti meg.
