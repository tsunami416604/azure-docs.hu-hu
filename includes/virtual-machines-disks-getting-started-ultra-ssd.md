---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/14/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0d081a8cec088f4743bd0dc7d3cc37a9fade61d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117043"
---
Az Azure ultra-lemezek nagy átviteli sebességű, nagy IOPS-t és konzisztens, alacsony késésű lemeztárolást kínálnak az Azure IaaS virtuális gépek (VM-ek) számára. Ez az új ajánlat a vonal csúcsteljesítményét biztosítja a meglévő lemezajánlatainkkal azonos rendelkezésre állási szinteken. Az ultralemezek egyik fő előnye, hogy dinamikusan módosíthatja az SSD teljesítményét a számítási feladatok mellett anélkül, hogy újra kellene indítani a virtuális gépeket. Az ultralemezek olyan adatigényes számítási feladatokhoz használhatók, mint az SAP HANA, a felső szintű adatbázisok és a tranzakcióigényes számítási feladatok.

## <a name="ga-scope-and-limitations"></a>GA hatálya és korlátai

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>A virtuális gép méretének és a régió elérhetőségének meghatározása

Az ultralemezek kihasználásához meg kell határoznia, hogy melyik rendelkezésre állási zónában van. Nem minden régió támogatja az ultralemezek minden virtuális gépméretét. Annak megállapításához, hogy a régió, a zóna és a virtuális gép mérete támogatja-e az ultralemezeket, futtassa az alábbi parancsok egyikét, először cserélje le a **régiót**, a **vmSize**és **az előfizetési** értékeket:

Cli:

