---
title: A Példányazonosítók ismertetése az Azure-beli Virtuálisgép-méretezési csoport virtuális gépek |} A Microsoft Docs
description: Az Azure Virtuálisgép-méretezési csoport a Példányazonosítók állítsa be a virtuális gépek ismertetése
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: manayar
ms.openlocfilehash: 6aeba722a0661979664f8d61efdb9b2bf47ad801
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981655"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Az Azure Virtuálisgép-méretezési csoport a Példányazonosítók állítsa be a virtuális gépek ismertetése
Ez a cikk ismerteti a példányok azonosítóit a méretezési csoportok és a surface, a különböző módszereket.

## <a name="scale-set-instance-ids"></a>Méretezési csoport Példányazonosítók

Minden egyes méretezési csoportban lévő Virtuálisgép-példány azonosítója, amely egyedileg azonosítja beolvasása. Ez a példány azonosítója segítségével a méretezési API-k az adott műveleteket a méretezési csoportban lévő virtuális gép beállítása. Megadhat például egy adott példány azonosítója a rendszerkép alaphelyzetbe állítását a rendszerkép alaphelyzetbe állítását API használata esetén:

REST API-val: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (további információkért lásd: a [REST API-dokumentáció](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage))

PowerShell: `Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (további információkért lásd: a [Powershell-dokumentáció](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm))

Parancssori felület: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (további információkért lásd: a [CLI dokumentációját](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)).

Példány azonosítók listáját megtekintheti a méretezési csoportban lévő összes példány listázásával:

REST API-val: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (további információkért lásd: a [REST API-dokumentáció](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list))

PowerShell: `Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (további információkért lásd: a [Powershell-dokumentáció](https://docs.microsoft.com/powershell/module/az.compute/get-azvmssvm))

Parancssori felület: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (további információkért lásd: a [CLI dokumentációját](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)).

Is [resources.azure.com](https://resources.azure.com) vagy a [Azure SDK-k](https://azure.microsoft.com/downloads/) a méretezési csoportban lévő virtuális gépek listáját.

A kimenet a pontos bemutató függ, a parancshoz adja meg a beállításokat, de Íme néhány a parancssori felület mintakimenete:

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

Amint láthatja, a "instanceId" tulajdonság csak egy tizedes tört szám. A példány azonosítók előfordulhat, hogy használja fel újra az új példányok után a régi példányok törlődnek.

>[!NOTE]
> Nincs **nincs garancia** módon-példányon a méretezési csoportban lévő virtuális gépek azonosítót kap. Előfordulhat, hogy úgy tűnik, növekvő sorrendben esetenként, de ez nem mindig a helyzet. Függőség nem lépnek, amelyben a Példányazonosítók a virtuális gépekhez rendelt adott módjáról.

## <a name="scale-set-vm-names"></a>Méretezési csoport egy virtuális gép neve

A fenti példa kimenetben is van egy "name" a virtuális gép számára. Ez a név formájában adható meg "{méretezési-neve} _ {példányazonosító}". Ez a név azt láthatja, amelyeket az Azure Portalon a méretezési csoportban lévő példányok listázásakor:

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

A név {példányazonosító} része decimális száma azonos a korábban tárgyalt "instanceId" tulajdonság.

## <a name="instance-metadata-vm-name"></a>Példány-metaadat virtuális gép neve

Ha lekérdezheti, ha a [példány-metaadat](../virtual-machines/windows/instance-metadata-service.md) a egy méretezési csoportot virtuális Gépen belül, lásd: "name" a kimenetben:

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

## <a name="scale-set-vm-computer-name"></a>Méretezési csoport virtuális gép számítógép nevét

Minden virtuális gép méretezési csoportban lévő is beolvassa a hozzárendelt számítógépnevet. Ennél a számítógépnévnél a virtuális gép állomásnevét a [Azure által biztosított DNS-névfeloldás a virtuális hálózaton belül](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). A számítógép neve: "{computer-name-prefix}{base-36-instance-id}" formátumban.

A {base-36-példány-azonosítója} [36 kiinduló](https://en.wikipedia.org/wiki/Base36) , és mindig hat számjegy hosszúságú. Ha alapszintű 36 leképezése az a szám kevesebb mint 6 számjegyű, az {base-36-példány-id} nullákat helyez el, hogy hosszabb 6 számjegyű számokhoz. {A(z) számítógép-neve-prefix} "nsgvmss" rendelkező példány és a példány azonosítója 85 például lesz az számítógép neve "nsgvmss00002D".

>[!NOTE]
> A számítógépnév előtagja egy tulajdonságot a méretezési csoport modelljéből beállíthatja, hogy a helyzet a méretezési csoport név eltérő lehet.
