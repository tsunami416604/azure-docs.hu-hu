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
ms.openlocfilehash: fcca912a8120a51d2f0a454ef0a6341cd5882015
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="modify-a-virtual-machine-scale-set"></a>Módosítsa a virtuálisgép-méretezési csoport
A cikkből megtudhatja, hogyan lehet módosítani egy meglévő virtuálisgép-méretezési készlet. A feladatok közé tartozik a skála konfigurációjának módosítása beállítása, a skála futó alkalmazások a konfiguráció módosítása beállításához rendelkezésre állását, és több kezelése.

## <a name="fundamental-concepts"></a>alapvető fogalmai

### <a name="scale-set-model"></a>Virtuális gépek méretezési csoportjának modell

A méretezési csoportban van olyan modellt, amely rögzíti a *kívánt* a skála állapotát állítja be a teljes. A modell skálázási készletének lekérdezéséhez használhatja:

* REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` 
   
  További információkért lásd: a [REST API-dokumentáció](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/get).

* PowerShell:

  `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}`
   
  További információkért lásd: a [PowerShell dokumentációs](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss).

* Azure CLI: 

  `az vmss show -g {resourceGroupName} -n {vmSaleSetName}` 
   
  További információkért lásd: a [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show).

Is [Azure erőforrás-kezelővel (előzetes verzió)](https://resources.azure.com) vagy a [Azure SDK-k](https://azure.microsoft.com/downloads/) skálázási készletének modell lekérdezni.

A kimeneti pontos bemutató attól függ, hogy a beállításokat, majd a parancs való. Itt egy minta kimenet az Azure parancssori felület:

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



### <a name="scale-set-instance-view"></a>Méretezési csoport példányait tartalmazó nézet

A méretezési készletben is rendelkezik egy példányait tartalmazó nézetet, amely rögzíti az aktuális *futásidejű* a skála állapotát állítja be a teljes. Az egy méretezési csoport példányait tartalmazó nézetet lekérdezéséhez használhatja:

* REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version={apiVersion}` 
   
  További információkért lásd: a [REST API-dokumentáció](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview).

* PowerShell: 

  `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceView` 
  
  További információkért lásd: a [PowerShell dokumentációs](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss).

* Azure CLI: 

  `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName}` 
   
  További információkért lásd: a [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view).

Is [Azure erőforrás-kezelővel (előzetes verzió)](https://resources.azure.com) vagy a [Azure SDK-k](https://azure.microsoft.com/downloads/) lekérdezni a egy méretezési csoport példányait tartalmazó nézetet.

A kimeneti pontos bemutató attól függ, hogy a beállításokat, majd a parancs való. Itt egy minta kimenet az Azure parancssori felület:

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

Ahogy látja, ezeket a tulajdonságokat meg az aktuális futási állapotát a méretezési csoportban lévő virtuális gépek. Az összegzés tartalmazza a méretezési készletben (elhagyott kivonatosan mutatja) alkalmazott bővítmények állapotát.



### <a name="scale-set-vm-model-view"></a>Méretezési VM modell megtekintése

Hasonló hogyan egy méretezési csoport rendelkezik a modell nézet, a méretezési csoportban lévő összes virtuális Géphez van saját modell nézet. A modell nézetét a méretezési lekérdezéséhez használhatja:

* REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}?api-version={apiVersion}` 
  
  További információkért lásd: a [REST API-dokumentáció](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/get).

* PowerShell: 

  `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` 
  
  További információkért lásd: a [PowerShell dokumentációs](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm).

* Azure CLI: 

  `az vmss show -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` 
  
  További információkért lásd: a [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show).

Is [Azure erőforrás-kezelővel (előzetes verzió)](https://resources.azure.com) vagy a [Azure SDK-k](https://azure.microsoft.com/downloads/) a modell egy Virtuálisgép-méretezési csoportban lévő lekérdezésére.

A kimeneti pontos bemutató attól függ, hogy a beállításokat, majd a parancs való. Itt egy minta kimenet az Azure parancssori felület:

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

Ahogy látja, ezek a Tulajdonságok konfigurálását a virtuális gép, nem a méretezési készletben egész konfigurációját. Például a skála modellel rendelkezik `overprovision` tulajdonságként, mivel a virtuális gépek egy méretezési modell viszont nem. Ezt a különbséget oka, hogy a elhelyezésétől-e a skála állítja be a teljes, nem az egyes virtuális gépek a méretezési csoportban lévő egy tulajdonság. (Elhelyezésétől kapcsolatos további információkért lásd: [kialakítási szempontok a méretezési készlet](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning).)



### <a name="scale-set-vm-instance-view"></a>Méretezési csoportban a virtuális gép példányait tartalmazó nézet

Hasonló hogyan rendelkezik, egy méretezési csoport példányait tartalmazó nézetet, a méretezési csoportban lévő összes virtuális Géphez van saját példányait tartalmazó nézetet. Az egy méretezési csoport példányait tartalmazó nézetet lekérdezéséhez használhatja:

* REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/instanceView?api-version={apiVersion}` 
 
  További információkért lásd: a [REST API-dokumentáció](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/getinstanceview).

