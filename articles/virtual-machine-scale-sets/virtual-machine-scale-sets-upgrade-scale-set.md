---
title: Azure virtuálisgép-méretezési készlet módosítása
description: Megtudhatja, hogyan módosíthatja és frissítheti az Azure virtuálisgép-méretezési készletét a REST API-kkal, az Azure PowerShell-lel és az Azure CLI-vel
author: mimckitt
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mimckitt
ms.openlocfilehash: 66fd656b5175547641150a048e57c978dc06d291
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476824"
---
# <a name="modify-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport módosítása

Az alkalmazások teljes életciklusa során szükség lehet a virtuálisgép-méretezési csoport módosítására vagy frissítésére. Ezek a frissítések magukban foglalhatják a méretezési csoport konfigurációjának frissítését vagy az alkalmazás konfigurációjának módosítását. Ez a cikk ismerteti, hogyan módosíthatja a meglévő méretezési csoport a REST API-k, az Azure PowerShell vagy az Azure CLI.

## <a name="fundamental-concepts"></a>Alapfogalmak

### <a name="the-scale-set-model"></a>A méretezési modell
A méretezési készlet rendelkezik egy "méretezési halmaz modell", amely rögzíti a *kívánt* állapot a méretezési készlet egészére. A modell méretezési készletének lekérdezéséhez használhatja a 

