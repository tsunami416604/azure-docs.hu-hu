---
title: Az Azure virtuálisgép-méretezési csoport virtuális gépei példányazonosítóinak ismertetése
description: Az Azure vm-méretezési példányazonosítók a virtuális gépek és a különböző módon, hogy azok felszínre.
author: mayanknayar
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: manayar
ms.openlocfilehash: 99ad4249a4134bcc1b1cf5aba92b8a95a034db33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534422"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Az Azure virtuálisgép-méretezési csoport virtuális gépei példányazonosítóinak ismertetése
Ez a cikk a méretezési csoportok példányazonosítóit és a felületük különböző módjait ismerteti.

## <a name="scale-set-instance-ids"></a>Méretezési példányazonosítók

A méretezési csoport ban minden virtuális gép kap egy példányazonosítót, amely egyedileg azonosítja azt. Ez a példányazonosító a méretezési csoport API-k műveletek et a méretezési csoportban egy adott virtuális gép műveleteket. Például megadhat egy adott példányazonosítót az újraképzéshez az újraképző API használatakor:

REST API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (További információ: A [REST API dokumentációja)](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage)

Powershell: `Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (további információt a [Powershell dokumentációjában](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm)talál)

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (További információkért lásd a [CLI dokumentációját](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)).

A példányazonosítók listáját a méretezési csoport összes példányának listázásával szerezheti be:

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (További információ: A [REST API dokumentációja)](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list)

Powershell: `Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (további információt a [Powershell dokumentációjában](https://docs.microsoft.com/powershell/module/az.compute/get-azvmssvm)talál)

CLI: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (További információkért lásd a [CLI dokumentációját](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)).

Resources.azure.com [vagy](https://resources.azure.com) az [Azure SDK-k](https://azure.microsoft.com/downloads/) is használhatja a virtuális gépek méretezési készletben való listázásához.

A kimenet pontos megjelenítése a parancsnak megadott beállításoktól függ, de itt van néhány mintakimenet a CLI-ből:

```azurecli
az vmss show -g {resourceGroupName} -n {vmScaleSetName}
```

```output
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

Mint látható, a "instanceId" tulajdonság csak egy tizedesszám. A példányazonosítók a régi példányok törlése után újra felhasználhatók az új példányokhoz.

>[!NOTE]
> Nincs **garancia** arra, ahogy a példányazonosítók vannak rendelve a méretezési csoportban lévő virtuális gépek. Úgy tűnhet, szekvenciálisen növekszik időnként, de ez nem mindig van így. Ne vegyen függőséget attól a konkrét módtól, ahogyan a példányazonosítók a virtuális gépekhez vannak rendelve.

## <a name="scale-set-vm-names"></a>Készletes virtuálisgép-nevek méretezése

A fenti kimeneti kimenetben van egy "név" is a virtuális gépszámára. Ez a név a következő formában jelenik meg: "{scale-set-name}_{instance-id}". Ez a név az, amely az Azure Portalon látható, amikor egy méretezési csoportban sorolja fel a példányokat:

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

A név {instance-id} része megegyezik a korábban tárgyalt "instanceId" tulajdonsággal.

## <a name="instance-metadata-vm-name"></a>Példány metaadat-virtuálisgép-neve

Ha lekérdezi a [példány metaadatait](../virtual-machines/windows/instance-metadata-service.md) egy méretezési csoportvirtuális gépből, a kimenetben megjelenik egy "név":

```output
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Ez a név megegyezik a korábban tárgyalt névvel.

## <a name="scale-set-vm-computer-name"></a>Készletes virtuálisgép-számítógép nevének méretezése

A méretezési csoportban lévő minden virtuális gép egy számítógépnevet is kap hozzá. Ez a számítógépnév a virtuális gép állomásneve az [Azure által biztosított DNS-névfeloldásban a virtuális hálózaton belül.](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) Ez a számítógépnév a következő típusú: "{számítógépnév-előtag}{base-36-instance-id}".

A(z) {base-36-instance-id} a [36-os alapban](https://en.wikipedia.org/wiki/Base36) van, és mindig hat számjegy hosszúságú. Ha a szám 36-os alapábrázolása hat számjegynél kevesebbet vesz igénybe, a(z) {base-36-instance-id} nullákkal van kipárnázva, hogy hat számjegy hosszúságú legyen. Például egy példány {számítógép-név-előtag} "nsgvmss" és példány azonosító 85 lesz számítógépneve "nsgvmss00002D".

>[!NOTE]
> A számítógépnév-előtag a méretezési csoport modelljének egy tulajdonsága, amelyet beállíthat, így eltérhet magától a méretezési csoport nevétől.