```azurecli
$subscription = "<yourSubID>"
# example value is southeastasia
$region = "<yourLocation>"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

PowerShell:

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

A válasz hasonló lesz az alábbi űrlaphoz, ahol X a kiválasztott régióban való üzembe helyezéshez használandó zóna. X lehet 1, 2 vagy 3.

Őrizze meg a **zónák** értékét, a rendelkezésre állási zónát jelöli, és szüksége lesz rá az Ultra lemez központi telepítéséhez.

|ResourceType  |Név  |Hely  |Zóna  |Korlátozás  |Képesség  |Érték  |
|---------|---------|---------|---------|---------|---------|---------|
|Lemezek     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Ha nem érkezett válasz a parancs, majd a kiválasztott virtuális gép mérete nem támogatott ultra lemezek a kiválasztott régióban.

Most, hogy tudja, melyik zónába kell telepíteni, kövesse a jelen cikkben leírt telepítési lépéseket egy olyan virtuális gép üzembe helyezéséhez, amelyhez ultralemez van csatlakoztatva, vagy egy ultralemezt egy meglévő virtuális géphez csatolhat.

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Ultralemez telepítése az Azure Resource Manager használatával

Először határozza meg a virtuális gép méretét az üzembe helyezve. A támogatott virtuális gép méreteinek listáját lásd: [GA hatókör és korlátozások](#ga-scope-and-limitations) szakaszban.

Ha több ultralemezzel rendelkező virtuális gép létrehozásához szeretne létrehozni egy virtuális gép, olvassa el a minta [Hozzon létre egy virtuális gép több ultra lemezt.](https://aka.ms/ultradiskArmTemplate)

Ha saját sablont kíván használni, győződjön `Microsoft.Compute/virtualMachines` meg `Microsoft.Compute/Disks` arról, hogy **az apiVersion** a (vagy újabb) beállításhoz `2018-06-01` van beállítva.

Állítsa a lemeztermék-kuta **UltraSSD_LRS**, majd állítsa be a lemez kapacitását, IOPS, rendelkezésre állási zóna és átviteli sebesség MBps létrehozni egy ultra lemez.

A virtuális gép kiépítése után particionálhatja és formázhatja az adatlemezeket, és konfigurálhatja őket a számítási feladatokhoz.


## <a name="deploy-an-ultra-disk-using-the-azure-portal"></a>Ultralemez üzembe helyezése az Azure Portal használatával

Ez a szakasz egy ultralemezzel rendelkező virtuális gép adatlemezként történő telepítését ismerteti. Feltételezi, hogy ismeri a virtuális gépek üzembe helyezését, ha nem, olvassa el [a rövid útmutató: Hozzon létre egy Windows virtuális gépet az Azure Portalon.](../articles/virtual-machines/windows/quick-create-portal.md)

- Jelentkezzen be az [Azure Portalon,](https://portal.azure.com/) és navigáljon egy virtuális gép (VM) üzembe helyezéséhez.
- Győződjön meg arról, hogy válasszon egy [támogatott virtuális gép méretét és régióját.](#ga-scope-and-limitations)
- Válassza a **Rendelkezésre állás i zónát** az **Elérhetőségi beállítások között.**
- Töltse ki a többi bejegyzést az Ön által kiválasztott kijelölésekkel.
- Válassza a **Lemezek** lehetőséget.

![create-ultra-disk-enabled-vm.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- A Lemezek panelen válassza az **Igen** lehetőséget az **Ultra Disk kompatibilitás engedélyezése**beállításhoz .
- Válassza **az Új lemez létrehozása és csatolása** lehetőséget az ultralemez csatlakoztatásához.

![enable-and-attach-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- Az **Új lemez létrehozása** panelen adjon meg egy nevet, majd válassza a **Méret módosítása lehetőséget.**
- Módosítsa a **Fiók típusát** **Ultra Disk -re.**
- Módosítsa az **egyéni lemezméret (GiB)**, **a Lemez IOPS**és a **Lemez átviteli értékeit** az Ön által választott lemezekre.
- Mindkét panelen válassza az **OK** gombot.
- Folytassa a virtuális gép üzembe helyezését, ugyanaz lesz, mint bármely más virtuális gép üzembe helyezése.

![create-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk.png)

## <a name="attach-an-ultra-disk-using-the-azure-portal"></a>Ultralemez csatolása az Azure Portal használatával

Másik lehetőségként, ha a meglévő virtuális gép egy régióban/rendelkezésre állási zónában, amely képes az ultra lemezek, használhatja az ultra lemezek, anélkül, hogy hozzon létre egy új virtuális gép. Az ultralemezek engedélyezése a meglévő virtuális gépen, majd csatlakoztatva őket adatlemezként.

- Nyissa meg a virtuális gépét, és válassza **a Lemezek**lehetőséget.
- Válassza a **Szerkesztés** elemet.

![beállítások-selector-ultra-disks.png](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- Válassza az **Igen** lehetőséget az **Ultra Disk kompatibilitás engedélyezése**beállításhoz.

![ultra-options-yes-enable.png](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- Kattintson a **Mentés** gombra.
- Válassza **az Adatlemez hozzáadása** lehetőséget a Név menüben a **Lemez** **létrehozása (Create disk) (Lemez létrehozása)** menülegördülő menüben.

![create-and-attach-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- Töltse ki az új lemez nevét, majd válassza a **Méret módosítása lehetőséget.**
- Módosítsa a **Fiók típusát** **Ultra Disk -re.**
- Módosítsa az **egyéni lemezméret (GiB)**, **a Lemez IOPS**és a **Lemez átviteli értékeit** az Ön által választott lemezekre.
- Válassza **az OK gombot,** majd a **Létrehozás gombot.**

![így-a-új-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/making-a-new-ultra-disk.png)

- Miután visszatért a lemez paneljéhez, válassza a **Mentés lehetőséget.**

![mentés-és-csatoló-új-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

### <a name="adjust-the-performance-of-an-ultra-disk-using-the-azure-portal"></a>Ultralemez teljesítményének beállítása az Azure Portal használatával

Az ultralemezek egyedülálló képességet kínálnak, amely lehetővé teszi teljesítményük beállítását. Ezeket a módosításokat az Azure Portalon, maguk a lemezeken is elvégezheti.

- Nyissa meg a virtuális gépét, és válassza **a Lemezek**lehetőséget.
- Válassza ki azt az ultralemezt, amelynek teljesítményét módosítani szeretné.

![kiválasztás-ultra-disk-to-modify.png](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- Válassza **a Konfiguráció** lehetőséget, majd hajtsa végre a módosításokat.
- Kattintson a **Mentés** gombra.

![konfigurálás-ultra-disk-teljesítmény-és méret.png](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

## <a name="deploy-an-ultra-disk-using-cli"></a>Ultralemez telepítése cli-vel

Először határozza meg a virtuális gép méretét az üzembe helyezve. Tekintse meg a [GA hatókörés korlátozások](#ga-scope-and-limitations) szakasz a támogatott virtuális gép méretei.

Létre kell hoznia egy virtuális gép, amely képes az ultra lemezek, annak érdekében, hogy csatolja az ultra lemez.

Cserélje le vagy állítsa be a **$vmname,** **$rgname,** **$diskname**, **$location** **$password** **változókat $user** saját értékeire. Állítsa **be $zone** a cikk [elejétől](#determine-vm-size-and-region-availability)kapott rendelkezésre állási zóna értékét. Ezután futtassa a következő CLI parancsot egy ultra-kompatibilis virtuális gép létrehozásához:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-disk-using-cli"></a>Ultralemez létrehozása cli használatával

Most, hogy rendelkezik egy virtuális gép, amely képes az ultra lemezek csatlakoztatására, létrehozhat és csatolhat egy ultra lemezt.

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

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>Ultralemez csatlakoztatása virtuális géphez a CLI használatával

Másik lehetőségként, ha a meglévő virtuális gép egy régióban/rendelkezésre állási zónában, amely képes az ultra lemezek, használhatja az ultra lemezek, anélkül, hogy hozzon létre egy új virtuális gép.

```azurecli
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>Ultralemez teljesítményének beállítása cli használatával

