---
title: Hogyan kezelheti az Azure-felhasználó hozzárendelt felügyelt identitások REST használatával
description: Részletes útmutatást létrehozása, listázása és törlése a felhasználó hozzárendelt felügyelt identitás a REST API-hívásokat.
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
ms.openlocfilehash: a6241c105019f04df09080a89e8fe3b77b5f9385
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888763"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-rest-api-calls"></a>Lista létrehozása, és a egy felhasználóhoz hozzárendelt identitás használatával REST API-hívások törlése

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Felügyelt identitás az Azure-szolgáltatások elvégezhessék a hitelesítést a szolgáltatások, hogy a támogatás az Azure AD-hitelesítés anélkül, hogy a kód a hitelesítő adatokat biztosít. 

Ebből a cikkből megismerheti, hogyan hozhatók létre, lista, és a REST API-hívásokat a CURL használatával felügyelt identitás hozzárendelt felhasználó törlése.

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a Felügyeltszolgáltatás-identitást, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség egy rendszer által hozzárendelt, és a felhasználóhoz hozzárendelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ha Windows használ, telepítse a [a Linux Windows alrendszere](https://msdn.microsoft.com/commandline/wsl/about) vagy használja a [Azure Cloud Shell](../../cloud-shell/overview.md) az Azure Portalon.
- Ha használja a [a Linux Windows alrendszere](https://msdn.microsoft.com/commandline/wsl/about) vagy egy [linuxos](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [a helyi Azure CLI-konzol telepítése](/cli/azure/install-azure-cli).
- Ha a helyi Azure CLI-konzolt használ, jelentkezzen be Azure-bA `az login` egy olyan fiókkal, amely az Azure-előfizetést, amelyet szeretne telepíteni, vagy a társított felhasználóhoz felügyelt azonosító adatokat.
- Ez a cikk a felügyeleti műveleteket hajt végre, a fiók az alábbi szerepkör-hozzárendelések van szüksége:
    - [Felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör létrehozása, olvasása (lista), frissítése és törlése a felhasználóhoz hozzárendelt identitás.
    - [Felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkört egy felhasználóhoz hozzárendelt identitás tulajdonságainak olvasása (lista).
- Lekérni a tulajdonosi hozzáférés segítségével `az account get-access-token` hajtsa végre a következő felhasználó rendelve a felügyelt identitás műveleteket.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Hozzon létre egy felügyelt identitás hozzárendelt felhasználó 

A felhasználóhoz rendelt felügyelt identitás létrehozásához használja a következő CURL-kérést az Azure Resource Manager API. Cserélje le a `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<USER ASSIGNED IDENTITY NAME>`,`<LOCATION>`, és `<ACCESS TOKEN>` értékeket a saját értékeire:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="list-user-assigned-managed-identities"></a>Lista felhasználóhoz felügyelt identitásokból

Felügyelt identitások a felhasználói listán, használja a következő CURL-kérést az Azure Resource Manager API. Cserélje le a `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, és `<ACCESS TOKEN>` értékeket a saját értékeire:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```
## <a name="delete-a-user-assigned-managed-identity"></a>Hozzárendelt felügyelt identitás felhasználó törlése

A felhasználóhoz rendelt felügyelt identitás törléséhez használja a következő CURL-kérést az Azure Resource Manager API. Cserélje le a `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, és `<ACCESS TOKEN>` paraméterek értékét a saját értékeire:

> [!NOTE]
> Egy felhasználóhoz hozzárendelt identitás törlése nem távolítja el a referencia a hozzá rendelt összes erőforrást. Hozzárendelt felhasználó eltávolítása a virtuális gép lásd a CURL használatával felügyelt [távolítsa el a felhasználóhoz hozzárendelt identitás az Azure virtuális](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>További lépések

Információ egy felhasználóhoz hozzárendelt identitás hozzárendelése egy Azure VM/VMSS a CURL használatával:, [felügyelt identitásának konfigurálása az Azure virtuális Gépekhez a CURL használatával](qs-configure-rest-vm.md#user-assigned-identity) és [felügyelt identitásának konfigurálása egy virtuálisgép-méretezési csoportot, a CURL használatával ](qs-configure-rest-vmss.md#user-assigned-identity).


