---
title: Felhasználó által hozzárendelt Azure kezelését felügyelt identitások REST használatával
description: Lépésről lépésre való létrehozása, listázása és törlése a felhasználó által hozzárendelt felügyelt identitás REST API-hívásokat.
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
ms.date: 06/26/2018
ms.author: daveba
ms.openlocfilehash: 72f098cac32422ddbd2632336dd82cfc09a8bc93
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344692"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Lista létrehozása, vagy egy REST API-hívások segítségével felügyelt felhasználó által hozzárendelt Identitások törlése

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Felügyelt identitások, az Azure-erőforrások teszi lehetővé Azure-szolgáltatásokkal hitelesítést szolgáltatásokat, hogy a támogatás az Azure AD-hitelesítés anélkül, hogy a kód a hitelesítő adatokat. 

Ebből a cikkből megismerheti, hogyan létrehozása, listázása és a egy REST API-hívásokat a CURL használatával felügyelt felhasználó által hozzárendelt Identitások törlése.

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség a rendszer által hozzárendelt, és a felhasználó által hozzárendelt felügyelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ha Windows használ, telepítse a [a Linux Windows alrendszere](https://msdn.microsoft.com/commandline/wsl/about) vagy használja a [Azure Cloud Shell](../../cloud-shell/overview.md) az Azure Portalon.
- Ha használja a [a Linux Windows alrendszere](https://msdn.microsoft.com/commandline/wsl/about) vagy egy [linuxos](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [a helyi Azure CLI-konzol telepítése](/cli/azure/install-azure-cli).
- Ha a helyi Azure CLI-konzolt használ, jelentkezzen be Azure-bA `az login` egy olyan fiókkal, amely van társítva az Azure-előfizetést, amelyet szeretne telepíteni, vagy felügyelt identitás felhasználó által hozzárendelt információk lekéréséhez.
- Ez a cikk a felügyeleti műveleteket hajt végre, a fiók az alábbi szerepkör-hozzárendelések van szüksége:
    - [Felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör létrehozása, olvasása (lista), frissítése és törlése a felhasználóhoz felügyelt identitásnak.
    - [Felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkört (lista) egy felhasználó által hozzárendelt felügyelt identitás tulajdonságainak olvasása.
- Lekérni a tulajdonosi hozzáférés segítségével `az account get-access-token` a következő felügyelt identitás felhasználó által hozzárendelt műveleteket.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása 

Egy felhasználó által hozzárendelt felügyelt identitás létrehozásához használja a következő CURL-kérést az Azure Resource Manager API. Cserélje le a `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<USER ASSIGNED IDENTITY NAME>`,`<LOCATION>`, és `<ACCESS TOKEN>` értékeket a saját értékeire:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="list-user-assigned-managed-identities"></a>Felügyelt identitások felhasználó által hozzárendelt listából

Felügyelt identitások felhasználó által hozzárendelt felsorolásához használja a következő CURL-kérést az Azure Resource Manager API. Cserélje le a `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, és `<ACCESS TOKEN>` értékeket a saját értékeire:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```
## <a name="delete-a-user-assigned-managed-identity"></a>Egy felhasználó által hozzárendelt felügyelt identitás törlése

Egy felhasználó által hozzárendelt felügyelt identitás törléséhez használja a következő CURL-kérést az Azure Resource Manager API. Cserélje le a `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, és `<ACCESS TOKEN>` paraméterek értékét a saját értékeire:

> [!NOTE]
> Egy felhasználó által hozzárendelt felügyelt identitás törlése nem távolítja el a referencia bármely erőforrásról hozzá lett rendelve. Eltávolítja a felhasználó által hozzárendelt virtuális gép lásd a CURL használatával felügyelt [távolítsa el a felhasználó által hozzárendelt identitását az Azure virtuális](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>További lépések

Információk egy felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy Azure VM/VMSS a CURL használatával:, [konfigurálása felügyelt identitások az Azure-erőforrások egy Azure virtuális gépen, REST API-hívásokkal](qs-configure-rest-vm.md#user-assigned-managed-identity) és [felügyelt konfigurálása REST API-hívás használatával virtuálisgép-méretezési csoportot az Azure-erőforrások identitásainak](qs-configure-rest-vmss.md#user-assigned-managed-identity).


