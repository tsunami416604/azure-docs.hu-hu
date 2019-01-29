---
title: Rendszer- és felhasználó által hozzárendelt felügyelt identitások konfigurálása-beli virtuális gépen az Azure CLI használatával
description: Lépés által lépés utasításokat a rendszer és a felhasználó által hozzárendelt felügyelt identitások-beli virtuális gépen az Azure CLI használatával.
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
ms.date: 11/10/2018
ms.author: priyamo
ms.openlocfilehash: a2e42b165596d26672ee22813e53d02ca0e3a7e9
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55195664"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Felügyelt identitások az Azure-erőforrások konfigurálása-beli virtuális gépen az Azure CLI használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt identitások az Azure-erőforrások Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebben a cikkben az Azure CLI használatával megismerheti, hogyan hajthat végre a következő felügyelt identitások az Azure-erőforrások operations-beli virtuális gépen:

- Engedélyezheti és tilthatja le a rendszer által hozzárendelt felügyelt identitás-beli virtuális gépen
- Hozzáadhat és eltávolíthat a felhasználó által hozzárendelt felügyelt identitás-beli virtuális gépen

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség a rendszer által hozzárendelt, és a felhasználó által hozzárendelt felügyelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Három lehetősége van a CLI-példaszkriptek futtatásához:
    - Használat [Azure Cloud Shell](../../cloud-shell/overview.md) az Azure Portalon (lásd a következő szakaszban).
    - Használja a beágyazott Azure Cloud Shell-t a "Kipróbálom" gomb, mindegyik blokk jobb felső sarkában található.
    - [Az Azure CLI legújabb verziójának telepítéséhez](https://docs.microsoft.com/cli/azure/install-azure-cli) Ha inkább a helyi CLI-konzol használatával. 
      
      > [!NOTE]
      > A parancsok frissítve lett-e, hogy a legújabb kiadása a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli).     

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebben a szakaszban megismerheti, hogyan engedélyezheti és tilthatja le a rendszer által hozzárendelt felügyelt identitás-beli virtuális gépen az Azure CLI használatával.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése egy Azure virtuális gép létrehozása során

Egy Azure virtuális gép létrehozása felügyelt rendszer által hozzárendelt identitással engedélyezve van, a fióknak rendelkeznie kell a [virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelés.  Nincsenek további Azure AD-címtár szerepkör-hozzárendelések szükségesek.

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az-login) paranccsal jelentkezzen be az Azure-ba. A használni kívánt fiók azon Azure-előfizetéshez legyen hozzárendelve, amely alatt üzembe fogja helyezni a virtuális gépet:

   ```azurecli-interactive
   az login
   ```

