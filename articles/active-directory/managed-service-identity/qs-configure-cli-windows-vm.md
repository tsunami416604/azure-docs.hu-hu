---
title: Rendszer- és felhasználói konfigurálása rendelt identitásokkal-beli virtuális gépen az Azure CLI használatával
description: Részletes utasításokat a rendszer és a felhasználó rendelt identitásokkal-beli virtuális gépen, az Azure CLI használatával.
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 064c2dfe625435d3faddae45c413d6b91eac8346
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188024"
---
# <a name="configure-managed-service-identity-msi-on-an-azure-vm-using-azure-cli"></a>Konfigurálja a Felügyeltszolgáltatás-identitás (MSI)-beli virtuális gépen az Azure CLI használatával

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyeltszolgáltatás-identitás az Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebből a cikkből elsajátíthatja az alábbi műveletek Felügyeltszolgáltatás-identitás-beli virtuális gépen, az Azure CLI használatával:
- Engedélyezheti és tilthatja le a rendszer hozzárendelt identitás-beli virtuális gépen
- Hozzáadhat és eltávolíthat egy a felhasználóhoz hozzárendelt identitás-beli virtuális gépen

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a Felügyeltszolgáltatás-identitást, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség egy rendszer által hozzárendelt, és a felhasználóhoz hozzárendelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ez a cikk a felügyeleti műveleteket hajt végre, a fiók az alábbi szerepkör-hozzárendelések van szüksége:
    - [Virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) virtuális gép létrehozása és engedélyezése, és távolítsa el a rendszer által hozzárendelt felügyelt identitás Azure virtuális gépből.
    - [Felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkört a felhasználóhoz hozzárendelt identitás létrehozása.
    - [Felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör hozzárendelése és a egy felhasználóhoz hozzárendelt identitás távolítsa el, és egy virtuális géphez.
- Három lehetősége van a CLI-példaszkriptek futtatásához:
    - Használat [Azure Cloud Shell](../../cloud-shell/overview.md) az Azure Portalon (lásd a következő szakaszban).
    - Használja a beágyazott Azure Cloud Shell-t a "Kipróbálom" gomb, mindegyik blokk jobb felső sarkában található.
    - [Telepítse a CLI 2.0 legújabb verzióját](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13-as vagy újabb) Ha inkább a helyi CLI-konzol használatával. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Rendszerhez rendelt identitáshoz

Ebben a szakaszban megismerheti, hogyan engedélyezheti és tilthatja le a rendszer hozzárendelt identitás-beli virtuális gépen az Azure CLI használatával.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Engedélyezze a rendszerhez rendelt identitáshoz egy Azure virtuális gép létrehozása során

Egy Azure virtuális gép létrehozása a rendszer a hozzárendelt identitás engedélyezve:

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az_login) paranccsal jelentkezzen be az Azure-ba. A használni kívánt fiók azon Azure-előfizetéshez legyen hozzárendelve, amely alatt üzembe fogja helyezni a virtuális gépet:

   ```azurecli-interactive
   az login
   ```

2. Az [az group create](/cli/azure/group/#az_group_create) paranccsal hozzon létre egy [erőforráscsoportot](../../azure-resource-manager/resource-group-overview.md#terminology) a virtuális gép és az ahhoz kapcsolódó erőforrások elkülönítéséhez és üzembe helyezéséhez. Ezt a lépést kihagyhatja, ha inkább egy meglévő erőforráscsoportot kíván használni:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm/#az_vm_create) paranccsal. A következő példában létrehozunk egy nevű virtuális Gépet *myVM* rendszerhez rendelt identitáshoz, kérésének megfelelően a `--assign-identity` paraméter. Az `--admin-username` és `--admin-password` paraméterek adják meg a virtuális gép bejelentkeztetéséhez tartozó rendszergazdanevet és -jelszót. A környezetnek megfelelően frissítse ezeket az értékeket: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Engedélyezze a rendszerhez rendelt identitáshoz egy meglévő Azure virtuális gépen

Ha a rendszer által hozzárendelt identitással egy meglévő virtuális gépen engedélyezni kell:

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az_login) paranccsal jelentkezzen be az Azure-ba. Használjon, amely tartalmazza a virtuális gép Azure-előfizetéssel társított fiókot.

   ```azurecli-interactive
   az login
   ```

2. Használja [az virtuális gép identitás hozzárendelése](/cli/azure/vm/identity/#az_vm_identity_assign) az a `identity assign` parancs engedélyezi a rendszer által hozzárendelt identitással meglévő virtuális géphez:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Tiltsa le a rendszer hozzárendelt identitás Azure virtuális gépből

> [!NOTE]
> A Felügyeltszolgáltatás-identitást a virtuális gépről letiltása jelenleg nem támogatott. Addig is válthat a rendszer által hozzárendelt, és a felhasználó hozzárendelt identitások között.

Ha egy virtuális gépet, amely már nincs szüksége a rendszerhez rendelt identitáshoz, de továbbra is a felhasználó által hozzárendelt identitások van szüksége, használja a következő parancsot:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```
Az MSI-Virtuálisgép-bővítmény eltávolítása felhasználói `-n ManagedIdentityExtensionForWindows` vagy `-n ManagedIdentityExtensionForLinux` kapcsoló (virtuális gép függően) és [vm-bővítmény törlése az](https://docs.microsoft.com/cli/azure/vm/#assign-identity):

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

## <a name="user-assigned-identity"></a>Felhasználói hozzárendelt identitás

Ebben a szakaszban megtudhatja, hogyan adhat hozzá és távolíthat el egy felhasználót hozzárendelt identitás az Azure CLI használatával Azure virtuális.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>Rendelje hozzá egy a felhasználóhoz hozzárendelt identitás egy Azure virtuális gép létrehozása során

Ez a szakasz végigvezeti egy felhasználóhoz hozzárendelt identitás hozzárendelését a virtuális gépek létrehozása. Ha már rendelkezik egy használni kívánt virtuális Gépet, kihagyhatja ezt a szakaszt, és folytassa a következő.

1. Ezt a lépést kihagyhatja, ha már rendelkezik egy használni kívánt erőforráscsoportot. Hozzon létre egy [erőforráscsoport](~/articles/azure-resource-manager/resource-group-overview.md#terminology) tartalmazási és telepítéséhez, az MSI használatával [az csoport létrehozása](/cli/azure/group/#az_group_create). Ne felejtse el a `<RESOURCE GROUP>` és `<LOCATION>` paraméterek értékeit a saját értékeire cserélni. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Hozzon létre egy felhasználót hozzárendelt identitás használatával [az identitás létrehozása](/cli/azure/identity#az_identity_create).  A `-g` paraméter adja meg az erőforráscsoport, amelyben a felhasználóhoz hozzárendelt identitás jön létre, és a `-n` paraméter adja meg a nevét.    
    
[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


```azurecli-interactive
az identity create -g myResourceGroup -n myUserAssignedIdentity
```
A válasz tartalmazza a felhasználóhoz hozzárendelt identitás létrehozása a következőhöz hasonló részleteit. A hozzárendelt a felhasználóhoz hozzárendelt identitás erőforrás-azonosítójának értéke a következő lépésben szolgál.

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

3. Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm/#az_vm_create) paranccsal. Az alábbi példa létrehoz egy virtuális Gépet, azokat az új felhasználóhoz hozzárendelt identitás társított a `--assign-identity` paraméter. A `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` és `<MSI ID>` paraméterek értékét mindenképp helyettesítse be a saját értékeivel. A `<MSI ID>`, használja a felhasználóhoz hozzárendelt identitás erőforrás `id` az előző lépésben létrehozott tulajdonság: 

```azurecli-interactive 
az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Rendelje hozzá egy a felhasználóhoz hozzárendelt identitás meglévő Azure virtuális géphez

1. Hozzon létre egy felhasználót hozzárendelt identitás használatával [az identitás létrehozása](/cli/azure/identity#az-identity-create).  A `-g` paraméter adja meg az erőforráscsoport, amelyben a felhasználóhoz hozzárendelt identitás jön létre, és a `-n` paraméter adja meg a nevét. Ne felejtse el a `<RESOURCE GROUP>` és `<MSI NAME>` paraméterek értékeit a saját értékeire cserélni:

    > [!IMPORTANT]
    > Felhasználó által hozzárendelt identitások a különleges karakterek (például aláhúzásjelet) nevében létrehozása jelenleg nem támogatott. Adja meg az alfanumerikus karaktereket használjon. Térjen vissza frissítésekért.  További információ: [– gyakori kérdések és ismert problémák](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
A válasz tartalmazza a felhasználó hozta létre, az alábbihoz hasonló MSI hozzárendelt részleteit. Az erőforrás `id` hozzárendelt a felhasználóhoz hozzárendelt identitás érték szerepel a következő lépéssel.

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

2. A felhasználóhoz hozzárendelt identitás hozzárendelése a virtuális gép használatával [az virtuális gép identitás hozzárendelése](/cli/azure/vm#az-vm-identity-assign). Ne felejtse el a `<RESOURCE GROUP>` és `<VM NAME>` paraméterek értékeit a saját értékeire cserélni. A `<MSI ID>` lesz a felhasználóhoz hozzárendelt identitás erőforrás `id` tulajdonságot, mert az előző lépésben létrehozott:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>Távolítsa el a a felhasználóhoz hozzárendelt identitás Azure virtuális gépből

> [!NOTE]
> Az összes felhasználó által hozzárendelt identitások eltávolítása a virtuális gép jelenleg nem támogatott, kivéve, ha a rendszerhez rendelt identitáshoz.

Ha a virtuális gépen több felhasználó által hozzárendelt identitások, eltávolíthatja az utolsó egy a kivételével az összes [távolítsa el a virtuálisgép-azonosítója az](/cli/azure/vm#az-vm-identity-remove). Ne felejtse el a `<RESOURCE GROUP>` és `<VM NAME>` paraméterek értékeit a saját értékeire cserélni. A `<MSI NAME>` a felhasználóhoz hozzárendelt identitás lesz `name` tulajdonság, amely a virtuális Gépet az identitás szakaszában található `az vm show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
```
Ha a virtuális Géphez hozzárendelt rendszer és a felhasználó által hozzárendelt identitások, eltávolíthatja az összes felhasználói hozzárendelt identitások között csak a rendszer által hozzárendelt használatára. Használja az alábbi parancsot:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null 
```

## <a name="related-content"></a>Kapcsolódó tartalom
- [Felügyelt Felügyeltszolgáltatás-identitás – áttekintés](overview.md)
- A teljes Azure virtuális gépek létrehozása rövid útmutatók lásd: 
  - [Windows virtuális gép létrehozása a CLI-vel](../../virtual-machines/windows/quick-create-cli.md)  
  - [Linux rendszerű virtuális gép létrehozása a CLI-vel](../../virtual-machines/linux/quick-create-cli.md) 

















