---
title: "A felhasználó által hozzárendelt MSI konfigurálása az Azure virtuális gép Azure parancssori felület használatával"
description: "Lépés lépés útmutatást, a felhasználó által hozzárendelt felügyelt szolgáltatás Identity (MSI) konfigurálása az Azure virtuális gép, Azure parancssori felület használatával."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e8d21aad23782f22b93baf12ce58d1aed4dd5dee
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-azure-cli"></a>A felhasználó által hozzárendelt felügyelt szolgáltatás Identity (MSI) konfigurálja a virtuális gépek Azure parancssori felület használatával

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Felügyelt Szolgáltatásidentitás az Azure Active Directory-identitással felügyelt Azure-szolgáltatásokhoz biztosít. Ezzel az identitással használhatja az Azure AD-alapú hitelesítés, anélkül, hogy hitelesítő adatok a kódban támogató szolgáltatások felé történő hitelesítésre. 

Ebből a cikkből megtudhatja engedélyezése, és távolítsa el a felhasználó által hozzárendelt MSI egy Azure virtuális gép, Azure parancssori felület használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Ebben az oktatóanyagban a CLI-parancsfájlt példák futtatásához két lehetőség közül választhat:

- Használjon [Azure Cloud rendszerhéj](~/articles/cloud-shell/overview.md) vagy Azure-portálról, vagy a "próbálja" gombra, keresztül minden kódblokk jobb felső sarkában található.
- [Telepítse a legújabb verziót a CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 vagy újabb verzió) Ha a helyi CLI-konzollal szeretné. Majd jelentkezzen be az Azure használatával [az bejelentkezési](/cli/azure/reference-index#az_login). Használjon olyan fiókot, amelybe szeretne telepíteni, a felhasználó által hozzárendelt MSI és a virtuális gép Azure-előfizetéssel társított:

   ```azurecli
   az login
   ```

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>MSI engedélyezése az Azure virtuális gép létrehozása

Ez a szakasz végigvezeti a virtuális gép létrehozása és a felhasználó által hozzárendelt MSI hozzárendelése a virtuális géphez. Ha már rendelkezik egy használni kívánt virtuális Gépet, hagyja ki ezt a szakaszt, és folytassa a Tovább gombra.

1. Ezt a lépést kihagyhatja, ha már rendelkezik egy használni kívánt erőforráscsoportot. Hozzon létre egy [erőforráscsoport](~/articles/azure-resource-manager/resource-group-overview.md#terminology) elszigetelési és telepítéséhez, az MSI-fájl használatával [az csoport létrehozása](/cli/azure/group/#az_group_create). Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>` és `<LOCATION>` paraméterértékeket a saját értékekkel. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Hozzon létre egy felhasználó által hozzárendelt MSI az [az identitás létrehozása](/cli/azure/identity#az_identity_create).  A `-g` paraméter határozza meg az erőforráscsoportot, ahol az MSI-fájl jön létre, és a `-n` paraméter határozza meg a nevét. Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>` és `<MSI NAME>` paraméterértékeket a saját értékekkel:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
A választ a felhasználó által hozzárendelt MSI-fájl létrehozása a következőhöz hasonló részleteit tartalmazza. Az erőforrás `id` az MSI rendelt érték a következő lépésben lesz szükség.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Hozzon létre egy virtuális gép az [az virtuális gép létrehozása](/cli/azure/vm/#az_vm_create). Az alábbi példa létrehoz egy virtuális Gépet az új felhasználó által hozzárendelt MSI, által megadott társított a `--assign-identity` paraméter. Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>`, és `<`MSI-azonosító >` parameter values with your own values. For `<MSI ID>`, use the user-assigned MSI's resource `id "tulajdonság az előző lépésben létrehozott: 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Egy meglévő Azure virtuális gépen az MSI engedélyezése

1. Hozzon létre egy felhasználó által hozzárendelt MSI az [az identitás létrehozása](/cli/azure/identity#az_identity_create).  A `-g` paraméter határozza meg az erőforráscsoportot, ahol az MSI-fájl jön létre, és a `-n` paraméter határozza meg a nevét. Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>` és `<MSI NAME>` paraméterértékeket a saját értékekkel:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
A választ a felhasználó által hozzárendelt MSI-fájl létrehozása a következőhöz hasonló részleteit tartalmazza. Az erőforrás `id` az MSI rendelt érték a következő lépésben lesz szükség.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. A felhasználó által hozzárendelt MSI hozzárendelése a virtuális gép használatával [az vm hozzárendelése-identitás](/cli/azure/vm#az_vm_assign_identity). Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>` és `<VM NAME>` paraméterértékeket a saját értékekkel. A `<MSI ID>` lesz a felhasználó által hozzárendelt MSI erőforrás `id` tulajdonság, mert az előző lépésben létrehozott:

    ```azurecli-interactive
    az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

## <a name="remove-msi-from-an-azure-vm"></a>Az Azure virtuális gép MSI eltávolítása

1. A virtuális gép használata a felhasználó által hozzárendelt MSI eltávolítása [az virtuálisgép-remove-azonosítója](/cli/azure/vm#az_vm_remove_identity). Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>` és `<VM NAME>` paraméterértékeket a saját értékekkel. A `<MSI NAME>` lesz a felhasználó által hozzárendelt MSI `name` tulajdonság megadott során a `az identity create` parancs (lásd az előző szakaszokban szereplő példák):

   ```azurecli-interactive
   az vm remove-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
   ```

## <a name="next-steps"></a>További lépések

- [Felügyelt Szolgáltatásidentitás áttekintése](msi-overview.md)
- A teljes Azure virtuális gépek létrehozására – Gyorsútmutatók lásd: 

  - [Windows virtuális gép létrehozása a parancssori felület](~/articles/virtual-machines/windows/quick-create-cli.md)  
  - [Linux virtuális gép létrehozása a parancssori felület](~/articles/virtual-machines/linux/quick-create-cli.md) 

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.
















