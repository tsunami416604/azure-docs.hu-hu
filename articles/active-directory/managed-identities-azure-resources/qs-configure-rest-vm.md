---
title: Rendszer és a felhasználó által hozzárendelt felügyelt identitások konfigurálása a REST használata Azure virtuális gép
description: Részletes utasításokat a rendszer és felhasználó által hozzárendelt felügyelt identitások-beli virtuális gépen, hogy a REST API-t a CURL használatával meghívja.
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
ms.openlocfilehash: 45f322f46ec26da9304acfad1c96a3978cac9cb7
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378792"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Felügyelt identitások az Azure-erőforrások konfigurálása a REST API hívásait használja, az Azure virtuális Gépekhez

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt identitások az Azure-erőforrások Azure-szolgáltatásokat az Azure Active Directoryban a rendszer automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebben a cikkben az Azure Resource Manager REST-végponthoz, hívásokat a CURL használatával mutatja a következő felügyelt identitások végrehajtani az Azure-erőforrások operations-beli virtuális gépen:

- Engedélyezheti és tilthatja le a rendszer által hozzárendelt felügyelt identitás-beli virtuális gépen
- Hozzáadhat és eltávolíthat a felhasználó által hozzárendelt felügyelt identitás-beli virtuális gépen

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség a rendszer által hozzárendelt, és a felhasználó által hozzárendelt felügyelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ez a cikk a felügyeleti műveleteket hajt végre, a fiók az alábbi Azure szerepkör-alapú access control-hozzárendelések van szüksége:

    > [!NOTE]
    > Nincsenek további Azure AD directory szerepkör-hozzárendelések megadása kötelező.

    - [Virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) virtuális gép létrehozása és engedélyezése, és távolítsa el a rendszer és/vagy felhasználó által hozzárendelt felügyelt identitás Azure virtuális gépből.
    - [Felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör, felhasználó által hozzárendelt identitás létrehozása.
    - [Felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör hozzárendelése és eltávolítása, felhasználó által hozzárendelt felügyelt identitás, a kezdő és a egy virtuális Gépet.
- Ha Windows használ, telepítse a [a Linux Windows alrendszere](https://msdn.microsoft.com/commandline/wsl/about) vagy használja a [Azure Cloud Shell](../../cloud-shell/overview.md) az Azure Portalon.
- [A helyi Azure CLI-konzol telepítése](/cli/azure/install-azure-cli), ha a [a Linux Windows alrendszere](https://msdn.microsoft.com/commandline/wsl/about) vagy egy [linuxos](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Ha a helyi Azure CLI-konzolt használ, jelentkezzen be Azure-bA `az login` egy olyan fiókkal, amely az Azure-előfizetés társítva van, a rendszer vagy felhasználó által hozzárendelt felügyelt identitások kezelése szeretné.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebben a szakaszban megismerheti, hogyan engedélyezheti és tilthatja le a rendszer által hozzárendelt felügyelt identitás-beli virtuális gépen az Azure Resource Manager REST-végponton való meghíváshoz a CURL használatával.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése egy Azure virtuális gép létrehozása során

Hozzon létre egy Azure virtuális Gépen a rendszer által kiosztott, felügyelt identitás engedélyezve van, kell hozzon létre egy virtuális Gépet, és lekér egy hozzáférési jogkivonatot a CURL használatával a Resource Manager-végpontot a rendszer által hozzárendelt felügyelt identitás típusú értékkel hívja.

1. Az [az group create](/cli/azure/group/#az-group-create) paranccsal hozzon létre egy [erőforráscsoportot](../../azure-resource-manager/resource-group-overview.md#terminology) a virtuális gép és az ahhoz kapcsolódó erőforrások elkülönítéséhez és üzembe helyezéséhez. Ezt a lépést kihagyhatja, ha inkább egy meglévő erőforráscsoportot kíván használni:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Hozzon létre egy [hálózati adapter](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) a virtuális géphez:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  A tulajdonosi hozzáférési jogkivonatot, amely a virtuális gép létrehozása felügyelt rendszer által hozzárendelt identitással az engedélyezési fejléc a következő lépésben használni fogja lekérni.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Hozzon létre egy virtuális Gépet a CURL használatával az Azure Resource Manager REST-végpont meghívására. A következő példában létrehozunk egy nevű virtuális Gépet *myVM* a rendszer által hozzárendelt felügyelt identitást, a kérelem törzsében szereplő értéke által meghatározott `"identity":{"type":"SystemAssigned"}`. Cserélje le `<ACCESS TOKEN>` értékkel az előző lépés során kapott egy tulajdonosi jogkivonatot kért és a `<SUBSCRIPTION ID>` válasszon a környezetének megfelelő értékét.
 
    ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
    ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Egy meglévő Azure virtuális gépen, rendszer által hozzárendelt identitás engedélyezése

Ahhoz, hogy egy meglévő virtuális Gépet a rendszer által hozzárendelt identitás, meg kell hozzáférési jogkivonat beszerzése és a CURL használatával a Resource Manager REST-végpont frissítése a identitástípus hívja.

1. A tulajdonosi hozzáférési jogkivonatot, amely a virtuális gép létrehozása felügyelt rendszer által hozzárendelt identitással az engedélyezési fejléc a következő lépésben használni fogja lekérni.

   ```azurecli-interactive
   az account get-access-token
   ```

2. A következő CURL-parancs használatával hívja meg az Azure Resource Manager REST-végpont engedélyezéséhez a rendszer által hozzárendelt felügyelt identitás, a virtuális Gépen, a kérelem törzsében szereplő értéke által meghatározott `{"identity":{"type":"SystemAssigned"}` nevű virtuális gép *myVM*.  Cserélje le `<ACCESS TOKEN>` értékkel az előző lépés során kapott egy tulajdonosi jogkivonatot kért és a `<SUBSCRIPTION ID>` válasszon a környezetének megfelelő értékét.
   
   > [!IMPORTANT]
   > Annak érdekében, ne törölje az összes meglévő, felhasználó által hozzárendelt felügyelt identitások, a virtuális géphez hozzárendelt, fel kell felügyelt felhasználó által hozzárendelt identitások a következő CURL-paranccsal: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Ha rendelkezik ilyennel a felhasználó által hozzárendelt felügyelt-e a virtuális géphez hozzárendelt identitások a `identity` értéke a válaszban, folytassa a 3. lépés, amely bemutatja, hogyan megőrizheti a felhasználó által hozzárendelt felügyelt identitások miközben a rendszer által hozzárendelt felügyelt identitás, a virtuális Gépen.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Ahhoz, hogy a rendszer által hozzárendelt felügyelt identitás a virtuális gép a meglévő felügyelt identitások felhasználó által hozzárendelt, hozzá kell `SystemAssigned` , a `type` értéket.  
   
   Például, ha a virtuális gép rendelkezik a felügyelt felhasználó által hozzárendelt identitások `ID1` és `ID2` rendelve, és szeretne felügyelt identitás alapértelmezett hozzáadni a virtuális Gépet, a következő CURL-hívást használja. Cserélje le `<ACCESS TOKEN>` és `<SUBSCRIPTION ID>` a környezetnek megfelelő értékekkel.

   API-verzió `2018-06-01` tárolja a felhasználó által hozzárendelt felügyelt identitások a `userAssignedIdentities` értéke egy szótár formátumú, nem pedig a `identityIds` értéke egy tömb formátumú API-verzióban használt `2017-12-01` és a korábbi verziói.
   
   **API-VERZIÓ 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **API-verzió 2017-12-01-os vagy korábbi**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Tiltsa le a rendszer által hozzárendelt felügyelt identitás Azure virtuális gépből

Tiltsa le a rendszer által hozzárendelt felügyelt identitás egy meglévő virtuális Gépet, a hozzáférési jogkivonat beszerzése és a CURL használatával a Resource Manager REST-végpont a identitástípus való frissítéséhez hívja szüksége `None`.

1. A tulajdonosi hozzáférési jogkivonatot, amely a virtuális gép létrehozása felügyelt rendszer által hozzárendelt identitással az engedélyezési fejléc a következő lépésben használni fogja lekérni.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Frissítse a virtuális Gépet az Azure Resource Manager REST-végpont hívja a CURL használatával felügyelt identitás alapértelmezett letiltása.  A következő példa letiltja a rendszer által hozzárendelt felügyelt identitás, a kérelem törzsében szereplő értéke által meghatározott `{"identity":{"type":"None"}}` nevű virtuális gépből *myVM*.  Cserélje le `<ACCESS TOKEN>` értékkel az előző lépés során kapott egy tulajdonosi jogkivonatot kért és a `<SUBSCRIPTION ID>` válasszon a környezetének megfelelő értékét.

   > [!IMPORTANT]
   > Annak érdekében, ne törölje az összes meglévő, felhasználó által hozzárendelt felügyelt identitások, a virtuális géphez hozzárendelt, fel kell felügyelt felhasználó által hozzárendelt identitások a következő CURL-paranccsal: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Ha rendelkezik ilyennel a felhasználó által hozzárendelt felügyelt-e a virtuális géphez hozzárendelt identitások a `identity` értéke a válaszban, folytassa a 3. lépés, amely bemutatja a felügyelt identitásokból felhasználó által hozzárendelt megőrzése a virtuális gép felügyelt identitás alapértelmezett letiltása közben.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Egy virtuális gépet, amely felhasználó által hozzárendelt felügyelt identitások felügyelt identitás alapértelmezett eltávolításához távolítsa el `SystemAssigned` származó a `{"identity":{"type:" "}}` értéket, miközben megtartja a `UserAssigned` érték és a `userAssignedIdentities` szótár értékek használata **API-verzió a 2018-06-01**. Ha használ **API 2017-12-01-es verzió** vagy korábbi, hagyja a `identityIds` tömb.

## <a name="user-assigned-managed-identity"></a>felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban megismerheti, hogyan hozzáadása és eltávolítása, felhasználó által hozzárendelt felügyelt identitás-beli virtuális gépen az Azure Resource Manager REST-végponton való meghíváshoz a CURL használatával.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Egy felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy Azure virtuális gép létrehozása során

1. A tulajdonosi hozzáférési jogkivonatot, amely a virtuális gép létrehozása felügyelt rendszer által hozzárendelt identitással az engedélyezési fejléc a következő lépésben használni fogja lekérni.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Hozzon létre egy [hálózati adapter](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) a virtuális géphez:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  A tulajdonosi hozzáférési jogkivonatot, amely a virtuális gép létrehozása felügyelt rendszer által hozzárendelt identitással az engedélyezési fejléc a következő lépésben használni fogja lekérni.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Az itt található utasításokat követve felügyelt felhasználó által hozzárendelt identitás létrehozása: [felügyelt felhasználó által hozzárendelt identitás létrehozása](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Hozzon létre egy virtuális Gépet a CURL használatával az Azure Resource Manager REST-végpont meghívására. A következő példában létrehozunk egy nevű virtuális Gépet *myVM* erőforráscsoportban *myResourceGroup* felügyelt felhasználó által hozzárendelt identitással `ID1`, a kérelem törzsében szereplő értékeáltalmeghatározott`"identity":{"type":"UserAssigned"}`. Cserélje le `<ACCESS TOKEN>` értékkel az előző lépés során kapott egy tulajdonosi jogkivonatot kért és a `<SUBSCRIPTION ID>` válasszon a környezetének megfelelő értékét.
 
   
   **API-VERZIÓ 2018-06-01**
    
   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ``` 

   **API-verzió 2017-12-01-os vagy korábbi**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Egy felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy meglévő Azure virtuális Gépen

1. A tulajdonosi hozzáférési jogkivonatot, amely a virtuális gép létrehozása felügyelt rendszer által hozzárendelt identitással az engedélyezési fejléc a következő lépésben használni fogja lekérni.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Az itt található utasításokat követve felügyelt felhasználó által hozzárendelt identitás létrehozása [felügyelt felhasználó által hozzárendelt identitás létrehozása](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3.  Annak érdekében, ne törölje a meglévő felhasználói vagy rendszer által hozzárendelt felügyelt identitások a virtuális géphez hozzárendelt, fel kell a következő CURL-parancs használatával a virtuális géphez hozzárendelt identitás típusát. A virtuálisgép-méretezési csoporthoz hozzárendelt identitások rendelkezik felügyelt, ha szerepelnek a a `identity` értéket.

    ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    Ha a felhasználó vagy a virtuális géphez hozzárendelt, a rendszer által hozzárendelt felügyelt identitások a `identity` értéke a válaszban, ugorjon az 5. lépés, amely bemutatja, hogyan csatlakoztatás a gazdagép alapértelmezett felügyelt identitás megőrzéséhez a felhasználó által hozzárendelt felügyelt identitás hozzáadása során a virtuális gép.

4. Ha nem rendelkezik a virtuális Géphez rendelt felügyelt felhasználó által hozzárendelt identitások, az első felhasználóhoz felügyelt identitásnak hozzárendelése a virtuális gép az Azure Resource Manager REST-végpont meghívása a következő CURL-parancs használatával.

   Az alábbi példák rendeli hozzá a felhasználó által hozzárendelt felügyelt identitást, `ID1` nevű virtuális géphez *myVM* erőforráscsoportban *myResourceGroup*.  Cserélje le `<ACCESS TOKEN>` értékkel az előző lépés során kapott egy tulajdonosi jogkivonatot kért és a `<SUBSCRIPTION ID>` válasszon a környezetének megfelelő értékét.

   **API-VERZIÓ 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **API-verzió 2017-12-01-os vagy korábbi**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

5. Ha rendelkezik egy meglévő felhasználó által hozzárendelt vagy rendszer által hozzárendelt felügyelt identitás a virtuális Géphez rendelt:
   
   **API-VERZIÓ 2018-06-01**

   Adja hozzá a felhasználóhoz felügyelt identitásnak a `userAssignedIdentities` szótár értéket.
    
   Például, ha a rendszer által hozzárendelt felügyelt identitás és a felhasználó által hozzárendelt felügyelt identitás `ID1` jelenleg a virtuális géphez hozzárendelt, és szeretne hozzáadni a felhasználóhoz felügyelt identitásnak `ID2` hozzá:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **API-verzió 2017-12-01-os vagy korábbi**

   Felügyelt felhasználó által hozzárendelt identitások ne szeretné megőrizni a `identityIds` tömb értéket az új felhasználóhoz felügyelt identitásnak hozzáadása során.

   Például, ha a rendszer által hozzárendelt felügyelt identitás és a felhasználó által hozzárendelt felügyelt identitás `ID1` jelenleg a virtuális géphez hozzárendelt, és szeretne hozzáadni a felhasználóhoz felügyelt identitásnak `ID2` hozzá: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Távolítsa el a felhasználó által hozzárendelt felügyelt identitás Azure virtuális gépből

1. A tulajdonosi hozzáférési jogkivonatot, amely a virtuális gép létrehozása felügyelt rendszer által hozzárendelt identitással az engedélyezési fejléc a következő lépésben használni fogja lekérni.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Annak érdekében, ne törölje a meglévő felhasználó által hozzárendelt felügyelt identitást, adja meg a virtuális géphez hozzárendelt megtartani, vagy távolítsa el a rendszer által hozzárendelt felügyelt identitás, fel kell a felügyelt identitások használatával a következő CURL-parancsot: 
 
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   Ha a virtuális géphez hozzárendelt identitások rendelkezik felügyelt, ezek a válaszban szereplő jelennek az `identity` érték.

   Például, ha a felhasználó által hozzárendelt felügyelt identitások rendelkezik `ID1` és `ID2` a virtuális géphez hozzárendelt, és csak szeretné megőrizni `ID1` hozzárendelve, és a rendszer által hozzárendelt identitás megőrzése:
   
   **API-VERZIÓ 2018-06-01**

   Adjon hozzá `null` felügyelt identitás, amelyet szeretne eltávolítani a felhasználó által hozzárendelt:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **API-verzió 2017-12-01-os vagy korábbi**

   Csak a felhasználó által hozzárendelt identity(s) szeretné tartani felügyelt megőrizni a `identityIds` tömb:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

Ha a virtuális gép rendelkezik, mindkét rendszer által hozzárendelt, és a felügyelt identitásokból felhasználó által hozzárendelt, eltávolíthatja az összes felhasználó által hozzárendelt felügyelt identitások közötti váltás csak alapértelmezett felügyelt identitás használatára a következő paranccsal:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
    
Ha a virtuális gép rendelkezik felügyelt identitások csak felhasználó által hozzárendelt, és szeretné eltávolítani az összes, a következő paranccsal:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
## <a name="next-steps"></a>További lépések

Létrehozása, listázása és törlése a felhasználó által hozzárendelt felügyelt identitások REST használatával kapcsolatos információkért lásd:

- [Lista létrehozása, vagy egy REST API-hívások segítségével felügyelt felhasználó által hozzárendelt Identitások törlése](how-to-manage-ua-identity-rest.md)
