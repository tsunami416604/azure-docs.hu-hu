---
title: Hogyan kezelheti a felhasználó hozzárendelt Felügyeltszolgáltatás-identitás (MSI) Azure parancssori felületével
description: Részletes útmutatást létrehozása, listázása és törlése a felhasználóhoz rendelt a felügyelt identitás, az Azure CLI használatával.
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
ms.openlocfilehash: 23567c985f4f9df46ee7d80051c15dc5910a1ea8
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37904062"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-cli"></a>Lista létrehozása vagy törlése egy a felhasználóhoz hozzárendelt identitás, az Azure CLI használatával

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Felügyeltszolgáltatás-identitás egy felügyelt identitás, az Azure Active Directory Azure-szolgáltatásokat biztosít. Ez az identitás használatával, amelyek támogatják az Azure AD-hitelesítés, anélkül, hogy hitelesítő adatok a kód a szolgáltatásokhoz való hitelesítéséhez. 

Ebből a cikkből megismerheti, hogyan hozhatók létre, lista és a egy felhasználóhoz hozzárendelt identitás, az Azure CLI-vel törlése.

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a Felügyeltszolgáltatás-identitást, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség egy rendszer által hozzárendelt, és a felhasználóhoz hozzárendelt identitás](overview.md#how-does-it-work)**.
- Ha még nem rendelkezik Azure-fiók [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/) a folytatás előtt.

- Három lehetősége van a CLI-példaszkriptek futtatásához:

    - Használat [Azure Cloud Shell](../../cloud-shell/overview.md) az Azure Portalon (lásd a következő szakaszban).
    - Használja a beágyazott Azure Cloud Shell-t a "Kipróbálom" gomb, mindegyik blokk jobb felső sarkában található.
    - [Telepítse a CLI 2.0 legújabb verzióját](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13-as vagy újabb) Ha inkább a helyi CLI-konzol használatával. Jelentkezzen be Azure-bA `az login`, egy olyan fiókkal, amely az Azure-előfizetést amellyel szeretné üzembe helyezése a felhasználóhoz hozzárendelt identitás társítva van.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Hozzon létre egy felügyelt identitás hozzárendelt felhasználó 

A felhasználóhoz hozzárendelt identitás létrehozása, használja a [az identitás létrehozása](/cli/azure/identity#az-identity-create) parancsot. A `-g` paraméter adja meg az erőforráscsoport helyét a felhasználóhoz hozzárendelt identitás létrehozása és a `-n` paraméter adja meg a nevét. Cserélje le a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterértékeket a saját értékeire:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Listát a felhasználóhoz hozzárendelt identitás

Felhasználó által hozzárendelt identitások listán, használja a [az identitás lista](/cli/azure/identity#az-identity-list) parancsot.  A `-g` paraméter adja meg az erőforráscsoport, ahol a felhasználóhoz hozzárendelt identitás létrehozták.  Cserélje le a `<RESOURCE GROUP>` saját értékét:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
A json-válasz, a felhasználói identitások van `"Microsoft.ManagedIdentity/userAssignedIdentities"` kulcs, a visszaadott érték `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-identity"></a>A felhasználóhoz hozzárendelt identitás törlése

Egy felhasználóhoz hozzárendelt identitás törléséhez használja a [az identitás törlése](/cli/azure/identity#az-identity-delete) parancsot.  A - n paraméter adja meg a nevét, és a – g paraméter adja meg az erőforráscsoport, ahol a felhasználóhoz hozzárendelt identitás létrehozták.  Cserélje le a `<USER ASSIGNED IDENTITY NAME>` és `<RESOURCE GROUP>` paraméterek értékét a saját értékeire:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Egy felhasználóhoz hozzárendelt identitás törlése nem távolítja el a hivatkozást, a hozzá rendelt összes erőforrást. Távolítsa el azokat a VM/VMSS használatával a `az vm/vmss identity remove` parancs

## <a name="related-content"></a>Kapcsolódó tartalom

Azure CLI-identitás parancsok teljes listájáért lásd: [az identitás](/cli/azure/identity).

Egy felhasználóhoz hozzárendelt identitás hozzárendelése egy Azure virtuális gép további tájékoztatást [konfigurálása Felügyeltszolgáltatás-identitás (MSI) Azure parancssori felületével](qs-configure-cli-windows-vm.md#user-assigned-identity)


 