2. Az [az group create](/cli/azure/group/#az-group-create) paranccsal hozzon létre egy [erőforráscsoportot](../../azure-resource-manager/resource-group-overview.md#terminology) a virtuális gép és az ahhoz kapcsolódó erőforrások elkülönítéséhez és üzembe helyezéséhez. Ezt a lépést kihagyhatja, ha inkább egy meglévő erőforráscsoportot kíván használni:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm/#az-vm-create) paranccsal. A következő példában létrehozunk egy nevű virtuális Gépet *myVM* a rendszer által hozzárendelt felügyelt identitást, kérésének megfelelően a `--assign-identity` paraméter. Az `--admin-username` és `--admin-password` paraméterek adják meg a virtuális gép bejelentkeztetéséhez tartozó rendszergazdanevet és -jelszót. A környezetnek megfelelően frissítse ezeket az értékeket: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>A meglévő Azure virtuális gép felügyelt identitás alapértelmezett engedélyezése

Ahhoz, hogy a rendszer által hozzárendelt felügyelt identitás, a virtuális gép, a fióknak rendelkeznie kell a [virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelés.  Nincsenek további Azure AD-címtár szerepkör-hozzárendelések szükségesek.

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az-login) paranccsal jelentkezzen be az Azure-ba. Használjon, amely tartalmazza a virtuális gép Azure-előfizetéssel társított fiókot.

   ```azurecli-interactive
   az login
   ```

2. Használat [az virtuális gép identitás hozzárendelése](/cli/azure/vm/identity/#az-vm-identity-assign) az a `identity assign` parancsot a meglévő virtuális géphez a rendszer által hozzárendelt identitás engedélyezése:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Tiltsa le a rendszer által hozzárendelt identitás Azure virtuális gépből

A virtuális gép felügyelt identitás alapértelmezett letiltásához a fióknak rendelkeznie kell a [virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelés.  Nincsenek további Azure AD-címtár szerepkör-hozzárendelések szükségesek.

Ha egy virtuális gépet, a rendszer által hozzárendelt identitás már nincs szüksége, de továbbra is a felhasználó által hozzárendelt identitások van szüksége, használja a következő parancsot:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Ha egy virtuális gépet, amely a rendszer által hozzárendelt identitás már nincs szüksége van, és nem felhasználó által hozzárendelt identitások, a következő paranccsal:

> [!NOTE]
> Az érték `none` megkülönbözteti a kis-és nagybetűket. Kisbetűnek kell lennie. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```

Távolítsa el a felügyelt identitását az Azure-erőforrások Virtuálisgép-bővítmény (tervezett elavult a január 2019 esetében), a felhasználó `-n ManagedIdentityExtensionForWindows` vagy `-n ManagedIdentityExtensionForLinux` kapcsoló (virtuális gép függően) és [vm-bővítmény törlése az](https://docs.microsoft.com/cli/azure/vm/#assign-identity):

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

## <a name="user-assigned-managed-identity"></a>felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban megtudhatja hogyan adhat hozzá, és távolítsa el a felhasználó által hozzárendelt felügyelt identitás az Azure CLI használatával Azure virtuális.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Egy felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy Azure virtuális gép létrehozása során

Egy felhasználó által hozzárendelt identitással hozzárendelése egy virtuális Gépet annak létrehozásakor, a fióknak rendelkeznie kell a [virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) és [felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör-hozzárendeléseket. Nincsenek további Azure AD-címtár szerepkör-hozzárendelések szükségesek.

1. Ezt a lépést kihagyhatja, ha már rendelkezik egy használni kívánt erőforráscsoportot. Hozzon létre egy [erőforráscsoport](~/articles/azure-resource-manager/resource-group-overview.md#terminology) tartalmazási és telepítéséhez, a felhasználó által hozzárendelt felügyelt identitást, használatával [az csoport létrehozása](/cli/azure/group/#az-group-create). Ne felejtse el a `<RESOURCE GROUP>` és `<LOCATION>` paraméterek értékeit a saját értékeire cserélni. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az [az identity create](/cli/azure/identity#az-identity-create) paranccsal.  A `-g` paraméter adja meg azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt felügyelt identitás létre lesz hozva, a `-n` paraméter pedig annak nevét határozza meg.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   A válasz tartalmazza a létrehozott, az alábbihoz hasonló felügyelt felhasználó által hozzárendelt identitás részleteit. A felhasználó által hozzárendelt felügyelt identitás hozzárendelt erőforrás-azonosítójának értéke a következő lépésben szolgál.

   ```json
   {
       "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
       "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<myUserAssignedIdentity>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
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

3. Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm/#az-vm-create) paranccsal. Az alábbi példa létrehoz egy virtuális Gépet, az identitáshoz tartozó új felhasználó által hozzárendelt, azokat a `--assign-identity` paraméter. A `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` és `<USER ASSIGNED IDENTITY NAME>` paraméterek értékét mindenképp helyettesítse be a saját értékeivel. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Egy felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy meglévő Azure virtuális Gépen

A felhasználó által hozzárendelt identitás hozzárendelése egy virtuális Gépet, a fióknak rendelkeznie kell a [virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) és [felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör-hozzárendeléseket. Nincsenek további Azure AD-címtár szerepkör-hozzárendelések szükségesek.

1. Hozzon létre egy felhasználóhoz rendelt identitást az [az identity create](/cli/azure/identity#az-identity-create) paranccsal.  A `-g` paraméter adja meg az erőforráscsoport, amelyben a felhasználó által hozzárendelt identitás jön létre, és a `-n` paraméter adja meg a nevét. Ne felejtse el a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterek értékeit a saját értékeire cserélni:

    > [!IMPORTANT]
    > A speciális karakterek (például aláhúzásjelet) nevét a felhasználó által hozzárendelt felügyelt identitások létrehozása jelenleg nem támogatott. Adja meg az alfanumerikus karaktereket használjon. Térjen vissza frissítésekért.  További információ: [– gyakori kérdések és ismert problémák](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   A válasz tartalmazza a létrehozott, az alábbihoz hasonló felügyelt felhasználó által hozzárendelt identitás részleteit. 

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

2. A felhasználó által hozzárendelt identitás hozzárendelése a virtuális gép használatával [az virtuális gép identitás hozzárendelése](/cli/azure/vm#az-vm-identity-assign). Ne felejtse el a `<RESOURCE GROUP>` és `<VM NAME>` paraméterek értékeit a saját értékeire cserélni. A `<USER ASSIGNED IDENTITY NAME>` a felhasználó által hozzárendelt felügyelt identitás erőforrás `name` tulajdonságot, mert az előző lépésben létrehozott:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Távolítsa el a felhasználó által hozzárendelt felügyelt identitás Azure virtuális gépből

Egy felhasználó által hozzárendelt identitással virtuális Géphez való eltávolításához a fióknak rendelkeznie kell a [virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelés. 

Ha ez az egyetlen felügyelt identitás, a virtuális géphez társított felhasználó által hozzárendelt `UserAssigned` típus azonosító értékét törlődni fog.  Ne felejtse el a `<RESOURCE GROUP>` és `<VM NAME>` paraméterek értékeit a saját értékeire cserélni. A `<USER ASSIGNED IDENTITY>` a felhasználó által hozzárendelt identitás lesz `name` tulajdonság, ami az identitási szakaszban, a virtuális gépet a található `az vm identity show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Ha a virtuális gép nem rendelkezik felügyelt identitás rendszer által hozzárendelt, és el szeretné távolítani az összes felhasználó által hozzárendelt identitások belőle, használja a következő parancsot:

> [!NOTE]
> Az érték `none` megkülönbözteti a kis-és nagybetűket. Kisbetűnek kell lennie.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Ha a virtuális gépen mindkét rendszer által hozzárendelt, és a felhasználó által hozzárendelt identitások, eltávolíthatja a felhasználó által hozzárendelt identitások használata csak alapértelmezett között. Használja az alábbi parancsot:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>További lépések
- [Felügyelt identitások Azure-erőforrások – áttekintés](overview.md)
- A teljes Azure virtuális gépek létrehozása rövid útmutatók lásd: 
  - [Windows virtuális gép létrehozása a CLI-vel](../../virtual-machines/windows/quick-create-cli.md)  
  - [Linux rendszerű virtuális gép létrehozása a CLI-vel](../../virtual-machines/linux/quick-create-cli.md) 

















