---
title: Felügyelt identitások konfigurálása az Azure VMSS a REST-Azure AD használatával
description: Részletes útmutató a rendszer és a felhasználó által hozzárendelt felügyelt identitások Azure-VMSS való konfigurálásához a CURL használatával REST API-hívások létrehozásához.
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
ms.openlocfilehash: dce9894b26d03c351a2209792cc076de91feba54
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429981"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Felügyelt identitások konfigurálása virtuálisgép-méretezési csoportba tartozó Azure-erőforrásokhoz REST API-hívások használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitásai Azure-szolgáltatásokat biztosítanak a Azure Active Directory automatikusan felügyelt rendszeridentitással. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, és nem rendelkezik hitelesítő adatokkal a kódban. 

Ebből a cikkből megtudhatja, hogyan hajthatja végre a következő felügyelt identitásokat az Azure-erőforrások műveleteihez egy virtuálisgép-méretezési csoporton a CURL használatával a Azure Resource Manager REST-végpontra irányuló hívásokhoz:

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
    - [Felügyelt identitás-kezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör egy felhasználó által hozzárendelt identitás hozzárendeléséhez és eltávolításához egy virtuálisgép-méretezési csoportba.
- Ha Windows rendszert használ, telepítse a [Linux Windows alrendszerét](https://msdn.microsoft.com/commandline/wsl/about) , vagy használja a Azure Portal [Azure Cloud Shell](../../cloud-shell/overview.md) .
- [Telepítse az Azure CLI helyi konzolját](/cli/azure/install-azure-cli), ha a [Windows alrendszert](https://msdn.microsoft.com/commandline/wsl/about) használja a Linux vagy Linux rendszerű [terjesztési operációs rendszeren](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Ha az Azure CLI helyi konzolt használja, jelentkezzen be az Azure-ba a `az login` használatával egy olyan fiókkal, amely a rendszer vagy a felhasználó által hozzárendelt felügyelt identitások kezeléséhez használni kívánt Azure-előfizetéshez van társítva.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebből a szakaszból megtudhatja, hogyan engedélyezheti és tilthatja le a rendszerhez rendelt felügyelt identitást egy virtuálisgép-méretezési csoporton a CURL használatával, hogy hívásokat hajtson végre a Azure Resource Manager REST-végpontra.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>A rendszer által hozzárendelt felügyelt identitás engedélyezése virtuálisgép-méretezési csoport létrehozása során

A rendszerhez rendelt felügyelt identitású virtuálisgép-méretezési csoport létrehozásához létre kell hoznia egy virtuálisgép-méretezési készletet, és be kell szereznie egy hozzáférési jogkivonatot, hogy a CURL használatával hívja meg a Resource Manager-végpontot a rendszerhez rendelt felügyelt identitás típusú értékkel.

1. Hozzon létre egy [erőforráscsoportot](../../azure-resource-manager/management/overview.md#terminology) a virtuálisgép-méretezési csoport és a kapcsolódó erőforrások tárolásához és üzembe helyezéséhez az [az Group Create](/cli/azure/group/#az-group-create)paranccsal. Ezt a lépést kihagyhatja, ha inkább egy meglévő erőforráscsoportot kíván használni:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Hozzon létre egy [hálózati adaptert](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) a virtuálisgép-méretezési csoport számára:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Kérje le a tulajdonos hozzáférési jogkivonatát, amelyet az engedélyezési fejléc következő lépésében fog használni a virtuálisgép-méretezési csoport létrehozásához a rendszerhez rendelt felügyelt identitással.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Hozzon létre egy virtuálisgép-méretezési készletet a CURL használatával a Azure Resource Manager REST-végpont meghívásához. A következő példa létrehoz egy *myVMSS* nevű virtuálisgép-méretezési készletet a *myResourceGroup* egy rendszerhez rendelt felügyelt identitással, amelyet a kérés törzse az `"identity":{"type":"SystemAssigned"}`értékkel azonosít. Cserélje le a `<ACCESS TOKEN>` értéket az előző lépésben kapott értékre, amikor a tulajdonos hozzáférési jogkivonatát és a `<SUBSCRIPTION ID>` értékét az adott környezetnek megfelelőnek kérte.

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Kérések fejlécei**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva. | 

   **Kérelem törzse**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }  
   ```  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>A rendszerhez rendelt felügyelt identitás engedélyezése meglévő virtuálisgép-méretezési csoportokon

Ha a rendszer által hozzárendelt felügyelt identitást szeretné engedélyezni egy meglévő virtuálisgép-méretezési csoporton, meg kell adnia egy hozzáférési jogkivonatot, majd a CURL használatával meg kell hívnia a Resource Manager REST-végpontot az identitás típusának frissítéséhez.

1. Kérje le a tulajdonos hozzáférési jogkivonatát, amelyet az engedélyezési fejléc következő lépésében fog használni a virtuálisgép-méretezési csoport létrehozásához a rendszerhez rendelt felügyelt identitással.

   ```azurecli-interactive
   az account get-access-token
   ```

2. A következő CURL-paranccsal hívhatja meg a Azure Resource Manager REST-végpontot, hogy engedélyezze a rendszerhez rendelt felügyelt identitást a virtuálisgép-méretezési csoporton a kérelem törzsében, a *myVMSS*nevű virtuálisgép-méretezési csoport értékével `{"identity":{"type":"SystemAssigned"}`ve.  Cserélje le a `<ACCESS TOKEN>` értéket az előző lépésben kapott értékre, amikor a tulajdonos hozzáférési jogkivonatát és a `<SUBSCRIPTION ID>` értékét az adott környezetnek megfelelőnek kérte.
   
   > [!IMPORTANT]
   > Annak biztosítása érdekében, hogy ne törölje a virtuálisgép-méretezési csoporthoz rendelt meglévő, felhasználóhoz rendelt felügyelt identitásokat, a következő CURL-parancs használatával fel kell sorolnia a felhasználó által hozzárendelt felügyelt identitásokat: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Ha a virtuálisgép-méretezési csoporthoz a válasz `identity` értékében azonosított, felhasználó által hozzárendelt felügyelt identitások vannak rendelve, ugorjon a 3. lépésre, amely bemutatja, hogyan őrizze meg a felhasználó által hozzárendelt felügyelt identitásokat, miközben engedélyezi a rendszerhez rendelt felügyelt identitást a virtuálisgép-méretezési csoporton.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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

3. Ha a rendszer által hozzárendelt felügyelt identitást szeretné engedélyezni egy virtuálisgép-méretezési csoporton meglévő, felhasználó által hozzárendelt felügyelt identitásokkal, hozzá kell adnia `SystemAssigned` a `type` értékhez.  
   
   Ha például a virtuálisgép-méretezési csoport a felhasználó által hozzárendelt felügyelt identitásokkal rendelkezik `ID1` és `ID2` hozzájuk rendelve, és a rendszerhez rendelt felügyelt identitást szeretné hozzáadni a virtuálisgép-méretezési csoporthoz, használja a következő CURL-hívást. Cserélje le a `<ACCESS TOKEN>` és a `<SUBSCRIPTION ID>` értéket a környezetének megfelelő értékekkel.

   Az API-verzió `2018-06-01` a felhasználó által hozzárendelt felügyelt identitásokat a szótár formátuma `userAssignedIdentities` értékében tárolja, szemben az API-verzió `2017-12-01`használt tömb formátumában lévő `identityIds` értékkel.
   
   **API-VERZIÓ 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
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

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Rendszer által hozzárendelt felügyelt identitás letiltása virtuálisgép-méretezési csoportból

Egy meglévő virtuálisgép-méretezési csoporton a rendszer által hozzárendelt identitás letiltásához hozzáférési jogkivonatot kell beszerezni, majd a CURL használatával meg kell hívnia a Resource Manager REST-végpontot, hogy frissítse az identitás típusát `None`re.

1. Kérje le a tulajdonos hozzáférési jogkivonatát, amelyet az engedélyezési fejléc következő lépésében fog használni a virtuálisgép-méretezési csoport létrehozásához a rendszerhez rendelt felügyelt identitással.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Frissítse a virtuálisgép-méretezési csoportját a CURL használatával, hogy meghívja a Azure Resource Manager REST-végpontot a rendszer által hozzárendelt felügyelt identitás letiltásához.  A következő példa letiltja a rendszer által hozzárendelt felügyelt identitást a kérelem törzsében azonosított módon, a *myVMSS*nevű virtuálisgép-méretezési csoport értéke `{"identity":{"type":"None"}}`.  Cserélje le a `<ACCESS TOKEN>` értéket az előző lépésben kapott értékre, amikor a tulajdonos hozzáférési jogkivonatát és a `<SUBSCRIPTION ID>` értékét az adott környezetnek megfelelőnek kérte.

   > [!IMPORTANT]
   > Annak biztosítása érdekében, hogy ne törölje a virtuálisgép-méretezési csoporthoz rendelt meglévő, felhasználóhoz rendelt felügyelt identitásokat, a következő CURL-parancs használatával fel kell sorolnia a felhasználó által hozzárendelt felügyelt identitásokat: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Ha rendelkezik egy, a virtuálisgép-méretezési csoporthoz rendelt, felhasználó által hozzárendelt felügyelt identitással, ugorjon a 3. lépésre, amely bemutatja, hogyan őrzi meg a felhasználó által hozzárendelt identitásokat, miközben eltávolítja a rendszer által hozzárendelt felügyelt identitást a virtuálisgép-méretezési csoportból.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
          "type":"None"
       }
    }
   ```

   Ha a rendszer által hozzárendelt felügyelt identitást egy olyan virtuálisgép-méretezési csoportból szeretné eltávolítani, amely felhasználó által hozzárendelt felügyelt identitásokkal rendelkezik, távolítsa el `SystemAssigned` a `{"identity":{"type:" "}}` értékről, miközben a `UserAssigned` értéket és a `userAssignedIdentities` szótár értékét használja, ha az **API 2018-06-01**-es verzióját használja. Ha a 2017-12-01-es vagy korábbi **API-verziót** használja, tartsa meg a `identityIds` tömböt.

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebből a szakaszból megtudhatja, hogyan adhat hozzá és távolíthat el felhasználó által hozzárendelt felügyelt identitást egy virtuálisgép-méretezési csoporton a CURL használatával, hogy hívásokat hajtson végre a Azure Resource Manager REST-végponton.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése virtuálisgép-méretezési csoport létrehozása során

1. Kérje le a tulajdonos hozzáférési jogkivonatát, amelyet az engedélyezési fejléc következő lépésében fog használni a virtuálisgép-méretezési csoport létrehozásához a rendszerhez rendelt felügyelt identitással.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Hozzon létre egy [hálózati adaptert](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) a virtuálisgép-méretezési csoport számára:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Kérje le a tulajdonos hozzáférési jogkivonatát, amelyet az engedélyezési fejléc következő lépésében fog használni a virtuálisgép-méretezési csoport létrehozásához a rendszerhez rendelt felügyelt identitással.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az itt található utasítások segítségével: [hozzon létre egy felhasználó által hozzárendelt felügyelt identitást](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Hozzon létre egy virtuálisgép-méretezési készletet a CURL használatával a Azure Resource Manager REST-végpont meghívásához. A következő példa egy *myVMSS* nevű virtuálisgép-méretezési csoportot hoz létre az erőforráscsoport *myResourceGroup* egy felhasználó által hozzárendelt felügyelt identitással `ID1`, a kérelem törzsében a `"identity":{"type":"UserAssigned"}`érték alapján azonosítva. Cserélje le a `<ACCESS TOKEN>` értéket az előző lépésben kapott értékre, amikor a tulajdonos hozzáférési jogkivonatát és a `<SUBSCRIPTION ID>` értékét az adott környezetnek megfelelőnek kérte.
 
   **API-VERZIÓ 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Kérések fejlécei**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva. | 

   **Kérelem törzse**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```   

   **API-VERZIÓ 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Kérések fejlécei**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva. |
 
   **Kérelem törzse**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Felhasználóhoz rendelt felügyelt identitás hozzárendelése meglévő Azure virtuálisgép-méretezési csoporthoz

1. Kérje le a tulajdonos hozzáférési jogkivonatát, amelyet az engedélyezési fejléc következő lépésében fog használni a virtuálisgép-méretezési csoport létrehozásához a rendszerhez rendelt felügyelt identitással.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az itt található utasítások alapján, [hozzon létre egy felhasználó által hozzárendelt felügyelt identitást](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Annak biztosítása érdekében, hogy ne törölje a virtuálisgép-méretezési csoporthoz rendelt meglévő felhasználó vagy rendszer által hozzárendelt felügyelt identitásokat, a következő CURL-parancs használatával fel kell sorolnia a virtuálisgép-méretezési csoporthoz rendelt identitási típusokat. Ha felügyelt identitások vannak hozzárendelve a virtuálisgép-méretezési csoporthoz, azok szerepelnek a `identity` értékben.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Kérések fejlécei**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva. |   
 

4. Ha nem rendelkezik a virtuálisgép-méretezési csoporthoz hozzárendelt felhasználó-vagy rendszerhez rendelt felügyelt identitásokkal, a következő CURL-paranccsal hívja meg az Azure Resource Manager REST-végpontot, hogy az első felhasználóhoz rendelt felügyelt identitást hozzárendelje a virtuális géphez. méretezési csoport.  Ha a virtuálisgép-méretezési csoporthoz hozzárendelt felhasználó vagy rendszer által hozzárendelt felügyelt identitás (ok) van, ugorjon az 5. lépésre, amely bemutatja, hogyan adhat hozzá több felhasználó által hozzárendelt felügyelt identitást egy virtuálisgép-méretezési csoporthoz, miközben a rendszer által hozzárendelt felügyelt identitás.

   A következő példa egy felhasználó által hozzárendelt felügyelt identitást rendel hozzá, `ID1` egy *myVMSS* nevű virtuálisgép-méretezési csoporthoz az erőforráscsoport *myResourceGroup*.  Cserélje le a `<ACCESS TOKEN>` értéket az előző lépésben kapott értékre, amikor a tulajdonos hozzáférési jogkivonatát és a `<SUBSCRIPTION ID>` értékét az adott környezetnek megfelelőnek kérte.

   **API-VERZIÓ 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01 HTTP/1.1
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
          "type":"userAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ``` 
    
   **API-VERZIÓ 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```  

5. Ha rendelkezik a virtuálisgép-méretezési csoporthoz rendelt, felhasználó által hozzárendelt vagy rendszerhez rendelt felügyelt identitással:
   
   **API-VERZIÓ 2018-06-01**

   Adja hozzá a felhasználó által hozzárendelt felügyelt identitást a `userAssignedIdentities` szótár értékéhez.

   Ha például a rendszer által hozzárendelt felügyelt identitással rendelkezik, és a felhasználó által hozzárendelt felügyelt identitás `ID1` jelenleg a virtuálisgép-méretezéshez van rendelve, és hozzá szeretné adni a felhasználó által hozzárendelt felügyelt identitást `ID2`hoz:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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

   Ha például a rendszerhez hozzárendelt identitás és a felhasználó által hozzárendelt felügyelt identitás `ID1` jelenleg a virtuálisgép-méretezési csoporthoz van rendelve, és hozzá szeretné adni a felhasználó által hozzárendelt felügyelt identitást `ID2`hoz:

    ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása virtuálisgép-méretezési csoportból

1. Kérje le a tulajdonos hozzáférési jogkivonatát, amelyet az engedélyezési fejléc következő lépésében fog használni a virtuálisgép-méretezési csoport létrehozásához a rendszerhez rendelt felügyelt identitással.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Annak biztosítása érdekében, hogy ne töröljön olyan meglévő, felhasználó által hozzárendelt felügyelt identitásokat, amelyeket meg szeretne őrizni a virtuálisgép-méretezési csoport számára, vagy távolítsa el a rendszer által hozzárendelt felügyelt identitást, a következő CURL-parancs használatával kell kilistáznia a felügyelt identitásokat: :

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Kérések fejlécei**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési tokenre van állítva. |
   
   Ha felügyelt identitások vannak hozzárendelve a virtuális géphez, azok a `identity` érték válaszában vannak felsorolva. 
    
   Ha például a felhasználó által hozzárendelt felügyelt identitásokkal rendelkezik `ID1` és `ID2` hozzá van rendelve a virtuálisgép-méretezési csoporthoz, és csak azt szeretné megőrizni `ID1` hozzárendelve és megőrizni a rendszer által hozzárendelt felügyelt identitást:

   **API-VERZIÓ 2018-06-01**

   `null` hozzáadása a felhasználó által hozzárendelt felügyelt identitáshoz, amelyet el szeretne távolítani:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Ha a virtuálisgép-méretezési csoporthoz a rendszerhez hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás is tartozik, akkor az összes felhasználó által hozzárendelt felügyelt identitást eltávolíthatja úgy, hogy csak a rendszer által hozzárendelt beállításokat használja a következő paranccsal:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
    
Ha a virtuálisgép-méretezési csoport csak a felhasználó által hozzárendelt felügyelt identitásokkal rendelkezik, és az összeset el szeretné távolítani, használja a következő parancsot:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Következő lépések

A felhasználó által hozzárendelt felügyelt identitások REST használatával történő létrehozásával, listázásával vagy törlésével kapcsolatos információkért lásd:

- [Felhasználó által hozzárendelt felügyelt identitás létrehozása, listázása vagy törlése REST API-hívások használatával](how-to-manage-ua-identity-rest.md)
