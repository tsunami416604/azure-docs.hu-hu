---
title: Oktatóanyag – Azure-lemezek kezelése az Azure PowerShell-lel | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre és felügyelhet Azure-lemezeket virtuális gépekhez az Azure PowerShell használatával.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/05/2018
ms.author: cynthn
ms.custom: mvc
ms.subservice: disks
ms.openlocfilehash: 1db4dd2f1aa550ea4ccac55882f147c0585cdd0c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55466727"
---
# <a name="tutorial---manage-azure-disks-with-azure-powershell"></a>Oktatóanyag – Azure-lemezek kezelése az Azure PowerShell-lel

Az Azure-beli virtuális gépek lemezeket használnak a virtuális gépek operációs rendszereinek, alkalmazásainak és adatainak tárolására. Virtuális gép létrehozásakor fontos szempont, hogy a számítási feladatokkal járó várható terhelésnek megfelelő lemezméretet és konfigurációt válasszon ki. Ez az oktatóanyag a virtuálisgép-lemezek üzembe helyezését és kezelését mutatja be. Az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Operációsrendszer-lemezek és ideiglenes lemezek
> * Adatlemezek
> * Standard és Prémium lemezek
> * Lemezek teljesítménye
> * Adatlemezek csatolása és előkészítése

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/powershell](https://shell.azure.com/powershell) cím megnyitásával. A **Másolás** kiválasztásával másolja és illessze be a kódrészleteket a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="default-azure-disks"></a>Alapértelmezett Azure-lemezek

Egy Azure-beli virtuális gép létrehozásakor a rendszer két lemezt automatikusan a virtuális géphez csatol. 

**Operációsrendszer-lemez** – Az operációsrendszer-lemezek akár 4 terabájt méretűek is lehetnek, és a virtuális gépek operációs rendszerei találhatók rajtuk.  Az operációs rendszert futtató lemezhez alapértelmezés szerint a *C:* meghajtóbetűjel van hozzárendelve. Az operációsrendszer-lemez gyorsítótárazási konfigurációja az operációs rendszer teljesítményéhez van optimizálva. Az operációsrendszer-lemez **nem** üzemeltethet alkalmazásokat, és nem tárolhat adatokat. Alkalmazásokhoz és adatokhoz használjon adatlemezeket, amelyekről később lesz szó.

**Ideiglenes lemez** – Az ideiglenes lemezek olyan tartós állapotú meghajtót (SSD-t) használnak, amely ugyanazon az Azure-gazdagépen található, mint a virtuális gép. Az ideiglenes lemezek nagy teljesítményűek és olyan műveletekhez használhatók, mint például az ideiglenes adatfeldolgozás. Ha azonban a virtuális gépet egy új gazdagépre költöztetik, az ideiglenes lemezen tárolt adatokat a rendszer eltávolítja. Az ideiglenes lemez méretét a [virtuális gép mérete](sizes.md) határozza meg. Az ideiglenes lemezekhez alapértelmezés szerint a *D:* meghajtóbetűjel van hozzárendelve.



## <a name="azure-data-disks"></a>Azure-adatlemezek

További adatlemezeket adhat hozzá, amelyekre alkalmazásokat telepíthet és amelyeken adatokat tárolhat. Az adatlemezeket akkor érdemes használni, ha tartós és rugalmas adattárolásra van szükség. Az egyes adatlemezek kapacitása maximum 4 terabájt lehet. A virtuális gép mérete határozza meg, hány adatlemez csatolható a virtuális géphez. A virtuális gépek minden vCPU-jához négy adatlemez csatolható. 


## <a name="vm-disk-types"></a>Virtuálisgép-lemezek típusai

Az Azure két lemeztípust kínál.

**Standard lemezek** – a merevlemez-meghajtókra épülő lemezek költséghatékony tárolási megoldást, ugyanakkor jó teljesítményt nyújtanak. A standard lemezek ideális megoldást jelentenek költséghatékony fejlesztési és tesztelési számítási feladatokhoz.

**Prémium lemezek** – SSD-alapú, nagy teljesítményű, kis késleltetésű lemezek. Az éles számítási feladatokat futtató virtuális gépek esetén érdemes a használatuk mellett dönteni. A Premium Storage támogatja a DS, a DSv2, a GS és az FS sorozatú virtuális gépeket. A prémium lemezeknek öt típusa van (P10, P20, P30, P40, P50), és a lemez mérete határozza meg a lemez típusát. Lemezméret kiválasztásakor az értéket felfelé kerekíti a rendszer a következő típusra. Ha például a lemez mérete kisebb 128 GB-nál, a lemez típusa P10. 129 GB és 512 GB között a lemez típusa P20 lesz.

### <a name="premium-disk-performance"></a>Prémium szintű lemezek teljesítménye

|Prémium szintű tárolólemezek típusai | P4 | P6 | P10 | P20 | P30 | P40 | P50 | P60 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Lemezméret (felfelé kerekítés) | 32 GiB | 64 GiB | 128 GiB | 512 GiB | 1024 GiB (1 TiB) | 2048 GiB (2 TiB) | 4095 GiB (4 TiB) | 8192 GiB (8 TiB)
| Lemezenkénti maximális IOPS-érték | 120 | 240 | 500 | 2300 | 5000 | 7500 | 7500 | 12 500 |
Adattovábbítás lemezenként | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s | 480 MB/s |

Míg a fenti táblázatban a lemezenkénti maximális IOPS-érték látható, nagyobb teljesítmény is elérhető több adatlemez összevonásával. Például a Standard_GS5 virtuális géphez 64 adatlemez csatolható. Ha ezen lemezek mindegyike P30-ra van méretezve, maximum 80 000-es IOPS-érték érhető el. A virtuális gépenkénti maximális IOPS-értékről szóló részletes információkért lásd a [virtuális gépek típusait és méreteit](./sizes.md) ismertető cikket.

## <a name="create-and-attach-disks"></a>Lemezek létrehozása és csatolása

Az oktatóanyagban található példa elvégzéséhez szüksége lesz egy meglévő virtuális gépre. Ha szükséges, hozzon létre egy virtuális gépet az alábbi parancsokkal.

A virtuális gép rendszergazdai fiókjának felhasználónevét és jelszavát állítsa be a [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal:


Hozza létre a virtuális gépet a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) parancsmaggal. Amikor a rendszer kéri, adja meg a virtuális gép rendszergazdai fiókjának felhasználónevét és jelszavát.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" 
```


Hozza létre a kezdeti konfigurációt a [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig) paranccsal. A következő példa egy 128 GB méretű lemezt konfigurál.

```azurepowershell-interactive
$diskConfig = New-AzureRmDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

Hozza létre az adatlemezt a [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk) paranccsal.

```azurepowershell-interactive
$dataDisk = New-AzureRmDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

A [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) paranccsal kérje le a virtuális gépet, amelyhez hozzá szeretné adni az adatlemezt.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

Adja hozzá az adatlemezt a virtuálisgép-konfigurációhoz az [Add-AzureRmVMDataDisk](/powershell/module/azurerm.compute/add-azurermvmdatadisk) paranccsal.

```azurepowershell-interactive
$vm = Add-AzureRmVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

Frissítse a virtuális gépet az [Update-AzureRmVM](/powershell/module/azurerm.compute/add-azurermvmdatadisk) paranccsal.

```azurepowershell-interactive
Update-AzureRmVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>Adatlemezek előkészítése

Miután hozzácsatolt egy lemezt a virtuális géphez, az operációs rendszert konfigurálni kell a lemez használatához. Az alábbi példa bemutatja, hogyan konfigurálhatja manuálisan a virtuális géphez elsőként hozzáadott lemezt. Ez a folyamat is automatizálható az [egyéni szkriptbővítmény](./tutorial-automate-vm-deployment.md) használatával.

### <a name="manual-configuration"></a>Manuális konfigurálás

Hozzon léte egy RDP-kapcsolatot a virtuális géppel. Nyissa meg a PowerShellt, és futtassa ezt a szkriptet.

```azurepowershell
Get-Disk | Where partitionstyle -eq 'raw' |
    Initialize-Disk -PartitionStyle MBR -PassThru |
    New-Partition -AssignDriveLetter -UseMaximumSize |
    Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="verify-the-data-disk"></a>Az adatlemez ellenőrzése

Annak ellenőrzéséhez, hogy az adatlemez csatlakozik-e, tekintse meg a csatlakoztatott `DataDisks` `StorageProfile` profilját.

```azurepowershell-interactive
$vm.StorageProfile.DataDisks
```

Az alábbi példában látható kimenethez hasonló eredményt kell kapnia:

```
Name            : myDataDisk
DiskSizeGB      : 128
Lun             : 1
Caching         : None
CreateOption    : Attach
SourceImage     :
VirtualHardDisk :
```


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a virtuálisgép-lemezekkel kapcsolatos témakörökkel ismerkedett meg, például:

> [!div class="checklist"]
> * Operációsrendszer-lemezek és ideiglenes lemezek
> * Adatlemezek
> * Standard és Prémium lemezek
> * Lemezek teljesítménye
> * Adatlemezek csatolása és előkészítése

Folytassa a következő oktatóanyaggal, amely a virtuális gép konfigurálásának automatizálását ismerteti.

> [!div class="nextstepaction"]
> [Virtuálisgép-konfiguráció létrehozása](./tutorial-automate-vm-deployment.md)
