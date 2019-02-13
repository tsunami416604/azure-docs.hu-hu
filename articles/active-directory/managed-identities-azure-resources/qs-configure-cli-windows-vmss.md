---
title: Az Azure CLI-vel az Azure VMSS rendszer és a felhasználó által hozzárendelt felügyelt identitások konfigurálása
description: Lépés útmutató rendszer és a felhasználó által hozzárendelt felügyelt identitások konfigurálja az Azure VMSS, Azure CLI használatával.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6457a04419012ef80432d8603caae21bbacde59b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56170958"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Az Azure-erőforrások felügyelt identitások konfigurálása a virtuális gép méretezési csoportban Azure CLI-vel

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt identitások az Azure-erőforrások Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebből a cikkből megismerheti, hogyan hajthat végre a következő felügyelt identitások Azure-erőforrások műveletek a egy Azure virtuális gép méretezési beállítása (VMSS), az Azure CLI használatával:
- Engedélyezheti és tilthatja le a rendszer által hozzárendelt felügyelt identitás az Azure VMSS
- Hozzáadhat és eltávolíthat egy felhasználó által hozzárendelt felügyelt identitás az Azure VMSS


## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség a rendszer által hozzárendelt, és a felhasználó által hozzárendelt felügyelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ez a cikk a felügyeleti műveleteket hajt végre, a fiók az alábbi Azure szerepkör-alapú access control-hozzárendelések van szüksége:

    > [!NOTE]
    > Nincsenek további Azure AD directory szerepkör-hozzárendelések megadása kötelező.

    - [Virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) egy virtuálisgép-méretezési csoport létrehozása és engedélyezése és rendszer-és/vagy felhasználó által hozzárendelt felügyelt identitás eltávolítása egy virtuálisgép-méretezési csoportot.
    - [Felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) hozhat létre felhasználó által hozzárendelt szerepkör felügyelt identitás.
    - [Felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör hozzárendelése és eltávolítása, felhasználó által hozzárendelt felügyelt identitás, a kezdő és a egy virtuálisgép-méretezési csoportot.
- Három lehetősége van a CLI-példaszkriptek futtatásához:
    - Használat [Azure Cloud Shell](../../cloud-shell/overview.md) az Azure Portalon (lásd a következő szakaszban).
    - Használja a beágyazott Azure Cloud Shell-t a "Kipróbálom" gomb, mindegyik blokk jobb felső sarkában található.
    - [Az Azure CLI legújabb verziójának telepítéséhez](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13-as vagy újabb) Ha inkább a helyi CLI-konzol használatával. 
      
      > [!NOTE]
      > A parancsok frissítve lett-e, hogy a legújabb kiadása a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebben a szakaszban megismerheti, hogyan engedélyezheti és tilthatja le a rendszer által hozzárendelt felügyelt identitás az az Azure vmss-hez az Azure CLI használatával.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése egy Azure-beli virtuálisgép-méretezési készlet létrehozása során

A felügyelt rendszer által hozzárendelt identitások engedélyezve van a virtuális gép méretezési csoport létrehozása:

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az-login) paranccsal jelentkezzen be az Azure-ba. Egy fiók használatával, amely alapján, amely a virtuálisgép-méretezési csoport üzembe szeretné az Azure-előfizetés társítva van:

   ```azurecli-interactive
   az login
   ```

2. Hozzon létre egy [erőforráscsoport](../../azure-resource-manager/resource-group-overview.md#terminology) tartalmazási és telepítéséhez, a virtuálisgép-méretezési csoportot és az összes kapcsolódó erőforrás, használatával [az csoport létrehozása](/cli/azure/group/#az-group-create). Ezt a lépést kihagyhatja, ha már rendelkezik egy használni kívánt erőforráscsoportot:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Hozzon létre egy virtuálisgép-méretezési csoportot a [az vmss létrehozásához](/cli/azure/vmss/#az-vmss-create) . Az alábbi példa létrehoz egy virtuálisgép-méretezési csoport nevű *myVMSS* a rendszer által hozzárendelt felügyelt identitást, kérésének megfelelően a `--assign-identity` paraméter. Az `--admin-username` és `--admin-password` paraméterek adják meg a virtuális gép bejelentkeztetéséhez tartozó rendszergazdanevet és -jelszót. A környezetnek megfelelően frissítse ezeket az értékeket: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Egy meglévő Azure-beli virtuálisgép-méretezési csoportot a felügyelt identitás alapértelmezett engedélyezése

Ha szeretné engedélyezni a rendszer által hozzárendelt felügyelt identitást egy meglévő Azure-beli virtuálisgép-méretezési csoportot:

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az-login) paranccsal jelentkezzen be az Azure-ba. Az Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoportba tartozó fiókot használnia.

   ```azurecli-interactive
   az login
   ```

