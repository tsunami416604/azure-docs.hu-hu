---
title: "Példány azonosítók megismerése az Azure Virtuálisgép-méretezési készlet virtuális gépek |} Microsoft Docs"
description: "Azure Virtuálisgép-méretezési példány azonosítóinak állítsa be a virtuális gépek ismertetése"
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
ms.date: 02/22/2018
ms.author: negat
ms.openlocfilehash: 3a43dc86f1fb53dfde4bce3938faaa30e18f5a6d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Azure Virtuálisgép-méretezési példány azonosítóinak állítsa be a virtuális gépek ismertetése
Ez a cikk ismerteti a méretezési készlet és a különböző módszereket azok surface példány azonosítóit.

## <a name="scale-set-instance-ids"></a>Méretezési példány azonosítók

Minden egyes Virtuálisgép-méretezési csoportban lévő lekérdezi egy példány azonosítója, amely egyedileg azonosítja. Ez a példány azonosítója egy adott műveletek végrehajtásához használatos a méretezési API-k a skála a Virtuálisgép-csoportban. Megadhat például egy egyedi Példányazonosítót a lemezkép-visszaállítási, a lemezkép-visszaállítási API használata esetén:

REST API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (további információkért lásd: a [REST API-dokumentáció](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage))

PowerShell: `Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (további információkért lásd: a [Powershell dokumentációs](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm))

Parancssori felület: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (további információkért lásd: a [CLI dokumentáció](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage)).

Méretezési csoportban lévő összes példányok listázása kaphat példány azonosítók listáját:

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (további információkért lásd: a [REST API-dokumentáció](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list))

PowerShell: `Get-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (további információkért lásd: a [Powershell dokumentációs](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm))

Parancssori felület: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (további információkért lásd: a [CLI dokumentáció](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_list_instances)).

Is [resources.azure.com](https://resources.azure.com) vagy a [Azure SDK-k](https://azure.microsoft.com/downloads/) a méretezési csoportban lévő virtuális gépek listáját.

A kimeneti pontos bemutatása a beállításokat, majd a parancs való függ, de itt van néhány minta kimenet a parancssori:

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
[
  {
    "instanceId": "85",
    "latestModelApplied": true,
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Ahogy látja, a "instanceId" tulajdonság csak olyan decimális számnak. A példány azonosítók előfordulhat, hogy használja fel újra az új példányok régi példányok törölt.

>[!NOTE]
> Nincs **garancia** módon példányon azonosítót kap a a méretezési csoportban lévő virtuális gépeket. Ezek tűnhet egymás után növekvő néha, de ez nem mindig a helyzet. A meghatározott módon, ahol a virtuális gépek példány azonosítót kap a függőség nem lépnek életbe.

## <a name="scale-set-vm-names"></a>Virtuális gépek méretezési csoportjának virtuális gép neve

A fenti minta kimenet is van egy "name" a virtuális gép számára. Ez a név "{példányazonosító} {méretezési-neve} _" formájában történik. Ez a név, amely akkor látni az Azure portálon méretezési csoportban lévő példányok felsorolja:

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

A {példányazonosító} nevét része a korábban tárgyalt "instanceId" tulajdonságban azonos tizedes tört.

## <a name="instance-metadata-vm-name"></a>Metaadatok virtuális gép neve

Ha lekérdezést hajt végre a [példány metaadatok](../virtual-machines/windows/instance-metadata-service.md) a belül egy méretezési VM, megjelenik egy "name" szöveget a kimenetben:

```
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Ez a név megegyezik a korábban tárgyalt nevét.

## <a name="scale-set-vm-computer-name"></a>Számítógépnév Virtuálisgép-méretezési csoportban

A méretezési készletben is az egyes virtuális gépek lekérdezi a számítógép nevének rendelve. A számítógép neve: a virtuális gép állomásnevét a [Azure által biztosított DNS-névfeloldás a virtuális hálózaton belül](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). A számítógép neve: "{computer-name-prefix}{base-36-instance-id}" formátumban.

A {base-36-példány-azonosító} [36 kiinduló](https://en.wikipedia.org/wiki/Base36) , és mindig hat jegyű. Ha száma 36 alap ábrázolását legfeljebb hat számjegy, a {base-36-példány-azonosító} program nullákat helyez könnyebb hat számjegyek hosszabb van kiegészítve. Például {számítógép-neve-előtag} "nsgvmss" példány és a példány azonosítója 85 kell számítógép neve "nsgvmss00002D".

>[!NOTE]
> A számítógép nevének előtagja érték a méretezési készlet modell, amely lehet, tulajdonság, ezért a méretezési készlet név eltérő lehet.