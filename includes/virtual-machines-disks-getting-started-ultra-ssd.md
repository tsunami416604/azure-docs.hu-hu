---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3525edb2a73811254b2a4dce70ce3edb58988492
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012443"
---
Az Azure Ultra Disks nagy teljesítményű, magas IOPS és konzisztens, alacsony késésű lemezes tárolást biztosít az Azure IaaS Virtual Machines (VM) szolgáltatásokhoz. Ez az új ajánlat a vonal teljesítményét a meglévő lemezekkel megegyező rendelkezésre állási szinten biztosítja. Az ultra Disks szolgáltatás egyik fő előnye, hogy dinamikusan megváltoztathatja az SSD teljesítményét a számítási feladatokkal együtt anélkül, hogy újra kellene indítania a virtuális gépeket. Az ultra-lemezek olyan adatigényes számítási feladatokhoz használhatók, mint a SAP HANA, a legfelső szintű adatbázisok és a tranzakció-nagy számítási feladatok.

## <a name="ga-scope-and-limitations"></a>A GA hatóköre és korlátai

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>A virtuális gép méretének és a régió rendelkezésre állásának meghatározása

Az ultra-lemezek kihasználása érdekében meg kell határoznia, hogy melyik rendelkezésre állási zónát használja. Nem minden régió támogatja a virtuálisgép-méreteket az ultra Disks szolgáltatással. Annak megállapításához, hogy a régió, a zóna és a virtuális gép mérete támogatja-e az ultra-lemezeket, futtassa a következő parancsok egyikét, és először cserélje le a **régió**, a **vmSize**és az **előfizetés** értékét:

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

A válasz az alábbi űrlaphoz hasonló lesz, ahol az X a kiválasztott régióban való üzembe helyezéshez használt zóna. Az X a következők egyike lehet: 1, 2 vagy 3.

Őrizze meg a **zónák** értékét, amely a rendelkezésre állási zónát képviseli, és szüksége lesz rá egy ultra-lemez üzembe helyezése érdekében.

|ResourceType  |Name (Név)  |Hely  |Zóna  |Korlátozás  |Képesség  |Érték  |
|---------|---------|---------|---------|---------|---------|---------|
|lemezek     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Ha nem volt válasz a parancstól, akkor a kiválasztott virtuálisgép-méret nem támogatott a kiválasztott régióban lévő Ultra-lemezek esetén.

Most, hogy megismerte, hogy melyik zónát kívánja telepíteni, kövesse a jelen cikk központi telepítési lépéseit, és telepítsen egy virtuális gépet egy csatlakoztatott, vagy egy ultra lemez csatlakoztatása egy meglévő virtuális géphez.

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Ultra-lemez üzembe helyezése Azure Resource Manager használatával

Először határozza meg a telepítendő virtuális gép méretét. A támogatott virtuálisgép-méretek listáját a [GA hatókör és korlátozások](#ga-scope-and-limitations) című szakaszban találja.

Ha több Ultra lemezzel rendelkező virtuális gépet szeretne létrehozni, tekintse meg a mintát [több Ultra lemezzel rendelkező virtuális gép létrehozása](https://aka.ms/ultradiskArmTemplate)című témakörben.

Ha saját sablont szeretne használni, győződjön meg róla, hogy a `Microsoft.Compute/virtualMachines` és `Microsoft.Compute/Disks` **apiVersion** beállítása `2018-06-01` (vagy újabb).

Állítsa be a lemez SKU-jának **UltraSSD_LRS**, majd állítsa be a lemez kapacitása, a IOPS, a rendelkezésre állási zóna és az átviteli sebesség (MB/s) értéket egy ultra-lemez létrehozásához.

A virtuális gép üzembe helyezése után particionálhatja és formázhatja az adatlemezeket, és konfigurálhatja azokat a számítási feladatokhoz.

## <a name="deploy-an-ultra-disk-using-cli"></a>Ultra-lemez üzembe helyezése a parancssori felület használatával

Először határozza meg a telepítendő virtuális gép méretét. A támogatott virtuálisgép-méretek listáját a [GA hatókör és korlátozások](#ga-scope-and-limitations) című szakaszban találja.

Ultra-lemez csatlakoztatásához létre kell hoznia egy olyan virtuális gépet, amely képes az ultrakönnyű lemezek használatára.

Cserélje le vagy állítsa be a **$vmname**, **$rgname**, **$diskname**, **$Location**, **$Password**, **$User** változókat a saját értékeivel. Állítsa **$Zone** értéket a [cikk elejétől](#determine-vm-size-and-region-availability)kapott rendelkezésre állási zóna értékére. Ezután futtassa az alábbi CLI-parancsot egy ultra-kompatibilis virtuális gép létrehozásához:

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

Először határozza meg a telepítendő virtuális gép méretét. A támogatott virtuálisgép-méretek listáját a [GA hatókör és korlátozások](#ga-scope-and-limitations) című szakaszban találja. További információ ezekről a virtuálisgép-méretekről.

Az ultra-lemezek használatához létre kell hoznia egy virtuális gépet, amely képes az ultra lemezek használatára. Cserélje le vagy állítsa be a **$resourcegroup** és a **$vmName** változót a saját értékeivel. Állítsa **$Zone** értéket a [cikk elejétől](#determine-vm-size-and-region-availability)kapott rendelkezésre állási zóna értékére. Ezután futtassa a következő [New-AzVm](/powershell/module/az.compute/new-azvm) parancsot egy ultra-kompatibilis virtuális gép létrehozásához:

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

## <a name="next-steps"></a>Következő lépések

Ha szeretné kipróbálni az új lemez típusának [elérésére vonatkozó kérést](https://aka.ms/UltraDiskSignup).