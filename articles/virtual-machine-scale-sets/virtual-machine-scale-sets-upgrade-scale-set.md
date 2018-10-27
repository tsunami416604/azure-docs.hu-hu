---
title: Egy Azure-beli virtuálisgép-méretezési csoportot módosítása |} A Microsoft Docs
description: Ismerje meg, hogyan módosíthatja, és a egy Azure-beli virtuálisgép-méretezési csoportot a REST API-k, az Azure PowerShell-lel és az Azure CLI frissítése
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: negat
ms.openlocfilehash: d065f9765ca279f14b9de45e2412b75c94ef3dd8
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139028"
---
# <a name="modify-a-virtual-machine-scale-set"></a>Virtuális gép méretezési csoportok módosítása
Az alkalmazások életciklusa során szükség lehet módosítására vagy frissítésére a virtuálisgép-méretezési csoportot. Ezek a frissítések tartalmazhatnak frissítse a konfigurációt a méretezési, vagy módosítsa az alkalmazás konfigurációját. Ez a cikk ismerteti, hogyan módosíthatja egy meglévő méretezési csoportot a REST API-k, az Azure PowerShell vagy az Azure CLI.

## <a name="fundamental-concepts"></a>Alapvető fogalmak

### <a name="the-scale-set-model"></a>A méretezési csoport modelljéből
Egy méretezési csoportot tartalmaz egy "méretezési csoport modelljéből", amely rögzíti a *kívánt* mint egészként beállítását a méretezési csoport állapotát. A méretezési csoportok modell lekérdezésére, használhatja a 

