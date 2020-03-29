---
title: Felügyelt identitások konfigurálása virtuálisgép-méretezési csoporton - Azure CLI - Azure AD
description: Lépésenkénti útmutató a rendszer és a felhasználó által hozzárendelt felügyelt identitások konfigurálása egy Azure virtuálisgép-méretezési csoportban, az Azure CLI használatával.
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
ms.openlocfilehash: 2832a8c584c0fbe707f22501809d772c6ffb970b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430086"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Felügyelt identitások konfigurálása az Azure-erőforrásokhoz egy virtuálisgép-méretezési csoporton az Azure CLI használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitásai automatikusan felügyelt identitást biztosítaz Azure-szolgáltatásokszámára az Azure Active Directoryban. Ezzel az identitással hitelesítheti magát minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítést, anélkül, hogy hitelesítő adatokat a kódot. 

Ebben a cikkben megtudhatja, hogyan hajthatja végre a következő felügyelt identitások az Azure-erőforrások műveletek egy Azure virtuálisgép-méretezési csoport, az Azure CLI használatával:
- A rendszeráltal hozzárendelt felügyelt identitás engedélyezése és letiltása egy Azure virtuálisgép-méretezési csoportban
- Felhasználó által hozzárendelt felügyelt identitás hozzáadása és eltávolítása Azure virtuálisgép-méretezési csoportban


## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [áttekintő szakaszt.](overview.md) **Mindenképpen tekintse át a [rendszerhez rendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget.](overview.md#how-does-the-managed-identities-for-azure-resources-work)**
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A jelen cikkben szereplő felügyeleti műveletek végrehajtásához a fióknak a következő Azure-szerepköralapú hozzáférés-vezérlési hozzárendelésekre van szüksége:

    > [!NOTE]
    > Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

    - [Virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) egy virtuálisgép-méretezési készlet létrehozásához, valamint a rendszer és/vagy a felhasználó által hozzárendelt felügyelt identitás virtuálisgép-méretezési készletből való engedélyezéséhez és eltávolításához.
    - [Felügyelt identitásközreműködő](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör a felhasználó által hozzárendelt felügyelt identitás létrehozásához.
    - [Felügyelt identitáskezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör a felhasználó által hozzárendelt felügyelt identitás hozzárendeléséhez és eltávolításához egy virtuálisgép-méretezési csoporthoz.
- A CLI parancsfájlpéldák futtatásához három lehetőség közül választhat:
    - Használja az [Azure Cloud Shell](../../cloud-shell/overview.md) az Azure Portalon (lásd a következő szakaszban).
    - Használja a beágyazott Azure Cloud Shell segítségével a "Try It" gomb, található a jobb felső sarokban minden kódblokk.
    - [Telepítse az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 vagy újabb) legújabb verzióját, ha helyi CLI-konzolt szeretne használni. 
      
      > [!NOTE]
      > A parancsok frissültek, hogy tükrözzék az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)legújabb kiadását.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Rendszerhez rendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan engedélyezheti és tilthatja le a rendszer által hozzárendelt felügyelt identitást egy Azure-beli virtuálisgép-méretezési csoporthoz az Azure CLI használatával.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>A rendszerhez rendelt felügyelt identitás engedélyezése az Azure virtuálisgép-méretezési csoport létrehozása során

Virtuálisgép-méretezési csoport létrehozása a rendszer által hozzárendelt felügyelt identitással:

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az-login) paranccsal jelentkezzen be az Azure-ba. Használjon olyan fiókot, amely az Azure-előfizetéshez van társítva, amely nek a virtuálisgép-méretezési készletét szeretné telepíteni:

   ```azurecli-interactive
   az login
   ```

2. Hozzon létre egy [erőforráscsoportot](../../azure-resource-manager/management/overview.md#terminology) a virtuálisgép-méretezési csoport és a kapcsolódó erőforrások elszigetelésére és telepítésére a [csoport létrehozása](/cli/azure/group/#az-group-create)használatával. Ezt a lépést kihagyhatja, ha már van egy erőforráscsoportja, amelyet helyette használni szeretne:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Hozzon létre egy virtuálisgép-méretezési csoportot [az vmss create](/cli/azure/vmss/#az-vmss-create) használatával. A következő példa létrehoz egy *myVMSS* nevű virtuálisgép-méretezési készletet egy `--assign-identity` rendszeráltal hozzárendelt felügyelt identitással, a paraméter kérésének megfelelően. Az `--admin-username` és `--admin-password` paraméterek adják meg a virtuális gép bejelentkeztetéséhez tartozó rendszergazdanevet és -jelszót. A környezetnek megfelelően frissítse ezeket az értékeket: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Rendszeráltal hozzárendelt felügyelt identitás engedélyezése meglévő Azure virtuálisgép-méretezési csoporton

Ha engedélyeznie kell a rendszer által hozzárendelt felügyelt identitást egy meglévő Azure virtuálisgép-méretezési csoporton:

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az-login) paranccsal jelentkezzen be az Azure-ba. Használjon egy fiókot, amely a virtuális gép méretezési készletét tartalmazó Azure-előfizetéshez van társítva.

   ```azurecli-interactive
   az login
   ```

