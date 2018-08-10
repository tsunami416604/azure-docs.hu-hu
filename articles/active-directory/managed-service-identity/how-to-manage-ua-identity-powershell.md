---
title: Hogyan hozhat létre, listázása és törlése a felhasználóhoz rendelt (MSI) Azure PowerShell-lel
description: Részletes útmutatást létrehozása, listázása és törlése a felhasználó hozzárendelt Felügyeltszolgáltatás-identitás az Azure PowerShell használatával.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: 31a632138a4946accfcab858b7b61782fb4e7d72
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005371"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-azure-powershell"></a>Lista létrehozása vagy törlése a felhasználóhoz hozzárendelt identitás Azure PowerShell-lel

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Felügyeltszolgáltatás-identitás egy felügyelt identitás, az Azure Active Directory Azure-szolgáltatásokat biztosít. Ez az identitás használatával, amelyek támogatják az Azure AD-hitelesítés, anélkül, hogy hitelesítő adatok a kód a szolgáltatásokhoz való hitelesítéséhez. 

Ebből a cikkből megismerheti, hogyan hozhatók létre, lista és a egy Azure PowerShell-lel a felhasználóhoz hozzárendelt identitás törlése.

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a Felügyeltszolgáltatás-identitást, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség egy rendszer által hozzárendelt, és a felhasználóhoz hozzárendelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Telepítés [az Azure PowerShell legújabb verzióját](https://www.powershellgallery.com/packages/AzureRM) Ha még nem tette.
- Ha helyi telepítése és használata PowerShell választja, az oktatóanyaghoz az Azure PowerShell 5.7.0 modul verzió vagy újabb. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ez a cikk a felügyeleti műveleteket hajt végre, a fiók az alábbi szerepkör-hozzárendelések van szüksége:
    - [Felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör létrehozása, olvasása (lista), frissítése és törlése a felhasználóhoz hozzárendelt identitás.
    - [Felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkört egy felhasználóhoz hozzárendelt identitás tulajdonságainak olvasása (lista).

> [!NOTE]
> Felhasználó által hozzárendelt identitások továbbra is előzetes verzióban érhető el a következő paranccsal AzureRM.ManagedServiceIdentity moduljának telepítése manuálisan kell is. 
```azurepowershell-interactive
Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease
```

## <a name="create-a-user-assigned-identity"></a>Felhasználóhoz rendelt identitás létrehozása

A felhasználóhoz hozzárendelt identitás létrehozása, használja a [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) parancsot. A `ResourceGroupName` paraméter adja meg az erőforráscsoport helyét a felhasználóhoz hozzárendelt identitás létrehozása és a `-Name` paraméter adja meg a nevét. Cserélje le a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterértékeket a saját értékeire:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Listát a felhasználóhoz hozzárendelt identitás

Felhasználó által hozzárendelt identitások listán, használja a [Get-AzureRmUserAssigned](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity) parancsot.  A `-ResourceGroupName` paraméter adja meg az erőforráscsoport, ahol a felhasználóhoz hozzárendelt identitás létrehozták. Cserélje le a `<RESOURCE GROUP>` saját értékét:

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
A választ, a felhasználói identitások van `"Microsoft.ManagedIdentity/userAssignedIdentities"` kulcs, a visszaadott érték `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-identity"></a>A felhasználóhoz hozzárendelt identitás törlése

A felhasználói identitás törléséhez használja a [Remove-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/remove-azurermuserassignedidentity) parancsot.  A `-ResourceGroupName` paraméter adja meg az erőforráscsoport, ahol a felhasználóhoz hozzárendelt identitás lett létrehozva, és a `-Name` paraméter adja meg a nevét. Cserélje le a `<RESOURCE GROUP>` és a `<USER ASSIGNED IDENTITY NAME>` paraméterek értékét a saját értékeire:

 ```azurepowershell-interactive
Remove-AzurRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Egy felhasználóhoz hozzárendelt identitás törlése nem távolítja el a hivatkozást, a hozzá rendelt összes erőforrást. Identitás-hozzárendelések kell külön lépésben lehet eltávolítani.

## <a name="related-content"></a>Kapcsolódó tartalom

Teljes listáját és az Azure PowerShell MSI-parancsok további részleteket lásd: [AzureRM.ManagedServiceIdentity](/powershell/module/azurerm.managedserviceidentity#managed_service_identity).
