---
title: Rendszer- és felhasználói konfigurálása rendelt identitást is-Azure VMSS REST használatával
description: Részletes utasításokat a rendszer és a felhasználó rendelt identitásokkal az Azure VMSS a REST API-hívásokat a CURL használatával.
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
ms.openlocfilehash: c4ccd75f679e58dd42f136bf1199eda88dc3f087
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431715"
---
# <a name="configure-managed-identity-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Virtuálisgép-méretezési REST API-hívások segítségével felügyelt identitás konfigurálása

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt identitások Azure-szolgáltatásokat az Azure Active Directoryban a rendszer automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ez a cikk ismerteti egy virtuálisgép-méretezési csoportot, a felügyelt identitást a következő műveletek végrehajtásához az Azure Resource Manager REST-végponton való meghíváshoz a CURL használatával:

- Engedélyezheti és tilthatja le a rendszer által hozzárendelt identitással egy Azure-beli virtuálisgép-méretezési
- Hozzáadhat és eltávolíthat egy Azure-beli virtuálisgép-méretezési egy felhasználóhoz hozzárendelt identitás

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a Felügyeltszolgáltatás-identitást, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség egy rendszer által hozzárendelt, és a felhasználóhoz hozzárendelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ez a cikk a felügyeleti műveleteket hajt végre, a fiók az alábbi szerepkör-hozzárendelések van szüksége:
    - [Virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) készlet egy virtuálisgép-méretezési csoport létrehozása és engedélyezése és a remove-rendszer és/vagy a felhasználó hozzárendelt felügyelt identitás egy virtuálisgép-méretezési csoportot.
    - [Felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkört a felhasználóhoz hozzárendelt identitás létrehozása.
    - [Felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör hozzárendelése, és távolítsa el a felhasználóhoz hozzárendelt identitás, a kezdő és a egy virtuálisgép-méretezési csoportot.
- Ha Windows használ, telepítse a [a Linux Windows alrendszere](https://msdn.microsoft.com/commandline/wsl/about) vagy használja a [Azure Cloud Shell](../../cloud-shell/overview.md) az Azure Portalon.
- [A helyi Azure CLI-konzol telepítése](/azure/install-azure-cli), ha a [a Linux Windows alrendszere](https://msdn.microsoft.com/commandline/wsl/about) vagy egy [linuxos](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Ha a helyi Azure CLI-konzolt használ, jelentkezzen be Azure-bA `az login` , amely az Azure társítva van egy olyan fiókkal, amelyet szeretne kezelni a rendszer vagy a felhasználó előfizetéshez rendelt identitásokkal.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Rendszerhez rendelt identitáshoz

Ebben a szakaszban megismerheti, hogyan engedélyezheti és tilthatja le a rendszerhez rendelt identitáshoz a CURL használatával az Azure Resource Manager REST-végponton való meghíváshoz virtuálisgép-méretezési csoportot.

### <a name="enable-system-assigned-identity-during-creation-of-a-virtual-machine-scale-set"></a>Rendszer által hozzárendelt identitással engedélyezése egy virtuálisgép-méretezési készlet létrehozása során

A rendszerhez rendelt identitáshoz engedélyezve van a virtuális gép méretezési csoport létrehozása, kell hozzon létre egy virtuálisgép-méretezési csoportot, és lekér egy hozzáférési jogkivonatot a CURL használatával hívja meg a Resource Manager-végpontot a rendszer identitást típusú értéket hozzárendelni.

1. Hozzon létre egy [erőforráscsoport](../../azure-resource-manager/resource-group-overview.md#terminology) tartalmazási és telepítéséhez, a virtuálisgép-méretezési csoportot és az összes kapcsolódó erőforrás, használatával [az csoport létrehozása](/cli/azure/group/#az-group-create). Ezt a lépést kihagyhatja, ha inkább egy meglévő erőforráscsoportot kíván használni:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Hozzon létre egy [hálózati adapter](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) a virtuálisgép-méretezési csoport beállítása:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Hozzáférés tulajdonosi jogkivonattal, amelyek alapján, az engedélyezési fejléc a következő lépésben hozzon létre egy felügyelt identitás hozzárendelt rendszerrel rendelkező virtuális gép méretezési lekéréséhez.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Hozzon létre egy virtuálisgép-méretezési csoportba a CURL használatával az Azure Resource Manager REST-végpont meghívására. Az alábbi példa létrehoz egy virtuálisgép-méretezési csoport nevű *myVMSS* a a *myResourceGroup* rendszerhez rendelt identitáshoz, a kérelem törzsében szereplő értéke által meghatározott `"identity":{"type":"SystemAssigned"}`. Cserélje le `<ACCESS TOKEN>` értékkel az előző lépés során kapott egy tulajdonosi jogkivonatot kért és a `<SUBSCRIPTION ID>` válasszon a környezetének megfelelő értékét.
 
    ```bash   
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
    ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Egy meglévő Azure-beli virtuálisgép-méretezési csoportot a rendszer által hozzárendelt identitással engedélyezése

Ahhoz, hogy a rendszerhez rendelt identitáshoz meg egy meglévő virtuálisgép-méretezési csoportot, akkor kell hozzáférési jogkivonat beszerzése és a CURL használatával a Resource Manager REST-végpont frissítése a identitástípus hívja.

1. Hozzáférés tulajdonosi jogkivonattal, amelyek alapján, az engedélyezési fejléc a következő lépésben hozzon létre egy felügyelt identitás hozzárendelt rendszerrel rendelkező virtuális gép méretezési lekéréséhez.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Használja a következő CURL-parancs az Azure Resource Manager REST-végpont ahhoz, hogy a virtuálisgép-méretezési csoport a rendszer által hozzárendelt identitással hívja be, mert az a kérelem törzsében szereplő értéke által azonosított `{"identity":{"type":"SystemAssigned"}` nevű virtuálisgép-méretezési csoportot a  *myVMSS*.  Cserélje le `<ACCESS TOKEN>` értékkel az előző lépés során kapott egy tulajdonosi jogkivonatot kért és a `<SUBSCRIPTION ID>` válasszon a környezetének megfelelő értékét.
   
   > [!IMPORTANT]
   > Annak érdekében, akkor ne törölje minden olyan meglévő felügyelt identitások, a virtuálisgép-méretezési csoporthoz rendelt felhasználó, fel kell a felhasználó által hozzárendelt identitások a következő CURL-paranccsal: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Ha a virtuálisgép-méretezési a hozzárendelt a felhasználóhoz hozzárendelt identitások a `identity` értéke a válaszban, folytassa a 3. lépés, amely bemutatja, miközben a rendszer által hozzárendelt identitással a virtuális a felhasználó által hozzárendelt identitások megőrzése Virtuálisgép-méretezési csoportot.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Ahhoz, hogy a rendszerhez rendelt identitáshoz egy meglévő felhasználó által hozzárendelt identitások virtuális gép méretezési, hozzá kell `SystemAssigned` , a `type` értéket.  
   
   Például ha a virtuális gép méretezési rendelkezik a felhasználó által hozzárendelt identitások `ID1` és `ID2` rendelve, és szeretné a rendszer által hozzárendelt identitással hozzáadni a virtuálisgép-méretezési csoportot, a következő CURL-hívást használja. Cserélje le `<ACCESS TOKEN>` és `<SUBSCRIPTION ID>` a környezetnek megfelelő értékekkel.
   
   ```bash
   curl -v 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="disable-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Tiltsa le az Azure-beli virtuálisgép-méretezési csoportot a rendszer által hozzárendelt identitással

Egy rendszer által hozzárendelt identitással egy meglévő virtuálisgép-méretezési csoportot a letiltásához szükséges hozzáférési jogkivonat beszerzése és a CURL használatával a Resource Manager REST-végpont a identitástípus való frissítéséhez hívja `None`.

1. Hozzáférés tulajdonosi jogkivonattal, amelyek alapján, az engedélyezési fejléc a következő lépésben hozzon létre egy felügyelt identitás hozzárendelt rendszerrel rendelkező virtuális gép méretezési lekéréséhez.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Állítsa be a CURL használatával az Azure Resource Manager REST-végpont meghívása a rendszerhez rendelt identitáshoz letiltása a virtuálisgép-méretezési csoport frissítése.  A következő példa letiltja a rendszer által hozzárendelt identitással, a kérelem törzsében szereplő értéke által meghatározott `{"identity":{"type":"None"}}` nevű virtuálisgép-méretezési csoportot a *myVMSS*.  Cserélje le `<ACCESS TOKEN>` értékkel az előző lépés során kapott egy tulajdonosi jogkivonatot kért és a `<SUBSCRIPTION ID>` válasszon a környezetének megfelelő értékét.

   > [!IMPORTANT]
   > Annak érdekében, akkor ne törölje minden olyan meglévő felügyelt identitások, a virtuálisgép-méretezési csoporthoz rendelt felhasználó, fel kell a felhasználó által hozzárendelt identitások a következő CURL-paranccsal: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Ha a virtuálisgép-méretezési csoporthoz rendelt identitást minden olyan felhasználó, ugorjon a 3. lépés, amely bemutatja, hogyan megőrizheti a felhasználó hozzárendelt identitások a rendszer a virtuálisgép-méretezési csoporthoz hozzárendelt identitás eltávolítása során.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Távolítsa el a rendszerhez rendelt identitáshoz felhasználó által hozzárendelt identitások rendelkező virtuális gép méretezési csoportból, távolítsa el a `SystemAssigned` származó a `{"identity":{"type:" "}}` értéket, miközben megtartja a `UserAssigned` érték és `identityIds` tömb a határozza meg, mely felhasználó által hozzárendelt identitások a virtuálisgép-méretezési csoporthoz vannak rendelve.

## <a name="user-assigned-identity"></a>A felhasználóhoz hozzárendelt identitás

Ebben a szakaszban megismerheti, hogyan adhat hozzá, és távolítsa el a felhasználóhoz hozzárendelt identitás beállítása a CURL használatával az Azure Resource Manager REST-végponton való meghíváshoz virtuálisgép-méretezési csoportot a.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Rendelje hozzá egy felhasználóhoz hozzárendelt identitás egy virtuálisgép-méretezési készlet létrehozása során

1. Hozzáférés tulajdonosi jogkivonattal, amelyek alapján, az engedélyezési fejléc a következő lépésben hozzon létre egy felügyelt identitás hozzárendelt rendszerrel rendelkező virtuális gép méretezési lekéréséhez.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Hozzon létre egy [hálózati adapter](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) a virtuálisgép-méretezési csoport beállítása:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Hozzáférés tulajdonosi jogkivonattal, amelyek alapján, az engedélyezési fejléc a következő lépésben hozzon létre egy felügyelt identitás hozzárendelt rendszerrel rendelkező virtuális gép méretezési lekéréséhez.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Az itt található utasításokat a felhasználóhoz hozzárendelt identitás létrehozása: [hozzon létre egy felügyelt identitás hozzárendelt felhasználó](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Hozzon létre egy virtuálisgép-méretezési csoportba a CURL használatával az Azure Resource Manager REST-végpont meghívására. Az alábbi példa létrehoz egy virtuálisgép-méretezési csoport nevű *myVMSS* erőforráscsoportban *myResourceGroup* egy felhasználó által hozzárendelt identitással `ID1`, ahogy az a kérelem törzsében által azonosított a érték `"identity":{"type":"UserAssigned"}`. Cserélje le `<ACCESS TOKEN>` értékkel az előző lépés során kapott egy tulajdonosi jogkivonatot kért és a `<SUBSCRIPTION ID>` válasszon a környezetének megfelelő értékét.
 
   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Egy felhasználóhoz hozzárendelt identitás hozzárendelése egy meglévő Azure-beli virtuálisgép-méretezési csoportot

1. Hozzáférés tulajdonosi jogkivonattal, amelyek alapján, az engedélyezési fejléc a következő lépésben hozzon létre egy felügyelt identitás hozzárendelt rendszerrel rendelkező virtuális gép méretezési lekéréséhez.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Az itt található utasításokat a felhasználóhoz hozzárendelt identitás létrehozása [hozzon létre egy felügyelt identitás hozzárendelt felhasználó](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Annak érdekében, ne törölje a meglévő felhasználói vagy felügyelt identitások, a virtuálisgép-méretezési csoporthoz rendelt hozzárendelt, fel kell az identitást, a virtuálisgép-méretezési csoport rendelt típusok beállítása a következő CURL-parancs használatával. A virtuálisgép-méretezési csoporthoz hozzárendelt identitások rendelkezik felügyelt, ha szerepelnek a a `identity` értéket.
 
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

4. Ha nem rendelkezik minden olyan felhasználó vagy rendszer által hozzárendelt identitások a virtuálisgép-méretezési csoporthoz rendelt, a következő CURL-parancs használatával hívja meg az Azure Resource Manager REST-végpont az első felhasználóhoz hozzárendelt identitás a virtuálisgép-méretezési csoporthoz hozzárendelni.  Ha egy felhasználó vagy rendszer hozzárendelni a virtuálisgép-méretezési csoporthoz rendelt identity(s), ugorjon az 5. lépés, amely bemutatja, hogyan több felhasználó által hozzárendelt identitások hozzáadása egy virtuális gép méretezési, mégis megmarad a rendszer által hozzárendelt identitással.

   Az alábbi példa hozzárendeli a felhasználóhoz hozzárendelt identitás `ID1` nevű virtuálisgép-méretezési *myVMSS* erőforráscsoportban *myResourceGroup*.  Cserélje le `<ACCESS TOKEN>` értékkel az előző lépés során kapott egy tulajdonosi jogkivonatot kért és a `<SUBSCRIPTION ID>` válasszon a környezetének megfelelő értékét.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

5. Ha a felhasználó vagy rendszer által hozzárendelt identitások a virtuálisgép-méretezési csoporthoz rendelt, hozzá kell az új felhasználóhoz hozzárendelt identitás `identityIDs` megőrzése mellett a felhasználó és a rendszer által hozzárendelt identitások, amelyek már hozzá vannak rendelve a virtuális tömb Virtuálisgép-méretezési csoportot.

   Például ha rendelkezik system hozzárendelt identitás- és a felhasználóhoz hozzárendelt identitás `ID1` jelenleg a virtuálisgép-méretezési csoporthoz hozzárendelt, és szeretne hozzáadni a felhasználói identitás `ID2` , használja a következő CURL-parancsot. Cserélje le `<ACCESS TOKEN>` értékkel a lépések során kapott egy tulajdonosi jogkivonatot kért és a `<SUBSCRIPTION ID>` válasszon a környezetének megfelelő értékét.

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="remove-a-user-assigned-identity-from-a-virtual-machine-scale-set"></a>Egy virtuálisgép-méretezési csoportot egy felhasználóhoz hozzárendelt identitás eltávolítása

1. Hozzáférés tulajdonosi jogkivonattal, amelyek alapján, az engedélyezési fejléc a következő lépésben hozzon létre egy felügyelt identitás hozzárendelt rendszerrel rendelkező virtuális gép méretezési lekéréséhez.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Annak érdekében, ne törölje a szeretné megtartani a virtuálisgép-méretezési csoporthoz hozzárendelt, vagy távolítsa el a rendszerhez rendelt identitáshoz felügyelt identitások bármely meglévő felhasználó, fel kell a felügyelt identitások használatával a következő CURL-parancsot: 
   
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```
   
   Ha a virtuális géphez hozzárendelt identitások rendelkezik felügyelt, ezek a válaszban szereplő jelennek az `identity` érték. 
    
   Például, ha a felhasználó által hozzárendelt identitások rendelkezik `ID1` és `ID2` a virtuálisgép-méretezési csoporthoz hozzárendelve, és csak szeretné tartani `ID1` hozzárendelt és megőrizni a rendszer által hozzárendelt identitással, használja a azonos CURL parancsot, Virtuálisgép-méretezési csoportot felügyelt identitás rendelt felhasználók beállítása csak tartja a `ID1` értékét, és tartsa a `SystemAssigned` értéket. Ezzel eltávolítja a `ID2` felhasználóhoz hozzárendelt identitás a virtuális gép méretezési, a rendszer által hozzárendelt identitással megőrzése.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

Ha a virtuálisgép-méretezési csoporthoz rendelt system és a felhasználó által hozzárendelt identitások, eltávolíthatja az összes felhasználói rendelt identitásokkal váltásával használata csak a rendszer által hozzárendelt, a következő paranccsal:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
    
Ha a virtuálisgép-méretezési csoporthoz rendelt identitásokkal kizárólag egyetlen felhasználó rendelkezik, és szeretné eltávolítani az összes, a következő paranccsal:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>További lépések

Létrehozása, listázása és törlése a felhasználóhoz hozzárendelt REST használatával kapcsolatos információkért lásd:

- [Lista létrehozása, és a egy felhasználóhoz hozzárendelt identitás használatával REST API-hívások törlése](how-to-manage-ua-identity-rest.md)