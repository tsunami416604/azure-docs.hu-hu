---
title: Felügyelt identitások konfigurálása az Azure VMSS-en a REST használatával – Azure AD
description: Lépésenkénti utasítások a rendszer és a felhasználó által hozzárendelt felügyelt identitások konfigurálása egy Azure VMSS curl segítségével REST API-hívások at.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253337"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Felügyelt identitások konfigurálása az Azure-erőforrásokhoz egy virtuálisgép-méretezési csoporton REST API-hívások használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitásai automatikusan felügyelt rendszeridentitást biztosítaz Azure-szolgáltatások számára az Azure Active Directoryban. Ezzel az identitással hitelesítheti magát minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítést, anélkül, hogy hitelesítő adatokat a kódot. 

Ebben a cikkben a CURL használatával hívásokat az Azure Resource Manager REST-végpont, megtudhatja, hogyan hajthatja végre a következő felügyelt identitások az Azure-erőforrások műveletek egy virtuális gép méretezési csoport:

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
    - [Felügyelt identitáskezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör a felhasználó által hozzárendelt identitás hozzárendeléséhez és eltávolításához egy virtuálisgép-méretezési csoporthoz.
- Windows használata esetén telepítse a [Windows Linux-alrendszert,](https://msdn.microsoft.com/commandline/wsl/about) vagy használja az [Azure Cloud Shellt](../../cloud-shell/overview.md) az Azure Portalon.
- [Telepítse az Azure CLI helyi konzolt,](/cli/azure/install-azure-cli)ha a [Windows Alrendszert Linuxhoz](https://msdn.microsoft.com/commandline/wsl/about) vagy [Linux disztribúciós operációs rendszert](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)használja.
- Ha az Azure CLI helyi konzolját használja, jelentkezzen be az Azure-ba egy olyan fiókkal, `az login` amely a rendszer- vagy felhasználó által hozzárendelt felügyelt identitások kezeléséhez társított Azure-előfizetéshez van társítva.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Rendszerhez rendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan engedélyezheti és tilthatja le a rendszeráltal hozzárendelt felügyelt identitást egy virtuálisgép-méretezési csoporton a CURL használatával az Azure Resource Manager REST-végponthívásához.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Rendszeráltal hozzárendelt felügyelt identitás engedélyezése virtuálisgép-méretezési csoport létrehozása során

Ha olyan virtuálisgép-méretezési csoportot szeretne létrehozni, amelyen engedélyezve van a rendszer által hozzárendelt felügyelt identitás, létre kell hoznia egy virtuálisgép-méretezési csoportot, és be kell olvasnia egy hozzáférési jogkivonatot a CURL használatához, hogy meghívja az Erőforrás-kezelő végpontot a rendszeráltal hozzárendelt felügyelt identitástípus-értékkel.

1. Hozzon létre egy [erőforráscsoportot](../../azure-resource-manager/management/overview.md#terminology) a virtuálisgép-méretezési csoport és a kapcsolódó erőforrások elszigetelésére és telepítésére a [csoport létrehozása](/cli/azure/group/#az-group-create)használatával. Ezt a lépést kihagyhatja, ha inkább egy meglévő erőforráscsoportot kíván használni:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Hozzon létre [egy hálózati adaptert](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) a virtuálisgép-méretezési csoporthoz:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Egy tulajdonosi hozzáférési jogkivonat lekérése, amelyet az Engedélyezés fejléc következő lépésében a rendszer által hozzárendelt felügyelt identitással hoz létre a virtuálisgép-méretezési készlet létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Hozzon létre egy virtuálisgép-méretezési készlet curl használatával hívja meg az Azure Resource Manager REST-végpont. A következő példa létrehoz egy *myVMSS* nevű virtuálisgép-méretezési csoportot a *myResourceGroup* ban egy rendszeráltal `"identity":{"type":"SystemAssigned"}`hozzárendelt felügyelt identitással, ahogy azt a kérelemtörzsben az érték azonosította. Cserélje `<ACCESS TOKEN>` le az előző lépésben kapott értéket, amikor egy `<SUBSCRIPTION ID>` bemutatóra szóló hozzáférési jogkivonatot kért, és a környezetének megfelelő értéket.

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Fejlécek kérése**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípusa*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot. | 

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

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Rendszeráltal hozzárendelt felügyelt identitás engedélyezése meglévő virtuálisgép-méretezési csoporton

Ahhoz, hogy a rendszer által hozzárendelt felügyelt identitás egy meglévő virtuálisgép-méretezési csoport, be kell szereznie egy hozzáférési jogkivonatot, majd curl hívja meg a Resource Manager REST végpont az identitástípus frissítéséhez.

1. Egy tulajdonosi hozzáférési jogkivonat lekérése, amelyet az Engedélyezés fejléc következő lépésében a rendszer által hozzárendelt felügyelt identitással hoz létre a virtuálisgép-méretezési készlet létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ```

2. A következő CURL paranccsal hívja meg az Azure Resource Manager REST-végpontját, hogy a rendszer által hozzárendelt `{"identity":{"type":"SystemAssigned"}` felügyelt identitást engedélyezze a virtuálisgép-méretezési csoportban a kéréstörzsben a myVMSS nevű virtuálisgép-méretezési készlet értéke által azonosított virtuálisgép-méretezési *csoportban.*  Cserélje `<ACCESS TOKEN>` le az előző lépésben kapott értéket, amikor egy `<SUBSCRIPTION ID>` bemutatóra szóló hozzáférési jogkivonatot kért, és a környezetének megfelelő értéket.
   
   > [!IMPORTANT]
   > Annak érdekében, hogy ne törölje a virtuálisgép-méretezési csoporthoz hozzárendelt, felhasználó által hozzárendelt felügyelt identitásokat, a CURL paranccsal fel kell sorolnia a felhasználó által hozzárendelt felügyelt identitásokat: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Ha rendelkezik a virtuálisgép-méretezési készlethez rendelt, a válaszban `identity` meghatározott, felhasználó által hozzárendelt felügyelt identitásokkal, ugorjon a 3.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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

3. Ahhoz, hogy a rendszer által hozzárendelt felügyelt identitás egy virtuálisgép-méretezési készlet `SystemAssigned` meglévő, `type` felhasználó által hozzárendelt felügyelt identitások, hozzá kell adnia az értéket.  
   
   Ha például a virtuálisgép-méretezési csoport rendelkezik `ID1` a `ID2` felhasználó által hozzárendelt felügyelt identitásokkal, és hozzá rendeli a rendszer által hozzárendelt felügyelt identitást a virtuálisgép-méretezési készlethez, használja a következő CURL-hívást. Cserélje `<ACCESS TOKEN>` `<SUBSCRIPTION ID>` le és az értékeket a környezetnek megfelelő.

   Az `2018-06-01` API-verzió a felhasználó által `userAssignedIdentities` hozzárendelt felügyelt identitásokat szótárformátumban `identityIds` tárolja, szemben az API-verzióban használt tömbformátumban megadott értékkel. `2017-12-01`
   
   **API-verzió 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
   
   **API-verzió 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>A rendszeráltal hozzárendelt felügyelt identitás letiltása egy virtuálisgép-méretezési csoportból

Ha le szeretne tiltani egy rendszeráltal hozzárendelt identitást egy meglévő virtuálisgép-méretezési csoporton, be kell szereznie egy hozzáférési `None`jogkivonatot, majd a CURL segítségével meg kell hívnia az Erőforrás-kezelő REST-végpontját az identitástípus frissítéséhez.

1. Egy tulajdonosi hozzáférési jogkivonat lekérése, amelyet az Engedélyezés fejléc következő lépésében a rendszer által hozzárendelt felügyelt identitással hoz létre a virtuálisgép-méretezési készlet létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Frissítse a virtuálisgép-méretezési készlet curl használatával hívja meg az Azure Resource Manager REST-végpont letiltásához a rendszer által hozzárendelt felügyelt identitás.  A következő példa letiltja a rendszer által hozzárendelt felügyelt `{"identity":{"type":"None"}}` identitást, ahogy azt a kéréstörzsben egy *myVMSS*nevű virtuálisgép-méretezési csoport értéke azonosította.  Cserélje `<ACCESS TOKEN>` le az előző lépésben kapott értéket, amikor egy `<SUBSCRIPTION ID>` bemutatóra szóló hozzáférési jogkivonatot kért, és a környezetének megfelelő értéket.

   > [!IMPORTANT]
   > Annak érdekében, hogy ne törölje a virtuálisgép-méretezési csoporthoz hozzárendelt, felhasználó által hozzárendelt felügyelt identitásokat, a CURL paranccsal fel kell sorolnia a felhasználó által hozzárendelt felügyelt identitásokat: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Ha a virtuálisgép-méretezési csoporthoz hozzárendelt felhasználó által hozzárendelt felügyelt identitást, ugorjon a 3.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
          "type":"None"
       }
    }
   ```

   Ha el szeretné távolítani a rendszerhez rendelt felügyelt identitást egy olyan virtuálisgép-méretezési csoportból, amely hez felhasználó által hozzárendelt felügyelt identitások tartoznak, távolítsa el `SystemAssigned` az `{"identity":{"type:" "}}` értékből, miközben megtartja az `UserAssigned` értéket és a `userAssignedIdentities` szótárértékeket, ha az API **2018-06-01-es verzióját**használja. Ha a **2017-12-01-es** vagy korábbi API-verziót használja, tartsa meg a `identityIds` tömböt.

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan adhat hozzá és távolíthat el felhasználó által hozzárendelt felügyelt identitást egy virtuálisgép-méretezési csoport curl használatával hívásokat az Azure Resource Manager REST végpont.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése virtuálisgép-méretezési csoport létrehozása során

1. Egy tulajdonosi hozzáférési jogkivonat lekérése, amelyet az Engedélyezés fejléc következő lépésében a rendszer által hozzárendelt felügyelt identitással hoz létre a virtuálisgép-méretezési készlet létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Hozzon létre [egy hálózati adaptert](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) a virtuálisgép-méretezési csoporthoz:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Egy tulajdonosi hozzáférési jogkivonat lekérése, amelyet az Engedélyezés fejléc következő lépésében a rendszer által hozzárendelt felügyelt identitással hoz létre a virtuálisgép-méretezési készlet létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az itt található utasítások alapján: [Felhasználó által hozzárendelt felügyelt identitás létrehozása](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Hozzon létre egy virtuálisgép-méretezési készlet curl használatával hívja meg az Azure Resource Manager REST-végpont. A következő példa létrehoz egy *myVMSS* nevű virtuálisgép-méretezési csoportot a *myResourceGroup* erőforráscsoportban egy felhasználó által hozzárendelt felügyelt identitással `ID1`, ahogy azt a kérelemtörzsben az érték `"identity":{"type":"UserAssigned"}`azonosítja. Cserélje `<ACCESS TOKEN>` le az előző lépésben kapott értéket, amikor egy `<SUBSCRIPTION ID>` bemutatóra szóló hozzáférési jogkivonatot kért, és a környezetének megfelelő értéket.
 
   **API-verzió 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Fejlécek kérése**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípusa*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot. | 

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

   **API-verzió 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Fejlécek kérése**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípusa*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot. |
 
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

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése meglévő Azure virtuálisgép-méretezési csoporthoz

1. Egy tulajdonosi hozzáférési jogkivonat lekérése, amelyet az Engedélyezés fejléc következő lépésében a rendszer által hozzárendelt felügyelt identitással hoz létre a virtuálisgép-méretezési készlet létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az itt [található, Felhasználó által hozzárendelt felügyelt identitás létrehozása](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Annak érdekében, hogy ne törölje a virtuálisgép-méretezési készlethez rendelt meglévő felhasználói vagy rendszeráltal hozzárendelt felügyelt identitásokat, a következő CURL paranccsal fel kell sorolnia a virtuálisgép-méretezési csoporthoz rendelt identitástípusokat. Ha a virtuálisgép-méretezési készlethez rendelt felügyelt identitásokat, `identity` azok az értékben jelennek meg.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Fejlécek kérése**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot. |   
 

4. Ha nem rendelkezik a virtuálisgép-méretezési csoporthoz rendelt felhasználó- vagy rendszer-hozzárendelt felügyelt identitásokkal, a következő CURL-paranccsal hívja meg az Azure Resource Manager REST-végpontját az első felhasználó által hozzárendelt felügyelt identitás hozzárendeléséhez a virtuális géphez. méretezési készlet.  Ha a virtuálisgép-méretezési csoporthoz hozzárendelt felhasználó- vagy rendszerszintű felügyelt identitás(ok) vannak hozzárendelve, ugorjon az 5. Identitás.

   A következő példa egy felhasználó által hozzárendelt `ID1` felügyelt identitást rendel egy *myVMSS* nevű virtuálisgép-méretezési csoporthoz a *myResourceGroup*erőforráscsoportban.  Cserélje `<ACCESS TOKEN>` le az előző lépésben kapott értéket, amikor egy `<SUBSCRIPTION ID>` bemutatóra szóló hozzáférési jogkivonatot kért, és a környezetének megfelelő értéket.

   **API-verzió 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01 HTTP/1.1
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
          "type":"userAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ``` 
    
   **API-verzió 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```  

5. Ha rendelkezik a virtuálisgép-méretezési csoporthoz egy meglévő, felhasználó által hozzárendelt vagy rendszeráltal hozzárendelt felügyelt identitással:
   
   **API-verzió 2018-06-01**

   Adja hozzá a felhasználó által hozzárendelt felügyelt identitást a `userAssignedIdentities` szótárértékhez.

   Ha például rendelkezik a rendszer által hozzárendelt felügyelt identitással `ID1` és a virtuálisgép-méretezéshez jelenleg hozzárendelt, a felhasználó által `ID2` hozzárendelt felügyelt identitással, és hozzá szeretné adni hozzá a felhasználó által hozzárendelt felügyelt identitást:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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

   Ha például rendelkezik a rendszer által hozzárendelt identitással és `ID1` a felhasználó által hozzárendelt felügyelt identitással, amely jelenleg a `ID2` virtuálisgép-méretezési csoporthoz van rendelve, és hozzá szeretné adni a felhasználó által hozzárendelt felügyelt identitást:

    ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása egy virtuálisgép-méretezési csoportból

1. Egy tulajdonosi hozzáférési jogkivonat lekérése, amelyet az Engedélyezés fejléc következő lépésében a rendszer által hozzárendelt felügyelt identitással hoz létre a virtuálisgép-méretezési készlet létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Annak érdekében, hogy ne törölje a meglévő, felhasználó által hozzárendelt felügyelt identitásokat, amelyeket meg szeretne tartani a virtuálisgép-méretezési csoporthoz rendelve, vagy távolítsa el a rendszer által hozzárendelt felügyelt identitást, a felügyelt identitásokat a következő CURL paranccsal kell felsorolnia. :

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Fejlécek kérése**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Engedélyezési*     | Kötelező. Állítsa be `Bearer` egy érvényes hozzáférési jogkivonatot. |
   
   Ha a virtuális géphez rendelt felügyelt identitásokat, azok szerepelnek `identity` az értékben szereplő válaszban. 
    
   Ha például a felhasználó által hozzárendelt `ID1` `ID2` felügyelt identitások vannak, és a virtuálisgép-méretezési csoporthoz van rendelve, és csak a hozzárendelt és a rendszeráltal hozzárendelt felügyelt identitást szeretné megtartani: `ID1`

   **API-verzió 2018-06-01**

   Adja `null` hozzá az eltávolítani kívánt, felhasználó által hozzárendelt felügyelt identitáshoz:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Ha a virtuálisgép-méretezési csoport rendszer- és felhasználóáltal hozzárendelt felügyelt identitásokkal is rendelkezik, a következő paranccsal eltávolíthatja az összes felhasználó által hozzárendelt felügyelt identitást, ha a következő paranccsal csak a rendszer által hozzárendelt rendszerhasználatára vált:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
    
Ha a virtuálisgép-méretezési csoport csak a felhasználó által hozzárendelt felügyelt identitások, és szeretné eltávolítani őket, használja a következő parancsot:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
      "type":"None"
   }
}
```

## <a name="next-steps"></a>További lépések

A felhasználó által hozzárendelt felügyelt identitások létrehozásáról, listázásáról és törléséről a REST használatával a következő témakörben talál tájékoztatást:

- [Felhasználó által hozzárendelt felügyelt identitás létrehozása, listázása vagy törlése REST API-hívások használatával](how-to-manage-ua-identity-rest.md)
