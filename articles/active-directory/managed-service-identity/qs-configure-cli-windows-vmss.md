---
title: Rendszer- és felhasználói konfigurálása rendelt identitást is-Azure CLI-vel az Azure VMSS
description: Részletes utasításokat a rendszer és a felhasználó rendelt identitásokkal az Azure VMSS az Azure CLI használatával.
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
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: 36df9d00d41f3c092320fa88772b41c9a41c6d8e
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237281"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>A virtuális gép konfigurálása méretezési Felügyeltszolgáltatás-identitás (MSI) Azure parancssori felületével

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyeltszolgáltatás-identitás az Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebből a cikkből megismerheti, hogyan hajthat végre a következő műveleteket a Felügyeltszolgáltatás-identitást a egy Azure virtuális gép méretezési beállítása (VMSS), az Azure CLI használatával:
- Engedélyezheti és tilthatja le a rendszer az Azure VMSS identitásának hozzárendelve
- Hozzáadhat és eltávolíthat az Azure VMSS identitásának hozzárendelt felhasználó


## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a Felügyeltszolgáltatás-identitást, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség egy rendszer által hozzárendelt, és a felhasználóhoz hozzárendelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ez a cikk a felügyeleti műveleteket hajt végre, a fiók az alábbi szerepkör-hozzárendelések van szüksége:
    - [Virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) készlet egy virtuálisgép-méretezési csoport létrehozása és engedélyezése és a remove-rendszer és/vagy a felhasználó hozzárendelt felügyelt identitás egy virtuálisgép-méretezési csoportot.
    - [Felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkört a felhasználóhoz hozzárendelt identitás létrehozása.
    - [Felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör hozzárendelése, és távolítsa el a felhasználóhoz hozzárendelt identitás, a kezdő és a egy virtuálisgép-méretezési csoportot.
- Három lehetősége van a CLI-példaszkriptek futtatásához:
    - Használat [Azure Cloud Shell](../../cloud-shell/overview.md) az Azure Portalon (lásd a következő szakaszban).
    - Használja a beágyazott Azure Cloud Shell-t a "Kipróbálom" gomb, mindegyik blokk jobb felső sarkában található.
    - [Telepítse a CLI 2.0 legújabb verzióját](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13-as vagy újabb) Ha inkább a helyi CLI-konzol használatával. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Rendszerhez rendelt identitáshoz

Ebben a szakaszban megismerheti, hogyan engedélyezheti és tilthatja le a rendszer hozzárendelt identitás, az az Azure vmss-hez az Azure CLI használatával.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Rendszer által hozzárendelt identitással engedélyezése egy Azure-beli virtuálisgép-méretezési készlet létrehozása során

Állítsa be a hozzárendelt identitás engedélyezve van a rendszer egy virtuálisgép-méretezési csoport létrehozása:

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az_login) paranccsal jelentkezzen be az Azure-ba. Egy fiók használatával, amely alapján, amely a virtuálisgép-méretezési csoport üzembe szeretné az Azure-előfizetés társítva van:

   ```azurecli-interactive
   az login
   ```

2. Hozzon létre egy [erőforráscsoport](../../azure-resource-manager/resource-group-overview.md#terminology) tartalmazási és telepítéséhez, a virtuálisgép-méretezési csoportot és az összes kapcsolódó erőforrás, használatával [az csoport létrehozása](/cli/azure/group/#az_group_create). Ezt a lépést kihagyhatja, ha már rendelkezik egy használni kívánt erőforráscsoportot:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Hozzon létre egy virtuálisgép-méretezési csoportot a [az vmss létrehozásához](/cli/azure/vmss/#az_vmss_create) . Az alábbi példa létrehoz egy virtuálisgép-méretezési csoport nevű *myVMSS* rendszerhez rendelt identitáshoz, kérésének megfelelően a `--assign-identity` paraméter. Az `--admin-username` és `--admin-password` paraméterek adják meg a virtuális gép bejelentkeztetéséhez tartozó rendszergazdanevet és -jelszót. A környezetnek megfelelően frissítse ezeket az értékeket: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Egy meglévő Azure-beli virtuálisgép-méretezési csoportot a rendszer által hozzárendelt identitással engedélyezése

Ha a rendszer által hozzárendelt identitással egy meglévő Azure-beli virtuálisgép-méretezési csoportot a engedélyeznie kell:

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az_login) paranccsal jelentkezzen be az Azure-ba. Az Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoportba tartozó fiókot használnia.

   ```azurecli-interactive
   az login
   ```

2. Használat [az vmss-identitási hozzárendelése](/cli/azure/vmss/identity/#az_vmss_identity_assign) parancsot a meglévő virtuális géphez a rendszer által hozzárendelt identitással engedélyezéséhez:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Tiltsa le az Azure-beli virtuálisgép-méretezési csoportot a rendszer által hozzárendelt identitással

Ha egy virtuális gép méretezési csoportot, amely már nincs szüksége a rendszerhez rendelt identitáshoz, de továbbra is a felhasználó által hozzárendelt identitások van szüksége, használja a következő parancsot:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Ha már nincs szüksége a rendszerhez rendelt identitáshoz egy virtuális gép, és nem a felhasználói identitásokat, használja a következő parancsot:

> [!NOTE]
> Az érték `none` megkülönbözteti a kis-és nagybetűket. Kisbetűnek kell lennie. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

Az MSI-Virtuálisgép-bővítmény eltávolításához használja [eltávolítása az vmss-identitási](/cli/azure/vmss/identity/#az_vmss_remove_identity) parancsot a rendszer által hozzárendelt identitással eltávolítása egy VMSS:

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

## <a name="user-assigned-identity"></a>A felhasználóhoz hozzárendelt identitás

Ebben a szakaszban megismerheti, hogyan engedélyezheti és a egy felhasználóhoz hozzárendelt identitás, az Azure CLI használatával távolítsa el.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Rendelje hozzá egy a felhasználóhoz hozzárendelt identitás létrehozása az Azure VMSS során

Ez a szakasz végigvezeti egy VMSS létrehozása és hozzárendelése egy a felhasználóhoz hozzárendelt identitás a vmss-hez. Ha már rendelkezik egy használni kívánt VMSS, kihagyhatja ezt a szakaszt, és folytassa a következő.

1. Ezt a lépést kihagyhatja, ha már rendelkezik egy használni kívánt erőforráscsoportot. Hozzon létre egy [erőforráscsoport](~/articles/azure-resource-manager/resource-group-overview.md#terminology) tartalmazási és telepítéséhez, a felhasználóhoz hozzárendelt identitás használatával [az csoport létrehozása](/cli/azure/group/#az_group_create). Ne felejtse el a `<RESOURCE GROUP>` és `<LOCATION>` paraméterek értékeit a saját értékeire cserélni. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Hozzon létre egy felhasználót hozzárendelt identitás használatával [az identitás létrehozása](/cli/azure/identity#az-identity-create).  A `-g` paraméter adja meg az erőforráscsoport, amelyben a felhasználóhoz hozzárendelt identitás jön létre, és a `-n` paraméter adja meg a nevét. Ne felejtse el a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterek értékeit a saját értékeire cserélni:

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   A válasz tartalmazza a felhasználóhoz hozzárendelt identitás létrehozása a következőhöz hasonló részleteit. Az erőforrás `id` hozzárendelt a felhasználóhoz hozzárendelt identitás érték szerepel a következő lépéssel.

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

3. Hozzon létre egy VMSS [az vmss létrehozásához](/cli/azure/vmss/#az-vmss-create). Az alábbi példa létrehoz egy társított leírtak szerint az új felhasználóhoz hozzárendelt identitás VMSS a `--assign-identity` paraméter. A `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` és `<USER ASSIGNED IDENTITY ID>` paraméterek értékét mindenképp helyettesítse be a saját értékeivel. A `<USER ASSIGNED IDENTITY ID>`, használja a felhasználóhoz hozzárendelt identitás erőforrás `id` az előző lépésben létrehozott tulajdonság: 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY ID>
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Rendelje hozzá egy a felhasználóhoz hozzárendelt identitás meglévő Azure virtuális géphez

1. Hozzon létre egy felhasználót hozzárendelt identitás használatával [az identitás létrehozása](/cli/azure/identity#az-identity-create).  A `-g` paraméter adja meg az erőforráscsoport, amelyben a felhasználóhoz hozzárendelt identitás jön létre, és a `-n` paraméter adja meg a nevét. Ne felejtse el a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterek értékeit a saját értékeire cserélni:

    > [!IMPORTANT]
    > Felhasználó által hozzárendelt identitások a különleges karakterek (például aláhúzásjelet) nevében létrehozása jelenleg nem támogatott. Adja meg az alfanumerikus karaktereket használjon. Térjen vissza frissítésekért.  További információ: [– gyakori kérdések és ismert problémák](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
A válasz tartalmazza a felhasználóhoz hozzárendelt identitás létrehozása a következőhöz hasonló részleteit. Az erőforrás `id` hozzárendelt a felhasználóhoz hozzárendelt identitás érték szerepel a következő lépéssel.

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

2. A felhasználóhoz hozzárendelt identitás hozzárendelése a VMSS használatával [az vmss-identitási hozzárendelése](/cli/azure/vmss/identity#az_vm_assign_identity). Ne felejtse el a `<RESOURCE GROUP>` és `<VMSS NAME>` paraméterek értékeit a saját értékeire cserélni. A `<USER ASSIGNED IDENTITY ID>` lesz a felhasználóhoz hozzárendelt identitás erőforrás `id` tulajdonságot, mert az előző lépésben létrehozott:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VMSS NAME> --identities <USER ASSIGNED IDENTITY ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Egy Azure-beli virtuálisgép-méretezési csoportot egy felhasználóhoz hozzárendelt identitás eltávolítása

Egy felhasználóhoz hozzárendelt identitás eltávolítása egy virtuális gép méretezési készlet használata [eltávolítása az vmss-identitási](/cli/azure/vmss/identity#az-vmss-identity-remove). Ne felejtse el a `<RESOURCE GROUP>` és `<VMSS NAME>` paraméterek értékeit a saját értékeire cserélni. A `<MSI NAME>` a felhasználóhoz hozzárendelt identitás lesz `name` tulajdonság, amely a virtuális Gépet az identitás szakaszában található `az vmss identity show`:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VMSS NAME> --identities <MSI NAME>
```

Ha el szeretné távolítani az összes felhasználó hozzárendelt identitások, a virtuálisgép-méretezési készlet nem rendelkezik a rendszerhez rendelt identitáshoz, használja a következő parancsot:

> [!NOTE]
> Az érték `none` megkülönbözteti a kis-és nagybetűket. Kisbetűnek kell lennie.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.identityIds=null
```

Ha a virtuálisgép-méretezési csoporthoz rendelt system és a felhasználó által hozzárendelt identitások, eltávolíthatja az összes felhasználói hozzárendelt identitások között csak a hozzárendelt rendszer használatára. Használja az alábbi parancsot:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null 
```

## <a name="next-steps"></a>További lépések

- [Felügyelt Felügyeltszolgáltatás-identitás – áttekintés](overview.md)
- A teljes Azure virtuálisgép-méretezési csoport létrehozása a rövid útmutatóban, olvassa el: 

  - [Egy virtuálisgép-méretezési készlet létrehozása a CLI-vel](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















