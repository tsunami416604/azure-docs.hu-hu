---
title: Rendszer- és felhasználói konfigurálása rendelt identitásokkal-beli virtuális gépen REST használatával
description: Részletes utasításokat a rendszer és a felhasználó rendelt identitásokkal-beli virtuális gépen a REST API-hívásokat a CURL használatával.
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
ms.date: 06/25/2018
ms.author: daveba
ms.openlocfilehash: 825f34d174c37c2ee5d4187048f7a31fbaeef226
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215929"
---
# <a name="configure-managed-identity-on-an-azure-vm-using-rest-api-calls"></a>A REST API-hívások használata Azure virtuális gép felügyelt identitás konfigurálása

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt identitások Azure-szolgáltatásokat az Azure Active Directoryban a rendszer automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebből a cikkből elsajátíthatja az alábbi műveletek felügyelt identitás-beli virtuális gépen, az Azure Resource Manager REST-végponton való meghíváshoz a CURL használatával:

- Engedélyezheti és tilthatja le a rendszer hozzárendelt identitás-beli virtuális gépen
- Hozzáadhat és eltávolíthat egy a felhasználóhoz hozzárendelt identitás-beli virtuális gépen

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a Felügyeltszolgáltatás-identitást, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség egy rendszer által hozzárendelt, és a felhasználóhoz hozzárendelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ez a cikk a felügyeleti műveleteket hajt végre, a fiók az alábbi szerepkör-hozzárendelések van szüksége:
    - [Virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) virtuális gép létrehozása és engedélyezése, és távolítsa el a rendszer és/vagy a felhasználó hozzárendelt felügyelt identitás Azure virtuális gépből.
    - [Felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkört a felhasználóhoz hozzárendelt identitás létrehozása.
    - [Felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör hozzárendelése és a egy felhasználóhoz hozzárendelt identitás távolítsa el, és egy virtuális géphez.
- Ha Windows használ, telepítse a [a Linux Windows alrendszere](https://msdn.microsoft.com/commandline/wsl/about) vagy használja a [Azure Cloud Shell](../../cloud-shell/overview.md) az Azure Portalon.
- [A helyi Azure CLI-konzol telepítése](/azure/install-azure-cli), ha a [a Linux Windows alrendszere](https://msdn.microsoft.com/commandline/wsl/about) vagy egy [linuxos](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Ha a helyi Azure CLI-konzolt használ, jelentkezzen be Azure-bA `az login` , amely az Azure társítva van egy olyan fiókkal, amelyet szeretne kezelni a rendszer vagy a felhasználó előfizetéshez rendelt identitásokkal.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Rendszerhez rendelt identitáshoz

Ebben a szakaszban megismerheti, hogyan engedélyezheti és tilthatja le a rendszerhez rendelt identitáshoz-beli virtuális gépen az Azure Resource Manager REST-végponton való meghíváshoz a CURL használatával.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Engedélyezze a rendszerhez rendelt identitáshoz egy Azure virtuális gép létrehozása során

Egy Azure virtuális gép létrehozása a rendszerhez rendelt identitáshoz engedélyezve van, kell hozzon létre egy virtuális Gépet, és lekér egy hozzáférési jogkivonatot a CURL használatával hívja meg a Resource Manager-végpontot a rendszer identitást típusú értéket hozzárendelni.

1. Az [az group create](/cli/azure/group/#az_group_create) paranccsal hozzon létre egy [erőforráscsoportot](../../azure-resource-manager/resource-group-overview.md#terminology) a virtuális gép és az ahhoz kapcsolódó erőforrások elkülönítéséhez és üzembe helyezéséhez. Ezt a lépést kihagyhatja, ha inkább egy meglévő erőforráscsoportot kíván használni:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Hozzon létre egy [hálózati adapter](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) a virtuális géphez:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  A tulajdonosi hozzáférési jogkivonatot, amely az engedélyezési fejléc a következő lépésben használandó hozza létre a virtuális Géphez hozzárendelt felügyelt identitás rendszerrel beolvasni.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Hozzon létre egy virtuális Gépet a CURL használatával az Azure Resource Manager REST-végpont meghívására. A következő példában létrehozunk egy nevű virtuális Gépet *myVM* rendszerhez rendelt identitáshoz, a kérelem törzsében szereplő értéke által meghatározott `"identity":{"type":"SystemAssigned"}`. Cserélje le `<ACCESS TOKEN>` értékkel az előző lépés során kapott egy tulajdonosi jogkivonatot kért és a `<SUBSCRIPTION ID>` válasszon a környezetének megfelelő értékét.
 
    ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"TestVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
    ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Engedélyezze a rendszerhez rendelt identitáshoz egy meglévő Azure virtuális gépen

Ahhoz, hogy a rendszerhez rendelt identitáshoz a meglévő virtuális gép, meg kell hozzáférési jogkivonat beszerzése és a CURL használatával a Resource Manager REST-végpont frissítése a identitástípus hívja.

1. A tulajdonosi hozzáférési jogkivonatot, amely az engedélyezési fejléc a következő lépésben használandó hozza létre a virtuális Géphez hozzárendelt felügyelt identitás rendszerrel beolvasni.

   ```azurecli-interactive
   az account get-access-token
   ```

2. A következő CURL-parancs használatával hívja meg az Azure Resource Manager REST-végpont engedélyezéséhez a virtuális Gépen a rendszer által hozzárendelt identitással, a kérelem törzsében szereplő értéke által meghatározott `{"identity":{"type":"SystemAssigned"}` nevű virtuális gép *myVM*.  Cserélje le `<ACCESS TOKEN>` értékkel az előző lépés során kapott egy tulajdonosi jogkivonatot kért és a `<SUBSCRIPTION ID>` válasszon a környezetének megfelelő értékét.
   
   > [!IMPORTANT]
   > Annak érdekében, akkor ne törölje minden olyan meglévő felügyelt identitások, a virtuális géphez rendelt felhasználó, fel kell a felhasználó által hozzárendelt identitások a következő CURL-paranccsal: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Ha a virtuális géphez hozzárendelt, a felhasználó által hozzárendelt identitások a `identity` értéke a válaszban, folytassa a 3. lépés, amely bemutatja, hogyan, ha a felhasználó által hozzárendelt identitások miközben a rendszer által hozzárendelt identitással a virtuális Gépen.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Ahhoz, hogy a rendszerhez rendelt identitáshoz a virtuális gép meglévő felhasználó által hozzárendelt identitásokkal, hozzá kell `SystemAssigned` , a `type` értéket.  
   
   Például, ha a virtuális gép rendelkezik a felhasználó által hozzárendelt identitások `ID1` és `ID2` rendelve, és szeretné a rendszer által hozzárendelt identitással hozzáadni a virtuális Gépet, a következő CURL-hívást használja. Cserélje le `<ACCESS TOKEN>` és `<SUBSCRIPTION ID>` a környezetnek megfelelő értékekkel.
   
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Tiltsa le a rendszerhez rendelt identitáshoz Azure virtuális gépből

Tiltsa le a meglévő virtuális gép egy rendszer által hozzárendelt identitással, kell hozzáférési jogkivonat beszerzése és a CURL használatával frissíteni az azonosító típusát a Resource Manager REST-végpont meghívása `None`.

1. A tulajdonosi hozzáférési jogkivonatot, amely az engedélyezési fejléc a következő lépésben használandó hozza létre a virtuális Géphez hozzárendelt felügyelt identitás rendszerrel beolvasni.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Frissítse a virtuális gép az Azure Resource Manager REST-végpont hívja a CURL használatával tiltsa le a rendszerhez rendelt identitáshoz.  A következő példa letiltja a rendszer által hozzárendelt identitással, a kérelem törzsében szereplő értéke által meghatározott `{"identity":{"type":"None"}}` nevű virtuális gépből *myVM*.  Cserélje le `<ACCESS TOKEN>` értékkel az előző lépés során kapott egy tulajdonosi jogkivonatot kért és a `<SUBSCRIPTION ID>` válasszon a környezetének megfelelő értékét.

   > [!IMPORTANT]
   > Annak érdekében, akkor ne törölje minden olyan meglévő felügyelt identitások, a virtuális géphez rendelt felhasználó, fel kell a felhasználó által hozzárendelt identitások a következő CURL-paranccsal: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Ha a virtuális géphez hozzárendelt, a felhasználó által hozzárendelt identitások a `identity` értéke a válaszban, folytassa a 3. lépés, amely bemutatja, hogyan, ha a felhasználó által hozzárendelt identitások a virtuális Gépen a rendszer által hozzárendelt identitással letiltása közben.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Rendszerhez rendelt identitáshoz felhasználó által hozzárendelt identitások rendelkező virtuális gép eltávolításához törölje `SystemAssigned` származó a `{"identity":{"type:" "}}` értéket, miközben megtartja a `UserAssigned` érték és `identityIds` tömb, amely meghatározza, mely felhasználó által hozzárendelt identitások vannak rendelve a virtuális gép.

## <a name="user-assigned-identity"></a>A felhasználóhoz hozzárendelt identitás

Ebben a szakaszban megismerheti, hogyan adhat hozzá, és távolítsa el a felhasználóhoz hozzárendelt identitás-beli virtuális gépen az Azure Resource Manager REST-végponton való meghíváshoz a CURL használatával.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>Rendelje hozzá egy a felhasználóhoz hozzárendelt identitás egy Azure virtuális gép létrehozása során

1. A tulajdonosi hozzáférési jogkivonatot, amely az engedélyezési fejléc a következő lépésben használandó hozza létre a virtuális Géphez hozzárendelt felügyelt identitás rendszerrel beolvasni.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Hozzon létre egy [hálózati adapter](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) a virtuális géphez:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  A tulajdonosi hozzáférési jogkivonatot, amely az engedélyezési fejléc a következő lépésben használandó hozza létre a virtuális Géphez hozzárendelt felügyelt identitás rendszerrel beolvasni.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Az itt található utasításokat a felhasználóhoz hozzárendelt identitás létrehozása: [hozzon létre egy felügyelt identitás hozzárendelt felhasználó](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Hozzon létre egy virtuális Gépet a CURL használatával az Azure Resource Manager REST-végpont meghívására. A következő példában létrehozunk egy nevű virtuális Gépet *myVM* erőforráscsoportban *myResourceGroup* egy felhasználó által hozzárendelt identitással `ID1`, a kérelem törzsében szereplő értéke által meghatározott `"identity":{"type":"UserAssigned"}`. Cserélje le `<ACCESS TOKEN>` értékkel az előző lépés során kapott egy tulajdonosi jogkivonatot kért és a `<SUBSCRIPTION ID>` válasszon a környezetének megfelelő értékét.
 
   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"TestVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Rendelje hozzá egy a felhasználóhoz hozzárendelt identitás meglévő Azure virtuális géphez

1. A tulajdonosi hozzáférési jogkivonatot, amely az engedélyezési fejléc a következő lépésben használandó hozza létre a virtuális Géphez hozzárendelt felügyelt identitás rendszerrel beolvasni.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Az itt található utasításokat a felhasználóhoz hozzárendelt identitás létrehozása [hozzon létre egy felügyelt identitás hozzárendelt felhasználó](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3.  Annak érdekében, ne törölje a meglévő felhasználói vagy rendszer által hozzárendelt felügyelt identitások, a virtuális géphez hozzárendelt, fel kell a következő CURL-parancs használatával a virtuális géphez hozzárendelt identitás típusát. A virtuálisgép-méretezési csoporthoz hozzárendelt identitások rendelkezik felügyelt, ha szerepelnek a a `identity` értéket.

    ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    Ha a felhasználó vagy a virtuális géphez hozzárendelt, a rendszer által hozzárendelt identitások a `identity` értéke a válaszban, ugorjon az 5. lépés, amely bemutatja, hogyan, ha a felhasználó által hozzárendelt identitások miközben a rendszer által hozzárendelt identitással a virtuális Gépen.

4. Ha nem rendelkezik hozzárendelt identitások, a virtuális Géphez rendelt minden felhasználó, a következő CURL-parancs használatával hívja meg az Azure Resource Manager REST-végpont az első felhasználóhoz hozzárendelt identitás a virtuális géphez rendelhet.  Ha a felhasználóhoz rendelt identity(s) a virtuális géphez hozzárendelt, ugorjon a következő lépés, amely bemutatja, hogyan több felhasználó által hozzárendelt identitások hozzáadása egy virtuális Géphez.

   Az alábbi példa hozzárendeli a felhasználóhoz hozzárendelt identitás `ID1` nevű virtuális géphez *myVM* erőforráscsoportban *myResourceGroup*.  Cserélje le `<ACCESS TOKEN>` értékkel az előző lépés során kapott egy tulajdonosi jogkivonatot kért és a `<SUBSCRIPTION ID>` válasszon a környezetének megfelelő értékét.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

5. Ha a felhasználó vagy rendszer által hozzárendelt identitások, a virtuális Géphez rendelt, hozzá kell az új felhasználóhoz hozzárendelt identitás `identityIDs` tömb megőrzése mellett a felhasználó és a rendszer által hozzárendelt identitások, amelyek már hozzá vannak rendelve a virtuális géphez.

   Például, ha a rendszer az identitás- és a felhasználóhoz hozzárendelt identitás rendelt `ID1` jelenleg a virtuális géphez hozzárendelt, és szeretne hozzáadni a felhasználói identitás `ID2` , használja a következő CURL-parancsot. Cserélje le `<ACCESS TOKEN>` értékkel a lépések során kapott egy tulajdonosi jogkivonatot kért és a `<SUBSCRIPTION ID>` válasszon a környezetének megfelelő értékét.

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>Távolítsa el a a felhasználóhoz hozzárendelt identitás Azure virtuális gépből

1. A tulajdonosi hozzáférési jogkivonatot, amely az engedélyezési fejléc a következő lépésben használandó hozza létre a virtuális Géphez hozzárendelt felügyelt identitás rendszerrel beolvasni.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Annak érdekében, ne törölje a felügyelt identitásokból, adja meg a virtuális géphez hozzárendelt megtartani, vagy távolítsa el a rendszerhez rendelt identitáshoz bármely meglévő felhasználó, fel kell a felügyelt identitások használatával a következő CURL-parancsot: 
 
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   Ha a virtuális géphez hozzárendelt identitások rendelkezik felügyelt, ezek a válaszban szereplő jelennek az `identity` érték.

   Például, ha a felhasználó által hozzárendelt identitások rendelkezik `ID1` és `ID2` a virtuális géphez hozzárendelt, és csak szeretné tartani `ID1` hozzárendelt és megőrizni a rendszer által hozzárendelt identitással, ugyanazt a CURL-parancs használna a hozzárendelt felhasználók, identitás csak gondoskodik a virtuális géphez a `ID1` értékét, és tartsa a `SystemAssigned` értéket. Ezzel eltávolítja a `ID2` felhasználóhoz hozzárendelt identitás, a rendszer által hozzárendelt identitással megőrzése a virtuális gépről.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

Ha a virtuális Géphez hozzárendelt rendszer és a felhasználó által hozzárendelt identitások, eltávolíthatja az összes felhasználói rendelt identitásokkal váltásával használata csak a rendszer által hozzárendelt, a következő paranccsal:

```bash
curl 'https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/TestVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
    
Ha a virtuális gép rendelkezik hozzárendelt identitások kizárólag egyetlen felhasználó, és szeretné eltávolítani az összes, a következő paranccsal:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
## <a name="next-steps"></a>További lépések

Létrehozása, listázása és törlése a felhasználóhoz hozzárendelt REST használatával kapcsolatos információkért lásd:

- [Lista létrehozása, és a egy felhasználóhoz hozzárendelt identitás használatával REST API-hívások törlése](how-to-manage-ua-identity-rest.md)