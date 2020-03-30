---
title: Felügyelt identitások konfigurálása az Azure Virtuális gépen az Azure CLI – Azure AD használatával
description: Lépésenkénti útmutató a rendszer és a felhasználó által hozzárendelt felügyelt identitások konfigurálása egy Azure virtuális gép az Azure CLI használatával.
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
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f2efaceefc53b3c0b5dfd899baf9fd30fdf9a76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244146"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Felügyelt identitások konfigurálása Azure-erőforrásokhoz azure-beli virtuális gépen az Azure CLI használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitásai automatikusan felügyelt identitást biztosítaz Azure-szolgáltatásokszámára az Azure Active Directoryban. Ezzel az identitással hitelesítheti magát minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítést, anélkül, hogy hitelesítő adatokat a kódot. 

Ebben a cikkben az Azure CLI használatával megtudhatja, hogyan hajthatja végre a következő felügyelt identitások az Azure-erőforrások műveletek egy Azure virtuális gép:

- A rendszeráltal hozzárendelt felügyelt identitás engedélyezése és letiltása egy Azure virtuális gépen
- Felhasználó által hozzárendelt felügyelt identitás hozzáadása és eltávolítása Azure-beli virtuális gépen

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [áttekintő szakaszt.](overview.md) **Mindenképpen tekintse át a [rendszerhez rendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget.](overview.md#how-does-the-managed-identities-for-azure-resources-work)**
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A CLI parancsfájlpéldák futtatásához három lehetőség közül választhat:
    - Használja az [Azure Cloud Shell](../../cloud-shell/overview.md) az Azure Portalon (lásd a következő szakaszban).
    - Használja a beágyazott Azure Cloud Shell segítségével a "Try It" gomb, található a jobb felső sarokban minden kódblokk.
    - [Telepítse az Azure CLI legújabb verzióját,](https://docs.microsoft.com/cli/azure/install-azure-cli) ha helyi CLI-konzolt szeretne használni. 
      
      > [!NOTE]
      > A parancsok frissültek, hogy tükrözzék az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)legújabb kiadását.     

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Rendszerhez rendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan engedélyezheti és tilthatja le a rendszer által hozzárendelt felügyelt identitást egy Azure-beli virtuális gépen az Azure CLI használatával.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Rendszeráltal hozzárendelt felügyelt identitás engedélyezése az Azure virtuális gép létrehozása során

Hozzon létre egy Azure-beli virtuális gép a rendszer által hozzárendelt felügyelt identitás engedélyezve van, a fióknak szüksége van a [Virtuálisgép közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelés.  Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az-login) paranccsal jelentkezzen be az Azure-ba. A használni kívánt fiók azon Azure-előfizetéshez legyen hozzárendelve, amely alatt üzembe fogja helyezni a virtuális gépet:

   ```azurecli-interactive
   az login
   ```

2. Az [az group create](/cli/azure/group/#az-group-create) paranccsal hozzon létre egy [erőforráscsoportot](../../azure-resource-manager/management/overview.md#terminology) a virtuális gép és az ahhoz kapcsolódó erőforrások elkülönítéséhez és üzembe helyezéséhez. Ezt a lépést kihagyhatja, ha inkább egy meglévő erőforráscsoportot kíván használni:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm/#az-vm-create) paranccsal. A következő példa létrehoz egy *myVM* nevű virtuális gép egy rendszer által `--assign-identity` hozzárendelt felügyelt identitás, a paraméter által kért. Az `--admin-username` és `--admin-password` paraméterek adják meg a virtuális gép bejelentkeztetéséhez tartozó rendszergazdanevet és -jelszót. A környezetnek megfelelően frissítse ezeket az értékeket: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Rendszeráltal hozzárendelt felügyelt identitás engedélyezése meglévő Azure-beli virtuális gépen

A virtuális gép rendszer által hozzárendelt felügyelt identitásának engedélyezéséhez a fióknak szüksége van a [Virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelésre.  Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az-login) paranccsal jelentkezzen be az Azure-ba. A virtuális gép tartalmazó Azure-előfizetéshez társított fiók használata.

   ```azurecli-interactive
   az login
   ```

2. Használja [az vm identitás hozzárendelése](/cli/azure/vm/identity/) a `identity assign` parancs lehetővé teszi a rendszer által hozzárendelt identitást egy meglévő virtuális gép:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>A rendszeráltal hozzárendelt identitás letiltása egy Azure virtuális gépről

A rendszer által hozzárendelt felügyelt identitás letiltásához a virtuális gépen a fióknak szüksége van a [Virtuálisgép közreműködőszerepkör-hozzárendelésre.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

Ha olyan virtuális géppel rendelkezik, amelynek már nincs szüksége a rendszer által hozzárendelt identitásra, de továbbra is szüksége van a felhasználó által hozzárendelt identitásokra, használja a következő parancsot:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Ha olyan virtuális géppel rendelkezik, amelynek már nincs szüksége rendszerhez rendelt identitásra, és nem rendelkezik felhasználó által hozzárendelt identitásokkal, használja a következő parancsot:

> [!NOTE]
> Az `none` érték a kis- és nagybetűket nem figyelembe vevő. Kisbetűsnek kell lennie. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan adhat hozzá és távolíthat el egy felhasználó által hozzárendelt felügyelt identitást egy Azure-beli virtuális gépből az Azure CLI használatával.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése az Azure virtuális gép létrehozása során

Ha egy felhasználó által hozzárendelt identitást rendel egy virtuális géphez a létrehozása során, a fióknak szüksége van a [virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) és a [felügyelt identitáskezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör-hozzárendelések. Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

1. Kihagyhatja ezt a lépést, ha már rendelkezik egy erőforráscsoporttal, amelyet használni szeretne. Hozzon létre egy [erőforráscsoportot](~/articles/azure-resource-manager/management/overview.md#terminology) a felhasználó által hozzárendelt felügyelt identitás elszigetelésére és telepítésére a [csoport létrehozása](/cli/azure/group/#az-group-create)használatával. Ne felejtse el a `<RESOURCE GROUP>` és `<LOCATION>` paraméterek értékeit a saját értékeire cserélni. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az [az identity create](/cli/azure/identity#az-identity-create) paranccsal.  A `-g` paraméter adja meg azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt felügyelt identitás létre lesz hozva, a `-n` paraméter pedig annak nevét határozza meg.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   A válasz a felhasználó által hozzárendelt felügyelt identitás hozlétre, hasonlóan a következő. A felhasználó által hozzárendelt felügyelt identitáshoz rendelt erőforrás-azonosító érték a következő lépésben használatos.

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

3. Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm/#az-vm-create) paranccsal. A következő példa létrehoz egy virtuális gép az új, felhasználó által `--assign-identity` hozzárendelt identitáshoz társítva, a paraméter által megadott módon. A `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` és `<USER ASSIGNED IDENTITY NAME>` paraméterek értékét mindenképp helyettesítse be a saját értékeivel. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése meglévő Azure-beli virtuális géphez

Ha egy felhasználó által hozzárendelt identitást rendel egy virtuális géphez, a fióknak szüksége van a [virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) és a [felügyelt identitáskezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör-hozzárendelések. Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

1. Hozzon létre egy felhasználóhoz rendelt identitást az [az identity create](/cli/azure/identity#az-identity-create) paranccsal.  A `-g` paraméter azt az erőforráscsoportot adja meg, amelyben a `-n` felhasználó által hozzárendelt identitás létrejön, és a paraméter megadja a nevét. Ne felejtse el a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterek értékeit a saját értékeire cserélni:

    > [!IMPORTANT]
    > A felhasználó által hozzárendelt felügyelt identitások létrehozása speciális karakterekkel (azaz aláhúzás) a névben jelenleg nem támogatott. Használjon alfanumerikus karaktereket. Térjen vissza frissítésekért.  További információkért lásd [a gyIK-et és az ismert problémákat](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   A válasz a felhasználó által hozzárendelt felügyelt identitás hozlétre, hasonlóan a következő. 

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

2. Rendelje hozzá a felhasználó által hozzárendelt identitást a virtuális géphez az [az vm identitás hozzárendelésével.](/cli/azure/vm) Ne felejtse el a `<RESOURCE GROUP>` és `<VM NAME>` paraméterek értékeit a saját értékeire cserélni. Az `<USER ASSIGNED IDENTITY NAME>` a felhasználó által hozzárendelt felügyelt `name` identitás erőforrástulajdonsága, az előző lépésben létrehozott módon:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása azure-beli virtuális gépből

A virtuális géphez felhasználó által hozzárendelt identitás eltávolításához a fióknak szüksége van a [Virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelésre. 

Ha ez az egyetlen felhasználó által hozzárendelt felügyelt identitás `UserAssigned` a virtuális géphez rendelve, a rendszer eltávolítja az identitástípus-értékből.  Ne felejtse el a `<RESOURCE GROUP>` és `<VM NAME>` paraméterek értékeit a saját értékeire cserélni. A `<USER ASSIGNED IDENTITY>` lesz a felhasználó által hozzárendelt `name` identitás tulajdonsága, amely megtalálható az identitás részben `az vm identity show`a virtuális gép segítségével:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Ha a virtuális gép nem rendelkezik rendszeráltal hozzárendelt felügyelt identitással, és el szeretné távolítani belőle az összes felhasználó által hozzárendelt identitást, használja a következő parancsot:

> [!NOTE]
> Az `none` érték a kis- és nagybetűket nem figyelembe vevő. Kisbetűsnek kell lennie.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Ha a virtuális gép rendelkezik a rendszer-és a felhasználó által hozzárendelt identitások, eltávolíthatja az összes felhasználó által hozzárendelt identitások váltással csak a rendszer által hozzárendelt. Használja az alábbi parancsot:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>További lépések
- [Felügyelt identitások az Azure-erőforrásokhoz – áttekintés](overview.md)
- A teljes Azure virtuális gép létrehozása rövid útmutatók, lásd: 
  - [Windows virtuális gép létrehozása CLI-vel](../../virtual-machines/windows/quick-create-cli.md)  
  - [Linuxos virtuális gép létrehozása CLI-vel](../../virtual-machines/linux/quick-create-cli.md) 

















