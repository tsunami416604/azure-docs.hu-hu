---
title: Rendszer és a felhasználó konfigurálása az Azure parancssori felület használatával Azure VMSS identitást hozzárendelve
description: Lépésről lépésre rendszer és a felhasználó konfigurálására vonatkozó útmutatásokat hozzárendelt identitások az Azure VMSS az Azure parancssori felület használatával.
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
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: 667d62f4ad7c8e4853204c238ec889935daf82ba
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Konfigurálja a virtuálisgép-méretezési készlet által felügyelt szolgáltatás Identity (MSI) Azure parancssori felület használatával

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt Szolgáltatásidentitást az Azure Active Directoryban automatikusan felügyelt identitással Azure szolgáltatásokat biztosít. Ez az identitás, amely támogatja az Azure AD-alapú hitelesítés, anélkül, hogy a hitelesítő adatokat a kódban a szolgáltatással való hitelesítésre szolgáló használhatja. 

Ebből a cikkből megismerheti, hogyan hajthat végre a következő felügyelt Szolgáltatásidentitás műveletek a egy Azure virtuális gép méretezési beállítása (VMSS), az Azure parancssori felület használatával:
- Engedélyezheti vagy letilthatja a rendszer egy Azure VMSS identitásának hozzárendelve
- Hozzáadhat és eltávolíthat egy felhasználó lehet hozzárendelve egy Azure VMSS identitása


## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri a felügyelt Szolgáltatásidentitás, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség egy hozzárendelt rendszer és a felhasználói identitás](overview.md#how-does-it-work)**.
- Ha még nem telepítette az Azure-fiók [regisztráljon egy ingyenes fiókot](https://azure.microsoft.com/free/) folytatása előtt.

A parancssori felület parancsfájl példák futtatásához három lehetőség közül választhat:

- Használjon [Azure Cloud rendszerhéj](../../cloud-shell/overview.md) Azure-portálról (lásd a következő szakaszt).
- A beágyazott Azure Cloud rendszerhéj a "próbálja" gombra, minden egyes kódblokk jobb felső sarkában található keresztül használja.
- [Telepítse a legújabb verziót a CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 vagy újabb verzió) Ha a helyi CLI-konzollal szeretné. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>A rendszer azonosító hozzárendelve

Ebben a szakaszban megismerheti, hogyan engedélyezheti vagy letilthatja a rendszer identitás hozzárendelése az Azure VMSS Azure parancssori felület használatával.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Egy Azure virtuálisgép-méretezési csoport létrehozásakor hozzárendelt rendszeridentitás engedélyezése

Hozzon létre egy virtuálisgép-méretezési állítsa be a rendszerrel identitás engedélyezve van hozzárendelve:

1. Az Azure parancssori felület a helyi konzol használata, először jelentkezzen be az Azure használatával [az bejelentkezési](/cli/azure/reference-index#az_login). Használjon olyan fiókot, amelybe szeretne telepíteni a virtuálisgép-méretezési csoport Azure-előfizetéssel társított:

   ```azurecli-interactive
   az login
   ```

2. Hozzon létre egy [erőforráscsoport](../../azure-resource-manager/resource-group-overview.md#terminology) elszigetelési és telepítéséhez, a virtuálisgép-méretezési csoport és azok kapcsolódó erőforrásait, használatával [az csoport létrehozása](/cli/azure/group/#az_group_create). Ezt a lépést kihagyhatja, ha már rendelkezik egy olyan erőforráscsoport, szeretné inkább:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Hozzon létre egy virtuálisgép-méretezési segítségével [az vmss létrehozása](/cli/azure/vmss/#az_vmss_create) . Az alábbi példa létrehoz egy virtuálisgép-méretezési beállítása a nevesített *myVMSS* hozzárendelt rendszer identitású, amint azt a `--assign-identity` paraméter. A `--admin-username` és `--admin-password` paraméterek fiókot ad meg a rendszergazda felhasználó nevét és jelszavát virtuálisgép-bejelentkezés. Ezeket az értékeket a környezetének megfelelő frissítése: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Egy meglévő Azure virtuálisgép-méretezési csoport hozzárendelt rendszer identitásának engedélyezése

Ha egy meglévő Azure virtuálisgép-méretezési csoport hozzárendelt rendszer identitásának engedélyezni kell:

1. Az Azure parancssori felület a helyi konzol használata, először jelentkezzen be az Azure használatával [az bejelentkezési](/cli/azure/reference-index#az_login). Az Azure-előfizetés, amely tartalmazza a virtuálisgép-méretezési csoport társított olyan fiókot használjon.

   ```azurecli-interactive
   az login
   ```

2. Használjon [az vmss identitás hozzárendelése](/cli/azure/vmss/identity/#az_vmss_identity_assign) parancsot annak engedélyezése egy hozzárendelt rendszeridentitás egy meglévő virtuális gépre:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Tiltsa le a rendszer hozzárendelt identitás egy Azure virtuálisgép-méretezési csoport

> [!NOTE]
> A virtuálisgép-méretezési készlet által felügyelt Szolgáltatásidentitás letiltása jelenleg nem támogatott. Időközben válthat rendszer rendelve, és a felhasználó hozzárendelt identitások segítségével. Biztonsági frissítések ellenőrzése.

Ha egy virtuálisgép-méretezési csoport, amely már nincs szüksége a hozzárendelt identitás rendszert, de továbbra is hozzá kell a felhasználói identitások, hajtsa végre a következő parancsot:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='UserAssigned' 
```

Az MSI-Virtuálisgép-bővítmény eltávolításához használja [az vmss identitás eltávolítása](/cli/azure/vmss/identity/#az_vmss_remove_identity) parancs futtatásával távolítsa el a hozzárendelt rendszeridentitás egy VMSS:

   ```azurecli-interactive
   az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
   ```

## <a name="user-assigned-identity"></a>A felhasználói identitás

Ebben a szakaszban megismerheti, hogyan engedélyezheti, és távolítsa el az Azure parancssori felület használatával hozzárendelt felhasználói azonosítót.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Rendelje hozzá a felhasználóhoz rendelt identitás egy Azure VMSS létrehozása közben

Ez a szakasz végigvezeti egy VMSS létrehozása és hozzárendelése egy felhasználó lehet hozzárendelve a VMSS identitás. Ha már rendelkezik egy használni kívánt VMSS, hagyja ki ezt a szakaszt, és folytassa a Tovább gombra.

1. Ezt a lépést kihagyhatja, ha már rendelkezik egy használni kívánt erőforráscsoportot. Hozzon létre egy [erőforráscsoport](~/articles/azure-resource-manager/resource-group-overview.md#terminology) a felhasználó személyazonosságát, telepítéséhez és elszigetelési használatával [az csoport létrehozása](/cli/azure/group/#az_group_create). Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>` és `<LOCATION>` paraméterértékeket a saját értékekkel. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Hozzon létre egy felhasználó lehet hozzárendelve identitás használatával [az identitás létrehozása](/cli/azure/identity#az-identity-create).  A `-g` paraméter határozza meg az erőforráscsoportot, ahol a felhasználó identitása jön létre, és a `-n` paraméter határozza meg a nevét. Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterértékeket a saját értékekkel:

    > [!IMPORTANT]
    > A felhasználói identitások létrehozása csak alfanumerikus és kötőjel (0 – 9 vagy a-z vagy A-Z vagy -) karaktereket. Emellett nevét kell korlátozni a virtuális gép/VMSS helyes működéséhez hozzárendelés 24 karakter hosszúságot. Biztonsági frissítések ellenőrzése. További információ: [– gyakori kérdések és ismert problémák](known-issues.md)


    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
A válasz tartalmazza a létrehozott, az alábbihoz hasonló a felhasználói identitás részletei. Az erőforrás `id` a felhasználó identitásának rendelt érték a következő lépésben lesz szükség.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Hozzon létre egy VMSS az [az vmss létrehozása](/cli/azure/vmss/#az-vmss-create). Az alábbi példa létrehoz egy leírtak szerint az új felhasználó lehet hozzárendelve identitás társított VMSS a `--assign-identity` paraméter. Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>`, és `<USER ASSIGNED IDENTITY ID>` paraméterértékeket a saját értékekkel. A `<USER ASSIGNED IDENTITY ID>`, használja a felhasználó identitása erőforrás `id` az előző lépésben létrehozott tulajdonság: 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY ID>
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Rendelje hozzá egy a felhasználói identitás egy meglévő Azure virtuális Gépen

1. Hozzon létre egy felhasználó lehet hozzárendelve identitás használatával [az identitás létrehozása](/cli/azure/identity#az-identity-create).  A `-g` paraméter határozza meg az erőforráscsoportot, ahol a felhasználó identitása jön létre, és a `-n` paraméter határozza meg a nevét. Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterértékeket a saját értékekkel:

    > [!IMPORTANT]
    > A felhasználói identitások a különleges karakterek (pl. aláhúzásjel) nevében létrehozása jelenleg nem támogatott. Alfanumerikus karaktereket használja. Biztonsági frissítések ellenőrzése.  További információ: [– gyakori kérdések és ismert problémák](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
A válasz tartalmazza a létrehozott, az alábbihoz hasonló a felhasználói identitás részletei. Az erőforrás `id` a felhasználó identitásának rendelt érték a következő lépésben lesz szükség.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Rendelje hozzá a felhasználó identitását a VMSS használatával [az vmss identitás hozzárendelése](/cli/azure/vmss/identity#az_vm_assign_identity). Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>` és `<VM NAME>` paraméterértékeket a saját értékekkel. A `<USER ASSIGNED IDENTITY ID>` lesz a felhasználó identitása erőforrás `id` tulajdonság, mert az előző lépésben létrehozott:

    ```azurecli-interactive
    az vmss assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>Egy Azure VMSS identitás hozzárendelt felhasználó eltávolítása

> [!NOTE]
>  Az összes hozzárendelt felhasználói azonosítók eltávolítása egy virtuálisgép-méretezési csoportban jelenleg nem támogatott, kivéve, ha az identitás hozzárendelt rendszer. 

Ha a VMSS több hozzárendelt felhasználói identitást, is távolítja el az utolsó használatával [az vmss identitás eltávolítása](/cli/azure/vmss/identity#az-vmss-identity-remove). Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>` és `<VM NAME>` paraméterértékeket a saját értékekkel. A `<MSI NAME>` a virtuális gép használatával az identitási szakaszban találhatók a felhasználó identitása name tulajdonság `az vm show`:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
```
Ha a VMSS a hozzárendelt rendszer és a felhasználói identitások is rendelkezik, eltávolíthatja az összes felhasználó hozzárendelt identitások váltásával hozzárendelt csak a rendszer. Használja az alábbi parancsot: 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null
```

## <a name="next-steps"></a>További lépések

- [Felügyelt Szolgáltatásidentitás áttekintése](overview.md)
- A teljes Azure-beli virtuálisgép-méretezési létrehozása gyors üzembe helyezés, olvassa el: 

  - [Hozzon létre egy virtuálisgép-méretezési csoport parancssori felület](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















