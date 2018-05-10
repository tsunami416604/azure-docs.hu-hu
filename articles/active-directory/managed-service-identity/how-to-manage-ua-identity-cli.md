---
title: Hogyan kezelheti a felhasználó hozzárendelt felügyelt szolgáltatás identitásának (MSI) Azure parancssori felület használatával
description: Lépésről lépésre történő létrehozása, listázása és hozzárendelt felhasználó törlése a felügyelt identitás, az Azure parancssori felület használatával.
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
ms.openlocfilehash: 5262914e469bdc07921c3b82e990d544349b5fd4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-cli"></a>Lista létrehozása vagy törlése a felhasználó identitása, az Azure parancssori felület használatával

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Felügyelt Szolgáltatásidentitás az Azure Active Directory-identitással felügyelt Azure-szolgáltatásokhoz biztosít. Ezzel az identitással használhatja az Azure AD-alapú hitelesítés, anélkül, hogy hitelesítő adatok a kódban támogató szolgáltatások felé történő hitelesítésre. 

Ebből a cikkből megismerheti, hogyan létrehozása, listázása és törlése az Azure parancssori felület használatával hozzárendelt felhasználói azonosítót.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri a felügyelt Szolgáltatásidentitás, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség egy hozzárendelt rendszer és a felhasználói identitás](overview.md#how-does-it-work)**.
- Ha még nem telepítette az Azure-fiók [regisztráljon egy ingyenes fiókot](https://azure.microsoft.com/free/) folytatása előtt.

- A parancssori felület parancsfájl példák futtatásához három lehetőség közül választhat:

    - Használjon [Azure Cloud rendszerhéj](../../cloud-shell/overview.md) Azure-portálról (lásd a következő szakaszt).
    - A beágyazott Azure Cloud rendszerhéj a "próbálja" gombra, minden egyes kódblokk jobb felső sarkában található keresztül használja.
    - [Telepítse a legújabb verziót a CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 vagy újabb verzió) Ha a helyi CLI-konzollal szeretné. Jelentkezzen be az Azure használatával `az login`, egy olyan fiókkal, amelybe szeretne telepíteni a hozzárendelt felhasználói azonosító az Azure-előfizetéshez társított.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Hozzon létre egy felügyelt identitás hozzárendelt felhasználó 

Egy felhasználó lehet hozzárendelve identitás létrehozásához használja a [az identitás létrehozása](/cli/azure/identity#az-identity-create) parancsot. A `-g` paraméter határozza meg az erőforráscsoportot, a felhasználói identitás létrehozásának a helyét és a `-n` paraméter határozza meg a nevét. Cserélje le a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterértékeket a saját értékekkel:

> [!IMPORTANT]
> A felhasználói identitások létrehozása csak alfanumerikus és kötőjel (0 – 9 vagy a-z vagy A-Z vagy -) karaktereket. Emellett nevét kell korlátozni a virtuális gép/VMSS helyes működéséhez hozzárendelés 24 karakter hosszúságot. Biztonsági frissítések ellenőrzése. További információkért lásd: [– gyakori kérdések és ismert problémák](known-issues.md)

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>A felhasználói listában identitások

A felhasználói identitások listán, használja a [az identitás lista](/cli/azure/identity#az-identity-list) parancsot.  A `-g` paraméter határozza meg az erőforráscsoportot, ahol a felhasználó identitása létrejött.  Cserélje le a `<RESOURCE GROUP>` saját értékkel:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
A json-választ, a felhasználói identitások van `"Microsoft.ManagedIdentity/userAssignedIdentities"` kulcs, a visszaadott érték `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-identity"></a>Identitás hozzárendelt felhasználó törlése

Egy felhasználó lehet hozzárendelve identitás törléséhez használja a [az identitás törlése](/cli/azure/identity#az-identity-delete) parancsot.  A - n paraméter határozza meg a nevét, és a -g paraméter határozza meg az erőforráscsoportot, ahol a felhasználó identitása létrejött.  Cserélje le a `<USER ASSIGNED IDENTITY NAME>` és `<RESOURCE GROUP>` paraméterek értékét a saját értékekkel:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Egy felhasználó lehet hozzárendelve identitás nem törlésével a hivatkozást a hozzá rendelt összes erőforrást. Távolítsa el azokat a virtuális gép/VMSS a `az vm/vmss identity remove` parancs

## <a name="related-content"></a>Kapcsolódó tartalom

Az Azure parancssori felület identitás parancsok teljes listáját lásd: [az identitás](/cli/azure/identity).

Információk egy felhasználó lehet hozzárendelve identitás hozzárendelése az Azure virtuális gép lásd [konfigurálása felügyelt szolgáltatás Identity (MSI) Azure parancssori felület használatával](qs-configure-cli-windows-vm.md#user-assigned-identity)


 
