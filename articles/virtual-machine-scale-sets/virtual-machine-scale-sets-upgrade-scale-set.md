---
title: "Egy Azure virtuálisgép-méretezési csoport módosítása |} Microsoft Docs"
description: "Egy Azure virtuálisgép-méretezési csoport módosítása"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: negat
ms.openlocfilehash: cdd1015f63e80b7ec51565c18f3440ce1828fb03
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="modify-a-virtual-machine-scale-set"></a>Módosítsa a virtuálisgép-méretezési csoport
A cikkből megtudhatja, hogyan lehet módosítani egy meglévő méretezési készlet. Ez magában foglalja a skála konfigurációjának módosítása beállítása, a skála futó alkalmazások a konfiguráció módosítása beállításához rendelkezésre állását, és több kezelése.

## <a name="fundamental-concepts"></a>alapvető fogalmai

### <a name="the-scale-set-model"></a>A méretezési modell

A méretezési rendelkezik egy "méretezési modellel", amely rögzíti a *kívánt* a skála állapotát állítja be a teljes. A modell skálázási készletének lekérdezéséhez használhatja:

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` (további információkért lásd: a [REST API-dokumentáció](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/get))

PowerShell: `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (további információkért lásd: a [Powershell dokumentációs](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss))

Parancssori felület: `az vmss show -g {resourceGroupName} -n {vmSaleSetName}` (további információkért lásd: a [CLI dokumentáció](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show))

Is [resources.azure.com](https://resources.azure.com) vagy a [Azure SDK-k](https://azure.microsoft.com/downloads/) skálázási készletének modell lekérdezni.

A kimeneti pontos bemutatása a beállításokat, majd a parancs való függ, de itt van néhány minta kimenet a parancssori:

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
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
  .
  .
  .
}
```

Ahogy látja, ezeket a tulajdonságokat a méretezési készletben egészére vonatkozik.



### <a name="the-scale-set-instance-view"></a>A méretezési csoport példányait tartalmazó nézet

A méretezési készletben is megtalálható a "méretezési készlet példánya megtekintése", amely rögzíti az aktuális *futásidejű* a skála állapotát állítja be a teljes. Az egy méretezési csoport példányait tartalmazó nézetet lekérdezéséhez használhatja:

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version={apiVersion}` (további információkért lásd: a [REST API-dokumentáció](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview))

PowerShell: `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceView` (további információkért lásd: a [Powershell dokumentációs](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss))

Parancssori felület: `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName}` (további információkért lásd: a [CLI dokumentáció](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view))

Is [resources.azure.com](https://resources.azure.com) vagy a [Azure SDK-k](https://azure.microsoft.com/downloads/) lekérdezni a egy méretezési csoport példányait tartalmazó nézetet.

A kimeneti pontos bemutatása a beállításokat, majd a parancs való függ, de ez a parancssori kimenet:

```
$ az vmss get-instance-view -g {resourceGroupName} -n {virtualMachineScaleSetName}
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
  .
  .
  .
}
```

Ahogy látja, ezeket a tulajdonságokat meg az aktuális futási állapotát a rendszer a virtuális gépek beállítása, beleértve kiterjed a méretezési (nincs megadva kivonatosan mutatja) alkalmazott bővítmények.



### <a name="the-scale-set-vm-model-view"></a>A méretezési VM modell megtekintése

Hasonló hogyan egy méretezési csoport rendelkezik a modell nézet, a méretezési csoportban lévő összes virtuális Géphez van saját modell nézet. A modell nézetét a méretezési lekérdezéséhez használhatja:

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}?api-version={apiVersion}` (további információkért lásd: a [REST API-dokumentáció](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/get))

PowerShell: `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` (további információkért lásd: a [Powershell dokumentációs](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm))

Parancssori felület: `az vmss show -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` (további információkért lásd: a [CLI dokumentáció](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show))

Is [resources.azure.com](https://resources.azure.com) vagy a [Azure SDK-k](https://azure.microsoft.com/downloads/) a modell egy Virtuálisgép-méretezési csoportban lévő lekérdezésére.

A kimeneti pontos bemutatása a beállításokat, majd a parancs való függ, de itt van néhány minta kimenet a parancssori:

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
  .
  .
  .
}
```

Ahogy látja, ezek a Tulajdonságok konfigurálását a virtuális gép, nem a méretezési készletben egész konfigurációját. Például a skála modellel rendelkezik `overprovision` tulajdonságként, míg a virtuális gépek méretezési csoportban lévő modell nem. Ezt a különbséget azért, mert a elhelyezésétől állítja be a teljes, nem az egyes virtuális gépek a méretezési csoportban lévő a skála tulajdonságának (elhelyezésétől kapcsolatos további információkért lásd: [ebben a dokumentációban](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning)).



