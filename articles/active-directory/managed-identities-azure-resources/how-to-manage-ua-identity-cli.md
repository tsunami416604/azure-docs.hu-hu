---
title: Felhasználó által hozzárendelt felügyelt identitás kezelése – Azure CLI – Azure AD
description: Részletes útmutató felhasználóhoz rendelt felügyelt identitás létrehozásához, listázásához és törléséhez az Azure CLI használatával.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5c3af6c46dd8ad69915e8f870d739f33375dba5e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266408"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása, listázása vagy törlése az Azure CLI használatával


Az Azure-erőforrások felügyelt identitásai biztosítják az Azure-szolgáltatásokat Azure Active Directory felügyelt identitással. Ezt az identitást hitelesítheti az Azure AD-hitelesítést támogató szolgáltatásokban anélkül, hogy hitelesítő adatokat kellene használnia a kódban. 

Ebből a cikkből megtudhatja, hogyan hozhat létre, listázhat és törölhet egy felhasználó által hozzárendelt felügyelt identitást az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md). **Mindenképpen tekintse át a [rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget](overview.md#managed-identity-types)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A CLI-szkriptek futtatásához a következő három lehetőség közül választhat:
    - Használja a Azure Portal [Azure Cloud shellt](../../cloud-shell/overview.md) (lásd a következő szakaszt).
    - A beágyazott Azure Cloud Shell az egyes kódrészletek jobb felső sarkában található "kipróbálás" gomb segítségével érheti el.
    - [Telepítse az Azure CLI legújabb verzióját](/cli/azure/install-azure-cli) (2.0.13 vagy újabb), ha helyi CLI-konzolt szeretne használni. Jelentkezzen be az Azure-ba `az login` egy olyan fiókkal, amely ahhoz az Azure-előfizetéshez van társítva, amelybe a felhasználó által hozzárendelt felügyelt identitást szeretné telepíteni.


> [!NOTE]
> Ha felhasználói engedélyeket szeretne módosítani a parancssori felület servivce használatakor, meg kell adnia az egyszerű szolgáltatásnak az Azure AD-ban Graph API a CLI-t a parancssori felület részeként kell megadnia a Graph API. Ellenkező esetben előfordulhat, hogy a "nincs megfelelő jogosultsága a művelet befejezéséhez" üzenetet kapja. Ehhez be kell lépnie az alkalmazás regisztrációja Azure Active Directoryban, ki kell választania az alkalmazást, kattintson az API-engedélyek elemre, görgessen le, és válassza a Azure Active Directory gráf lehetőséget. Válassza ki az alkalmazás engedélyei elemet, majd adja hozzá a megfelelő engedélyeket. 



[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása 

Felhasználó által hozzárendelt felügyelt identitás létrehozásához a fióknak rendelkeznie kell a [felügyelt identitás közreműködői](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) szerepkör-hozzárendelésével.

A felhasználó által hozzárendelt felügyelt identitás létrehozásához használja az az [Identity Create](/cli/azure/identity#az-identity-create) parancsot. A `-g` paraméter határozza meg azt az erőforráscsoportot, amelyben létre kell hozni a felhasználó által hozzárendelt felügyelt identitást, és a `-n` paraméter megadja a nevét. Cserélje le a `<RESOURCE GROUP>` és a `<USER ASSIGNED IDENTITY NAME>` paraméter értékét a saját értékeire:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Felhasználó által hozzárendelt felügyelt identitások listázása

Felhasználó által hozzárendelt felügyelt identitás listázásához/olvasásához a fióknak a [felügyelt identitás-kezelő](../../role-based-access-control/built-in-roles.md#managed-identity-operator) vagy a [felügyelt identitás közreműködői](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) szerepkör-hozzárendelésre van szüksége.

A felhasználó által hozzárendelt felügyelt identitások listázásához használja az az [Identity List](/cli/azure/identity#az-identity-list) parancsot. Cserélje le a `<RESOURCE GROUP>` értéket a saját értékére:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
A JSON-válaszban a felhasználó által hozzárendelt felügyelt identitások `"Microsoft.ManagedIdentity/userAssignedIdentities"` értéke a kulcshoz lett visszaadva `type` .

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás törlése

Felhasználó által hozzárendelt felügyelt identitás törléséhez a fióknak rendelkeznie kell a [felügyelt identitás közreműködői](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) szerepkör-hozzárendelésével.

Felhasználó által hozzárendelt felügyelt identitás törléséhez használja az az [Identity delete](/cli/azure/identity#az-identity-delete) parancsot.  Az-n paraméter megadja a nevét, és a-g paraméter határozza meg azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt felügyelt identitás létrejött. Cserélje le a `<USER ASSIGNED IDENTITY NAME>` és a `<RESOURCE GROUP>` Paraméterek értékét a saját értékeire:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> A felhasználó által hozzárendelt felügyelt identitás törlése nem távolítja el a hivatkozást, a hozzá rendelt összes erőforrásból. Távolítsa el ezeket a VM/VMSS a `az vm/vmss identity remove` parancs használatával.

## <a name="next-steps"></a>További lépések

Az Azure CLI-azonosító parancsainak teljes listáját lásd [az Identity](/cli/azure/identity).

A felhasználó által hozzárendelt felügyelt identitás Azure-beli virtuális géphez való hozzárendelésével kapcsolatos információkért lásd: [felügyelt identitások konfigurálása](qs-configure-cli-windows-vm.md#user-assigned-managed-identity) Azure-beli virtuális gépeken az Azure CLI használatával


