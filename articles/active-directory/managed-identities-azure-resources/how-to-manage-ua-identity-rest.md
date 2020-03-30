---
title: A felhasználó által hozzárendelt felügyelt identitások kezelése rest használatával – Azure AD
description: Lépésenkénti útmutató a felhasználó által hozzárendelt felügyelt identitás létrehozásához, listázásához és törléséhez a REST API-hívások kezdeményezéséhez.
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
ms.date: 06/26/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39e108451e4c19e77e01b5bcc5d8dd21e86ad73a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547416"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása, listázása vagy törlése REST API-hívások használatával

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Az Azure-erőforrások felügyelt identitásai lehetővé teszik az Azure-szolgáltatások hitelesítését az Azure AD-hitelesítést támogató szolgáltatások számára anélkül, hogy hitelesítő adatokra lenne szükség a kódban. 

Ebben a cikkben megtudhatja, hogyan hozhat létre, listázhat és törölhet egy felhasználó által hozzárendelt felügyelt identitást a CURL használatával A REST API-hívások kezdeményezéséhez.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [áttekintő szakaszt.](overview.md) **Mindenképpen tekintse át a [rendszerhez rendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget.](overview.md#how-does-the-managed-identities-for-azure-resources-work)**
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Windows használata esetén telepítse a [Windows Linux-alrendszert,](https://msdn.microsoft.com/commandline/wsl/about) vagy használja az [Azure Cloud Shellt](../../cloud-shell/overview.md) az Azure Portalon.
- Ha a [Windows Alrendszert Linuxhoz](https://msdn.microsoft.com/commandline/wsl/about) vagy [Linux disztribúciós operációs rendszert](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)használja, telepítse az Azure [CLI helyi konzolját.](/cli/azure/install-azure-cli)
- Ha az Azure CLI helyi konzolját használja, jelentkezzen be az Azure-ba egy olyan fiókkal, `az login` amely az Azure-előfizetéshez van társítva, amelyet telepíteni vagy lekérni szeretne a felhasználó által hozzárendelt felügyelt identitásadatokat.
- A következő, felhasználó `az account get-access-token` által hozzárendelt felügyelt identitásműveletek végrehajtásához használjon tulajdonosi hozzáférési jogkivonatot.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása 

Felhasználó által hozzárendelt felügyelt identitás létrehozásához a fióknak szüksége van a [felügyelt identitás közreműködőszerepkör-hozzárendelésre.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**Fejlécek kérése**

|Kérelem fejléce  |Leírás  |
|---------|---------|
|*Tartalomtípusa*     | Kötelező. Állítsa `application/json` értékre.        |
|*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot.        |

**Kérelem törzse**

|Név  |Leírás  |
|---------|---------|
|location     | Kötelező. Erőforrás helye.        |

## <a name="list-user-assigned-managed-identities"></a>Felhasználó által hozzárendelt felügyelt identitások listázása

A felhasználó által hozzárendelt felügyelt identitás okának listázásához/olvasásához a fióknak szüksége van a [Felügyelt identitásoperátorra](/azure/role-based-access-control/built-in-roles#managed-identity-operator) vagy a [Felügyelt identitásközreműködő](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelésre.

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|Kérelem fejléce  |Leírás  |
|---------|---------|
|*Tartalomtípusa*     | Kötelező. Állítsa `application/json` értékre.        |
|*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás törlése

A felhasználó által hozzárendelt felügyelt identitás törléséhez a fióknak szüksége van a [Felügyelt identitás közreműködő](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelésre.

> [!NOTE]
> A felhasználó által hozzárendelt felügyelt identitás törlése nem távolítja el a hivatkozást egyetlen olyan erőforrásból sem, amelyhez hozzá volt rendelve. Ha el szeretne távolítani egy felhasználó által hozzárendelt felügyelt identitást egy virtuális gépről a CURL használatával, [lásd: A felhasználó által hozzárendelt identitás eltávolítása egy Azure virtuális gépről.](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm)

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|Kérelem fejléce  |Leírás  |
|---------|---------|
|*Tartalomtípusa*     | Kötelező. Állítsa `application/json` értékre.        |
|*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot.        |

## <a name="next-steps"></a>További lépések

Ha további információban része, hogyan rendelhet hozzá egy felhasználó által hozzárendelt felügyelt identitást egy Azure VM/VMSS szolgáltatáshoz curl használatával, [olvassa el az Azure-erőforrások felügyelt identitásainak konfigurálása egy Azure-beli virtuális gépen REST API-hívások használatával](qs-configure-rest-vm.md#user-assigned-managed-identity) és az [Azure-erőforrások felügyelt identitásainak konfigurálása REST API-hívások használatával című témakört.](qs-configure-rest-vmss.md#user-assigned-managed-identity)