Az ultralemezek egyedülálló képességet kínálnak, amely lehetővé teszi teljesítményük beállítását, a következő parancs bemutatja, hogyan kell használni ezt a funkciót:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>Ultralemez telepítése a PowerShell használatával

Először határozza meg a virtuális gép méretét az üzembe helyezve. Tekintse meg a [GA hatókörés korlátozások](#ga-scope-and-limitations) szakasz a támogatott virtuális gép méretei.

Az ultralemezek használatához létre kell hoznia egy virtuális gép, amely képes az ultra lemezek használatára. Cserélje le vagy állítsa be a **$resourcegroup** és **$vmName** változókat a saját értékeire. Állítsa **be $zone** a cikk [elejétől](#determine-vm-size-and-region-availability)kapott rendelkezésre állási zóna értékét. Ezután futtassa a következő [New-AzVm](/powershell/module/az.compute/new-azvm) parancsot egy ultra-kompatibilis virtuális gép létrehozásához:

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

### <a name="create-an-ultra-disk-using-powershell"></a>Ultralemez létrehozása a PowerShell használatával

Most, hogy rendelkezik egy olyan virtuális gép, amely képes az ultra lemezek használatára, létrehozhat és csatlakoztathat hozzá egy ultralemezt:

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

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>Ultralemez csatolása virtuális géphez a PowerShell használatával

Másik lehetőségként, ha a meglévő virtuális gép egy régióban/rendelkezésre állási zónában, amely képes az ultra lemezek, használhatja az ultra lemezek, anélkül, hogy hozzon létre egy új virtuális gép.

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

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Ultralemez teljesítményének beállítása a PowerShell használatával

Az ultralemezek egyedülálló képességgel rendelkeznek, amely lehetővé teszi teljesítményük beállítását, a következő parancs egy példa, amely a lemez leválasztása nélkül állítja be a teljesítményt:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>További lépések

Ha szeretné kipróbálni az új lemeztípus [ta-](https://aka.ms/UltraDiskSignup)