### <a name="the-scale-set-vm-instance-view"></a>A méretezési virtuális gép példányait tartalmazó nézet

Hasonló hogyan rendelkezik, egy méretezési csoport példányait tartalmazó nézetet, a méretezési csoportban lévő összes virtuális Géphez van saját példányait tartalmazó nézetet. Az egy méretezési csoport példányait tartalmazó nézetet lekérdezéséhez használhatja:

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/instanceView?api-version={apiVersion}` (további információkért lásd: a [REST API-dokumentáció](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/getinstanceview))

PowerShell: `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -InstanceView` (további információkért lásd: a [Powershell dokumentációs](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm))

Parancssori felület: `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` (további információkért lásd: a [CLI dokumentáció](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view))

Is [resources.azure.com](https://resources.azure.com) vagy a [Azure SDK-k](https://azure.microsoft.com/downloads/) lekérdezni a méretezési csoportban lévő virtuális gép példányait tartalmazó nézetet.

A kimeneti pontos bemutatása a beállításokat, majd a parancs való függ, de itt van néhány minta kimenet a parancssori:

```
$ az vmss get-instance-view -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}
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
  .
  .
  .
}
```

Ahogy látja, akkor ezeket a tulajdonságokat a virtuális gépért, beleértve a méretezési (nincs megadva kivonatosan mutatja) alkalmazott kiterjesztések aktuális futási állapotának ismertetik.




## <a name="how-to-update-global-scale-set-properties"></a>Globális méretű frissítése tulajdonságainak beállítása

A globális méretű tulajdonságbeállító frissítéséhez frissítenie kell a méretezési készlet modell tulajdonság. A frissítés keresztül teheti meg:

REST API: `PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` (további információkért lásd: a [REST API-dokumentáció](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate))

Resource Manager-sablonok: Másik megoldásként központilag telepíthető a Resource Manager-sablon tulajdonságait a REST API használatával globális méretű készlet tulajdonságainak frissítése.

PowerShell: `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -VirtualMachineScaleSet {scaleSetConfigPowershellObject}` (további információkért lásd: a [Powershell dokumentációs](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss))

CLI. Ha egy tulajdonság módosítására: `az vmss update --set {propertyPath}={value}`. Az objektum hozzáadása egy lista tulajdonság méretezési csoportban lévő: `az vmss update --add {propertyPath} {JSONObjectToAdd}`. Az objektum eltávolítása egy lista tulajdonság méretezési csoportban lévő: `az vmss update --remove {propertyPath} {indexToRemove}`. (további információkért lásd: a [CLI dokumentáció](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update)). Azt is megteheti Ha korábban telepítette a méretezési készletben, használja a `az vmss create` parancsot futtathatja a `az vmss create` újra a parancsot a skála frissítéséhez. Ehhez az szükséges, gondoskodnia kell arról, hogy az összes tulajdonság a `az vmss create` parancs ugyanazok, mint korábban, kivéve a módosítani kívánt tulajdonságokat.



Is [resources.azure.com](https://resources.azure.com) vagy a [Azure SDK-k](https://azure.microsoft.com/downloads/) frissíteni a skála beállítása a modell.

A skála modellel frissül, ha az új konfiguráció bármely új virtuális gépek létrehozása a méretezési csoportban lévő vonatkozik. Azonban a meglévő virtuális gépek, a méretezési csoportban lévő modellek kell továbbra is vonhatók naprakész, és a legújabb összesített méretezési modellel. A modell az egyes virtuális gépek logikai tulajdonság neve `latestModelApplied` , amely azt jelzi, hogy-e a virtuális Gépet a legújabb összesített méretezési modellel naprakészen (`true` azt jelenti, hogy a virtuális Gépet a legújabb modellt naprakész).




## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Virtuális gépek naprakész, amely a legfrissebb állapotba állítsa be a modell

Méretezési csoportok rendelkezik egy "frissítési házirend", amely határozza meg, hogyan kerülnek naprakész a legújabb méretezési modellel, és a virtuális gépek. A házirend három módot a következők:

- Automatikus: Ebben a módban a méretezési lehetővé teszi virtuális gépek indítottak sorrendjével kapcsolatos nem garantálja. A méretezési előfordulhat, hogy minden virtuális gép le egyszerre. 
- Működés közbeni: Ebben a módban a méretezési bevezeti a kötegek egy választható szüneteltetése, amikor a frissítés kötegek között.
- Kézi: Ebben a módban a skála modellel, frissítésekor semmi nem történik a meglévő virtuális gépekhez. Meglévő virtuális gépek frissítéséhez tegye minden meglévő virtuális gép "Kézi frissítés". A kézi frissítés keresztül teheti meg:

REST API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/manualupgrade?api-version={apiVersion}` (további információkért lásd: a [REST API-dokumentáció](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/updateinstances))

