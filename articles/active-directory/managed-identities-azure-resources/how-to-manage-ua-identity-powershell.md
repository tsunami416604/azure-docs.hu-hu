---
title: Hogyan hozhat létre, listázása és törlése a felhasználó által hozzárendelt felügyelt identitás az Azure PowerShell-lel
description: Részletes útmutatást létrehozása, listázása és törlése a felhasználó által hozzárendelt felügyelt identitás az Azure PowerShell-lel.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: priyamo
ms.openlocfilehash: 4abe003eff9e37acbcce63e1293b65fbe500631f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55186178"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Lista létrehozása, vagy egy Azure PowerShell-lel felügyelt felhasználó által hozzárendelt Identitások törlése

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Felügyelt identitások az Azure-erőforrások egy felügyelt identitás, az Azure Active Directory Azure-szolgáltatásokat biztosít. Ez az identitás használatával, amelyek támogatják az Azure AD-hitelesítés, anélkül, hogy hitelesítő adatok a kód a szolgáltatásokhoz való hitelesítéséhez. 

Ebből a cikkből megismerheti, hogyan hozhat létre, listázása és törlése az Azure PowerShell-lel felügyelt felhasználó által hozzárendelt identitás.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség a rendszer által hozzárendelt, és a felhasználó által hozzárendelt felügyelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Telepítés [az Azure PowerShell legújabb verzióját](/powershell/azure/install-az-ps) Ha még nem tette.
- Ha helyileg futtatja a PowerShellt, akkor emellett a következőket kell tennie: 
    - Futtassa a `Connect-AzAccount` parancsot, hogy kapcsolatot hozzon létre az Azure-ral.
    - Telepítse a [PowerShellGet legújabb verzióját](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Futtassa a következőt: `Install-Module -Name PowerShellGet -AllowPrerelease` a `PowerShellGet` modul kiadás előtti verziójának eléréséhez (előfordulhat, hogy a parancs futtatása után ki kell lépnie (`Exit`) az aktuális PowerShell-munkamenetből, hogy telepíteni tudja az `Az.ManagedServiceIdentity` modult).
    - Futtatás `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` előzetes verziójának telepítése a `Az.ManagedServiceIdentity` végrehajtásához, a felhasználó által hozzárendelt modul felügyelt identitás műveletek ebben a cikkben.

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

A felügyelt felhasználó által hozzárendelt identitás létrehozása, a fióknak rendelkeznie kell a [felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelés.

Egy felhasználó által hozzárendelt felügyelt identitás létrehozásához használja a `New-AzUserAssignedIdentity` parancsot. A `ResourceGroupName` paraméter adja meg az erőforráscsoport, hová hozza létre a felhasználó által hozzárendelt felügyelt identitást, és a `-Name` paraméter adja meg a nevét. Cserélje le a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterértékeket a saját értékeire:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Felügyelt identitások felhasználó által hozzárendelt listából

A felhasználó által hozzárendelt felügyelt identitás lista/Olvasás, a fióknak rendelkeznie kell a [felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) vagy [felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelés.

Felhasználó által hozzárendelt listához felügyelt identitások, a [Get-AzUserAssigned] paranccsal.  A `-ResourceGroupName` paraméter adja meg az erőforráscsoport, ahol a felhasználó által hozzárendelt felügyelt identitás létrehozták. Cserélje le a `<RESOURCE GROUP>` saját értékét:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
A választ, a felhasználó által hozzárendelt felügyelt identitások van `"Microsoft.ManagedIdentity/userAssignedIdentities"` kulcs, a visszaadott érték `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Egy felhasználó által hozzárendelt felügyelt identitás törlése

Ha törölni szeretné egy felhasználó által hozzárendelt felügyelt identitás, a fióknak rendelkeznie kell a [felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelés.

Egy felhasználó által hozzárendelt felügyelt identitás törléséhez használja a `Remove-AzUserAssignedIdentity` parancsot.  A `-ResourceGroupName` paraméter adja meg az erőforráscsoport, ahol a felhasználó által hozzárendelt identitás létrehozása és a `-Name` paraméter adja meg a nevét. Cserélje le a `<RESOURCE GROUP>` és a `<USER ASSIGNED IDENTITY NAME>` paraméterek értékét a saját értékeire:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Egy felhasználó által hozzárendelt felügyelt identitás törlése nem távolítja el a hivatkozást, bármely erőforrásról hozzá lett rendelve. Identitás-hozzárendelések kell külön lépésben lehet eltávolítani.

## <a name="next-steps"></a>További lépések

Teljes listáját és a további információt az Azure PowerShell felügyelt identitások Azure-erőforrások parancsokért lásd: [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).