2. Az [vmss identity assign](/cli/azure/vmss/identity/#az-vmss-identity-assign) paranccsal engedélyezheti a rendszer által hozzárendelt felügyelt identitást egy meglévő virtuális géphez:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>A rendszeráltal hozzárendelt felügyelt identitás letiltása egy Azure virtuálisgép-méretezési csoportból

Ha olyan virtuálisgép-méretezési készletet használ, amelynek már nincs szüksége a rendszer által hozzárendelt felügyelt identitásra, de továbbra is szüksége van a felhasználó által hozzárendelt felügyelt identitásokra, használja a következő parancsot:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Ha olyan virtuális gépe van, amelynek már nincs szüksége rendszeráltal hozzárendelt felügyelt identitásra, és nem rendelkezik a felhasználó által hozzárendelt felügyelt identitásokkal, használja a következő parancsot:

> [!NOTE]
> Az `none` érték a kis- és nagybetűket nem figyelembe vevő. Kisbetűsnek kell lennie. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```



## <a name="user-assigned-managed-identity"></a>felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan engedélyezheti és távolíthatja el a felhasználó által hozzárendelt felügyelt identitást az Azure CLI használatával.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése virtuálisgép-méretezési csoport létrehozása során

Ez a szakasz végigvezeti egy virtuálisgép-méretezési csoport létrehozásán és a felhasználó által hozzárendelt felügyelt identitás virtuálisgép-méretezési készlethez való hozzárendelésén. Ha már rendelkezik használni kívánt virtuálisgép-méretezési készlet, hagyja ki ezt a szakaszt, és folytassa a következő.

1. Kihagyhatja ezt a lépést, ha már rendelkezik egy erőforráscsoporttal, amelyet használni szeretne. Hozzon létre egy [erőforráscsoportot](~/articles/azure-resource-manager/management/overview.md#terminology) a felhasználó által hozzárendelt felügyelt identitás elszigetelésére és telepítésére a [csoport létrehozása](/cli/azure/group/#az-group-create)használatával. Ne felejtse el a `<RESOURCE GROUP>` és `<LOCATION>` paraméterek értékeit a saját értékeire cserélni. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az [az identity create](/cli/azure/identity#az-identity-create) paranccsal.  A `-g` paraméter adja meg azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt felügyelt identitás létre lesz hozva, a `-n` paraméter pedig annak nevét határozza meg. Ne felejtse el a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterek értékeit a saját értékeire cserélni:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   A válasz a felhasználó által hozzárendelt felügyelt identitás hozlétre, hasonlóan a következő. A `id` felhasználó által hozzárendelt felügyelt identitáshoz rendelt erőforrásérték a következő lépésben használatos.

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

3. Hozzon létre egy virtuálisgép-méretezési csoportot [az vmss create](/cli/azure/vmss/#az-vmss-create)használatával. A következő példa létrehoz egy virtuálisgép-méretezési készletet az új, felhasználó `--assign-identity` által hozzárendelt felügyelt identitáshoz társítva, a paraméter által megadott módon. A `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` és `<USER ASSIGNED IDENTITY>` paraméterek értékét mindenképp helyettesítse be a saját értékeivel. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése meglévő virtuálisgép-méretezési készlethez

1. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az [az identity create](/cli/azure/identity#az-identity-create) paranccsal.  A `-g` paraméter adja meg azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt felügyelt identitás létre lesz hozva, a `-n` paraméter pedig annak nevét határozza meg. Ne felejtse el a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterek értékeit a saját értékeire cserélni:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   A válasz a felhasználó által hozzárendelt felügyelt identitás hozlétre, hasonlóan a következő.

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

2. Rendelje hozzá a felhasználó által hozzárendelt felügyelt identitást a virtuálisgép-méretezési csoporthoz [az vmss identitáshozzárendeléssel.](/cli/azure/vmss/identity) Ne felejtse el a `<RESOURCE GROUP>` és `<VIRTUAL MACHINE SCALE SET NAME>` paraméterek értékeit a saját értékeire cserélni. Az `<USER ASSIGNED IDENTITY>` a felhasználó által hozzárendelt identitás `name` erőforrástulajdonsága, az előző lépésben létrehozott módon:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása egy Azure virtuálisgép-méretezési csoportból

A felhasználó által hozzárendelt felügyelt identitás eltávolítása egy virtuálisgép-méretezési csoportból használja [az vmss identitás eltávolítást.](/cli/azure/vmss/identity#az-vmss-identity-remove) Ha ez az egyetlen felhasználó által hozzárendelt felügyelt identitás a `UserAssigned` virtuálisgép-méretezési csoporthoz rendelve, a rendszer eltávolítja az identitástípus-értékből.  Ne felejtse el a `<RESOURCE GROUP>` és `<VIRTUAL MACHINE SCALE SET NAME>` paraméterek értékeit a saját értékeire cserélni. A `<USER ASSIGNED IDENTITY>` lesz a felhasználó által hozzárendelt `name` felügyelt identitás tulajdonsága, amely megtalálható az identitás szakaszban `az vmss identity show`a virtuálisgép méretezési csoport a következő használatával:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Ha a virtuálisgép-méretezési csoport nem rendelkezik rendszeráltal hozzárendelt felügyelt identitással, és el szeretné távolítani belőle az összes felhasználó által hozzárendelt felügyelt identitást, használja a következő parancsot:

> [!NOTE]
> Az `none` érték a kis- és nagybetűket nem figyelembe vevő. Kisbetűsnek kell lennie.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Ha a virtuálisgép-méretezési csoport rendszer- és felhasználó által hozzárendelt felügyelt identitásokkal is rendelkezik, eltávolíthatja az összes felhasználó által hozzárendelt identitást, ha csak a rendszer által hozzárendelt felügyelt identitást használja. Használja az alábbi parancsot:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>További lépések

- [Felügyelt identitások az Azure-erőforrásokhoz – áttekintés](overview.md)
- A teljes Azure virtuálisgép-méretezési készlet létrehozásáról szóló rövid útmutató a következő témakörben található: 

  - [Virtuálisgép-méretezési készlet létrehozása CLI-vel](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















