---
title: Felhasználó által hozzárendelt felügyelt identitás kezelése – Azure CLI – Azure AD
description: Részletes útmutató a felhasználó által hozzárendelt felügyelt identitások létrehozásához, listázásához és törléséhez az Azure CLI használatával.
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
ms.date: 10/15/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1e8587562ff452373fe2ee3b98fa20309e77cc7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266584"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása, listázása vagy törlése az Azure CLI használatával


Az Azure-erőforrások felügyelt identitásai Azure-szolgáltatásokat biztosítanak a Azure Active Directory felügyelt identitásával. Ezt az identitást hitelesítheti az Azure AD-hitelesítést támogató szolgáltatásokban anélkül, hogy hitelesítő adatokat kellene használnia a kódban. 

Ebből a cikkből megtudhatja, hogyan hozhat létre, listázhat és törölhet egy felhasználó által hozzárendelt felügyelt identitást az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md). **Mindenképpen tekintse át a [rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Három lehetősége van a CLI-példaszkriptek futtatásához:
    - Használja a Azure Portal [Azure Cloud shellt](../../cloud-shell/overview.md) (lásd a következő szakaszt).
    - Használja a beágyazott Azure Cloud Shell-t a "Kipróbálom" gomb, mindegyik blokk jobb felső sarkában található.
    - [Telepítse az Azure CLI legújabb verzióját](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 vagy újabb), ha helyi CLI-konzolt szeretne használni. Jelentkezzen be az Azure-ba `az login`használatával, egy olyan fiókkal, amely ahhoz az Azure-előfizetéshez van társítva, amelybe a felhasználó által hozzárendelt felügyelt identitást szeretné telepíteni.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása 

Felhasználó által hozzárendelt felügyelt identitás létrehozásához a fióknak rendelkeznie kell a [felügyelt identitás közreműködői](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelésével.

A felhasználó által hozzárendelt felügyelt identitás létrehozásához használja az az [Identity Create](/cli/azure/identity#az-identity-create) parancsot. A `-g` paraméter határozza meg azt az erőforráscsoportot, amelyben létre kell hozni a felhasználó által hozzárendelt felügyelt identitást, és a `-n` paraméter adja meg a nevét. Cserélje le a `<RESOURCE GROUP>` és a `<USER ASSIGNED IDENTITY NAME>` paraméter értékét a saját értékeire:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Felhasználó által hozzárendelt felügyelt identitások listázása

Felhasználó által hozzárendelt felügyelt identitás listázásához/olvasásához a fióknak a [felügyelt identitás-kezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) vagy a [felügyelt identitás közreműködői](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelésre van szüksége.

A felhasználó által hozzárendelt felügyelt identitások listázásához használja az az [Identity List](/cli/azure/identity#az-identity-list) parancsot. Cserélje le a `<RESOURCE GROUP>`t a saját értékére:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
A JSON-válaszban a felhasználó által hozzárendelt felügyelt identitások `"Microsoft.ManagedIdentity/userAssignedIdentities"` értéket adtak vissza a kulcshoz, `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás törlése

Felhasználó által hozzárendelt felügyelt identitás törléséhez a fióknak rendelkeznie kell a [felügyelt identitás közreműködői](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelésével.

Felhasználó által hozzárendelt felügyelt identitás törléséhez használja az az [Identity delete](/cli/azure/identity#az-identity-delete) parancsot.  Az-n paraméter megadja a nevét, és a-g paraméter határozza meg azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt felügyelt identitás létrejött. Cserélje le a `<USER ASSIGNED IDENTITY NAME>` és az `<RESOURCE GROUP>` paraméterek értékét a saját értékeire:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> A felhasználó által hozzárendelt felügyelt identitás törlése nem távolítja el a hivatkozást, a hozzá rendelt összes erőforrásból. Távolítsa el ezeket a VM/VMSS a `az vm/vmss identity remove` parancs használatával

## <a name="next-steps"></a>További lépések

Az Azure CLI-azonosító parancsainak teljes listáját lásd [az Identity](/cli/azure/identity).

A felhasználó által hozzárendelt felügyelt identitás Azure-beli virtuális géphez való hozzárendelésével kapcsolatos információkért lásd: [felügyelt identitások konfigurálása](qs-configure-cli-windows-vm.md#user-assigned-managed-identity) Azure-beli virtuális gépeken az Azure CLI használatával


 
