---
title: Felügyelt identitások konfigurálása az Azure virtuális gépén a REST használatával – Azure AD
description: Lépésenkénti utasítások a rendszer és a felhasználó által hozzárendelt felügyelt identitások konfigurálása egy Azure virtuális gép curl segítségével REST API-hívások.
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
ms.openlocfilehash: 9f975595e935a5c0254450168aa295e6e7366a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244159"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Felügyelt identitások konfigurálása Azure-erőforrásokhoz egy Azure virtuális gépen REST API-hívások használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitásai automatikusan felügyelt rendszeridentitást biztosítaz Azure-szolgáltatások számára az Azure Active Directoryban. Ezzel az identitással hitelesítheti magát minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítést, anélkül, hogy hitelesítő adatokat a kódot. 

Ebben a cikkben a CURL használatával hívásokat az Azure Resource Manager REST-végpont, megtudhatja, hogyan hajthatja végre a következő felügyelt identitások az Azure-erőforrások műveletek egy Azure virtuális gép:

- A rendszeráltal hozzárendelt felügyelt identitás engedélyezése és letiltása egy Azure virtuális gépen
- Felhasználó által hozzárendelt felügyelt identitás hozzáadása és eltávolítása Azure-beli virtuális gépen

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [áttekintő szakaszt.](overview.md) **Mindenképpen tekintse át a [rendszerhez rendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget.](overview.md#how-does-the-managed-identities-for-azure-resources-work)**
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Windows használata esetén telepítse a [Windows Linux-alrendszert,](https://msdn.microsoft.com/commandline/wsl/about) vagy használja az [Azure Cloud Shellt](../../cloud-shell/overview.md) az Azure Portalon.
- [Telepítse az Azure CLI helyi konzolt,](/cli/azure/install-azure-cli)ha a [Windows Alrendszert Linuxhoz](https://msdn.microsoft.com/commandline/wsl/about) vagy [Linux disztribúciós operációs rendszert](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)használja.
- Ha az Azure CLI helyi konzolját használja, jelentkezzen be az Azure-ba egy olyan fiókkal, `az login` amely a rendszer- vagy felhasználó által hozzárendelt felügyelt identitások kezeléséhez társított Azure-előfizetéshez van társítva.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Rendszerhez rendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan engedélyezheti és tilthatja le a rendszeráltal hozzárendelt felügyelt identitást egy Azure virtuális gépen a CURL használatával az Azure Resource Manager REST-végponthívásához.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Rendszeráltal hozzárendelt felügyelt identitás engedélyezése az Azure virtuális gép létrehozása során

Hozzon létre egy Azure-beli virtuális gép a rendszer által hozzárendelt felügyelt identitás engedélyezve van, a fióknak szüksége van a [Virtuálisgép közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelés.  Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

1. Az [az group create](/cli/azure/group/#az-group-create) paranccsal hozzon létre egy [erőforráscsoportot](../../azure-resource-manager/management/overview.md#terminology) a virtuális gép és az ahhoz kapcsolódó erőforrások elkülönítéséhez és üzembe helyezéséhez. Ezt a lépést kihagyhatja, ha inkább egy meglévő erőforráscsoportot kíván használni:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Hozzon létre egy [hálózati adaptert](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) a virtuális géphez:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Egy tulajdonosi hozzáférési jogkivonat lekérése, amelyet az engedélyezés fejlécének következő lépésében a virtuális gép rendszeráltal hozzárendelt felügyelt identitással való létrehozásához fog használni.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Hozzon létre egy virtuális gép curl használatával hívja meg az Azure Resource Manager REST-végpont. A következő példa létrehoz egy *myVM* nevű virtuális gép egy rendszer által hozzárendelt felügyelt `"identity":{"type":"SystemAssigned"}`identitás, akérelem törzsében az érték által azonosított. Cserélje `<ACCESS TOKEN>` le az előző lépésben kapott értéket, amikor egy `<SUBSCRIPTION ID>` bemutatóra szóló hozzáférési jogkivonatot kért, és a környezetének megfelelő értéket.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Fejlécek kérése**
   
   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípusa*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot.        | 
   
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

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Rendszeráltal hozzárendelt identitás engedélyezése meglévő Azure-beli virtuális gépen

A rendszer által hozzárendelt felügyelt identitás engedélyezéséhez egy virtuális gépen, amely eredetileg nélküle lett kiépítve, a fióknak szüksége van a [Virtuálisgép közreműködőszerepkör-hozzárendelésre.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

1. Egy tulajdonosi hozzáférési jogkivonat lekérése, amelyet az engedélyezés fejlécének következő lépésében a virtuális gép rendszeráltal hozzárendelt felügyelt identitással való létrehozásához fog használni.

   ```azurecli-interactive
   az account get-access-token
   ```

2. A következő CURL paranccsal hívja meg az Azure Resource Manager REST-végpontját, hogy a rendszer által `{"identity":{"type":"SystemAssigned"}` hozzárendelt felügyelt identitást engedélyezze a virtuális gépen, ahogy azt a kérelemtörzsben egy *myVM*nevű virtuális gép értéke azonosította.  Cserélje `<ACCESS TOKEN>` le az előző lépésben kapott értéket, amikor egy `<SUBSCRIPTION ID>` bemutatóra szóló hozzáférési jogkivonatot kért, és a környezetének megfelelő értéket.
   
   > [!IMPORTANT]
   > Annak érdekében, hogy ne törölje a virtuális géphez rendelt, a felhasználó által hozzárendelt felügyelt identitásokat, a CURL paranccsal fel kell sorolnia a felhasználó által hozzárendelt felügyelt identitásokat: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Ha rendelkezik a virtuális géphez rendelt, a válasz értékében `identity` azonosított, felhasználó által hozzárendelt felügyelt identitásokkal, ugorjon a 3.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Fejlécek kérése**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípusa*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot.        | 
   
   **Kérelem törzse**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Ahhoz, hogy a rendszer által hozzárendelt felügyelt identitás a virtuális gép a `SystemAssigned` meglévő, `type` a felhasználó által hozzárendelt felügyelt identitások, hozzá kell adnia az értéket.  
   
   Például ha a virtuális gép rendelkezik a `ID1` felhasználó `ID2` által hozzárendelt felügyelt identitások és hozzárendelt, és szeretné hozzáadni a rendszer által hozzárendelt felügyelt identitása a virtuális géphez, használja a következő CURL-hívást. Cserélje `<ACCESS TOKEN>` `<SUBSCRIPTION ID>` le és az értékeket a környezetnek megfelelő.

   Az `2018-06-01` API-verzió a felhasználó által `userAssignedIdentities` hozzárendelt felügyelt identitásokat szótárformátumban `identityIds` tárolja, szemben az API-verzióban használt tömbformátumban megadott értékkel. `2017-12-01`
   
   **API-verzió 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Fejlécek kérése**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípusa*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot.        | 

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

   **API-verzió 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **Fejlécek kérése**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípusa*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot.        | 

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

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>A rendszer által hozzárendelt felügyelt identitás letiltása Egy Azure virtuális gépről

A rendszer által hozzárendelt felügyelt identitás letiltásához a virtuális gépen a fióknak szüksége van a [Virtuálisgép közreműködőszerepkör-hozzárendelésre.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

1. Egy tulajdonosi hozzáférési jogkivonat lekérése, amelyet az engedélyezés fejlécének következő lépésében a virtuális gép rendszeráltal hozzárendelt felügyelt identitással való létrehozásához fog használni.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Frissítse a virtuális gép curl használatával hívja meg az Azure Resource Manager REST-végpont letiltásához a rendszer által hozzárendelt felügyelt identitás.  A következő példa letiltja a rendszer által hozzárendelt felügyelt `{"identity":{"type":"None"}}` identitást, ahogy azt a kérelemtörzsben egy *myVM*nevű virtuális gép értéke azonosította.  Cserélje `<ACCESS TOKEN>` le az előző lépésben kapott értéket, amikor egy `<SUBSCRIPTION ID>` bemutatóra szóló hozzáférési jogkivonatot kért, és a környezetének megfelelő értéket.

   > [!IMPORTANT]
   > Annak érdekében, hogy ne törölje a virtuális géphez rendelt, a felhasználó által hozzárendelt felügyelt identitásokat, a CURL paranccsal fel kell sorolnia a felhasználó által hozzárendelt felügyelt identitásokat: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Ha rendelkezik a virtuális géphez rendelt, a válaszértékben `identity` azonosított, felhasználó által hozzárendelt felügyelt identitásokkal, ugorjon a 3.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Fejlécek kérése**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípusa*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot.        | 

   **Kérelem törzse**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Ha el szeretné távolítani a rendszerhez rendelt felügyelt identitást egy olyan `SystemAssigned` virtuális `{"identity":{"type:" "}}` gépről, `UserAssigned` amely hez `userAssignedIdentities` felhasználó által hozzárendelt felügyelt identitások tartoznak, távolítsa el az értékből, miközben megtartja az értéket és a szótárértékeket, ha **a 2018-06-01-es API-verziót**használja. Ha a **2017-12-01-es** vagy korábbi API-verziót használja, tartsa meg a `identityIds` tömböt.

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan adhat hozzá és távolíthat el felhasználó által hozzárendelt felügyelt identitást egy Azure virtuális gép curl használatával hívásokat az Azure Resource Manager REST-végpontra.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése az Azure virtuális gép létrehozása során

Ha egy felhasználó által hozzárendelt identitást rendel egy virtuális géphez, a fióknak szüksége van a [virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) és a [felügyelt identitáskezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör-hozzárendelések. Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

1. Egy tulajdonosi hozzáférési jogkivonat lekérése, amelyet az engedélyezés fejlécének következő lépésében a virtuális gép rendszeráltal hozzárendelt felügyelt identitással való létrehozásához fog használni.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Hozzon létre egy [hálózati adaptert](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) a virtuális géphez:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Egy tulajdonosi hozzáférési jogkivonat lekérése, amelyet az engedélyezés fejlécének következő lépésében a virtuális gép rendszeráltal hozzárendelt felügyelt identitással való létrehozásához fog használni.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az itt található utasítások alapján: [Felhasználó által hozzárendelt felügyelt identitás létrehozása](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Hozzon létre egy virtuális gép curl használatával hívja meg az Azure Resource Manager REST-végpont. A következő példa létrehoz egy *myVM* nevű virtuális gép a *myResourceGroup* erőforráscsoportban egy felhasználó által hozzárendelt felügyelt identitással `ID1`, ahogy azt a kérelemtörzsben az érték `"identity":{"type":"UserAssigned"}`azonosította. Cserélje `<ACCESS TOKEN>` le az előző lépésben kapott értéket, amikor egy `<SUBSCRIPTION ID>` bemutatóra szóló hozzáférési jogkivonatot kért, és a környezetének megfelelő értéket.
 
   **API-verzió 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Fejlécek kérése**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípusa*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot.        | 

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
  
   **API-verzió 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Fejlécek kérése**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípusa*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot.        | 

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

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése meglévő Azure-beli virtuális géphez

Ha egy felhasználó által hozzárendelt identitást rendel egy virtuális géphez, a fióknak szüksége van a [virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) és a [felügyelt identitáskezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör-hozzárendelések. Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

1. Egy tulajdonosi hozzáférési jogkivonat lekérése, amelyet az engedélyezés fejlécének következő lépésében a virtuális gép rendszeráltal hozzárendelt felügyelt identitással való létrehozásához fog használni.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az itt [található, Felhasználó által hozzárendelt felügyelt identitás létrehozása](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Annak érdekében, hogy ne törölje a virtuális géphez rendelt meglévő felhasználói vagy rendszeráltal hozzárendelt felügyelt identitásokat, a következő CURL paranccsal fel kell sorolnia a virtuális géphez rendelt identitástípusokat. Ha a virtuálisgép-méretezési készlethez rendelt felügyelt identitásokat, azok `identity` az érték alatt jelennek meg.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Fejlécek kérése**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot.

    Ha rendelkezik a virtuális géphez rendelt bármely felhasználó- vagy rendszeráltal `identity` hozzárendelt felügyelt identitással a válasz értékében meghatározottmódon, ugorjon az 5.

4. Ha nem rendelkezik a virtuális géphez hozzárendelt, felhasználó által hozzárendelt felügyelt identitások, használja a következő CURL parancsot az Azure Resource Manager REST-végpont meghívásához az első felhasználó által hozzárendelt felügyelt identitás hozzárendeléséhez a virtuális géphez.

   A következő példák egy felhasználó által hozzárendelt felügyelt identitást `ID1` rendelnek hozzá egy *myVM* nevű virtuális géphez a *myResourceGroup*erőforráscsoportban.  Cserélje `<ACCESS TOKEN>` le az előző lépésben kapott értéket, amikor egy `<SUBSCRIPTION ID>` bemutatóra szóló hozzáférési jogkivonatot kért, és a környezetének megfelelő értéket.

   **API-verzió 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Fejlécek kérése**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípusa*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot.        |
 
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

   **API-verzió 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **Fejlécek kérése**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípusa*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot.        | 

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

5. Ha rendelkezik a virtuális géphez egy meglévő, felhasználó által hozzárendelt vagy rendszeráltal hozzárendelt felügyelt identitással:
   
   **API-verzió 2018-06-01**

   Adja hozzá a felhasználó által hozzárendelt felügyelt identitást a `userAssignedIdentities` szótárértékhez.
    
   Ha például rendelkezik a rendszer által hozzárendelt felügyelt identitással `ID1` és a virtuális géphez jelenleg hozzárendelt, a felhasználó által `ID2` hozzárendelt felügyelt identitással rendelkezik, és hozzá szeretné adni hozzá a felhasználó által hozzárendelt felügyelt identitást:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Fejlécek kérése**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípusa*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot.        | 

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

   **API-verzió 2017-12-01**

   Őrizze meg a felhasználó által hozzárendelt felügyelt `identityIds` identitások szeretné tartani a tömb érték hozzáadása közben az új felhasználó által hozzárendelt felügyelt identitás.

   Ha például rendelkezik a rendszer által hozzárendelt felügyelt identitással `ID1` és a virtuális géphez jelenleg hozzárendelt, a felhasználó által `ID2` hozzárendelt felügyelt identitással rendelkezik, és hozzá szeretné adni hozzá a felhasználó által hozzárendelt felügyelt identitást: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Fejlécek kérése**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípusa*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot.        | 

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

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása azure-beli virtuális gépből

A virtuális géphez felhasználó által hozzárendelt identitás eltávolításához a fióknak szüksége van a [Virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelésre.

1. Egy tulajdonosi hozzáférési jogkivonat lekérése, amelyet az engedélyezés fejlécének következő lépésében a virtuális gép rendszeráltal hozzárendelt felügyelt identitással való létrehozásához fog használni.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Annak érdekében, hogy ne törölje a meglévő, felhasználó által hozzárendelt felügyelt identitásokat, amelyeket meg szeretne tartani a virtuális géphez rendelve, vagy távolítsa el a rendszer által hozzárendelt felügyelt identitást, a felügyelt identitásokat a következő CURL paranccsal kell felsorolnia: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Fejlécek kérése**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípusa*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot.
 
   Ha a virtuális géphez rendelt felügyelt identitásokat, azok szerepelnek `identity` az értékben szereplő válaszban.

   Például ha a felhasználó által hozzárendelt `ID1` `ID2` felügyelt identitások és hozzárendelt a virtuális `ID1` géphez, és csak szeretné megtartani a hozzárendelt és megtarthatja a rendszer által hozzárendelt identitás:
   
   **API-verzió 2018-06-01**

   Adja `null` hozzá az eltávolítani kívánt, felhasználó által hozzárendelt felügyelt identitáshoz:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Fejlécek kérése**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípusa*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot.        | 

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

   **API-verzió 2017-12-01**

   Csak a felhasználó által hozzárendelt felügyelt identitás(ok) megőrzésére, amelyet meg szeretne tartani a `identityIds` tömbben:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Fejlécek kérése**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípusa*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot.        | 

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

Ha a virtuális gép rendszer- és felhasználó által hozzárendelt felügyelt identitásokkal is rendelkezik, eltávolíthatja az összes felhasználó által hozzárendelt felügyelt identitást, ha a következő paranccsal csak a rendszer által hozzárendelt felügyelt identitást használja:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Fejlécek kérése**

|Kérelem fejléce  |Leírás  |
|---------|---------|
|*Tartalomtípusa*     | Kötelező. Állítsa `application/json` értékre.        |
|*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot. | 

**Kérelem törzse**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Ha a virtuális gép csak a felhasználó által hozzárendelt felügyelt identitások, és szeretné eltávolítani őket, használja a következő parancsot:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Fejlécek kérése**

|Kérelem fejléce  |Leírás  |
|---------|---------|
|*Tartalomtípusa*     | Kötelező. Állítsa `application/json` értékre.        |
|*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot.| 

**Kérelem törzse**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>További lépések

A felhasználó által hozzárendelt felügyelt identitások létrehozásáról, listázásáról és törléséről a REST használatával a következő témakörben talál tájékoztatást:

- [Felhasználó által hozzárendelt felügyelt identitások létrehozása, listázása vagy törlése REST API-hívások használatával](how-to-manage-ua-identity-rest.md)
