---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3f910a3d0466153bd60fe23ef2f9f656cac292ee
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70919689"
---
# <a name="using-azure-ultra-disks"></a>Az Azure Ultra Disks használata

Az Azure Ultra Disks nagy teljesítményű, magas IOPS és konzisztens, alacsony késésű lemezes tárolást biztosít az Azure IaaS Virtual Machines (VM) szolgáltatásokhoz. Ez az új ajánlat a vonal teljesítményét a meglévő lemezekkel megegyező rendelkezésre állási szinten biztosítja. Az ultra Disks szolgáltatás egyik fő előnye, hogy dinamikusan megváltoztathatja az SSD teljesítményét a számítási feladatokkal együtt anélkül, hogy újra kellene indítania a virtuális gépeket. Az ultra-lemezek olyan adatigényes számítási feladatokhoz használhatók, mint a SAP HANA, a legfelső szintű adatbázisok és a tranzakció-nagy számítási feladatok.

## <a name="check-if-your-subscription-has-access"></a>Ellenőrizze, hogy az előfizetése rendelkezik-e hozzáféréssel

Ha már regisztrált az ultra-lemezekre, és szeretné megtekinteni, hogy az előfizetése engedélyezve van-e az ultra-lemezekhez, használja az alábbi parancsokat: 

CLI`az feature show --namespace Microsoft.Compute --name UltraSSD`

PowerShell: `Get-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName UltraSSD`

Ha az előfizetés engedélyezve van, a kimenetnek a következőhöz hasonlóan kell kinéznie:

```bash
{
  "id": "/subscriptions/<yoursubID>/providers/Microsoft.Features/providers/Microsoft.Compute/features/UltraSSD",
  "name": "Microsoft.Compute/UltraSSD",
  "properties": {
    "state": "Registered"
  },
  "type": "Microsoft.Features/providers/features"
}
```

## <a name="determine-your-availability-zone"></a>A rendelkezésre állási zóna meghatározása

A jóváhagyást követően el kell döntenie, hogy melyik rendelkezésre állási zónát kívánja használni az ultra-lemezek használatához. Futtassa a következő parancsok egyikét annak meghatározásához, hogy melyik zónában szeretné üzembe helyezni az ultra-lemezt, és először cserélje le a **régiót**, a **vmSize**és az **előfizetési** értékeket:

CLI:

```bash
$subscription = "<yourSubID>"
$region = "<yourLocation>, example value is southeastasia"
$vmSize = "<yourVMSize>, example value is Standard_E64s_v3"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

PowerShell:

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

A válasz az alábbi űrlaphoz hasonló lesz, ahol az X a kiválasztott régióban való üzembe helyezéshez használt zóna. Az X a következők egyike lehet: 1, 2 vagy 3. Jelenleg csak három régió támogatja az ultra-lemezeket, a következők: USA 2. keleti régiója, Délkelet-Ázsia és Észak-Európa.

Őrizze meg a **zónák** értékét, amely a rendelkezésre állási zónát képviseli, és szüksége lesz rá egy ultra-lemez üzembe helyezése érdekében.

|Erőforrástípus  |Name (Név)  |Location  |Zónák  |Korlátozás  |Képesség  |Value  |
|---------|---------|---------|---------|---------|---------|---------|
|lemezek     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Ha nem volt válasz a parancstól, akkor a szolgáltatásra való regisztráció még függőben van, vagy a CLI vagy a PowerShell egy régebbi verzióját használja.

Most, hogy megismerte, hogy melyik zónát kívánja telepíteni, kövesse a jelen cikk központi telepítési lépéseit, és telepítsen egy virtuális gépet egy csatlakoztatott, vagy egy ultra lemez csatlakoztatása egy meglévő virtuális géphez.

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Ultra-lemez üzembe helyezése Azure Resource Manager használatával

Először határozza meg a telepítendő virtuális gép méretét. Egyelőre csak a DsV3 és a EsV3 virtuálisgép-családok támogatják az ultra lemezeket. A virtuális gépek méretével kapcsolatos további részletekért tekintse meg a [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) második táblázatát.

Ha több Ultra lemezzel rendelkező virtuális gépet szeretne létrehozni, tekintse meg a mintát [több Ultra lemezzel rendelkező virtuális gép létrehozása](https://aka.ms/ultradiskArmTemplate)című témakörben.

Ha saját sablont szeretne használni, győződjön meg róla, hogy a **apiVersion** a `Microsoft.Compute/virtualMachines` `2018-06-01` és `Microsoft.Compute/Disks` a (vagy újabb) értékre van beállítva.

Állítsa be a lemez SKU- **UltraSSD_LRS**, majd állítsa be a lemez kapacitása, a IOPS, a rendelkezésre állási zóna és az átviteli sebesség (MB/s) értéket egy ultra-lemez létrehozásához.

A virtuális gép üzembe helyezése után particionálhatja és formázhatja az adatlemezeket, és konfigurálhatja azokat a számítási feladatokhoz.

## <a name="deploy-an-ultra-disk-using-cli"></a>Ultra-lemez üzembe helyezése a parancssori felület használatával

Először határozza meg a telepítendő virtuális gép méretét. Egyelőre csak a DsV3 és a EsV3 virtuálisgép-családok támogatják az ultra lemezeket. A virtuális gépek méretével kapcsolatos további részletekért tekintse meg a [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) második táblázatát.

Ultra-lemez csatlakoztatásához létre kell hoznia egy olyan virtuális gépet, amely képes az ultrakönnyű lemezek használatára.

Cserélje le vagy állítsa be a **$vmname**, **$rgname**, **$diskname**, **$Location**, **$Password**, **$User** változókat a saját értékeivel. Állítsa **$Zone** értéket a [cikk elejétől](#determine-your-availability-zone)kapott rendelkezésre állási zóna értékére. Ezután futtassa az alábbi CLI-parancsot egy ultra-kompatibilis virtuális gép létrehozásához:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-disk-using-cli"></a>Ultra-lemez létrehozása a parancssori felület használatával

Most, hogy rendelkezik egy olyan virtuális géppel, amely képes az ultra-lemezek csatolására, létrehozhat és csatlakoztathat egy ultra-lemezt.

```azurecli-interactive
$location="eastus2"
$subscription="xxx"
$rgname="ultraRG"
$diskname="ssd1"
$vmname="ultravm1"
$zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>Ultra-lemez csatlakoztatása virtuális géphez a CLI használatával

