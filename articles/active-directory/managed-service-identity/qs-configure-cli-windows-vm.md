---
title: Rendszer és a felhasználó konfigurálása hozzárendelt identitások egy Azure virtuális gépen, Azure parancssori felület használatával
description: Lépésről lépésre egy rendszer és a felhasználó hozzárendelt identitások egy Azure virtuális gépen, Azure parancssori felület használatával.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 0f167fef2303ff3b552b9924db3eae9a07c1cb69
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="configure-managed-service-identity-msi-on-an-azure-vm-using-azure-cli"></a>Felügyelt szolgáltatás identitásának (MSI) konfigurálása az Azure virtuális gép Azure parancssori felület használatával

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt Szolgáltatásidentitást az Azure Active Directoryban automatikusan felügyelt identitással Azure szolgáltatásokat biztosít. Ez az identitás, amely támogatja az Azure AD-alapú hitelesítés, anélkül, hogy a hitelesítő adatokat a kódban a szolgáltatással való hitelesítésre szolgáló használhatja. 

Ebben a cikkben megismerheti, hogyan hajthat végre a következő felügyelt Szolgáltatásidentitás műveletek egy Azure virtuális gépen, az Azure parancssori felület használatával:
- Engedélyezheti vagy letilthatja a rendszer az Azure virtuális gép identitásának hozzárendelt
- Hozzáadhat és eltávolíthat egy Azure virtuális gép identitásának hozzárendelt felhasználó

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri a felügyelt Szolgáltatásidentitás, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség egy hozzárendelt rendszer és a felhasználói identitás](overview.md#how-does-it-work)**.
- Ha még nem telepítette az Azure-fiók [regisztráljon egy ingyenes fiókot](https://azure.microsoft.com/free/) folytatása előtt.
- A parancssori felület parancsfájl példák futtatásához három lehetőség közül választhat:

    - Használjon [Azure Cloud rendszerhéj](../../cloud-shell/overview.md) Azure-portálról (lásd a következő szakaszt).
    - A beágyazott Azure Cloud rendszerhéj a "próbálja" gombra, minden egyes kódblokk jobb felső sarkában található keresztül használja.
    - [Telepítse a legújabb verziót a CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 vagy újabb verzió) Ha a helyi CLI-konzollal szeretné. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>A rendszer azonosító hozzárendelve

Ebben a szakaszban megismerheti, hogyan engedélyezheti vagy letilthatja a rendszer az Azure parancssori felület használatával Azure virtuális gép identitásának rendelt.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Identitás hozzárendelése az Azure virtuális gép létrehozása során rendszer engedélyezése

Egy Azure virtuális gép létrehozása a rendszer a hozzárendelt engedélyezve identitás:

1. Az Azure parancssori felület a helyi konzol használata, először jelentkezzen be az Azure használatával [az bejelentkezési](/cli/azure/reference-index#az_login). Használjon olyan fiókot, amelybe szeretne telepíteni a virtuális Gépet az Azure-előfizetéshez társított:

   ```azurecli-interactive
   az login
   ```

2. Hozzon létre egy [erőforráscsoport](../../azure-resource-manager/resource-group-overview.md#terminology) elszigetelési és telepítéséhez, a virtuális Gépet, és azok kapcsolódó erőforrásait, használatával [az csoport létrehozása](/cli/azure/group/#az_group_create). Ezt a lépést kihagyhatja, ha már van erőforráscsoport szeretné használni:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Hozzon létre egy virtuális gép az [az virtuális gép létrehozása](/cli/azure/vm/#az_vm_create). Az alábbi példakód létrehozza a virtuális gépek nevű *myVM* hozzárendelt rendszer identitású, amint azt a `--assign-identity` paraméter. A `--admin-username` és `--admin-password` paraméterek fiókot ad meg a rendszergazda felhasználó nevét és jelszavát virtuálisgép-bejelentkezés. Ezeket az értékeket a környezetének megfelelő frissítése: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>A rendszer egy meglévő Azure virtuális gép identitásának hozzárendelt engedélyezése

Ha a hozzárendelt rendszeridentitás egy meglévő virtuális gépen engedélyezni kell:

1. Az Azure parancssori felület a helyi konzol használata, először jelentkezzen be az Azure használatával [az bejelentkezési](/cli/azure/reference-index#az_login). Az Azure-előfizetés, amely tartalmazza a virtuális Géphez társított olyan fiókot használjon. Emellett győződjön meg arról, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi a virtuális Gépen, például a "Virtuális gép közreműködő" írási engedélyekkel:

   ```azurecli-interactive
   az login
   ```

2. Használjon [hozzárendelni a virtuálisgép-azonosítója az](/cli/azure/vm/identity/#az_vm_identity_assign) rendelkező a `identity assign` paranccsal engedélyezheti a hozzárendelt rendszeridentitás egy meglévő virtuális gépre:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>A rendszer az Azure virtuális gép identitásának hozzárendelt letiltása

> [!NOTE]
> Szolgáltatásidentitás felügyelt virtuális gépről letiltása jelenleg nem támogatott. Időközben válthat rendszer rendelve, és a felhasználó hozzárendelt identitások segítségével.

Ha egy virtuális gép, amely már nincs szüksége a rendszer identitás hozzárendelt, de továbbra is hozzá kell a felhasználói identitások, a következő paranccsal:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```
Az MSI-Virtuálisgép-bővítmény eltávolítása felhasználói `-n ManagedIdentityExtensionForWindows` vagy `-n ManagedIdentityExtensionForLinux` kapcsoló (virtuális gép függően) [az virtuálisgép-bővítmény törlése](https://docs.microsoft.com/cli/azure/vm/#assign-identity):

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

## <a name="user-assigned-identity"></a>Felhasználói hozzárendelt azonosító

Ebben a szakaszban megismerheti, hogyan hozzáadása és eltávolítása a felhasználó identitása rendelve egy Azure virtuális Gépen, Azure parancssori felület használatával a rendszer.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>Rendelje hozzá a felhasználóhoz rendelt identitás egy Azure virtuális gép létrehozása közben

Ez a szakasz végigvezeti egy felhasználó lehet hozzárendelve identitás hozzárendelése a virtuális gépek létrehozása. Ha már rendelkezik egy használni kívánt virtuális Gépet, hagyja ki ezt a szakaszt, és folytassa a Tovább gombra.

1. Ezt a lépést kihagyhatja, ha már rendelkezik egy használni kívánt erőforráscsoportot. Hozzon létre egy [erőforráscsoport](~/articles/azure-resource-manager/resource-group-overview.md#terminology) elszigetelési és telepítéséhez, az MSI-fájl használatával [az csoport létrehozása](/cli/azure/group/#az_group_create). Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>` és `<LOCATION>` paraméterértékeket a saját értékekkel. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Hozzon létre egy felhasználó lehet hozzárendelve identitás használatával [az identitás létrehozása](/cli/azure/identity#az_identity_create).  A `-g` paraméter határozza meg az erőforráscsoportot, ahol a felhasználó identitása jön létre, és a `-n` paraméter határozza meg a nevét.
    
    > [!IMPORTANT]
    > A felhasználói identitások a különleges karakterek (pl. aláhúzásjel) nevében létrehozása jelenleg nem támogatott. Alfanumerikus karaktereket használja. Biztonsági frissítések ellenőrzése.  További információ: [– gyakori kérdések és ismert problémák](known-issues.md)

    ```azurecli-interactive
    az identity create -g myResourceGroup -n myUserAssignedIdentity
    ```
A válasz tartalmazza a létrehozott, az alábbihoz hasonló a felhasználói identitás részletei. A felhasználó identitása rendelt erőforrás-azonosítójának értéke a következő lépésben szolgál.

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

3. Hozzon létre egy virtuális gép az [az virtuális gép létrehozása](/cli/azure/vm/#az_vm_create). Az alábbi példa létrehoz egy virtuális Gépet az új felhasználó lehet hozzárendelve identitás által megadott társított a `--assign-identity` paraméter. Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>`, és `<MSI ID>` paraméterértékeket a saját értékekkel. A `<MSI ID>`, használja a felhasználó identitása erőforrás `id` az előző lépésben létrehozott tulajdonság: 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Rendelje hozzá egy a felhasználói identitás egy meglévő Azure virtuális Gépen

1. Hozzon létre egy felhasználó lehet hozzárendelve identitás használatával [az identitás létrehozása](/cli/azure/identity#az-identity-create).  A `-g` paraméter határozza meg az erőforráscsoportot, ahol a felhasználó identitása jön létre, és a `-n` paraméter határozza meg a nevét. Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>` és `<MSI NAME>` paraméterértékeket a saját értékekkel:

    > [!IMPORTANT]
    > A felhasználói identitások a különleges karakterek (pl. aláhúzásjel) nevében létrehozása jelenleg nem támogatott. Alfanumerikus karaktereket használja. Biztonsági frissítések ellenőrzése.  További információ: [– gyakori kérdések és ismert problémák](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
A válasz a felhasználó hozott létre, az alábbihoz hasonló MSI részleteit tartalmazza. Az erőforrás `id` a felhasználó identitásának rendelt érték a következő lépésben lesz szükség.

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

2. A felhasználó identitásának hozzárendelése a virtuális gép használatával [virtuálisgép-azonosítója az hozzárendelése](/cli/azure/vm#az-vm-identity-assign). Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>` és `<VM NAME>` paraméterértékeket a saját értékekkel. A `<MSI ID>` lesz a felhasználó identitása erőforrás `id` tulajdonság, mert az előző lépésben létrehozott:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>Egy Azure virtuális gép identitásának hozzárendelt felhasználó eltávolítása

> [!NOTE]
> Az összes hozzárendelt felhasználói azonosítók eltávolítása a virtuális gép jelenleg nem támogatott, kivéve, ha az identitás hozzárendelt rendszer.

Ha a virtuális gép több hozzárendelt felhasználói identitást, is távolítja el az utolsó használatával [távolítsa el a virtuálisgép-azonosítója az](/cli/azure/vm#az-vm-identity-remove). Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>` és `<VM NAME>` paraméterértékeket a saját értékekkel. A `<MSI NAME>` a felhasználó identitása lesz `name` tulajdonság, amely a virtuális gép használatával az identitási szakaszban találhatók `az vm show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
```
Ha a virtuális Gépet a hozzárendelt rendszer és a felhasználói identitások is rendelkezik, eltávolíthatja a minden felhasználó hozzárendelt identitások váltásával hozzárendelt csak a rendszer. Használja az alábbi parancsot:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null 
```

## <a name="related-content"></a>Kapcsolódó tartalom
- [Felügyelt Szolgáltatásidentitás áttekintése](overview.md)
- A teljes Azure virtuális gépek létrehozására – Gyorsútmutatók lásd: 
  - [Windows virtuális gép létrehozása a parancssori felület](../../virtual-machines/windows/quick-create-cli.md)  
  - [Linux virtuális gép létrehozása a parancssori felület](../../virtual-machines/linux/quick-create-cli.md) 

















