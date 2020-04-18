---
title: A felhasználó által hozzárendelt felügyelt identitás kezelése - Azure CLI - Azure AD
description: Lépésenkénti útmutató a felhasználó által hozzárendelt felügyelt identitás létrehozásához, listájához és törléséhez az Azure CLI használatával.
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
ms.date: 04/17/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: be5defb85547e8750dea9ceaa481217aa40a004e
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639763"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása, listázása vagy törlése az Azure CLI használatával


Az Azure-erőforrások felügyelt identitásai felügyelt identitást biztosítaz Azure-szolgáltatások felügyelt identitással az Azure Active Directoryban. Ezzel az identitással hitelesítheti az Azure AD-hitelesítést támogató szolgáltatások, anélkül, hogy hitelesítő adatokat a kódban. 

Ebben a cikkben megtudhatja, hogyan hozhat létre, listázhat és törölhet egy felhasználó által hozzárendelt felügyelt identitást az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [áttekintő szakaszt.](overview.md) **Mindenképpen tekintse át a [rendszerhez rendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget.](overview.md#how-does-the-managed-identities-for-azure-resources-work)**
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A CLI parancsfájlpéldák futtatásához három lehetőség közül választhat:
    - Használja az [Azure Cloud Shell](../../cloud-shell/overview.md) az Azure Portalon (lásd a következő szakaszban).
    - Használja a beágyazott Azure Cloud Shell segítségével a "Try It" gomb, található a jobb felső sarokban minden kódblokk.
    - [Telepítse az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 vagy újabb) legújabb verzióját, ha helyi CLI-konzolt szeretne használni. Jelentkezzen be az `az login`Azure-ba egy azure-előfizetéshez társított fiók használatával, amely nek a felhasználó által hozzárendelt felügyelt identitást szeretné telepíteni.


> [!NOTE]
> Annak érdekében, hogy módosítsa a felhasználói engedélyeket, ha egy alkalmazás servivce egyszerű cli használatával kell biztosítania a szolgáltatás egyszerű további engedélyeket az Azure AD Graph API-ban a CLI részeget get kérelmeket a Graph API-t. Ellenkező esetben előfordulhat, hogy a "Művelet végrehajtásához szükséges elégtelen jogosultságok" üzenetet kapja. Ehhez meg kell néznie az alkalmazás regisztrációját az Azure Active Directoryban, válassza ki az alkalmazást, kattintson az API-engedélyekre, görgessen le, és válassza az Azure Active Directory Graph lehetőséget. Itt válassza az alkalmazásengedélyeket, majd adja hozzá a megfelelő engedélyeket. 



[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása 

Felhasználó által hozzárendelt felügyelt identitás létrehozásához a fióknak szüksége van a [felügyelt identitás közreműködőszerepkör-hozzárendelésre.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Használja az [az identity create](/cli/azure/identity#az-identity-create) parancsot a felhasználó által hozzárendelt felügyelt identitás létrehozásához. A `-g` paraméter azt az erőforráscsoportot határozza meg, ahol a felhasználó `-n` által hozzárendelt felügyelt identitást létre kell hozni, és a paraméter megadja a nevét. Cserélje `<RESOURCE GROUP>` le `<USER ASSIGNED IDENTITY NAME>` a és a paraméterértékeket a saját értékeire:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Felhasználó által hozzárendelt felügyelt identitások listázása

A felhasználó által hozzárendelt felügyelt identitás okának listázásához/olvasásához a fióknak szüksége van a [Felügyelt identitásoperátorra](/azure/role-based-access-control/built-in-roles#managed-identity-operator) vagy a [Felügyelt identitásközreműködő](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelésre.

A felhasználó által hozzárendelt felügyelt identitások listázásához használja az [az identity list](/cli/azure/identity#az-identity-list) parancsot. Cserélje `<RESOURCE GROUP>` ki a saját értékére:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
A json-válaszban a felhasználó által `"Microsoft.ManagedIdentity/userAssignedIdentities"` hozzárendelt felügyelt `type`identitások értéke a kulcshoz adott vissza, .

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás törlése

A felhasználó által hozzárendelt felügyelt identitás törléséhez a fióknak szüksége van a [Felügyelt identitás közreműködő](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelésre.

Felhasználó által hozzárendelt felügyelt identitás törléséhez használja az [az identity delete](/cli/azure/identity#az-identity-delete) parancsot.  A -n paraméter megadja a nevét, a -g paraméter pedig azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt felügyelt identitás t létrehozták. Cserélje `<USER ASSIGNED IDENTITY NAME>` le `<RESOURCE GROUP>` a és a paraméterek értékeit a saját értékeire:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> A felhasználó által hozzárendelt felügyelt identitás törlése nem távolítja el a hivatkozást egyetlen olyan erőforrásról sem, amelyhez hozzá volt rendelve. Távolítsa el azokat a VM/VMSS parancsból a `az vm/vmss identity remove` paranccsal

## <a name="next-steps"></a>További lépések

Az Azure CLI identitásparancsok teljes listáját az [az identity](/cli/azure/identity).

Ha további információra van szükség arról, hogy miként rendelhet hozzá egy felhasználó által hozzárendelt felügyelt identitást egy Azure virtuális géphez, [olvassa el az Azure-erőforrások felügyelt identitásának konfigurálása Azure-beli virtuális gépen az Azure CLI használatával című témakört.](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)


 
