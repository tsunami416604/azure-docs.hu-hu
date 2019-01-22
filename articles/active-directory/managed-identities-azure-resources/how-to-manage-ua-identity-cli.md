---
title: Egy Azure CLI használatával felügyelt felhasználó által hozzárendelt identitások kezelése
description: Részletes útmutatást létrehozása, listázása és törlése a felhasználó által hozzárendelt felügyelt identitás, az Azure CLI használatával.
services: active-directory
documentationcenter: ''
author: daveba
manager: daveba
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: 34f1eaae85fd222be43624429570c77536691a2d
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54435906"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Lista létrehozása vagy törlése az Azure CLI használatával felügyelt felhasználó által hozzárendelt identitás

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Felügyelt identitások az Azure-erőforrások egy felügyelt identitás, az Azure Active Directory Azure-szolgáltatásokat biztosít. Ez az identitás használatával, amelyek támogatják az Azure AD-hitelesítés, anélkül, hogy hitelesítő adatok a kód a szolgáltatásokhoz való hitelesítéséhez. 

Ebből a cikkből megismerheti, hogyan hozhat létre, listázása és törlése az Azure CLI használatával felügyelt felhasználó által hozzárendelt identitás.

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség a rendszer által hozzárendelt, és a felhasználó által hozzárendelt felügyelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Három lehetősége van a CLI-példaszkriptek futtatásához:
    - Használat [Azure Cloud Shell](../../cloud-shell/overview.md) az Azure Portalon (lásd a következő szakaszban).
    - Használja a beágyazott Azure Cloud Shell-t a "Kipróbálom" gomb, mindegyik blokk jobb felső sarkában található.
    - [Az Azure CLI legújabb verziójának telepítéséhez](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13-as vagy újabb) Ha inkább a helyi CLI-konzol használatával. Jelentkezzen be Azure-bA `az login`, egy olyan fiókkal, amely alatt szeretné telepíteni, a felhasználó által hozzárendelt Azure-előfizetéshez társított felügyelt identitás.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása 

A felügyelt felhasználó által hozzárendelt identitás létrehozása, a fióknak rendelkeznie kell a [felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelés.

Használja a [az identitás létrehozása](/cli/azure/identity#az-identity-create) paranccsal hozzon létre egy felhasználó által hozzárendelt felügyelt identitás. A `-g` paraméter adja meg az erőforráscsoport, hová hozza létre a felhasználó által hozzárendelt felügyelt identitást, és a `-n` paraméter adja meg a nevét. Cserélje le a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterértékeket a saját értékeire:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Felügyelt identitások felhasználó által hozzárendelt listából

A felhasználó által hozzárendelt felügyelt identitás lista/Olvasás, a fióknak rendelkeznie kell a [felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) vagy [felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelés.

Felügyelt identitások felhasználó által hozzárendelt listán, használja a [az identitás lista](/cli/azure/identity#az-identity-list) parancsot. Cserélje le a `<RESOURCE GROUP>` saját értékét:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
A json-választ a felhasználó által hozzárendelt felügyelt identitások van `"Microsoft.ManagedIdentity/userAssignedIdentities"` kulcs, a visszaadott érték `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Egy felhasználó által hozzárendelt felügyelt identitás törlése

Ha törölni szeretné egy felhasználó által hozzárendelt felügyelt identitás, a fióknak rendelkeznie kell a [felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelés.

Egy felhasználó által hozzárendelt felügyelt identitás törléséhez használja a [az identitás törlése](/cli/azure/identity#az-identity-delete) parancsot.  A - n paraméter adja meg a nevét, és a – g paraméter adja meg az erőforráscsoport, ahol a felhasználó által hozzárendelt felügyelt identitás létrehozták. Cserélje le a `<USER ASSIGNED IDENTITY NAME>` és `<RESOURCE GROUP>` paraméterek értékét a saját értékeire:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Egy felhasználó által hozzárendelt felügyelt identitás törlése nem távolítja el a hivatkozást, bármely erőforrásról hozzá lett rendelve. Távolítsa el azokat a VM/VMSS használatával a `az vm/vmss identity remove` parancs

## <a name="next-steps"></a>További lépések

Azure CLI-identitás parancsok teljes listájáért lásd: [az identitás](/cli/azure/identity).

Információk egy felhasználó által hozzárendelt hozzárendelése felügyelt identitás egy Azure virtuális gép lásd [konfigurálása felügyelt identitások az Azure-erőforrások egy Azure virtuális gépen az Azure CLI használatával](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)


 
