---
title: Felügyelt identitások konfigurálása az Azure-beli virtuális gépen a REST-Azure AD használatával
description: Részletes útmutató a rendszer és a felhasználó által hozzárendelt felügyelt identitások Azure-beli virtuális gépeken történő konfigurálásához a CURL használatával REST API-hívások létrehozásához.
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
ms.date: 06/25/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d78ddaaae886a33b4d22e8724ade04ab63508f1
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547340"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Felügyelt identitások konfigurálása Azure-beli virtuális gépeken lévő Azure-erőforrásokhoz REST API hívások használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitásai Azure-szolgáltatásokat biztosítanak a Azure Active Directory automatikusan felügyelt rendszeridentitással. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, és nem rendelkezik hitelesítő adatokkal a kódban. 

Ebből a cikkből megtudhatja, hogyan hajthatja végre a következő felügyelt identitásokat az Azure-beli virtuális gépeken az Azure-erőforrások műveleteihez a CURL használatával a Azure Resource Manager REST-végpont hívásához:

- A rendszer által hozzárendelt felügyelt identitás engedélyezése és letiltása egy Azure-beli virtuális gépen
- Felhasználó által hozzárendelt felügyelt identitás hozzáadása és eltávolítása egy Azure-beli virtuális gépen

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md). **Mindenképpen tekintse át a [rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ha Windows rendszert használ, telepítse a [Linux Windows alrendszerét](https://msdn.microsoft.com/commandline/wsl/about) , vagy használja a Azure Portal [Azure Cloud Shell](../../cloud-shell/overview.md) .
- [Telepítse az Azure CLI helyi konzolját](/cli/azure/install-azure-cli), ha a [Windows alrendszert](https://msdn.microsoft.com/commandline/wsl/about) használja a Linux vagy Linux rendszerű [terjesztési operációs rendszeren](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Ha az Azure CLI helyi konzolt használja, jelentkezzen be az Azure-ba a `az login` használatával egy olyan fiókkal, amely a rendszer vagy a felhasználó által hozzárendelt felügyelt identitások kezeléséhez használni kívánt Azure-előfizetéshez van társítva.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebből a szakaszból megtudhatja, hogyan engedélyezheti és tilthatja le a rendszerhez rendelt felügyelt identitást egy Azure virtuális gépen a CURL használatával a Azure Resource Manager REST-végpontra irányuló hívások kezdeményezéséhez.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>A rendszer által hozzárendelt felügyelt identitás engedélyezése egy Azure-beli virtuális gép létrehozásakor

Ha olyan Azure virtuális gépet szeretne létrehozni, amelyen engedélyezve van a rendszerhez rendelt felügyelt identitás, a fióknak szüksége van a [virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelésre.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Az [az group create](/cli/azure/group/#az-group-create) paranccsal hozzon létre egy [erőforráscsoportot](../../azure-resource-manager/resource-group-overview.md#terminology) a virtuális gép és az ahhoz kapcsolódó erőforrások elkülönítéséhez és üzembe helyezéséhez. Ezt a lépést kihagyhatja, ha inkább egy meglévő erőforráscsoportot kíván használni:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Hozzon létre egy [hálózati adaptert](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) a virtuális géphez:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Kérje le a tulajdonos hozzáférési jogkivonatát, amelyet az engedélyezési fejléc következő lépésében fog használni a virtuális gép rendszerhez rendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Hozzon létre egy virtuális gépet a CURL használatával a Azure Resource Manager REST-végpont meghívásához. Az alábbi példa egy *myVM* nevű virtuális gépet hoz létre egy rendszer által hozzárendelt felügyelt identitással, amelyet a kérés törzse a `"identity":{"type":"SystemAssigned"}`értékkel azonosít. Cserélje le a `<ACCESS TOKEN>` értéket az előző lépésben kapott értékre, amikor a tulajdonos hozzáférési jogkivonatát és a `<SUBSCRIPTION ID>` értékét az adott környezetnek megfelelőnek kérte.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Kérések fejlécei**
   
   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva.        | 
   
   **Kérelem törzse**

   ```JSON
     {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }  
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Rendszerhez rendelt identitás engedélyezése meglévő Azure-beli virtuális gépen

Ha olyan virtuális gépen szeretné engedélyezni a rendszer által hozzárendelt felügyelt identitást, amelyet eredetileg anélkül osztott ki, a fióknak szüksége van a [virtuális gép közreműködői](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkörének hozzárendelésére.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Kérje le a tulajdonos hozzáférési jogkivonatát, amelyet az engedélyezési fejléc következő lépésében fog használni a virtuális gép rendszerhez rendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ```

2. A következő CURL-paranccsal hívhatja meg a Azure Resource Manager REST-végpontot, hogy engedélyezze a rendszerhez rendelt felügyelt identitást a virtuális gépen a kérés törzsében a *myVM*nevű virtuális gép értéke `{"identity":{"type":"SystemAssigned"}` értékével.  Cserélje le a `<ACCESS TOKEN>` értéket az előző lépésben kapott értékre, amikor a tulajdonos hozzáférési jogkivonatát és a `<SUBSCRIPTION ID>` értékét az adott környezetnek megfelelőnek kérte.
   
   > [!IMPORTANT]
   > Annak biztosítása érdekében, hogy ne törölje a virtuális géphez hozzárendelt meglévő, felhasználóhoz rendelt felügyelt identitásokat, a következő CURL-parancs használatával fel kell sorolnia a felhasználó által hozzárendelt felügyelt identitásokat: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Ha a virtuális géphez a válasz `identity` értékében azonosított, felhasználó által hozzárendelt felügyelt identitások vannak társítva, ugorjon a 3. lépésre, amely bemutatja, hogyan őrzi meg a felhasználó által hozzárendelt felügyelt identitásokat, miközben engedélyezi a rendszerhez rendelt felügyelt identitást a virtuális gépen.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Kérések fejlécei**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva.        | 
   
   **Kérelem törzse**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Ha a rendszer által hozzárendelt felügyelt identitást egy meglévő, felhasználó által hozzárendelt felügyelt identitással rendelkező virtuális gépen szeretné engedélyezni, `SystemAssigned`t kell hozzáadnia a `type` értékhez.  
   
   Ha például a virtuális gép rendelkezik a felhasználó által hozzárendelt felügyelt identitásokkal `ID1` és `ID2` hozzájuk rendelve, és a virtuális géphez a rendszerhez rendelt felügyelt identitást szeretné hozzáadni, használja a következő CURL-hívást. Cserélje le a `<ACCESS TOKEN>` és a `<SUBSCRIPTION ID>` értéket a környezetének megfelelő értékekkel.

   Az API-verzió `2018-06-01` a felhasználó által hozzárendelt felügyelt identitásokat a szótár formátuma `userAssignedIdentities` értékében tárolja, szemben az API-verzió `2017-12-01`használt tömb formátumában lévő `identityIds` értékkel.
   
   **API-VERZIÓ 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Kérések fejlécei**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva.        | 

   **Kérelem törzse**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{  
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{  
    
             }
          }
       }
    }
   ```

   **API-VERZIÓ 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **Kérések fejlécei**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva.        | 

   **Kérelem törzse**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Rendszer által hozzárendelt felügyelt identitás letiltása egy Azure-beli virtuális gépről

Ha le szeretné tiltani a rendszerhez rendelt felügyelt identitást egy virtuális gépen, a fióknak szüksége van a [virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelésre.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Kérje le a tulajdonos hozzáférési jogkivonatát, amelyet az engedélyezési fejléc következő lépésében fog használni a virtuális gép rendszerhez rendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Frissítse a virtuális gépet a CURL használatával, hogy meghívja a Azure Resource Manager REST-végpontot a rendszer által hozzárendelt felügyelt identitás letiltásához.  A következő példa letiltja a rendszer által hozzárendelt felügyelt identitást a kérelem törzsében azonosított módon, a *myVM*nevű virtuális gépről `{"identity":{"type":"None"}}` értékkel.  Cserélje le a `<ACCESS TOKEN>` értéket az előző lépésben kapott értékre, amikor a tulajdonos hozzáférési jogkivonatát és a `<SUBSCRIPTION ID>` értékét az adott környezetnek megfelelőnek kérte.

   > [!IMPORTANT]
   > Annak biztosítása érdekében, hogy ne törölje a virtuális géphez hozzárendelt meglévő, felhasználóhoz rendelt felügyelt identitásokat, a következő CURL-parancs használatával fel kell sorolnia a felhasználó által hozzárendelt felügyelt identitásokat: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Ha a virtuális géphez a válasz `identity` értékében azonosított, felhasználó által hozzárendelt felügyelt identitások vannak társítva, ugorjon a 3. lépésre, amely bemutatja, hogyan őrzi meg a felhasználó által hozzárendelt felügyelt identitásokat, miközben letiltja a rendszerhez rendelt felügyelt identitást a virtuális gépen.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Kérések fejlécei**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva.        | 

   **Kérelem törzse**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Ha a rendszer által hozzárendelt felügyelt identitást egy olyan virtuális gépről szeretné eltávolítani, amely felhasználó által hozzárendelt felügyelt identitásokkal rendelkezik, távolítsa el `SystemAssigned` a `{"identity":{"type:" "}}` értékről, miközben a `UserAssigned` értéket és a `userAssignedIdentities` szótár értékét használja, ha az **API 2018-06-01**-es verzióját használja. Ha a 2017-12-01-es vagy korábbi **API-verziót** használja, tartsa meg a `identityIds` tömböt.

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebből a szakaszból megtudhatja, hogyan adhat hozzá és távolíthat el felhasználó által hozzárendelt felügyelt identitást egy Azure virtuális gépen a CURL használatával a Azure Resource Manager REST-végpontra irányuló hívások kezdeményezéséhez.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy Azure-beli virtuális gép létrehozásakor

Ha felhasználó által hozzárendelt identitást szeretne hozzárendelni egy virtuális géphez, a fióknak szüksége van a [virtuális gép közreműködői](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) és [felügyelt identitás-kezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör-hozzárendeléseire. Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Kérje le a tulajdonos hozzáférési jogkivonatát, amelyet az engedélyezési fejléc következő lépésében fog használni a virtuális gép rendszerhez rendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Hozzon létre egy [hálózati adaptert](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) a virtuális géphez:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Kérje le a tulajdonos hozzáférési jogkivonatát, amelyet az engedélyezési fejléc következő lépésében fog használni a virtuális gép rendszerhez rendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az itt található utasítások segítségével: [hozzon létre egy felhasználó által hozzárendelt felügyelt identitást](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Hozzon létre egy virtuális gépet a CURL használatával a Azure Resource Manager REST-végpont meghívásához. A következő példa egy *myVM* nevű virtuális gépet hoz létre az erőforráscsoport *myResourceGroup* egy felhasználó által hozzárendelt felügyelt identitással `ID1`, a kérelem törzsében a `"identity":{"type":"UserAssigned"}`érték alapján azonosítva. Cserélje le a `<ACCESS TOKEN>` értéket az előző lépésben kapott értékre, amikor a tulajdonos hozzáférési jogkivonatát és a `<SUBSCRIPTION ID>` értékét az adott környezetnek megfelelőnek kérte.
 
   **API-VERZIÓ 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Kérések fejlécei**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva.        | 

   **Kérelem törzse**

   ```JSON
    {  
       "location":"westus",
       "name":"myVM",
       "identity":{  
          "type":"UserAssigned",
          "identityIds":[  
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{  
          "hardwareProfile":{  
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{  
             "imageReference":{  
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{  
                "caching":"ReadWrite",
                "managedDisk":{  
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[  
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{  
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{  
             "networkInterfaces":[  
                {  
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{  
                      "primary":true
                   }
                }
             ]
          }
       }
    }

   ```
  
   **API-VERZIÓ 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Kérések fejlécei**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva.        | 

   **Kérelem törzse**

   ```JSON
    {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Felhasználóhoz rendelt felügyelt identitás hozzárendelése meglévő Azure-beli virtuális géphez

Ha felhasználó által hozzárendelt identitást szeretne hozzárendelni egy virtuális géphez, a fióknak szüksége van a [virtuális gép közreműködői](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) és [felügyelt identitás-kezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör-hozzárendeléseire. Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Kérje le a tulajdonos hozzáférési jogkivonatát, amelyet az engedélyezési fejléc következő lépésében fog használni a virtuális gép rendszerhez rendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az itt található utasítások alapján, [hozzon létre egy felhasználó által hozzárendelt felügyelt identitást](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Annak biztosítása érdekében, hogy ne törölje a virtuális géphez hozzárendelt meglévő felhasználó vagy rendszer által hozzárendelt felügyelt identitásokat, a következő CURL-parancs használatával fel kell sorolnia a virtuális géphez rendelt identitási típusokat. Ha felügyelt identitások vannak hozzárendelve a virtuálisgép-méretezési csoporthoz, azok a `identity` érték alatt vannak felsorolva.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Kérések fejlécei**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva.

    Ha a virtuális géphez hozzárendelt felhasználói vagy rendszerszintű felügyelt identitásokkal rendelkezik a válasz `identity` értékében azonosítva, ugorjon az 5. lépésre, amely bemutatja, hogyan őrizze meg a rendszer által hozzárendelt felügyelt identitást, miközben hozzáad egy felhasználó által hozzárendelt felügyelt identitást a virtuális géphez.

4. Ha nem rendelkezik a virtuális géphez hozzárendelt, felhasználóhoz rendelt felügyelt identitásokkal, a következő CURL-paranccsal hívja meg az Azure Resource Manager REST-végpontot, hogy az első felhasználóhoz rendelt felügyelt identitást hozzárendelje a virtuális géphez.

   Az alábbi példák egy felhasználó által hozzárendelt felügyelt identitást rendelnek hozzá, `ID1` egy *myVM* nevű virtuális géphez az erőforráscsoport *myResourceGroup*.  Cserélje le a `<ACCESS TOKEN>` értéket az előző lépésben kapott értékre, amikor a tulajdonos hozzáférési jogkivonatát és a `<SUBSCRIPTION ID>` értékét az adott környezetnek megfelelőnek kérte.

   **API-VERZIÓ 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Kérések fejlécei**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva.        |
 
   **Kérelem törzse**

   ```JSON
    {
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ```

   **API-VERZIÓ 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **Kérések fejlécei**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva.        | 

   **Kérelem törzse**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

5. Ha rendelkezik egy meglévő, felhasználó által hozzárendelt vagy rendszerhez rendelt felügyelt identitással a virtuális géphez:
   
   **API-VERZIÓ 2018-06-01**

   Adja hozzá a felhasználó által hozzárendelt felügyelt identitást a `userAssignedIdentities` szótár értékéhez.
    
   Ha például rendszerhez rendelt felügyelt identitással rendelkezik, és a felhasználó által hozzárendelt felügyelt identitás `ID1` jelenleg hozzá van rendelve a virtuális géphez, és hozzá szeretné adni a felhasználóhoz rendelt felügyelt identitást `ID2`:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Kérések fejlécei**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva.        | 

   **Kérelem törzse**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```

   **API-VERZIÓ 2017-12-01**

   Tartsa meg a felhasználó által hozzárendelt felügyelt identitásokat, amelyeket meg szeretne őrizni a `identityIds` Array értékben az új felhasználó által hozzárendelt felügyelt identitás hozzáadásakor.

   Ha például rendszerhez rendelt felügyelt identitással rendelkezik, és a felhasználó által hozzárendelt felügyelt identitás `ID1` jelenleg hozzá van rendelve a virtuális géphez, és hozzá szeretné adni a felhasználóhoz rendelt felügyelt identitást `ID2`: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Kérések fejlécei**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva.        | 

   **Kérelem törzse**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása Azure-beli virtuális gépről

A felhasználó által hozzárendelt identitás egy virtuális géphez való eltávolításához a fióknak szüksége van a [virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelésre.

1. Kérje le a tulajdonos hozzáférési jogkivonatát, amelyet az engedélyezési fejléc következő lépésében fog használni a virtuális gép rendszerhez rendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Annak biztosítása érdekében, hogy ne töröljön olyan meglévő, felhasználó által hozzárendelt felügyelt identitásokat, amelyeket meg szeretne őrizni a virtuális géphez, vagy távolítsa el a rendszer által hozzárendelt felügyelt identitást, a következő CURL-paranccsal kell kilistáznia a felügyelt identitásokat: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Kérések fejlécei**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva.
 
   Ha felügyelt identitások vannak hozzárendelve a virtuális géphez, azok a `identity` érték válaszában vannak felsorolva.

   Ha például a felhasználó által hozzárendelt felügyelt identitásokkal rendelkezik `ID1` és `ID2` a virtuális géphez, és csak azt szeretné megőrizni `ID1` hozzárendelve és megőrizni a rendszer által hozzárendelt identitást:
   
   **API-VERZIÓ 2018-06-01**

   `null` hozzáadása a felhasználó által hozzárendelt felügyelt identitáshoz, amelyet el szeretne távolítani:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Kérések fejlécei**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva.        | 

   **Kérelem törzse**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null
          }
       }
    }
   ```

   **API-VERZIÓ 2017-12-01**

   Csak azokat a felhasználó által hozzárendelt felügyelt identitásokat őrizze meg, amelyeket meg szeretne őrizni a `identityIds` tömbben:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Kérések fejlécei**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva.        | 

   **Kérelem törzse**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Ha a virtuális gépen a rendszerhez hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás is van, akkor a következő paranccsal távolíthatja el az összes felhasználó által hozzárendelt felügyelt identitást úgy, hogy csak a rendszerhez rendelt felügyelt identitás használatára váltson:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Kérések fejlécei**

|Kérelem fejléce  |Leírás  |
|---------|---------|
|*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
|*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva. | 

**Kérelem törzse**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Ha a virtuális gépen csak a felhasználó által hozzárendelt felügyelt identitások vannak, és az összeset el szeretné távolítani, használja a következő parancsot:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Kérések fejlécei**

|Kérelem fejléce  |Leírás  |
|---------|---------|
|*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
|*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva.| 

**Kérelem törzse**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Következő lépések

A felhasználó által hozzárendelt felügyelt identitások REST használatával történő létrehozásával, listázásával vagy törlésével kapcsolatos információkért lásd:

- [Felhasználó által hozzárendelt felügyelt identitások létrehozása, listázása és törlése REST API hívásokkal](how-to-manage-ua-identity-rest.md)