2. Használat [az vmss-identitási hozzárendelése](/cli/azure/vmss/identity/#az-vmss-identity-assign) parancsot a rendszer által hozzárendelt engedélyezéséhez felügyelt identitás meglévő virtuális géphez:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>A felügyelt identitás tiltsa le a rendszer által hozzárendelt egy Azure-beli virtuálisgép-méretezési csoportot

Ha egy virtuális gép méretezési csoportot, amely már nincs szüksége a rendszer által hozzárendelt felügyelt identitást, de továbbra is a felügyelt identitásokból felhasználó által hozzárendelt van szüksége, használja a következő parancsot:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Ha már nincs szüksége a felügyelt identitás rendszer által hozzárendelt virtuális gépet, és nem felügyelt felhasználó által hozzárendelt identitások rendelkezik, használja a következő parancsot:

> [!NOTE]
> Az érték `none` megkülönbözteti a kis-és nagybetűket. Kisbetűnek kell lennie. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

A felügyelt identitások Azure-erőforrások Virtuálisgép-bővítmény (elavult. január 2019 a tervezett) eltávolításához használja [eltávolítása az vmss-identitási](/cli/azure/vmss/identity/) paranccsal távolítható el, a rendszer által hozzárendelt egy VMSS felügyelt identitás:

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

## <a name="user-assigned-managed-identity"></a>felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban megismerheti, hogyan engedélyezheti és távolítsa el az Azure CLI használatával felügyelt felhasználó által hozzárendelt identitás.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Egy felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy virtuálisgép-méretezési készlet létrehozása során

Ez a szakasz végigvezeti egy VMSS létrehozásának és a egy felhasználó által hozzárendelt felügyelt identitás hozzárendelését a vmss-hez. Ha már rendelkezik egy használni kívánt VMSS, kihagyhatja ezt a szakaszt, és folytassa a következő.

1. Ezt a lépést kihagyhatja, ha már rendelkezik egy használni kívánt erőforráscsoportot. Hozzon létre egy [erőforráscsoport](~/articles/azure-resource-manager/resource-group-overview.md#terminology) tartalmazási és telepítéséhez, a felhasználó által hozzárendelt felügyelt identitást, használatával [az csoport létrehozása](/cli/azure/group/#az-group-create). Ne felejtse el a `<RESOURCE GROUP>` és `<LOCATION>` paraméterek értékeit a saját értékeire cserélni. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az [az identity create](/cli/azure/identity#az-identity-create) paranccsal.  A `-g` paraméter adja meg azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt felügyelt identitás létre lesz hozva, a `-n` paraméter pedig annak nevét határozza meg. Ne felejtse el a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterek értékeit a saját értékeire cserélni:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   A válasz tartalmazza a létrehozott, az alábbihoz hasonló felügyelt felhasználó által hozzárendelt identitás részleteit. Az erőforrás `id` felügyelt felhasználó által hozzárendelt identitások rendelt érték szerepel a következő lépéssel.

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

3. Hozzon létre egy VMSS [az vmss létrehozásához](/cli/azure/vmss/#az-vmss-create). A következő példában létrehozunk egy új felhasználó által hozzárendelt felügyelt identitáshoz társított, által megadott VMSS a `--assign-identity` paraméter. A `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` és `<USER ASSIGNED IDENTITY>` paraméterek értékét mindenképp helyettesítse be a saját értékeivel. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Egy felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy meglévő virtuálisgép-méretezési csoportot

1. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az [az identity create](/cli/azure/identity#az-identity-create) paranccsal.  A `-g` paraméter adja meg azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt felügyelt identitás létre lesz hozva, a `-n` paraméter pedig annak nevét határozza meg. Ne felejtse el a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterek értékeit a saját értékeire cserélni:

    > [!IMPORTANT]
    > A speciális karakterek (például aláhúzásjelet) nevét a felhasználó által hozzárendelt felügyelt identitások létrehozása jelenleg nem támogatott. Adja meg az alfanumerikus karaktereket használjon. Térjen vissza frissítésekért.  További információ: [– gyakori kérdések és ismert problémák](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
A válasz tartalmazza a létrehozott, az alábbihoz hasonló felügyelt felhasználó által hozzárendelt identitás részleteit.

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

2. A felhasználó által hozzárendelt felügyelt identitás hozzárendelése a VMSS használatával [az vmss-identitási hozzárendelése](/cli/azure/vmss/identity). Ne felejtse el a `<RESOURCE GROUP>` és `<VMSS NAME>` paraméterek értékeit a saját értékeire cserélni. A `<USER ASSIGNED IDENTITY>` a felhasználó által hozzárendelt identitás erőforrás `name` tulajdonságot, mert az előző lépésben létrehozott:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VMSS NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Egy Azure-beli virtuálisgép-méretezési csoportot egy felhasználó által hozzárendelt felügyelt identitás eltávolítása

Egy felhasználó által hozzárendelt felügyelt identitás eltávolítása egy virtuális gép méretezési készlet használata [eltávolítása az vmss-identitási](/cli/azure/vmss/identity#az-vmss-identity-remove). Ha ez az egyetlen felhasználó által hozzárendelt felügyelt virtuálisgép-méretezési csoporthoz rendelt identitást `UserAssigned` típus azonosító értékét törlődni fog.  Ne felejtse el a `<RESOURCE GROUP>` és `<VMSS NAME>` paraméterek értékeit a saját értékeire cserélni. A `<USER ASSIGNED IDENTITY>` lesz a felhasználó által hozzárendelt felügyelt identitás `name` tulajdonság, amely a virtuálisgép-méretezési csoportot a identitás szakaszában található `az vmss identity show`:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VMSS NAME> --identities <USER ASSIGNED IDENTITY>
```

Ha a virtuálisgép-méretezési készlet nem rendelkezik egy rendszer által hozzárendelt felügyelt identitást, és szeretné távolítsa el az összes felhasználó által hozzárendelt felügyelt identitást, használja a következő parancsot:

> [!NOTE]
> Az érték `none` megkülönbözteti a kis-és nagybetűket. Kisbetűnek kell lennie.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Ha a virtuálisgép-méretezési rendelkezik, mindkét rendszer által hozzárendelt, és a felügyelt identitások felhasználó által hozzárendelt, eltávolíthatja az összes felhasználó által hozzárendelt identitások egyetlen, a rendszer által hozzárendelt felügyelt identitás használatára vált át. Használja az alábbi parancsot:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>További lépések

- [Felügyelt identitások Azure-erőforrások – áttekintés](overview.md)
- A teljes Azure virtuálisgép-méretezési csoport létrehozása a rövid útmutatóban, olvassa el: 

  - [Egy virtuálisgép-méretezési készlet létrehozása a CLI-vel](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















