---
title: Egy Azure virtuálisgép-méretezési csoport módosítása |} Microsoft Docs
description: Megtudhatja, hogyan módosítása vagy egy Azure virtuálisgép-méretezési állítsa be a REST API-k, az Azure PowerShell és az Azure CLI 2.0 frissítése
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
ms.openlocfilehash: 662cea7ac47e411b127540faf5cab8b3c4d8964a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="modify-a-virtual-machine-scale-set"></a>Módosítsa a virtuálisgép-méretezési csoport
Az alkalmazások életciklusa során szükség lehet módosítani, vagy a virtuálisgép-méretezési csoport frissítése. Ezeket a frissítéseket is venni, hogyan lehet frissíteni a beállításait, a méretezési, vagy módosítsa az alkalmazás konfigurációját. A cikkből megtudhatja, hogyan lehet módosítani egy meglévő méretezési a REST API-k, az Azure PowerShell vagy Azure CLI 2.0 készletben.

## <a name="fundamental-concepts"></a>alapvető fogalmai

### <a name="the-scale-set-model"></a>A méretezési modell
A méretezési rendelkezik egy "méretezési modellel", amely rögzíti a *kívánt* a skála állapotát állítja be a teljes. A modell skálázási készletének lekérdezéséhez használja a 

- REST API-t [compute/virtualmachinescalesets/get](/rest/api/compute/virtualmachinescalesets/get) az alábbiak szerint:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Az Azure PowerShell [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss):

    ```powershell
    Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Az Azure CLI 2.0 [az vmss megjelenítése](/cli/azure/vmss#az_vmss_show):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- Is [resources.azure.com](https://resources.azure.com) vagy a nyelvspecifikus [Azure SDK-k](https://azure.microsoft.com/downloads/).

A kimeneti pontos bemutató attól függ, hogy a beállításokat, majd a parancs való. A következő példa bemutatja a tömörített minta kimenet az Azure CLI 2.0:

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

Ezeket a tulajdonságokat a méretezési készletben egészére vonatkozik.


### <a name="the-scale-set-instance-view"></a>A méretezési csoport példányait tartalmazó nézet
A méretezési készletben is megtalálható a "méretezési készlet példánya megtekintése", amely rögzíti az aktuális *futásidejű* a skála állapotát állítja be a teljes. Az egy méretezési csoport példányait tartalmazó nézetet lekérdezéséhez használhatja:

- REST API-t [compute/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) az alábbiak szerint:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Az Azure PowerShell [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss):

    ```powershell
    Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Az Azure CLI 2.0 [get-példány-nézet az vmss](/cli/azure/vmss#az_vmss_get_instance_view):

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Is [resources.azure.com](https://resources.azure.com) vagy a nyelvspecifikus [Azure SDK-k](https://azure.microsoft.com/downloads/)

A kimeneti pontos bemutató attól függ, hogy a beállításokat, majd a parancs való. A következő példa bemutatja a tömörített minta kimenet az Azure CLI 2.0:

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

Ezeket a tulajdonságokat adja meg az aktuális futási állapotát, például a méretezési alkalmazott bővítmények állapotának a méretezési csoportban lévő virtuális gépek.


### <a name="the-scale-set-vm-model-view"></a>A méretezési VM modell megtekintése
Hasonló hogyan egy méretezési csoport rendelkezik a modell nézet, a méretezési csoportban lévő összes virtuális Géphez van saját modell nézet. A modell nézetét a méretezési lekérdezéséhez használhatja:

- REST API-t [compute/virtualmachinescalesetvms/get](/rest/api/compute/virtualmachinescalesetvms/get) az alábbiak szerint:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Az Azure PowerShell [Get-AzureRmVmssVm](/powershell/module/azurerm.compute/get-azurermvmssvm):

    ```powershell
    Get-AzureRmVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Az Azure CLI 2.0 [az vmss megjelenítése](/cli/azure/vmss#az_vmss_show):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Is [resources.azure.com](https://resources.azure.com) vagy a [Azure SDK-k](https://azure.microsoft.com/downloads/).

A kimeneti pontos bemutató attól függ, hogy a beállításokat, majd a parancs való. A következő példa bemutatja a tömörített minta kimenet az Azure CLI 2.0:

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

Ezek a Tulajdonságok konfigurálását a virtuális gép, nem a méretezési készletben egész konfigurációját. Például a skála modellel rendelkezik `overprovision` tulajdonságként, míg a virtuális gépek méretezési csoportban lévő modell nem. Ezt a különbséget azért, mert a elhelyezésétől állítja be a teljes, nem az egyes virtuális gépek a méretezési csoportban lévő a skála tulajdonságának (elhelyezésétől kapcsolatos további információkért lásd: [kialakítási szempontok a méretezési készlet](virtual-machine-scale-sets-design-overview.md#overprovisioning)).


### <a name="the-scale-set-vm-instance-view"></a>A méretezési virtuális gép példányait tartalmazó nézet
Hasonló hogyan rendelkezik, egy méretezési csoport példányait tartalmazó nézetet, a méretezési csoportban lévő összes virtuális Géphez van saját példányait tartalmazó nézetet. Az egy méretezési csoport példányait tartalmazó nézetet lekérdezéséhez használhatja:

- REST API-t [compute/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) az alábbiak szerint:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Az Azure PowerShell [Get-AzureRmVmssVm](/powershell/module/azurerm.compute/get-azurermvmssvm):

    ```powershell
    Get-AzureRmVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- Az Azure CLI 2.0 [az vmss get-példány-nézet](/cli/azure/vmss#az_vmss_get_instance_view)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Is [resources.azure.com](https://resources.azure.com) vagy a [Azure SDK-k](https://azure.microsoft.com/downloads/)

A kimeneti pontos bemutató attól függ, hogy a beállításokat, majd a parancs való. A következő példa bemutatja a tömörített minta kimenet az Azure CLI 2.0:

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

Ezeket a tulajdonságokat a virtuális Gépet, a méretezési alkalmazott kiterjesztések tartalmazó aktuális futási állapotának ismertetik.


## <a name="how-to-update-global-scale-set-properties"></a>Globális méretű frissítése tulajdonságainak beállítása
A globális méretű tulajdonságbeállító frissítéséhez frissítenie kell a méretezési készlet modell tulajdonság. A frissítés keresztül teheti meg:

- REST API-t [compute/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate) az alábbiak szerint:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- A Resource Manager-sablon a tulajdonságokkal a REST API, globális méretű készlet tulajdonságainak frissítésére való telepítése.

- Az Azure PowerShell [frissítés-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss):

    ```powershell
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Az Azure CLI 2.0 [az vmss frissítése](/cli/azure/vmss#az_vmss_update):
    - Ha egy tulajdonság módosítására:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Méretezési csoportban lévő lista tulajdonság az objektum hozzáadása: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Méretezési csoportban lévő lista tulajdonság az objektum eltávolítása: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Ha korábban telepítette a méretezési készletben a `az vmss create` parancsot futtathatja a `az vmss create` újra a parancsot a skála frissítéséhez. Győződjön meg arról, hogy az összes tulajdonság a `az vmss create` parancs ugyanazok, mint korábban, kivéve a módosítani kívánt tulajdonságokat.

- Is [resources.azure.com](https://resources.azure.com) vagy a [Azure SDK-k](https://azure.microsoft.com/downloads/).

A skála modellel frissül, ha az új konfiguráció bármely új virtuális gépek létrehozása a méretezési csoportban lévő vonatkozik. Azonban a meglévő virtuális gépek, a méretezési csoportban lévő modellek kell továbbra is vonhatók naprakész, és a legújabb összesített méretezési modellel. A modell az egyes virtuális gépek logikai tulajdonság neve `latestModelApplied` , amely azt jelzi, hogy-e a virtuális Gépet a legújabb összesített méretezési modellel naprakészen (`true` azt jelenti, hogy a virtuális Gépet a legújabb modellt naprakész).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Virtuális gépek naprakész, amely a legfrissebb állapotba állítsa be a modell
Méretezési csoportok rendelkezik egy "frissítési házirend", amely határozza meg, hogyan kerülnek naprakész a legújabb méretezési modellel, és a virtuális gépek. A házirend három módot a következők:

- **Automatikus** -ebben a módban a méretezési készlet lehetővé teszi virtuális gépek indítottak sorrendjével kapcsolatos nem garantálja. A méretezési előfordulhat, hogy minden virtuális gép le egyszerre. 
- **Működés közbeni** -ebben a módban a méretezési bevezeti a kötegek egy választható szüneteltetése, amikor a frissítés kötegek között.
- **Manuális** – ebben a módban, ha frissíti a skála modellel, semmi nem történik a meglévő virtuális gépekhez.
 
Meglévő virtuális gépek frissítéséhez tegye minden meglévő virtuális gép "Kézi frissítés". Ez a kézi frissítés teheti meg:

- REST API-t [compute/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances) az alábbiak szerint:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Az Azure PowerShell [frissítés-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance):
    
    ```powershell
    Update-AzureRmVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Az Azure CLI 2.0 [az vmss frissítés-példányok](/cli/azure/vmss#az_vmss_update_instances)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- Használhatja a nyelvspecifikus [Azure SDK-k](https://azure.microsoft.com/downloads/).

>[!NOTE]
> Service Fabric-fürtök csak használható *automatikus* mód, de a frissítés eltérően kell kezelni. További információkért lásd: [ Service Fabric az alkalmazásfrissítéseket](../service-fabric/service-fabric-application-upgrade.md).

Nincs a globális méretű tulajdonságainak beállítása, amely nem követi a házirend módosítása egy adott típusú. A méretezési operációsrendszer-profilt (például a rendszergazda felhasználónevét és jelszavát) csak akkor módosítható az API-verzió vált *2017-12-01* vagy újabb. A módosítások csak a virtuális gépek létrehozása után a változás a skála modell vonatkoznak. Ahhoz, hogy a meglévő virtuális gépek naprakész, hajtsa végre a "lemezkép alaphelyzetbe" minden meglévő virtuális gép. A lemezkép-visszaállítási keresztül teheti meg:

- REST API-t [compute/virtualmachinescalesets vagy lemezkép-visszaállítási](/rest/api/compute/virtualmachinescalesets/reimage) az alábbiak szerint:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Az Azure PowerShell [Set-AzureRmVmssVm](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm):

    ```powershell
    Set-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Az Azure CLI 2.0 [az vmss lemezkép-visszaállítási](https://docs.microsoft.com/cli/azure/vmss#az_vmss_reimage):

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Használhatja a nyelvspecifikus [Azure SDK-k](https://azure.microsoft.com/downloads/).


## <a name="properties-with-restrictions-on-modification"></a>Tulajdonság módosítása korlátozásai

### <a name="create-time-properties"></a>Hozzon létre idő tulajdonságai
Néhány tulajdonság csak akkor állítható be, a méretezési csoport létrehozásakor. Ezek a tulajdonságok a következők:

- Rendelkezésre állási zónák
- lemezkép referencia publisher
- lemezkép referencia ajánlat
- Felügyelt operációsrendszer-lemez tárfióktípus

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>A jelenlegi érték alapján, amely csak akkor módosítható, tulajdonságok
Néhány tulajdonságát lehet módosítani, és kivételeket megadni, attól függően, hogy a jelenlegi érték. Ezek a tulajdonságok a következők:

- **singlePlacementGroup** – Ha singlePlacementGroup értéke igaz, hamis értékre módosíthatja. Azonban, ha hamis, singlePlacementGroup azt **nem** módosítható igaz értékre.
- **alhálózati** – az alhálózat egy méretezési addig, amíg az eredeti alhálózat módosíthat, és az új alhálózat ugyanabban a virtuális hálózatban.

### <a name="properties-that-require-deallocation-to-change"></a>Felszabadítás módosításához szükséges tulajdonságokat
Néhány tulajdonság csak akkor bizonyos értékekre módosítható, ha a virtuális gépeket, a méretezési csoportban lévő fel van szabadítva. Ezek a tulajdonságok a következők:

- **Termékváltozat**– Ha az új virtuális gép SKU nem támogatott a méretezési, a skála a termékváltozat módosítása előtt állítsa be a virtuális gépek felszabadítani kell hardveren. További információkért lásd: [egy Azure virtuális gép átméretezése](../virtual-machines/windows/resize-vm.md).


## <a name="vm-specific-updates"></a>VM-specifikus frissítések
Egyes módosítások alkalmazhatók az adott virtuális gépen a globális méretű tulajdonságainak beállítása helyett. Jelenleg az egyetlen támogatott Virtuálisgép-specifikus frissítés, hogy az adatlemezek/virtuális gépek a méretezési csoportban lévő csatlakoztatási/leválasztási. A funkció jelenleg előzetes verzió. További információkért lásd: a [dokumentáció előzetes](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).


## <a name="scenarios"></a>Forgatókönyvek

### <a name="application-updates"></a>Alkalmazás frissítései
Ha egy alkalmazás központi telepítése egy méretezési extensions segítségével állíthatók be, a egy frissítést adunk ki a bővítmény konfigurációja hatására az alkalmazás a frissítési házirendjével összhangban frissíteni. Például ha egy parancsfájl futtatását egy egyéni parancsprogramok futtatására szolgáló bővítmény új verzióját, akkor sikerült frissíteni a *fileUris* tulajdonság úgy, hogy az új parancsfájl mutasson. Bizonyos esetekben érdemes lehet kényszeríteni a frissítést, annak ellenére, hogy a bővítmény konfigurációja nem változott (például a parancsfájl megváltoztatása nélkül történő frissítés a parancsfájl URI). Ezekben az esetekben, módosíthatja a *forceUpdateTag* frissítés kényszerítéséhez. Az Azure platformon értelmezi ezt a tulajdonságot. Ha az érték módosításához nincs nincs hatással a bővítmény működésével. Változás egyszerűen kényszeríti kattintva futtassa újra a bővítményt. További információ a *forceUpdateTag*, tekintse meg a [bővítmények REST API dokumentációja](/rest/api/compute/virtualmachineextensions/createorupdate). Vegye figyelembe, hogy a *forceUpdateTag* együtt az összes bővítmény, nem csak az egyéni parancsprogramok futtatására szolgáló bővítmény.

Azt is alkalmazások esetében gyakori egyéni lemezkép keresztül telepíteni. Ebben a forgatókönyvben a következő szakasz foglalkozik.

### <a name="os-updates"></a>Operációs rendszer frissítése érdekében
Ha Azure platformon lemezképeket használ, a kép módosításával frissítheti a *imageReference* (további információkért lásd: a [REST API-dokumentáció](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> Platform képekkel esetében gyakori, a lemezkép referencia verziója "legújabb" megadása. Létrehozásakor réteget, és lemezkép alaphelyzetbe, virtuális gépek jönnek létre az elérhető legújabb verzióra. Azonban az **nem** jelenti azt, hogy az operációsrendszer-lemezképek automatikusan frissül időbeli kiadott új lemezkép-verziók. Egy külön funkció jelenleg előzetes, amely automatikus operációsrendszer-frissítések. További információkért lásd: a [automatikus operációsrendszer-frissítések dokumentáció](virtual-machine-scale-sets-automatic-upgrade.md).

Egyéni lemezképek használatakor a lemezkép frissítéséhez frissítheti a *imageReference* azonosítója (további információkért tekintse meg a [REST API-dokumentáció](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

## <a name="examples"></a>Példák

### <a name="update-the-os-image-for-your-scale-set"></a>Az operációs rendszer lemezképét a méretezési készlet
Előfordulhat, hogy egy méretezési csoport, amely az Ubuntu LTS 16.04 régebbi verzióját futtatja. Egy újabb verzióra az Ubuntu LTS 16.04, például a verziót frissíti *16.04.201801090*. A lemezkép referencia version tulajdonság nem része a listáját, és így közvetlenül módosíthatja ezeket a tulajdonságokat, a következő parancsok egyikét:

- Az Azure PowerShell [frissítés-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) az alábbiak szerint:

    ```powershell
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Az Azure CLI 2.0 [az vmss frissítése](/cli/azure/vmss#az_vmss_update_instances):

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>A terheléselosztó a méretezési készlet frissítése
Tegyük fel, a méretezési Azure terheléselosztással készletben rendelkezik, és cserélje le az Azure Load Balancer egy Azure Application Gateway szeretné. A terheléselosztó és a skálázási készletének Alkalmazásátjáró tulajdonságok listáját, részét képezik, a parancsok segítségével távolítsa el, vagy adja hozzá a lista elemeinek helyett közvetlenül módosítsák a tulajdonságait:

- Az Azure Powershell:

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

- Azure CLI 2.0:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> A parancsok használata nincs csak egy IP konfigurációs és a terheléselosztó a méretezési készlet. Ha több, szükség lehet egy listaindex eltérő használandó *0*.


## <a name="next-steps"></a>További lépések
Általános kezelési feladatai a skálázási készletek is elvégezheti a [Azure CLI 2.0](virtual-machine-scale-sets-manage-cli.md) vagy [Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md).