- REST API-t [számítási/virtualmachinescalesets/get](/rest/api/compute/virtualmachinescalesets/get) módon:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Az Azure PowerShell [a Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss):

    ```powershell
    Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Az Azure CLI-t [az vmss show](/cli/azure/vmss#az_vmss_show):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- Is [resources.azure.com](https://resources.azure.com) vagy a nyelvspecifikus [Azure SDK-k](https://azure.microsoft.com/downloads/).

A kimenet a pontos bemutató függ, a parancshoz adja meg a beállításokat. Az alábbi példa bemutatja a tömörített minta kimenet az Azure parancssori felületen:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Ezek a tulajdonságok a méretezési csoport egészére vonatkozik.


### <a name="the-scale-set-instance-view"></a>A méretezési csoport példányait tartalmazó nézet
Egy méretezési csoportot is megtalálható a "méretezési csoport példánya megtekintése", amely rögzíti az aktuális *futásidejű* mint egészként beállítását a méretezési csoport állapotát. A méretezési csoport példányait tartalmazó nézetet lekérdezéséhez használhatja:

- REST API-t [számítási/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) módon:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Az Azure PowerShell [a Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss):

    ```powershell
    Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Az Azure CLI-t [az vmss get-instance-view](/cli/azure/vmss#az_vmss_get_instance_view):

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Is [resources.azure.com](https://resources.azure.com) vagy a nyelvspecifikus [Azure SDK-k](https://azure.microsoft.com/downloads/)

A kimenet a pontos bemutató függ, a parancshoz adja meg a beállításokat. Az alábbi példa bemutatja a tömörített minta kimenet az Azure parancssori felületen:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
}
```

Ezeket a tulajdonságokat adja meg a virtuális gépeket a méretezési csoportba, például az alkalmazása a méretezési csoporthoz bővítmények állapotát futásidejű aktuális állapotának összegzését.


### <a name="the-scale-set-vm-model-view"></a>A méretezési csoport Virtuálisgép-modellnézet
Hogyan méretezési rendelkezik-e egy modellnézet hasonlóan, a méretezési csoportban lévő egyes Virtuálisgép-példány rendelkezik a saját modellnézet. A modell lekérdezésnézet egy méretezési csoportban egy adott Virtuálisgép-példánnyal, használhatja:

- REST API-t [számítási/virtualmachinescalesetvms/get](/rest/api/compute/virtualmachinescalesetvms/get) módon:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Az Azure PowerShell [Get-AzureRmVmssVm](/powershell/module/azurerm.compute/get-azurermvmssvm):

    ```powershell
    Get-AzureRmVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Az Azure CLI-t [az vmss show](/cli/azure/vmss#az_vmss_show):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Is [resources.azure.com](https://resources.azure.com) vagy a [Azure SDK-k](https://azure.microsoft.com/downloads/).

A kimenet a pontos bemutató függ, a parancshoz adja meg a beállításokat. Az alábbi példa bemutatja a tömörített minta kimenet az Azure parancssori felületen:

```azurecli
$ az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Ezek a tulajdonságok egy méretezési csoportot, nem a teljes méretezési konfiguráció belül egy VM-példány konfigurálását. Például, a méretezési csoport modelljéből, `overprovision` tulajdonságként, amíg a modell számára egy egy méretezési csoportban lévő Virtuálisgép-példány nem létezik. Ez a különbség van, mert túlzott egy tulajdonságot a méretezési csoport, egy teljes, nem az egyéni Virtuálisgép-példányok a méretezési csoportban lévő (túlzott kapcsolatos további információkért lásd: [kialakítási szempontok a méretezési csoportok](virtual-machine-scale-sets-design-overview.md#overprovisioning)).


### <a name="the-scale-set-vm-instance-view"></a>A méretezési csoport Virtuálisgép-példányokat tartalmazó nézet
Hogyan rendelkezik egy méretezési csoportot, egy példányait tartalmazó nézet hasonlóan a méretezési csoportban lévő egyes Virtuálisgép-példány rendelkezik a saját példányait tartalmazó nézet. Példányait tartalmazó nézetet egy adott Virtuálisgép-példányhoz egy méretezési csoportban lévő lekérdezéséhez használhatja:

- REST API-t [számítási/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) módon:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Az Azure PowerShell [Get-AzureRmVmssVm](/powershell/module/azurerm.compute/get-azurermvmssvm):

    ```powershell
    Get-AzureRmVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- Az Azure CLI-t [az vmss get-instance-view](/cli/azure/vmss#az_vmss_get_instance_view)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Is [resources.azure.com](https://resources.azure.com) vagy a [Azure SDK-k](https://azure.microsoft.com/downloads/)

A kimenet a pontos bemutató függ, a parancshoz adja meg a beállításokat. Az alábbi példa bemutatja a tömörített minta kimenet az Azure parancssori felületen:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
}
```

Ezek a Tulajdonságok írják le egy Virtuálisgép-példány egy méretezési csoportot, a méretezési csoporthoz a alkalmazni kiterjesztések tartalmazó belüli aktuális futási állapotát.


## <a name="how-to-update-global-scale-set-properties"></a>Globális méretezhetőség frissítése tulajdonságainak beállítása
Egy globális méretű tulajdonság beállítása frissítéséhez frissítenie kell a tulajdonságot a méretezési csoport modelljéből. Ez a frissítés keresztül teheti meg:

- REST API-t [számítási/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate) módon:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- A tulajdonságokkal a REST API-t, globális méretű készlet tulajdonságainak frissítése a Resource Manager-sablon is telepítheti.

- Az Azure PowerShell [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss):

    ```powershell
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Az Azure CLI-t [az vmss update](/cli/azure/vmss#az_vmss_update):
    - Egy tulajdonság módosítása:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Egy lista tulajdonságot a méretezési csoportban lévő objektum hozzáadása: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Egy lista tulajdonságot a méretezési csoportban lévő objektum eltávolítása: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Ha korábban telepítette a méretezési csoport a `az vmss create` parancsot futtathatja a `az vmss create` parancsot újra frissíteni a méretezési csoportot. Győződjön meg arról, hogy az összes tulajdonság a `az vmss create` parancs ugyanazok, mint korábban, kivéve a módosítani kívánt tulajdonságokat.

- Is [resources.azure.com](https://resources.azure.com) vagy a [Azure SDK-k](https://azure.microsoft.com/downloads/).

A méretezési csoport modelljéből frissül, amint a méretezési csoportban létrehozott összes új virtuális gépet az új konfiguráció vonatkozik. Azonban a modellt a meglévő virtuális gépeket a méretezési csoportban lévő továbbra is tudomására kell hozni naprakészen tartása a legújabb összesített méretezési csoport modelljéből. A modell virtuális gépek logikai tulajdonság neve `latestModelApplied` , amely azt jelzi, hogy a virtuális gép-e naprakészen tartása a legújabb összesített méretezési csoport modelljéből (`true` azt jelenti, hogy a virtuális gép naprakészen tartása a legújabb modellre).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Virtuális gépek naprakészen tartása a legújabb méretezési csoport modelljéből való
A méretezési csoportok rendelkezik egy "frissítési házirendet", amely határozza meg, hogyan kerül naprakészen tartása a legújabb méretezési csoport modelljéből, virtuális gépeket. A három mód a házirend a következők:

- **Automatikus** – ebben a módban a méretezési csoportban nem garantálja a virtuális gépek telepítik sorrendjével kapcsolatos teszi. A méretezési csoportban egyszerre le virtuális gépeket vehet igénybe. 
- **A működés közbeni** – ebben a módban a méretezési vezet be a frissítést egy nem kötelező szüneteltetési idő kötegekben kötegek között.
- **Manuális** – ebben a módban, ha frissíti a méretezési csoport modelljéből, semmi nem történik a meglévő virtuális gépekhez.
 
Frissítse a meglévő virtuális gépek, hajtsa végre az egyes meglévő virtuális gépek "Kézi frissítés". A manuális frissítés esetén teheti meg:

- REST API-t [számítási/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances) módon:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Az Azure PowerShell [Update-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance):
    
    ```powershell
    Update-AzureRmVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Az Azure CLI-t [az vmss update-instances](/cli/azure/vmss#az_vmss_update_instances)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- Is használhatja a nyelvspecifikus [Azure SDK-k](https://azure.microsoft.com/downloads/).

>[!NOTE]
> Service Fabric-fürtök csak használhat *automatikus* mód, de a frissítés eltérően kell kezelni. További információkért lásd: [ Service Fabric alkalmazásfrissítések](../service-fabric/service-fabric-application-upgrade.md).

Globális méretezhetőség tulajdonságainak beállítása, amely nem követi a házirend módosítása egy adott típusú van. A méretezési csoportot (például rendszergazdai felhasználónév és jelszó) operációsrendszer-profil csak akkor módosítható az API-verzió vált *2017-12-01* vagy újabb. Ezek a változások csak után a változás a méretezési csoportban lévő méretezésicsoport-modellben létrehozott virtuális gépekre vonatkozik. Ahhoz, hogy a meglévő virtuális gépek naprakész, hajtsa végre az egyes meglévő virtuális gépek "alaphelyzetbe". A rendszerkép alaphelyzetbe állítását keresztül teheti meg:

- REST API-t [számítási/virtualmachinescalesets vagy rendszerképét alaphelyzetbe állítani](/rest/api/compute/virtualmachinescalesets/reimage) módon:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Az Azure PowerShell [Set-AzureRmVmssVm](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm):

    ```powershell
    Set-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Az Azure CLI-t [az vmss reimage](https://docs.microsoft.com/cli/azure/vmss#az_vmss_reimage):

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Is használhatja a nyelvspecifikus [Azure SDK-k](https://azure.microsoft.com/downloads/).


## <a name="properties-with-restrictions-on-modification"></a>A módosítás korlátozásokkal tulajdonságai

### <a name="create-time-properties"></a>Hozzon létre futásidejű tulajdonságok
Néhány tulajdonság csak akkor állítható be, a méretezési csoportban létrehozott. Ezek a tulajdonságok a következők:

- Rendelkezésre állási zónák
- Lemezkép-hivatkozás közzétevője
- Lemezkép-hivatkozás ajánlat
- Felügyelt operációsrendszer-lemez tárfióktípus

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>A jelenlegi érték alapján, amely csak akkor módosítható tulajdonságok
Egyes tulajdonságok kivételekkel aktuális értékétől függően változhat. Ezek a tulajdonságok a következők:

- **singlePlacementGroup** – Ha a singlePlacementGroup értéke igaz, hamis értékre módosíthatja. Azonban, ha a singlePlacementGroup false (hamis), akkor **nem lehetséges, hogy** módosítható igaz értékre.
- **alhálózat** – addig, amíg az eredeti alhálózat lehet módosítani az alhálózat egy méretezési csoportot, és az új alhálózat van ugyanazon a virtuális hálózaton.

### <a name="properties-that-require-deallocation-to-change"></a>Tulajdonságok módosításához felszabadítási igénylő
Néhány tulajdonság csak akkor bizonyos értékeinek módosítható, ha a méretezési csoportban lévő virtuális gépek felszabadított állapotban vannak. Ezek a tulajdonságok a következők:

- **SKU-név**– Ha az új VM-Termékváltozat nem támogatja a hardvert, a méretezési csoportban jelenleg fel, a virtuális gépeket a méretezési csoportban a termékváltozat módosítása előtt szabadítsa fel kell. További információkért lásd: [egy Azure virtuális gép átméretezése](../virtual-machines/windows/resize-vm.md).


## <a name="vm-specific-updates"></a>Virtuálisgép-specifikus frissítések
Egyes módosítások a globális szintű tulajdonságainak megadása helyett a megadott virtuális gépekre lesznek érvényesek. Jelenleg az egyetlen támogatott virtuális Gépre jellemző frissítés, hogy az adatlemezek/virtuális gépek a méretezési csoportban lévő csatlakoztatási/leválasztási. Ez a funkció előzetes verzióban érhető el. További információkért lásd: a [előzetes verzió dokumentációjában](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).


## <a name="scenarios"></a>Forgatókönyvek

### <a name="application-updates"></a>Alkalmazásfrissítések
Ha van egy alkalmazás központi telepítése egy méretezési csoportot bővítmények révén, a bővítmény konfigurációjának frissítése hatására a frissítési szabályzatának megfelelően frissíteni az alkalmazást. Például ha egy parancsfájl futtatását egy egyéni szkriptek futtatására szolgáló bővítmény új verziója, hogy sikerült frissíteni a *fileUris* tulajdonságot úgy, hogy az új parancsfájl mutasson. Bizonyos esetekben érdemes lehet frissítheti a annak ellenére, hogy a bővítmény konfigurációja nem változott (például frissítése átírása nélkül a parancsfájl-szkript URI-ra). Ezekben az esetekben, módosíthatja a *forceUpdateTag* frissítés kényszerítése. Ez a tulajdonság nem értelmezi az Azure platformon. Módosítsa az értéket, ha nem, hogy a bővítmény fut-e nincs hatással. Változást egyszerűen kényszeríti a bővítmény ismételt futtatásához használható. További információ a *forceUpdateTag*, tekintse meg a [REST API – a bővítményekről](/rest/api/compute/virtualmachineextensions/createorupdate). Vegye figyelembe, hogy a *forceUpdateTag* összes kiterjesztésű, nem csak az egyéni szkriptbővítmény is használható.

Emellett akkor is egyéni rendszerkép keresztül telepíthető alkalmazások esetében gyakori. Ebben a forgatókönyvben a következő szakasz tárgyalja.

### <a name="os-updates"></a>Operációs rendszer frissítése
Ha az Azure platform rendszerképét használja, a lemezkép módosításával frissítheti a *imageReference* (további információkért tekintse meg a [REST API-dokumentáció](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> A platform rendszerképeit közös "legújabb" adja meg a lemezkép referencia verziója esetén. Létrehozásakor, horizontális felskálázás, és a rendszerkép alaphelyzetbe állítását, virtuális gépek jönnek létre az elérhető legújabb verzióra. Azonban ez **nem** jelenti azt, hogy az operációs rendszer lemezképe automatikusan frissül idővel kiadott új lemezkép-verzió. Egy külön szolgáltatás jelenleg előzetes verziója, amely biztosítja az operációs rendszer automatikus verziófrissítését. További információkért lásd: a [operációs rendszer automatikus verziófrissítését dokumentáció](virtual-machine-scale-sets-automatic-upgrade.md).

Ha egyéni lemezképeket használ, a kép frissítésével frissítheti a *imageReference* azonosítója (további információkért tekintse meg a [REST API-dokumentáció](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

## <a name="examples"></a>Példák

### <a name="update-the-os-image-for-your-scale-set"></a>A méretezési csoport operációsrendszer-lemezkép frissítése
Előfordulhat, hogy egy méretezési csoportot, amely az Ubuntu LTS 16.04 régebbi verzióját futtatja. Ubuntu LTS 16.04, mint például verziója újabb verziójára frissíteni kívánt *16.04.201801090*. A lemezkép referencia verziója tulajdonság nem szerepel a listában, így közvetlenül módosíthatja ezeket a tulajdonságokat, a következő parancsok egyikét:

- Az Azure PowerShell [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) módon:

    ```powershell
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Az Azure CLI-t [az vmss update](/cli/azure/vmss#az_vmss_update_instances):

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>Frissítse a terheléselosztót a méretezési csoporthoz
Tegyük fel, van egy méretezési csoportot egy Azure Load Balancert, és cserélje le az Azure Application Gateway az Azure Load Balancer szeretne. A load balancer és a egy méretezési csoportot az Application Gateway tulajdonságok listáját, részét képezik, így a parancsok segítségével távolítsa el, vagy adja hozzá a lista elemeinek helyett közvetlenül módosítja a tulajdonságokat:

- Azure PowerShell-lel:

    ```powershell
    # Get the current model of the scale set and store it in a local PowerShell object named $vmss
    $vmss=Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
    
    # Create a local PowerShell object for the new desired IP configuration, which includes the referencerence to the application gateway
    $ipconf = New-AzureRmVmssIPConfig "myNic" -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name
    
    # Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf
    
    # Update the model of the scale set with the new configuration in the local PowerShell object
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -virtualMachineScaleSet $vmss
    ```

- Azure CLI:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> A parancsok használata előtt, a méretezési csoportban csak egy IP konfigurációja és a load balancer van. Ha több, szükség lehet egy listaindex nem használandó *0*.


## <a name="next-steps"></a>További lépések
Gyakori felügyeleti feladatok végrehajtása a méretezési csoportokkal együtt az [Azure CLI-vel](virtual-machine-scale-sets-manage-cli.md) vagy [Azure PowerShell-lel](virtual-machine-scale-sets-manage-powershell.md).