* PowerShell: 

  `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -InstanceView` 
  
  További információkért lásd: a [PowerShell dokumentációs](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm).

* Azure CLI: 

  `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` 
  
  További információkért lásd: a [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view).

Is [Azure erőforrás-kezelővel (előzetes verzió)](https://resources.azure.com) vagy a [Azure SDK-k](https://azure.microsoft.com/downloads/) lekérdezni a méretezési csoportban lévő virtuális gép példányait tartalmazó nézetet.

A kimeneti pontos bemutató attól függ, hogy a beállításokat, majd a parancs való. Itt egy minta kimenet az Azure parancssori felület:

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

Ahogy látja, akkor ezeket a tulajdonságokat a virtuális gépért aktuális futási állapotának ismertetik. Az állapot-bővítmény sem. a méretezési készletben (elhagyott kivonatosan mutatja) alkalmazott tartalmazza.




## <a name="techniques-for-updating-global-scale-set-properties"></a>Globális méretű frissítési módszerek tulajdonságainak beállítása

A globális méretű tulajdonságbeállító frissítéséhez frissítenie kell a méretezési készlet modell tulajdonság. A frissítés keresztül teheti meg:

* REST API: 

  `PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` 
  
  További információkért lásd: a [REST API-dokumentáció](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

  Másik lehetőségként telepítése Azure Resource Manager-sablonok globális méretű készlet tulajdonságainak módosítása a tulajdonságok a REST API használatával.

* PowerShell: 

  `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -VirtualMachineScaleSet {scaleSetConfigPowershellObject}` 
  
  További információkért lásd: a [PowerShell dokumentációs](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss).

* Azure CLI:

  * Ha egy tulajdonság módosítására: `az vmss update --set {propertyPath}={value}` 
  
  * Méretezési csoportban lévő lista tulajdonság az objektum hozzáadása: `az vmss update --add {propertyPath} {JSONObjectToAdd}` 
  
  * Méretezési csoportban lévő lista tulajdonság az objektum eltávolítása: `az vmss update --remove {propertyPath} {indexToRemove}` 
  
  További információkért lásd: a [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update). 
  
  Azt is megteheti Ha korábban telepítette a méretezési készletben használatával a `az vmss create` parancsot futtathatja a `az vmss create` újra a parancsot a skála frissítéséhez. Ehhez az szükséges, győződjön meg arról, hogy az összes tulajdonság a `az vmss create` parancs ugyanazok, mint korábban, kivéve a módosítani kívánt tulajdonságokat.



Is [Azure erőforrás-kezelővel (előzetes verzió)](https://resources.azure.com) vagy a [Azure SDK-k](https://azure.microsoft.com/downloads/) frissíteni a skála beállítása a modell.

Miután a skála modellel frissült, az új konfiguráció bármely új virtuális gépek létrehozása a méretezési csoportban lévő vonatkozik. Azonban a meglévő virtuális gépek, a méretezési csoportban lévő modellek kell továbbra is vonhatók naprakészek legyenek a legújabb összesített méretezési készlet modellt. A modell az egyes virtuális gépek, logikai tulajdonsággal nevű `latestModelApplied` azt jelzi, hogy-e a virtuális gép naprakészen a legújabb összesített méretezési modellel. (A érték `true` azt jelenti, hogy a virtuális Gépet a legújabb modellt naprakész.)




## <a name="techniques-for-bringing-vms-up-to-date-with-the-latest-scale-set-model"></a>Annak érdekében, hogy a virtuális gépek naprakészen a legújabb méretezési modellel technikák

Méretezési csoportok rendelkezik egy *házirend frissítése* , amely meghatározza, hogy hogyan virtuális gépek naprakészen a legújabb méretezési modellel kerülnek. A házirend három módot a következők:

- **Automatikus**: Ebben a módban a méretezési lehetővé teszi virtuális gépek, amelyek le kerülnek sorrendjével kapcsolatos nem garantálja. A méretezési előfordulhat, hogy minden virtuális gép le egy időben. 
- **Működés közbeni**: Ebben a módban a méretezési bevezeti a kötegek kötegek között egy választható szüneteltetése, amikor a frissítés.
- **Manuális**: Ebben a módban, amikor frissíti a skála modellel, semmi nem történik a meglévő virtuális gépekhez. Meglévő virtuális gépek frissítése, kézzel kell frissítenie a minden egyes. A kézi frissítés keresztül teheti meg:

  - REST API: 
  
    `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/manualupgrade?api-version={apiVersion}` 
    
    További információkért lásd: a [REST API-dokumentáció](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/updateinstances).

  - PowerShell: 
  
    `Update-AzureRmVmssInstance -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` 
    
    További információkért lásd: a [PowerShell dokumentációs](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance).

  - Azure CLI: 
  
    `az vmss update-instances -g {resourceGroupName} -n {vmScaleSetName} --instance-ids {instanceIds}` 
    
    További információkért lásd: a [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update_instances).

  Használhatja a [Azure SDK-k](https://azure.microsoft.com/downloads/) manuális frissítése a Virtuálisgép-méretezési csoportban lévő.

>[!NOTE]
> Az Azure Service Fabric-fürtök csak automatikus módot használhat, de a frissítés eltérően kell kezelni. A Service Fabric-frissítések további információkért lásd: a [Service Fabric-dokumentáció](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade).

Egy adott típusú globális méretű készlet tulajdonságainak módosítása nem követi a házirend: a skála módosításai operációsrendszer-profil beállítása. (Többek között az rendszergazda felhasználónevét és jelszavát.) Ezek a tulajdonságok csak az API-verzió 2017-12-01 vagy későbbi lehet. A módosítások csak virtuális gépek létrehozása után a változás a skála modell vonatkoznak. Ahhoz, hogy a meglévő virtuális gépek naprakész, meg kell újból lemezképet létrehozni meglévő virtuális gépek. Ön újból lemezképet létrehozni egy virtuális gépről:

* REST API: 

  `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` 
  
  További információkért lásd: a [REST API-dokumentáció](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage).

* PowerShell: 

  `Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` 
  
  További információkért lásd: a [PowerShell dokumentációs](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm).

* Azure CLI: 

  `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` 
  
  További információkért lásd: a [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage).

Használhatja a [Azure SDK-k](https://azure.microsoft.com/downloads/) egy Virtuálisgép-méretezési csoportban lévő lemezképet.




## <a name="properties-with-restrictions-on-modification"></a>Tulajdonság módosítása korlátozásai

### <a name="create-time-properties"></a>Hozzon létre idő tulajdonságai

Néhány tulajdonság csak akkor, amikor először hoz létre, a méretezési állítható be. Ezek a tulajdonságok a következők:

- Zóna
- lemezkép referencia publisher
- lemezkép referencia ajánlat

### <a name="properties-that-can-be-changed-based-on-the-current-value-only"></a>Csak az aktuális érték alapján, amelyek módosíthatók tulajdonságai

Néhány tulajdonság módosítható, és kivételeket megadni, attól függően, hogy a jelenlegi érték. Ezek a tulajdonságok a következők:

- `singlePlacementGroup`: Ha `singlePlacementGroup` értéke igaz, hamis értékre módosítani. Azonban ha `singlePlacementGroup` értéke HAMIS, akkor *nem* módosítható igaz értékre.
- `subnet`: Az alhálózat egy méretezési módosítható, ha az eredeti alhálózat és az új alhálózat ugyanabban a virtuális hálózatban.

### <a name="properties-that-require-deallocation-to-change"></a>Felszabadítás módosításához szükséges tulajdonságokat

Néhány tulajdonság módosítható egyes értékek csak akkor, ha a virtuális gépeket, a méretezési csoportban lévő fel van szabadítva. Ezek a tulajdonságok a következők:

- `sku name`: Ha az új virtuális gép Termékváltozat nem támogatja a hardvert, amely a méretezési csoportban, szeretné-e a skála módosítása előtt állítsa be a virtuális gépek felszabadítani `sku name`. A virtuális gépek átméretezésével további információkért lásd: [Azure ebben a blogbejegyzésben](https://azure.microsoft.com/blog/resize-virtual-machines/).


## <a name="vm-specific-updates"></a>VM-specifikus frissítések

Egyes módosítások alkalmazhatók az adott virtuális gépen a globális méretű tulajdonságainak beállítása helyett. Jelenleg az egyetlen támogatott Virtuálisgép-specifikus frissítés van csatolása/leválasztása adatlemezek/virtuális gépek a méretezési csoportban lévő. A funkció jelenleg előzetes verzió. További információkért lásd: a [dokumentáció előzetes](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).

## <a name="scenarios"></a>Forgatókönyvek

### <a name="application-updates"></a>Alkalmazás frissítései

Ha egy alkalmazás központi telepítése egy méretezési extensions segítségével állíthatók be, a bővítmény-konfigurációjának frissítése hatására az alkalmazás megfelel a házirend-e frissíteni kell. Például ha egy parancsfájl futtatását egy egyéni parancsprogramok futtatására szolgáló bővítmény új verzióját, előfordulhat, hogy frissíti a `fileUris` tulajdonság úgy, hogy az új parancsfájl mutasson. 

Bizonyos esetekben előfordulhat, hogy kényszeríteni kívánja frissítés annak ellenére, hogy a bővítmény konfigurációja nem változott. (Például frissítése a parancsfájl URI-azonosítója a parancsfájl módosítása nélkül.) Ezekben az esetekben módosíthatja `forceUpdateTag` frissítés kényszerítéséhez. Az Azure platformon értelmezi ezt a tulajdonságot, ezért az érték módosítása nem befolyásolja a bővítmény működésével. Egyszerűen módosításához futtassa újra a bővítmény kényszeríti. 

További információ a `forceUpdateTag`, tekintse meg a [bővítmények REST API dokumentációja](https://docs.microsoft.com/rest/api/compute/virtualmachineextensions/createorupdate).

Azt is alkalmazások esetében gyakori egyéni lemezkép keresztül telepíteni. Ebben a forgatókönyvben a következő szakasz foglalkozik.

### <a name="os-updates"></a>Operációs rendszer frissítése érdekében

Platform lemezképek használata, frissítheti úgy a lemezképeket módosításával `imageReference`. További információkért lásd: a [REST API-dokumentáció](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate).

>[!NOTE]
> Platform képekkel esetében gyakori, a lemezkép referencia verziója "legújabb" megadása. Ez azt jelenti, hogy méretezési csoportok létrehozásakor ki méretezett, valamint azon, a virtuális gépeket hoz létre az elérhető legújabb verzióra. Azonban az *nem* jelenti azt, hogy az operációsrendszer-lemezképek automatikusan frissíti az adott idő alatt kiadott új lemezkép-verziók. Ez az külön szolgáltatása, jelenleg előzetes verzió. További információkért lásd: [frissíti az operációs rendszer automatikus](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade).

Egyéni lemezképek használata, a lemezképek frissítéséhez frissítheti a `imageReference` azonosítóját. További információkért lásd: a [REST API-dokumentáció](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="examples"></a>Példák

### <a name="update-the-os-image-for-your-scale-set"></a>Az operációs rendszer lemezképét a méretezési készlet

Tegyük fel, hogy rendelkezik-e a méretezési készletben, az Ubuntu LTS 16.04 régebbi verzióját futtató. Ubuntu LTS 16.04 (például a 16.04.201801090 verzió) egy újabb verziójára frissíteni kívánt. A lemezkép referencia version tulajdonság nem része a listáját, és így ezek a parancsok segítségével közvetlenül módosíthatja ezeket a tulajdonságokat:

* PowerShell: 

  `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -ImageReferenceVersion 16.04.201801090`

* Azure CLI: 

  `az vmss update -g {resourceGroupName} -n {vmScaleSetName} --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090`


### <a name="update-the-load-balancer-for-your-scale-set"></a>A terheléselosztó a méretezési készlet frissítése

Tegyük fel, a méretezési Azure terheléselosztással készletben rendelkezik, és cserélje le a terheléselosztó olyan Azure átjárót szeretne. A betöltési terheléselosztó és az alkalmazás átjáró tulajdonságait egy méretezési listáját a részét képezik. Igen a parancsokat használhatja eltávolítása és a lista elemekkel helyett közvetlenül tulajdonságainak módosításával.

PowerShell:
```
# Get the current model of the scale set and store it in a local PowerShell object named $vmss
> $vmss=Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName}

# Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
> $ipconf = New-AzureRmVmssIPConfig myNic -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name

# Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure load balancer) with the new IP configuration
> $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf

# Update the model of the scale set with the new configuration in the local PowerShell object
> Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName} -virtualMachineScaleSet $vmss

```

Azure CLI:
```
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0 # Remove the load balancer back-end pool from the scale set model
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0 # Remove the load balancer back-end pool from the scale set model; only necessary if you have NAT pools configured on the scale set
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}' # Add the application gateway back-end pool to the scale set model
```

>[!NOTE]
> A parancsok használata nincs csak egy IP konfigurációs és a terheléselosztó a méretezési készlet. Ha több, szükség lehet egy lista index eltérő 0 használatát.