PowerShell: `Update-AzureRmVmssInstance -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` (további információkért lásd: a [Powershell dokumentációs](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance))

Parancssori felület: `az vmss update-instances -g {resourceGroupName} -n {vmScaleSetName} --instance-ids {instanceIds}` (további információkért lásd: a [CLI dokumentáció](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update_instances)).

Használhatja a [Azure SDK-k](https://azure.microsoft.com/downloads/) ehhez a méretezési csoportban lévő virtuális gép kézi frissítés.

>[!NOTE]
> Service Fabric-fürtök automatikus mód csak használható, de a frissítés eltérően kell kezelni. A service fabric-frissítések további információkért lásd: [a Service Fabric-dokumentáció](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade).

>[!NOTE]
> Nincs a globális méretű tulajdonságainak beállítása, amely nem követi a házirend módosítása egy adott típusú. Ezek a módosítások a skála állítsa operációsrendszer-profilt (például rendszergazda felhasználónevét és jelszavát). Ezek a tulajdonságok csak az API-verzió 2017-12-01 vagy későbbi lehet. A módosítások csak a virtuális gépek létrehozása után a változás a skála modell vonatkoznak. Ahhoz, hogy a meglévő virtuális gépek naprakész, hajtsa végre a "lemezkép alaphelyzetbe" minden meglévő virtuális gép. A lemezkép-visszaállítási keresztül teheti meg:

REST API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (további információkért lásd: a [REST API-dokumentáció](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage))

PowerShell: `Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (további információkért lásd: a [Powershell dokumentációs](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm))

Parancssori felület: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (további információkért lásd: a [CLI dokumentáció](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage)).

Használhatja a [Azure SDK-k](https://azure.microsoft.com/downloads/) egy Virtuálisgép-méretezési csoportban lévő lemezképet.




## <a name="properties-with-restrictions-on-modification"></a>Tulajdonság módosítása korlátozásai

### <a name="create-time-properties"></a>Hozzon létre idő tulajdonságai

Néhány tulajdonság csak akkor állítható be, a méretezési létrehozása beállítása közben. Ezek a tulajdonságok a következők:

- Zónák
- lemezkép referencia publisher
- lemezkép referencia ajánlat

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>A jelenlegi érték alapján, amely csak akkor módosítható, tulajdonságok

Néhány tulajdonságát lehet módosítani, és kivételeket megadni, attól függően, hogy a jelenlegi érték. Ezek a tulajdonságok a következők:

- singlePlacementGroup: singlePlacementGroup értéke igaz, ha azt hamis értékre módosítható. Azonban, ha hamis, singlePlacementGroup azt **nem** módosítható igaz értékre.
- alhálózati: az alhálózat egy méretezési módosíthatja, ha az eredeti alhálózat és az új alhálózat ugyanabban a virtuális hálózatban.

### <a name="properties-that-require-deallocation-to-change"></a>Felszabadítás módosításához szükséges tulajdonságokat

Néhány tulajdonság csak akkor bizonyos értékekre módosítható, ha a virtuális gépeket, a méretezési csoportban lévő fel van szabadítva. Ezek a tulajdonságok a következők:

- Termékváltozat: Ha az új virtuális gép SKU nem támogatott a skála hardveren beállítása jelenleg, akkor kell a méretezési készletben a termékváltozat módosítása előtt olvassa el a virtuális gépek felszabadítani. A virtuális gépek átméretezésével további információkért lásd: [Azure ebben a blogbejegyzésben](https://azure.microsoft.com/blog/resize-virtual-machines/).


## <a name="vm-specific-updates"></a>VM-specifikus frissítések

Egyes módosítások alkalmazhatók az adott virtuális gépen a globális méretű tulajdonságainak beállítása helyett. Jelenleg az egyetlen támogatott Virtuálisgép-specifikus frissítés van csatolása/leválasztása adatlemezek/virtuális gépek a méretezési csoportban lévő. A funkció jelenleg előzetes verzió. További információkért lásd: a [dokumentáció előzetes](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).

## <a name="scenarios-application-updates-os-updates-etc"></a>Forgatókönyv: Alkalmazás frissítések, operációs rendszer frissítése, stb.

### <a name="application-updates"></a>Alkalmazás frissítései

Ha egy alkalmazás központi telepítése egy méretezési extensions segítségével állíthatók be, a bővítmény-konfigurációjának frissítése hatására az alkalmazás megfelel-e a házirend frissítése. Ha egy parancsfájl futtatását egy egyéni parancsprogramok futtatására szolgáló bővítmény új verzióját, például tudta, hogy az új parancsfájl mutasson a fileUris tulajdonság frissíteni. Néhány esetben azonban Kezdésként érdemes lehet kényszeríteni a frissítést, annak ellenére, hogy a bővítmény konfigurációja nem változott (például frissítése a parancsfájl URI-azonosítója a parancsfájl módosítása nélkül). Ebben az esetben a frissítés kényszerítése forceUpdateTag módosíthatja. Az Azure platformon értelmezi ezt a tulajdonságot, ezért az érték módosítása nem befolyásolja a bővítmény működésével. Egyszerűen módosításához futtassa újra a bővítmény kényszeríti. A forceUpdateTag további információkért tekintse meg a [bővítmények REST API dokumentációja](https://docs.microsoft.com/rest/api/compute/virtualmachineextensions/createorupdate).

Azt is alkalmazások esetében gyakori egyéni lemezkép keresztül telepíteni. Ebben a forgatókönyvben az alábbi "Az operációs rendszer frissítések" szakasz kapcsolatban lásd:

### <a name="os-updates"></a>Operációs rendszer frissítése érdekében

Ha platform lemezképet használ, a kép frissítheti az imageReference módosításával (a további információk a [REST API-dokumentáció](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> Platform képekkel esetében gyakori, a lemezkép referencia verziója "legújabb" megadása. Ez azt jelenti, hogy során méretezési készlet létrehozása kibővítési és lemezkép alaphelyzetbe, a virtuális gépek jönnek létre, az elérhető legújabb verzióra. Azonban az **nem** jelenti azt, hogy az operációsrendszer-lemezképek automatikusan frissíti az adott idő alatt kiadott új lemezkép-verziók. Ez az külön szolgáltatása, jelenleg előzetes verzió. További információkért lásd: a [automatikus operációsrendszer-frissítések dokumentáció](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade).

Ha egyéni rendszerképet használ, a kép frissítheti az imageReference azonosító frissítésével (a további információk a [REST API-dokumentáció](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate)).

## <a name="examples"></a>Példák

### <a name="updating-the-os-image-for-your-scale-set"></a>Az operációsrendszer-lemezképek, a méretezési készlet frissítése

Tegyük fel, a méretezési készletben, az Ubuntu LTS 16.04 régebbi verzióját futtató rendelkezik, és frissíti a Ubuntu LTS 16.04 (például a 16.04.201801090 verzió) egy újabb verziója. A lemezkép referencia version tulajdonság nem része a listáját, és így közvetlenül módosíthatja ezeket a tulajdonságokat, a következő parancsokkal:

PowerShell: `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -ImageReferenceVersion 16.04.201801090`

CLI: `az vmss update -g {resourceGroupName} -n {vmScaleSetName} --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090`


### <a name="updating-the-load-balancer-for-your-scale-set"></a>A terheléselosztó a méretezési készlet frissítése

Tegyük fel, a méretezési Azure terheléselosztással készletben rendelkezik, és cserélje le az Azure Load Balancer egy Azure Application Gateway szeretné. A betöltési terheléselosztó és az alkalmazás átjáró tulajdonságait egy méretezési a listáját, és részét képezik, eltávolítása és a lista elemekkel helyett a tulajdonságok közvetlenül módosítsák a parancsokat használhatja:

PowerShell: 
```
# get the current model of the scale set and store it in a local powershell object named $vmss
> $vmss=Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName}

# create a local powershell object for the new desired IP configuration, which includes the referencerence to the application gateway
> $ipconf = New-AzureRmVmssIPConfig myNic -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name

# replace the existing IP configuration in the local powershell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
> $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf

# Update the model of the scale set with the new configuration in the local powershell object
> Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName} -virtualMachineScaleSet $vmss

```

CLI:
```
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0 # remove the load balancer backend pool from the scale set model
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0 # remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}' # add the application gateway backend pool to the scale set model
```

>[!NOTE]
> A parancsok használata nincs csak egy IP konfigurációs és a terheléselosztó a méretezési készlet. Ha több, szükség lehet egy lista index eltérő 0 használatát.