Ha a meglévő virtuális gép egy olyan régióban vagy rendelkezésre állási zónában van, amely képes az ultra-lemezek használatára, az új virtuális gép létrehozása nélkül is használhatja az ultra lemezeket.

```bash
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>Ultra-lemez teljesítményének módosítása a parancssori felület használatával

Az ultra Disks olyan egyedi képességet kínál, amely lehetővé teszi a teljesítmény módosítását, a következő parancs a funkció használatát mutatja be:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>Ultra-lemez üzembe helyezése a PowerShell használatával

Először határozza meg a telepítendő virtuális gép méretét. Egyelőre csak a DsV3 és a EsV3 virtuálisgép-családok támogatják az ultra lemezeket. A virtuális gépek méretével kapcsolatos további részletekért tekintse meg a [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) második táblázatát.

Az ultra-lemezek használatához létre kell hoznia egy virtuális gépet, amely képes az ultra lemezek használatára. Cserélje le vagy állítsa be a **$resourcegroup** és a **$vmName** változót a saját értékeivel. Állítsa **$Zone** értéket a [cikk elejétől](#determine-your-availability-zone)kapott rendelkezésre állási zóna értékére. Ezután futtassa a következő [New-AzVm](/powershell/module/az.compute/new-azvm) parancsot egy ultra-kompatibilis virtuális gép létrehozásához:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-an-ultra-disk-using-powershell"></a>Ultra-lemez létrehozása a PowerShell használatával

Most, hogy rendelkezik egy olyan virtuális géppel, amely képes az ultra-lemezek használatára, létrehozhat és csatlakoztathat egy ultravékony lemezt:

```powershell
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName 'Disk02' `
-Disk $diskconfig;
```

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>Ultra-lemez csatlakoztatása virtuális géphez a PowerShell használatával

Ha a meglévő virtuális gép egy olyan régióban vagy rendelkezésre állási zónában van, amely képes az ultra-lemezek használatára, az új virtuális gép létrehozása nélkül is használhatja az ultra lemezeket.

```powershell
# add disk to VM
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Login-AzureRMAccount -SubscriptionId $subscription
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Ultra-lemez teljesítményének módosítása a PowerShell használatával

Az ultra-lemezek egyedi képességgel rendelkeznek, amely lehetővé teszi a teljesítmény módosítását, a következő parancs egy példa, amely a lemez leválasztása nélkül módosítja a teljesítményt:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>További lépések

Ha szeretné kipróbálni az új lemez típusának [elérésére vonatkozó kérést](https://aka.ms/UltraDiskSignup).