---
title: Felügyelt identitások konfigurálása a virtuálisgép-méretezési csoporton – Azure CLI – Azure AD
description: Részletes útmutató a rendszer és a felhasználó által hozzárendelt felügyelt identitások konfigurálásához egy Azure virtuálisgép-méretezési csoporton az Azure CLI használatával.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: MarkusVi
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74d8faed0637b5b5b82e1ad450a3b1535bb063e4
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547299"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Felügyelt identitások konfigurálása Azure-erőforrásokhoz virtuálisgép-méretezési csoportokban az Azure CLI használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitásai az Azure-szolgáltatásokat a Azure Active Directory automatikusan felügyelt identitással biztosítják. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, és nem rendelkezik hitelesítő adatokkal a kódban. 

Ebből a cikkből megtudhatja, hogyan hajthatja végre a következő felügyelt identitásokat Azure-beli virtuálisgép-méretezési csoportokon az Azure CLI használatával:
- A rendszer által hozzárendelt felügyelt identitás engedélyezése és letiltása egy Azure virtuálisgép-méretezési csoporton
- Felhasználó által hozzárendelt felügyelt identitás hozzáadása és eltávolítása egy Azure-beli virtuálisgép-méretezési csoporton


## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md). **Mindenképpen tekintse át a [rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A cikkben szereplő felügyeleti műveletek végrehajtásához a fióknak a következő Azure-beli szerepköralapú hozzáférés-vezérlési hozzárendelésekre van szüksége:

    > [!NOTE]
    > Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

    - Virtuálisgép-méretezési csoport létrehozásához, illetve a virtuálisgép-méretezési csoportból származó rendszer-és/vagy felhasználó által hozzárendelt felügyelt identitás engedélyezéséhez és eltávolításához a [virtuális gépek közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .
    - [Felügyelt identitás közreműködői](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör felhasználó által hozzárendelt felügyelt identitás létrehozásához.
    - [Felügyelt identitás-kezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör a felhasználó által hozzárendelt felügyelt identitás hozzárendeléséhez és eltávolításához egy virtuálisgép-méretezési csoportba.
- Három lehetősége van a CLI-példaszkriptek futtatásához:
    - Használja a Azure Portal [Azure Cloud shellt](../../cloud-shell/overview.md) (lásd a következő szakaszt).
    - Használja a beágyazott Azure Cloud Shell-t a "Kipróbálom" gomb, mindegyik blokk jobb felső sarkában található.
    - [Telepítse az Azure CLI legújabb verzióját](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 vagy újabb), ha helyi CLI-konzolt szeretne használni. 
      
      > [!NOTE]
      > A parancsok frissítve lettek, hogy tükrözzék az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)legújabb kiadását.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebből a szakaszból megtudhatja, hogyan engedélyezheti és tilthatja le egy Azure virtuálisgép-méretezési csoport rendszerhez rendelt felügyelt identitását az Azure CLI használatával.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>A rendszer által hozzárendelt felügyelt identitás engedélyezése egy Azure virtuálisgép-méretezési csoport létrehozása során

Virtuálisgép-méretezési csoport létrehozása a rendszerhez rendelt felügyelt identitás engedélyezésével:

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az-login) paranccsal jelentkezzen be az Azure-ba. Olyan fiókot használjon, amely ahhoz az Azure-előfizetéshez van társítva, amelybe telepíteni szeretné a virtuálisgép-méretezési csoportját:

   ```azurecli-interactive
   az login
   ```

2. Hozzon létre egy [erőforráscsoportot](../../azure-resource-manager/resource-group-overview.md#terminology) a virtuálisgép-méretezési csoport és a kapcsolódó erőforrások tárolásához és üzembe helyezéséhez az [az Group Create](/cli/azure/group/#az-group-create)paranccsal. Ezt a lépést kihagyhatja, ha már rendelkezik a használni kívánt erőforráscsoporthoz:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Hozzon létre egy virtuálisgép-méretezési készletet [az az vmss Create](/cli/azure/vmss/#az-vmss-create) paranccsal. A következő példában egy *myVMSS* nevű virtuálisgép-méretezési csoport jön létre egy rendszer által hozzárendelt felügyelt identitással, amelyet a `--assign-identity` paraméter kér. Az `--admin-username` és `--admin-password` paraméterek adják meg a virtuális gép bejelentkeztetéséhez tartozó rendszergazdanevet és -jelszót. A környezetnek megfelelően frissítse ezeket az értékeket: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Rendszerhez rendelt felügyelt identitás engedélyezése meglévő Azure virtuálisgép-méretezési csoportokban

Ha egy meglévő Azure virtuálisgép-méretezési csoporton kell engedélyeznie a rendszer által hozzárendelt felügyelt identitást:

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az-login) paranccsal jelentkezzen be az Azure-ba. Olyan fiókot használjon, amely a virtuálisgép-méretezési csoportját tartalmazó Azure-előfizetéshez van társítva.

   ```azurecli-interactive
   az login
   ```

2. Az [az vmss Identity assign](/cli/azure/vmss/identity/#az-vmss-identity-assign) paranccsal engedélyezheti a rendszerhez rendelt felügyelt identitást egy meglévő virtuális géphez:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Rendszer által hozzárendelt felügyelt identitás letiltása egy Azure virtuálisgép-méretezési csoportból

Ha olyan virtuálisgép-méretezési csoporttal rendelkezik, amelynek már nincs szüksége a rendszerhez rendelt felügyelt identitásra, de továbbra is szükség van a felhasználó által hozzárendelt felügyelt identitásokra, használja a következő parancsot:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Ha olyan virtuális géppel rendelkezik, amelyhez már nincs szükség a rendszerhez rendelt felügyelt identitásra, és nincs felhasználó által hozzárendelt felügyelt identitása, használja a következő parancsot:

> [!NOTE]
> Az érték `none` a kis-és nagybetűk megkülönböztetése. Kisbetűnek kell lennie. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```



## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebből a szakaszból megtudhatja, hogyan engedélyezheti és távolíthatja el a felhasználó által hozzárendelt felügyelt identitásokat az Azure CLI használatával.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése virtuálisgép-méretezési csoport létrehozása során

Ez a szakasz végigvezeti egy virtuálisgép-méretezési csoport létrehozásán, valamint egy felhasználó által hozzárendelt felügyelt identitásnak a virtuálisgép-méretezési csoportba való hozzárendelésén. Ha már rendelkezik a használni kívánt virtuálisgép-méretezési csoporttal, ugorja át ezt a szakaszt, és folytassa a következő lépéssel.

1. Ezt a lépést kihagyhatja, ha már rendelkezik egy használni kívánt erőforráscsoport-csoporttal. Hozzon létre egy [erőforráscsoportot](~/articles/azure-resource-manager/resource-group-overview.md#terminology) a felhasználó által hozzárendelt felügyelt identitás tárolásához és üzembe helyezéséhez az [az Group Create](/cli/azure/group/#az-group-create)paranccsal. Ne felejtse el a `<RESOURCE GROUP>` és `<LOCATION>` paraméterek értékeit a saját értékeire cserélni. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az [az identity create](/cli/azure/identity#az-identity-create) paranccsal.  A `-g` paraméter adja meg azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt felügyelt identitás létre lesz hozva, a `-n` paraméter pedig annak nevét határozza meg. Ne felejtse el a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterek értékeit a saját értékeire cserélni:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   A válasz a következőhöz hasonló, felhasználó által hozzárendelt felügyelt identitás részleteit tartalmazza. A felhasználó által hozzárendelt felügyelt identitáshoz rendelt erőforrás-`id` értéket a következő lépésben kell használni.

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

3. Hozzon létre egy virtuálisgép-méretezési készletet [az az vmss Create](/cli/azure/vmss/#az-vmss-create)paranccsal. Az alábbi példa létrehoz egy virtuálisgép-méretezési készletet, amely az új, felhasználó által hozzárendelt felügyelt identitáshoz van társítva, a `--assign-identity` paraméterrel megadott módon. A `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` és `<USER ASSIGNED IDENTITY>` paraméterek értékét mindenképp helyettesítse be a saját értékeivel. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Felhasználóhoz rendelt felügyelt identitás hozzárendelése meglévő virtuálisgép-méretezési csoportokhoz

1. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az [az identity create](/cli/azure/identity#az-identity-create) paranccsal.  A `-g` paraméter adja meg azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt felügyelt identitás létre lesz hozva, a `-n` paraméter pedig annak nevét határozza meg. Ne felejtse el a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterek értékeit a saját értékeire cserélni:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   A válasz a következőhöz hasonló, felhasználó által hozzárendelt felügyelt identitás részleteit tartalmazza.

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

2. Rendelje hozzá a felhasználóhoz rendelt felügyelt identitást a virtuálisgép-méretezési csoporthoz az [az vmss Identity assign](/cli/azure/vmss/identity)paranccsal. Ne felejtse el a `<RESOURCE GROUP>` és `<VIRTUAL MACHINE SCALE SET NAME>` paraméterek értékeit a saját értékeire cserélni. A `<USER ASSIGNED IDENTITY>` a felhasználó által hozzárendelt identitás erőforrás-`name` tulajdonsága, az előző lépésben létrehozott módon:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása egy Azure virtuálisgép-méretezési csoportból

Ha egy felhasználó által hozzárendelt felügyelt identitást szeretne eltávolítani egy virtuálisgép-méretezési csoportból, használja [az az vmss Identity Remove](/cli/azure/vmss/identity#az-vmss-identity-remove)parancsot. Ha ez az egyetlen, a virtuálisgép-méretezési csoporthoz rendelt, felhasználó által hozzárendelt felügyelt identitás, `UserAssigned` el lesz távolítva az Identity Type értékből.  Ne felejtse el a `<RESOURCE GROUP>` és `<VIRTUAL MACHINE SCALE SET NAME>` paraméterek értékeit a saját értékeire cserélni. A `<USER ASSIGNED IDENTITY>` a felhasználó által hozzárendelt felügyelt identitás `name` tulajdonsága lesz, amely a virtuálisgép-méretezési csoport identitás szakaszában található `az vmss identity show`használatával:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Ha a virtuálisgép-méretezési csoport nem rendelkezik rendszerhez rendelt felügyelt identitással, és el szeretné távolítani az összes felhasználó által hozzárendelt felügyelt identitást, használja a következő parancsot:

> [!NOTE]
> Az érték `none` a kis-és nagybetűk megkülönböztetése. Kisbetűnek kell lennie.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Ha a virtuálisgép-méretezési csoporthoz a rendszerhez hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás is tartozik, az összes felhasználó által hozzárendelt identitást eltávolíthatja úgy, hogy csak a rendszer által hozzárendelt felügyelt identitás használatára váltson. Használja az alábbi parancsot:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Következő lépések

- [Felügyelt identitások az Azure-erőforrásokhoz – áttekintés](overview.md)
- A teljes Azure-beli virtuálisgép-méretezési csoport létrehozásával kapcsolatos gyors útmutató: 

  - [Virtuálisgép-méretezési csoport létrehozása a parancssori felülettel](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















