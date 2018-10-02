---
title: Rendszer- és felhasználó által hozzárendelt felügyelt identitások konfigurálása az Azure VMSS REST használatával
description: Részletes utasításokat a rendszer és felhasználó által hozzárendelt felügyelt identitások a CURL használatával, hogy a REST API-t egy Azure-VMSS-hívás.
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
ms.openlocfilehash: e76375ac06824d03e519ba06da838a0707922280
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48017416"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Felügyelt identitások az Azure-erőforrások konfigurálása egy virtuálisgép-méretezési csoportot a REST API-hívások

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt identitások az Azure-erőforrások Azure-szolgáltatásokat az Azure Active Directoryban a rendszer automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebben a cikkben az Azure Resource Manager REST-végponthoz, hívásokat a CURL használatával mutatja a következő felügyelt identitások egy virtuálisgép-méretezési csoportot az Azure-erőforrások műveletek végrehajtásához:

- Engedélyezheti és tilthatja le a rendszer által hozzárendelt felügyelt identitás egy Azure-beli virtuálisgép-méretezési csoportot
- Hozzáadhat és eltávolíthat egy felhasználó által hozzárendelt felügyelt identitás egy Azure-beli virtuálisgép-méretezési csoportot

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség a rendszer által hozzárendelt, és a felhasználó által hozzárendelt felügyelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ez a cikk a felügyeleti műveleteket hajt végre, a fiók az alábbi Azure szerepkör-alapú access control-hozzárendelések van szüksége:

    > [!NOTE]
    > Nincsenek további Azure AD directory szerepkör-hozzárendelések megadása kötelező.

    - [Virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) egy virtuálisgép-méretezési csoport létrehozása és engedélyezése és rendszer-és/vagy felhasználó által hozzárendelt felügyelt identitás eltávolítása egy virtuálisgép-méretezési csoportot.
    - [Felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) hozhat létre felhasználó által hozzárendelt szerepkör felügyelt identitás.
    - [Felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör hozzárendelése és a egy felhasználó által hozzárendelt identitással eltávolítása a kezdő és a egy virtuálisgép-méretezési csoportot.
- Ha Windows használ, telepítse a [a Linux Windows alrendszere](https://msdn.microsoft.com/commandline/wsl/about) vagy használja a [Azure Cloud Shell](../../cloud-shell/overview.md) az Azure Portalon.
- [A helyi Azure CLI-konzol telepítése](/cli/azure/install-azure-cli), ha a [a Linux Windows alrendszere](https://msdn.microsoft.com/commandline/wsl/about) vagy egy [linuxos](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Ha a helyi Azure CLI-konzolt használ, jelentkezzen be Azure-bA `az login` egy olyan fiókkal, amely az Azure-előfizetés társítva van, a rendszer vagy felhasználó által hozzárendelt felügyelt identitások kezelése szeretné.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebben a szakaszban megismerheti, hogyan engedélyezheti és tilthatja le a rendszer által hozzárendelt felügyelt identitás a CURL használatával az Azure Resource Manager REST-végponton való meghíváshoz virtuálisgép-méretezési csoportot.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése egy virtuálisgép-méretezési készlet létrehozása során

A rendszer által kiosztott, felügyelt identitás engedélyezve van a virtuális gép méretezési csoport létrehozása, kell hozzon létre egy virtuálisgép-méretezési csoportot, és lekér egy hozzáférési jogkivonatot a CURL használatával a Resource Manager-végpontot a rendszer által hozzárendelt felügyelt identitás típusú értékkel hívja.

1. Hozzon létre egy [erőforráscsoport](../../azure-resource-manager/resource-group-overview.md#terminology) tartalmazási és telepítéséhez, a virtuálisgép-méretezési csoportot és az összes kapcsolódó erőforrás, használatával [az csoport létrehozása](/cli/azure/group/#az-group-create). Ezt a lépést kihagyhatja, ha inkább egy meglévő erőforráscsoportot kíván használni:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Hozzon létre egy [hálózati adapter](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) a virtuálisgép-méretezési csoport beállítása:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Hozzáférés tulajdonosi jogkivonattal, amelyek alapján, az engedélyezési fejléc a következő lépésben hozzon létre egy felügyelt identitás rendszer által hozzárendelt virtuális gép méretezési lekéréséhez.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Hozzon létre egy virtuálisgép-méretezési csoportba a CURL használatával az Azure Resource Manager REST-végpont meghívására. Az alábbi példa létrehoz egy virtuálisgép-méretezési csoport nevű *myVMSS* a a *myResourceGroup* a rendszer által hozzárendelt felügyelt identitást, a kérelem törzsében szereplő értéke által meghatározott `"identity":{"type":"SystemAssigned"}`. Cserélje le `<ACCESS TOKEN>` értékkel az előző lépés során kapott egy tulajdonosi jogkivonatot kért és a `<SUBSCRIPTION ID>` válasszon a környezetének megfelelő értékét.
 
    ```bash   
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
    ```

### <a name="enable-system-assigned-managed-identity-on-a-existing-virtual-machine-scale-set"></a>Egy meglévő virtuálisgép-méretezési csoportot a felügyelt identitás alapértelmezett engedélyezése

Ahhoz, hogy egy meglévő virtuálisgép-méretezési csoportot a felügyelt identitás rendszer által hozzárendelt, meg kell hozzáférési jogkivonat beszerzése és a CURL használatával a Resource Manager REST-végpont frissítése a identitástípus hívja.

1. Hozzáférés tulajdonosi jogkivonattal, amelyek alapján, az engedélyezési fejléc a következő lépésben hozzon létre egy felügyelt identitás rendszer által hozzárendelt virtuális gép méretezési lekéréséhez.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Használja a következő CURL-parancs az Azure Resource Manager REST-végpont engedélyezni a rendszer által hozzárendelt felügyelt identitást, a virtuálisgép-méretezési hívja be, mert az a kérelem törzsében szereplő értéke által azonosított `{"identity":{"type":"SystemAssigned"}` a virtuálisgép-méretezési csoportot nevű *myVMSS*.  Cserélje le `<ACCESS TOKEN>` értékkel az előző lépés során kapott egy tulajdonosi jogkivonatot kért és a `<SUBSCRIPTION ID>` válasszon a környezetének megfelelő értékét.
   
   > [!IMPORTANT]
   > Annak érdekében, ne törölje a meglévő felhasználó által hozzárendelt felügyelt identitást a virtuálisgép-méretezési csoporthoz rendelt, fel kell felügyelt felhasználó által hozzárendelt identitások a következő CURL-paranccsal: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Ha rendelkezik ilyennel a felhasználó által hozzárendelt felügyelt-e a virtuálisgép-méretezési a hozzárendelt identitások a `identity` értéke a válaszban, folytassa a 3. lépés, amely bemutatja, miközben a rendszer által hozzárendelt felhasználó által hozzárendelt felügyelt identitások megőrzése a virtuálisgép-méretezési felügyelt identitás.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Ahhoz, hogy a rendszer által hozzárendelt felügyelt identitás egy meglévő felhasználó által hozzárendelt felügyelt identitások virtuális gép méretezési, hozzá kell `SystemAssigned` , a `type` értéket.  
   
   Például, ha a virtuális gép méretezési a felhasználó által hozzárendelt felügyelt identitások `ID1` és `ID2` rendelve, és szeretné a rendszer által hozzárendelt felügyelt identitás hozzáadni a virtuálisgép-méretezési csoportot, a következő CURL-hívást használja. Cserélje le `<ACCESS TOKEN>` és `<SUBSCRIPTION ID>` a környezetnek megfelelő értékekkel.

   API-verzió `2018-06-01` tárolja a felhasználó által hozzárendelt felügyelt identitások a `userAssignedIdentities` értéke egy szótár formátumú, nem pedig a `identityIds` értéke egy tömb formátumú API-verzióban használt `2017-12-01`.
   
   **API-VERZIÓ 2018-06-01**

   ```bash
   curl -v 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```
   
   **API-VERZIÓ 2017-12-01**

   ```bash
   curl -v 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>A felügyelt identitás tiltsa le a rendszer által hozzárendelt virtuálisgép-méretezési csoportot

Egy rendszer által hozzárendelt identitással egy meglévő virtuálisgép-méretezési csoportot a letiltásához a hozzáférési jogkivonat beszerzése és a CURL használatával frissíteni az azonosító típusát a Resource Manager REST-végpont meghívása szeretne `None`.

1. Hozzáférés tulajdonosi jogkivonattal, amelyek alapján, az engedélyezési fejléc a következő lépésben hozzon létre egy felügyelt identitás rendszer által hozzárendelt virtuális gép méretezési lekéréséhez.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Állítsa be a CURL használatával az Azure Resource Manager REST-végpont meghívására alapértelmezett felügyelt identitás letiltása a virtuálisgép-méretezési csoport frissítése.  A következő példa letiltja a rendszer által hozzárendelt felügyelt identitás, a kérelem törzsében szereplő értéke által meghatározott `{"identity":{"type":"None"}}` nevű virtuálisgép-méretezési csoportot a *myVMSS*.  Cserélje le `<ACCESS TOKEN>` értékkel az előző lépés során kapott egy tulajdonosi jogkivonatot kért és a `<SUBSCRIPTION ID>` válasszon a környezetének megfelelő értékét.

   > [!IMPORTANT]
   > Annak érdekében, ne törölje a meglévő felhasználó által hozzárendelt felügyelt identitást a virtuálisgép-méretezési csoporthoz rendelt, fel kell felügyelt felhasználó által hozzárendelt identitások a következő CURL-paranccsal: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Ha bármely a virtuálisgép-méretezési csoporthoz rendelt felügyelt felhasználó által hozzárendelt identitások, ugorjon a 3. lépés, amely bemutatja, hogyan felügyelt felhasználó által hozzárendelt identitások megőrzi során a rendszer által hozzárendelt felügyelt identitás eltávolítását a virtuálisgép-méretezési csoportot.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Egy virtuális gép méretezési csoportot, amelynek a felhasználó által hozzárendelt felügyelt identitások felügyelt identitás alapértelmezett eltávolításához távolítsa el `SystemAssigned` származó a `{"identity":{"type:" "}}` értéket, miközben megtartja a `UserAssigned` érték és a `userAssignedIdentities` szótár értékkel, használ **API-verzió a 2018-06-01**. Ha használ **API 2017-12-01-es verzió** vagy korábbi, hagyja a `identityIds` tömb.

## <a name="user-assigned-managed-identity"></a>felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban megismerheti, hogyan hozzáadása és eltávolítása, felhasználó által hozzárendelt felügyelt identitás a CURL használatával az Azure Resource Manager REST-végponton való meghíváshoz virtuálisgép-méretezési csoportot.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Egy felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy virtuálisgép-méretezési készlet létrehozása során

1. Hozzáférés tulajdonosi jogkivonattal, amelyek alapján, az engedélyezési fejléc a következő lépésben hozzon létre egy felügyelt identitás rendszer által hozzárendelt virtuális gép méretezési lekéréséhez.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Hozzon létre egy [hálózati adapter](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) a virtuálisgép-méretezési csoport beállítása:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Hozzáférés tulajdonosi jogkivonattal, amelyek alapján, az engedélyezési fejléc a következő lépésben hozzon létre egy felügyelt identitás rendszer által hozzárendelt virtuális gép méretezési lekéréséhez.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Az itt található utasításokat követve felügyelt felhasználó által hozzárendelt identitás létrehozása: [felügyelt felhasználó által hozzárendelt identitás létrehozása](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Hozzon létre egy virtuálisgép-méretezési csoportba a CURL használatával az Azure Resource Manager REST-végpont meghívására. Az alábbi példa létrehoz egy virtuálisgép-méretezési csoport nevű *myVMSS* erőforráscsoportban *myResourceGroup* felügyelt felhasználó által hozzárendelt identitással `ID1`, ahogy az a kérelem törzsében szereplő az érték `"identity":{"type":"UserAssigned"}`. Cserélje le `<ACCESS TOKEN>` értékkel az előző lépés során kapott egy tulajdonosi jogkivonatot kért és a `<SUBSCRIPTION ID>` válasszon a környezetének megfelelő értékét.
 
   **API-VERZIÓ 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus",{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   **API-VERZIÓ 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Egy felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy meglévő Azure-beli virtuálisgép-méretezési csoportot

1. Hozzáférés tulajdonosi jogkivonattal, amelyek alapján, az engedélyezési fejléc a következő lépésben hozzon létre egy felügyelt identitás rendszer által hozzárendelt virtuális gép méretezési lekéréséhez.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Az itt található utasításokat követve felügyelt felhasználó által hozzárendelt identitás létrehozása [felügyelt felhasználó által hozzárendelt identitás létrehozása](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Annak érdekében, ne törölje a meglévő felhasználói vagy rendszer által hozzárendelt felügyelt identitások a virtuálisgép-méretezési csoporthoz rendelt, fel kell az identitást, a virtuálisgép-méretezési csoport rendelt típusok beállítása a következő CURL-parancs használatával. A virtuálisgép-méretezési csoporthoz hozzárendelt identitások rendelkezik felügyelt, ha szerepelnek a `identity` értéket.
 
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

4. Ha nem kell minden olyan felhasználó vagy a rendszer által hozzárendelt felügyelt a virtuálisgép-méretezési csoport hozzárendelt identitások állított, akkor a következő CURL-parancs használatával az első felhasználóhoz felügyelt identitásnak hozzárendelése a virtuális gép az Azure Resource Manager REST-végpont meghívása méretezési csoportot.  Ha egy felhasználó vagy rendszer által kiosztott, felügyelt identity(s) a virtuálisgép-méretezési csoporthoz rendelt, hagyja ki az 5. lépés, amely bemutatja, hogyan több felhasználó által hozzárendelt felügyelt identitás hozzáadása egy virtuálisgép-méretezési csoportot is a rendszer által hozzárendelt felügyelt fenntartásához identitás.

   Az alábbi példa hozzárendeli a felhasználóhoz felügyelt identitásnak, `ID1` nevű virtuálisgép-méretezési *myVMSS* erőforráscsoportban *myResourceGroup*.  Cserélje le `<ACCESS TOKEN>` értékkel az előző lépés során kapott egy tulajdonosi jogkivonatot kért és a `<SUBSCRIPTION ID>` válasszon a környezetének megfelelő értékét.

   **API-VERZIÓ 2018-06-01**

    ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   
    
   **API-VERZIÓ 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

5. Ha rendelkezik egy meglévő felhasználó által hozzárendelt vagy rendszer által hozzárendelt felügyelt identitás hozzárendelni a virtuálisgép-méretezési csoporthoz:
   
   **API-VERZIÓ 2018-06-01**

   Adja hozzá a felhasználóhoz felügyelt identitásnak a `userAssignedIdentities` szótár értéket.

   Például, ha a rendszer által hozzárendelt felügyelt identitás és a felhasználó által hozzárendelt felügyelt identitás `ID1` jelenleg a virtuálisgép-méretezési csoport rendelve, és szeretne hozzáadni a felhasználóhoz felügyelt identitásnak `ID2` hozzá:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **API-VERZIÓ 2017-12-01**

   Felügyelt felhasználó által hozzárendelt identitások ne szeretné megőrizni a `identityIds` tömb értéket az új felhasználóhoz felügyelt identitásnak hozzáadása során.

   Például, ha a rendszer által hozzárendelt identitás- és a felhasználó által hozzárendelt felügyelt identitás rendelkezik `ID1` jelenleg a virtuálisgép-méretezési csoporthoz hozzárendelve, és szeretne hozzáadni a felhasználóhoz felügyelt identitásnak `ID2` hozzá: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Egy virtuálisgép-méretezési csoportot egy felhasználó által hozzárendelt felügyelt identitás eltávolítása

1. Hozzáférés tulajdonosi jogkivonattal, amelyek alapján, az engedélyezési fejléc a következő lépésben hozzon létre egy felügyelt identitás rendszer által hozzárendelt virtuális gép méretezési lekéréséhez.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Annak érdekében, ne törölje a meglévő felhasználó által hozzárendelt felügyelt identitást, amely a szeretné megtartani a virtuálisgép-méretezési csoporthoz hozzárendelt, vagy távolítsa el a rendszer által hozzárendelt felügyelt identitás, fel kell a felügyelt identitásokból a következő CURL-parancs használatával : 
   
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```
   
   Ha a virtuális géphez hozzárendelt identitások rendelkezik felügyelt, ezek a válaszban szereplő jelennek az `identity` érték. 
    
   Például, ha a felhasználó által hozzárendelt felügyelt identitások rendelkezik `ID1` és `ID2` a virtuálisgép-méretezési csoporthoz hozzárendelve, és csak szeretné megőrizni `ID1` hozzárendelve, és a rendszer által hozzárendelt felügyelt identitás megőrzése:

   **API-VERZIÓ 2018-06-01**

   Adjon hozzá `null` felügyelt identitás, amelyet szeretne eltávolítani a felhasználó által hozzárendelt:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **API-VERZIÓ 2017-12-01**

   Csak a felhasználó által hozzárendelt identity(s) szeretné tartani felügyelt megőrizni a `identityIds` tömb:   

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

Ha a virtuálisgép-méretezési rendelkezik, mindkét rendszer által hozzárendelt, és a felügyelt identitások felhasználó által hozzárendelt is eltávolítja az összes felhasználó által hozzárendelt felügyelt identitások használata között csak alapértelmezett a következő paranccsal:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
    
Ha a virtuálisgép-méretezési rendelkezik felügyelt identitások csak felhasználó által hozzárendelt, és szeretné őket az összes eltávolításához használja a következő parancsot:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>További lépések

Létrehozása, listázása és törlése a felhasználó által hozzárendelt felügyelt identitások REST használatával kapcsolatos információkért lásd:

- [Lista létrehozása, vagy egy REST API-hívások segítségével felügyelt felhasználó által hozzárendelt Identitások törlése](how-to-manage-ua-identity-rest.md)