- REST API [számítási/virtualgép-skálákkal/a](/rest/api/compute/virtualmachinescalesets/get) következőképpen:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Azure PowerShell [get-azvmss-szel:](/powershell/module/az.compute/get-azvmss)

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Az Azure CLI [az vmss show-val:](/cli/azure/vmss)

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- Használhatja [resources.azure.com](https://resources.azure.com) vagy a nyelvspecifikus [Azure SDK-kat](https://azure.microsoft.com/downloads/)is.

A kimenet pontos megjelenítése a parancsnak megadott beállításoktól függ. A következő példa az Azure CLI kondenzált mintakimenetét mutatja be:

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

Ezek a tulajdonságok a méretezési készlet egészére vonatkoznak.


### <a name="the-scale-set-instance-view"></a>A méretezési csoport példánynézete
A méretezési csoport is rendelkezik egy "méretezési csoport példány nézet", amely rögzíti az aktuális *futásidejű* állapotát a méretezési csoport egésze. A méretezési csoport példánynézetének lekérdezéséhez a következőket használhatja:

- REST API [számítási/virtualgép-skálákkal/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) az alábbiak szerint:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell [get-azvmss-szel:](/powershell/module/az.compute/get-azvmss)

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Azure CLI [az vmss get-instance-view nézet:](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Használhatja [a resources.azure.com](https://resources.azure.com) vagy a nyelvspecifikus [Azure SDK-kat](https://azure.microsoft.com/downloads/) is

A kimenet pontos megjelenítése a parancsnak megadott beállításoktól függ. A következő példa az Azure CLI kondenzált mintakimenetét mutatja be:

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

Ezek a tulajdonságok összegzést adnak a méretezési csoportban lévő virtuális gépek aktuális futásidejű állapotáról, például a méretezési csoportra alkalmazott bővítmények állapotáról.


### <a name="the-scale-set-vm-model-view"></a>A méretezési készlet virtuálisgép-modell nézete
Hasonlóan ahhoz, ahogyan egy méretezési csoport rendelkezik egy modellnézet, a méretezési csoport minden virtuálisgép-példány a saját modellnézet. Ha egy méretezési csoportban egy adott virtuálisgép-példány modellnézetét szeretné lekérdezni, a következőket használhatja:

- REST API [számítási/virtualgép-megmérettetési gépekkel/a következőképpen:](/rest/api/compute/virtualmachinescalesetvms/get)

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Azure PowerShell [a Get-AzVmssVm-mel:](/powershell/module/az.compute/get-azvmssvm)

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Az Azure CLI [az vmss show-val:](/cli/azure/vmss)

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Használhatja [resources.azure.com](https://resources.azure.com) vagy az [Azure SDK-kat](https://azure.microsoft.com/downloads/)is.

A kimenet pontos megjelenítése a parancsnak megadott beállításoktól függ. A következő példa az Azure CLI kondenzált mintakimenetét mutatja be:

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

Ezek a tulajdonságok egy méretezési csoporton belüli virtuálisgép-példány konfigurációját írják le, nem pedig a méretezési csoport egészének konfigurációját. Például a méretezési `overprovision` csoport modell tulajdonságként rendelkezik, míg a modell egy virtuálisgép-példány egy méretezési csoporton belül nem. Ez a különbség azért van, mert a túlterhelés a méretezési csoport egészének tulajdonsága, nem pedig a méretezési csoport egyedi virtuálisgép-példányai (a túlterheléssel kapcsolatos további információkért lásd: [Méretezési csoportok tervezési szempontok).](virtual-machine-scale-sets-design-overview.md#overprovisioning)


### <a name="the-scale-set-vm-instance-view"></a>A méretezési csoport virtuálisgép-példánynézete
Hasonlóan ahhoz, ahogyan egy méretezési csoport rendelkezik egy példánynézetkel, a méretezési csoport minden virtuálisgép-példánya saját példánynézetet rendelkezik. Egy méretezési csoporton belüli adott virtuálisgép-példány példánynézetének lekérdezéséhez a következőket használhatja:

- REST API [számítási/virtualgép-scalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) az alábbiak szerint:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell [a Get-AzVmssVm-mel:](/powershell/module/az.compute/get-azvmssvm)

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- Azure CLI [az vmss get-instance-view-val](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Használhatja [resources.azure.com](https://resources.azure.com) vagy az [Azure SDK-kat](https://azure.microsoft.com/downloads/) is

A kimenet pontos megjelenítése a parancsnak megadott beállításoktól függ. A következő példa az Azure CLI kondenzált mintakimenetét mutatja be:

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

Ezek a tulajdonságok egy méretezési csoporton belüli virtuálisgép-példány aktuális futásidejű állapotát írják le, amely tartalmazza a méretezési csoportra alkalmazott bővítményeket.


## <a name="how-to-update-global-scale-set-properties"></a>A globális méretezési készlet tulajdonságainak frissítése
Globális méretezési tulajdonság frissítéséhez frissítenie kell a tulajdonságot a méretezési halmaz modellben. Ezt a frissítést a következő címen teheti meg:

- REST API [számítási/virtualgép-skálákkal/createorupdate-al](/rest/api/compute/virtualmachinescalesets/createorupdate) az alábbiak szerint:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- A REST API-ból származó tulajdonságokkal rendelkező Resource Manager-sablont telepíthet a globális méretezési csoport tulajdonságainak frissítéséhez.

- Azure PowerShell [update-AzVmss](/powershell/module/az.compute/update-azvmss)segítségével:

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Azure CLI [az vmss frissítéssel:](/cli/azure/vmss)
    - Tulajdonság módosítása:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Objektum hozzáadása méretezési tulajdonság listatulajdonsághoz: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Objektum eltávolítása méretezési halmaz listatulajdonságából: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Ha korábban már telepítette `az vmss create` a méretezési készletet a paranccsal, a skálakészlet frissítéséhez újra futtathatja a `az vmss create` parancsot. Győződjön meg arról, hogy a parancs ban lévő `az vmss create` összes tulajdonság megegyezik a korábban, kivéve a módosítani kívánt tulajdonságokat.

- Használhatja [resources.azure.com](https://resources.azure.com) vagy az [Azure SDK-kat](https://azure.microsoft.com/downloads/)is.

A méretezési csoport modell frissítése után az új konfiguráció a méretezési csoportban létrehozott új virtuális gépekre vonatkozik. Azonban a modellek a meglévő virtuális gépek a méretezési csoportban továbbra is naprakészak kell lenniük a legújabb általános méretezési csoport modell. Az egyes virtuális gépek modelljében egy `latestModelApplied` logikai tulajdonság, amely azt jelzi, hogy a virtuális gép naprakész-e a legújabb általános méretezési készlet modellel (azt`true` jelenti, hogy a virtuális gép naprakész a legújabb modellel).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>A virtuális gépek naprakészen beállítása a legújabb méretezési készlet modellel
A méretezési csoportok rendelkeznek egy "frissítési szabályzattal", amely meghatározza, hogy a virtuális gépek hogyan kerülnek naprakészen a legújabb méretezési csoport modelljével. A frissítési házirend három módja a következő:

- **Automatikus** – Ebben a módban a méretezési csoport nem garantálja a virtuális gépek leállított sorrendjét. A méretezési készlet egyszerre az összes virtuális gépet csökkentheti. 
- **Gördülő** – Ebben a módban a méretezési csoport kötegekben vezeti ki a frissítést, a kötegek közötti szüneteltetési idő vel.
- **Manuális** – Ebben a módban a méretezési csoport modelljének frissítésekénél semmi sem történik a meglévő virtuális gépekkel.
 
Meglévő virtuális gépek frissítéséhez el kell végeznie egy "manuális frissítés" minden meglévő virtuális gép. Ezt a manuális frissítést a következőkkel teheti meg:

- REST API [számítási/virtualgép-skálákkal/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances) az alábbiak szerint:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Azure PowerShell [update-AzVmssInstance -szel:](/powershell/module/az.compute/update-azvmssinstance)
    
    ```powershell
    Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI [az vmss frissítési példányokkal](/cli/azure/vmss)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- A nyelvspecifikus [Azure SDK-k is](https://azure.microsoft.com/downloads/)használhatók.

>[!NOTE]
> A Service Fabric-fürtök csak *automatikus* módot használhatnak, de a frissítés kezelése eltérő. További információ: [Service Fabric-alkalmazások frissítései.](../service-fabric/service-fabric-application-upgrade.md)

A globális méretezési csoport tulajdonságainak egy olyan módosítása van, amely nem követi a frissítési házirendet. A méretezési csoport operációs rendszerének és adatlemezprofiljának módosításai (például rendszergazdai felhasználónév és jelszó) csak a *2017-12-01-es* vagy újabb API-verzióban módosíthatók. Ezek a módosítások csak a méretezési készlet modell módosítása után létrehozott virtuális gépekre vonatkoznak. A meglévő virtuális gépek naprakészen, minden meglévő virtuális gép "újraképzése" kell végeznie. Meg tudod csinálni ezt reimage keresztül:

- REST API [számítási/virtualgép-megskálázattal/újraképezéssel](/rest/api/compute/virtualmachinescalesets/reimage) az alábbiak szerint:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Azure PowerShell [set-AzVmssVm-mel:](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm)

    ```powershell
    Set-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Azure CLI [az vmss reimage:](https://docs.microsoft.com/cli/azure/vmss)

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- A nyelvspecifikus [Azure SDK-k is](https://azure.microsoft.com/downloads/)használhatók.


## <a name="properties-with-restrictions-on-modification"></a>Módosítási korlátozásokkal rendelkező tulajdonságok

### <a name="create-time-properties"></a>Létrehozási idejű tulajdonságok
Egyes tulajdonságok csak a méretezési csoport létrehozásakor állíthatók be. Ezek a tulajdonságok a következők:

- Rendelkezésre állási zónák
- Képreferencia-közzétevő
- Képreferencia-ajánlat
- Képreferencia-termékváltozat
- Felügyelt operációsrendszer-lemeztárfiók típusa

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Az aktuális érték alapján csak módosítható tulajdonságok
Egyes tulajdonságok módosíthatók, az aktuális értéktől függő kivételekvel. Ezek a tulajdonságok a következők:

- **singlePlacementGroup** - Ha a singlePlacementGroup igaz, akkor hamisra módosulhat. Ha azonban a singlePlacementGroup hamis, **előfordulhat,** hogy nem igaz.
- **alhálózat** – A méretezési halmaz alhálózata mindaddig módosítható, amíg az eredeti alhálózat és az új alhálózat ugyanabban a virtuális hálózatban van.

### <a name="properties-that-require-deallocation-to-change"></a>A módosításhoz deallocation-ot igénylő tulajdonságok
Egyes tulajdonságok csak akkor módosíthatók bizonyos értékekre, ha a méretezési csoportban lévő virtuális gépek felvannak osztva. Ezek a tulajdonságok a következők:

- **Termékváltozat neve**– Ha az új virtuálisgép-termékváltozat nem támogatott azon a hardveren, amelyen a méretezési készlet jelenleg van, a méretezési készletben lévő virtuális gépek felszabadítását a méretezési készletben a termékváltozat nevének módosítása előtt. További információ: [Hogyan átméretezheti az Azure virtuális gépek.](../virtual-machines/windows/resize-vm.md)


## <a name="vm-specific-updates"></a>Virtuális gép-specifikus frissítések
Bizonyos módosítások at lehet alkalmazni az adott virtuális gépek helyett a globális méretezési készlet tulajdonságait. Jelenleg az egyetlen virtuális gép-specifikus frissítés, amely támogatott, hogy csatolja/leválassza az adatlemezeket a méretezési csoportban lévő virtuális gépekhez. Ez a funkció előzetes verzióban érhető el. További információt az [előzetes verzió dokumentációjában](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk)talál.


## <a name="scenarios"></a>Forgatókönyvek

### <a name="application-updates"></a>Alkalmazásfrissítések
Ha egy alkalmazás bővítményen keresztül iskálán van telepítve, a bővítmény konfigurációjának frissítése miatt az alkalmazás a frissítési házirendnek megfelelően frissül. Ha például egy parancsfájl új verzióját futtatja egy egyéni parancsfájl-bővítményben, frissítheti a *fileUris tulajdonságot,* hogy az új parancsfájlra mutasson. Bizonyos esetekben előfordulhat, hogy a bővítmény konfigurációja változatlan marad (például a parancsfájl módosítása nélkül frissítette a parancsfájlt). Ezekben az esetekben módosíthatja a *forceUpdateTag-ot* a frissítés kényszerítéséhez. Az Azure platform nem értelmezi ezt a tulajdonságot. Ha módosítja az értéket, nincs hatással a bővítmény futására. A változás egyszerűen kényszeríti a kiterjesztést az ismétlésre. A *forceUpdateTag*szolgáltatásról a [REST API bővítményekkel kapcsolatos dokumentációjában olvashat bővebben.](/rest/api/compute/virtualmachineextensions/createorupdate) Ne feledje, hogy a *forceUpdateTag* használható az összes kiterjesztéssel, nem csak az egyéni parancsfájl kiterjesztéssel.

Az is gyakori, hogy az alkalmazások egyéni lemezképen keresztül telepíthetők. Ezt a forgatókönyvet a következő szakasz ismerteti.

### <a name="os-updates"></a>Operációs rendszer frissítései
Ha Azure platformrendszerképeket használ, frissítheti a lemezképet a *imageReference* módosításával (további információ: [A REST API dokumentációja).](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)

>[!NOTE]
> A platformképek esetében gyakori, hogy a "legújabb" értéket adja meg a képreferencia-verzióhoz. Létrehozásakor, horizontális felskálázása és újraképzése, virtuális gépek jönnek létre a legújabb elérhető verzióval. Ez azonban **nem** jelenti azt, hogy az operációs rendszer lemezképe idővel automatikusan frissül, ahogy az új lemezképverziók megjelennek. Jelenleg egy külön szolgáltatás előzetes verzióban érhető el, amely automatikus operációsrendszer-frissítéseket biztosít. További információt az [Automatikus operációsrendszer-frissítések dokumentációban](virtual-machine-scale-sets-automatic-upgrade.md)talál.

Ha egyéni lemezképeket használ, frissítheti a lemezképet a *képhivatkozási* azonosító frissítésével (további információ: [REST API dokumentáció).](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)

## <a name="examples"></a>Példák

### <a name="update-the-os-image-for-your-scale-set"></a>A méretezési készlet operációsrendszer-lemezképének frissítése
Lehet, hogy egy méretezési készlet, amely fut egy régi változata Ubuntu LTS 16.04. Az Ubuntu LTS 16.04 újabb verziójára szeretne frissíteni, például *a 16.04.201801090 verzióra.* A képreferencia-verzió tulajdonság nem része a listának, így közvetlenül módosíthatja ezeket a tulajdonságokat az alábbi parancsok egyikével:

- Az Azure PowerShell [az Update-AzVmss](/powershell/module/az.compute/update-azvmss) az alábbiak szerint:

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Azure CLI [az vmss frissítéssel:](/cli/azure/vmss)

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

Azt is megteheti, hogy módosítani szeretné a méretezési készlet által használt képet. Előfordulhat például, hogy frissíteni vagy módosítani szeretné a méretezési készlet által használt egyéni lemezképet. A méretezési halmaz által használt lemezképet a képhivatkozás-azonosító tulajdonság frissítésével módosíthatja. A képhivatkozás-azonosító tulajdonság nem része a listának, így közvetlenül módosíthatja ezt a tulajdonságot az alábbi parancsok egyikével:

- Az Azure PowerShell [az Update-AzVmss](/powershell/module/az.compute/update-azvmss) az alábbiak szerint:

    ```powershell
    Update-AzVmss `
        -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -ImageReferenceId /subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```

- Azure CLI [az vmss frissítéssel:](/cli/azure/vmss)

    ```azurecli
    az vmss update \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>A méretezési készlet terheléselosztójának frissítése
Tegyük fel, hogy egy Azure Load Balancer skálázott készlettel rendelkezik, és le szeretné cserélni az Azure Load Balancer-t egy Azure Application Gateway-re. A méretezési csoport terheléselosztó és Alkalmazásátjáró tulajdonságai egy lista részét képezik, így a parancsokkal a tulajdonságok közvetlen módosítása helyett eltávolíthatja vagy hozzáadhatja a listaelemeket:

- Azure Powershell:

    ```powershell
    # Get the current model of the scale set and store it in a local PowerShell object named $vmss
    $vmss=Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
    
    # Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
    $ipconf = New-AzVmssIPConfig "myNic" -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name
    
    # Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf
    
    # Update the model of the scale set with the new configuration in the local PowerShell object
    Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -virtualMachineScaleSet $vmss
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
> Ezek a parancsok feltételezik, hogy csak egy IP-konfiguráció és terheléselosztó van a méretezési csoportban. Ha több is van, előfordulhat, hogy *0-tól*eltérő listaindexet kell használnia.


## <a name="next-steps"></a>További lépések
Az [Azure CLI-vel](virtual-machine-scale-sets-manage-cli.md) vagy az [Azure PowerShell-lel](virtual-machine-scale-sets-manage-powershell.md)scale-készleteken is elvégezhet általános felügyeleti feladatokat.
