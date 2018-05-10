---
title: Létrehozása, listázása és egy felhasználó lehet hozzárendelve (MSI) az Azure PowerShell törlése
description: Lépésről lépésre történő létrehozása, listázása és törlése a felhasználó hozzárendelése az Azure PowerShell felügyelt Szolgáltatásidentitás.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: cc9b1b002c882a847d0ba2359caf4a193ea8d648
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-azure-powershell"></a>Lista létrehozása, vagy egy Azure PowerShell használatával a felhasználói identitás törlése

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Felügyelt Szolgáltatásidentitás az Azure Active Directory-identitással felügyelt Azure-szolgáltatásokhoz biztosít. Ezzel az identitással használhatja az Azure AD-alapú hitelesítés, anélkül, hogy hitelesítő adatok a kódban támogató szolgáltatások felé történő hitelesítésre. 

Ebből a cikkből megismerheti, hogyan létrehozása, listázása és törlése az Azure PowerShell hozzárendelt felhasználói azonosítót.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri a felügyelt Szolgáltatásidentitás, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség egy hozzárendelt rendszer és a felhasználói identitás](overview.md#how-does-it-work)**.
- Ha még nem telepítette az Azure-fiók [regisztráljon egy ingyenes fiókot](https://azure.microsoft.com/free/) folytatása előtt.
- Telepítés [Azure PowerShell legújabb verziójának](https://www.powershellgallery.com/packages/AzureRM) Ha még nem tette meg.
- Ha PowerShell telepítéséhez és használatához helyileg, ez az oktatóanyag igényel-e az Azure PowerShell modul verziója 5.7.0 vagy újabb. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-user-assigned-identity"></a>Hozzon létre egy felhasználó lehet hozzárendelve identitás

Egy felhasználó lehet hozzárendelve identitás létrehozásához használja a [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) parancsot. A `ResourceGroupName` paraméter határozza meg az erőforráscsoportot, a felhasználói identitás létrehozásának a helyét és a `-Name` paraméter határozza meg a nevét. Cserélje le a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterértékeket a saját értékekkel:

> [!IMPORTANT]
> A felhasználói identitások létrehozása csak alfanumerikus és kötőjel (0 – 9 vagy a-z vagy A-Z vagy -) karaktereket. Emellett nevét kell korlátozni a virtuális gép/VMSS helyes működéséhez hozzárendelés 24 karakter hosszúságot. Biztonsági frissítések ellenőrzése. További információkért lásd: [– gyakori kérdések és ismert problémák](known-issues.md)

 ```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>A felhasználói listában identitások

A felhasználói identitások listán, használja a [Get-AzureRmUserAssigned](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity) parancsot.  A `-ResourceGroupName` paraméter határozza meg az erőforráscsoportot, ahol a felhasználó identitása létrejött.  Cserélje le a `<RESOURCE GROUP>` saját értékkel:

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
A válasz a felhasználói identitások van `"Microsoft.ManagedIdentity/userAssignedIdentities"` kulcs, a visszaadott érték `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-identity"></a>Identitás hozzárendelt felhasználó törlése

A felhasználói identitás törléséhez használja a [Remove-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/remove-azurermuserassignedidentity) parancsot.  A `-ResourceGroupName` paraméter határozza meg az erőforráscsoportot, ahol a felhasználó identitása létrejött, és a `-Name` paraméter határozza meg a nevét.  Cserélje le a `<RESOURCE GROUP>` és a `<USER ASSIGNED IDENTITY NAME>` paraméterek értékét a saját értékekkel:

 ```azurecli-interactive
Remove-AzurRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Egy felhasználó lehet hozzárendelve identitás nem törlésével a hivatkozást a hozzá rendelt összes erőforrást. Identitás hozzárendeléseket nem kell külön-külön el kell távolítani.

## <a name="related-content"></a>Kapcsolódó tartalom

Teljes listáját és az Azure PowerShell MSI parancsok további részletekért lásd: [AzureRM.ManagedServiceIdentity](/powershell/module/azurerm.managedserviceidentity#managed_service_identity).


 
