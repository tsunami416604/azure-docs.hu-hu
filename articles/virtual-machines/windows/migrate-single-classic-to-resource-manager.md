---
title: A klasszikus virtuális gépek áttelepítése egy ARM felügyelt lemezes virtuális gép |} Microsoft Docs
description: Telepítse át egy Azure virtuális a klasszikus telepítési modellből felügyelt lemezeket a Resource Manager üzembe helyezési modellben.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: cynthn
ms.openlocfilehash: b81f3719f8781cf6cdb724108f4dd730f3380c86
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="manually-migrate-a-classic-vm-to-a-new-arm-managed-disk-vm-from-the-vhd"></a>Manuális áttelepítésével egy klasszikus virtuális Gépet egy új ARM kezelt lemez virtuális géphez a virtuális merevlemezről 


Ez a szakasz segítséget nyújt a meglévő Azure virtuális gépek áttelepítéséhez a klasszikus telepítési modellből [kezelt lemezek](managed-disks-overview.md) a Resource Manager üzembe helyezési modellben.


## <a name="plan-for-the-migration-to-managed-disks"></a>Felügyelt lemezek az áttelepítés megtervezése

Ez a szakasz segítséget nyújt a legjobb döntést a virtuális gép és a lemez típusok.


### <a name="location"></a>Hely

Jelölje ki a helyet, ahol Azure felügyelt lemezek érhetőek el. Ha az áttelepítés prémium felügyelt lemezekre, is ellenőrizze, hogy prémium szintű storage elérhető a régióban, ahol szeretne áttelepíteni. Lásd: [Azure Services byRegion](https://azure.microsoft.com/regions/#services) elérhető helyről naprakész tájékoztatást.

### <a name="vm-sizes"></a>A virtuális gépek mérete

Ha áttelepítés prémium szintű felügyelt lemez, hogy frissítse a virtuális gép méretét prémium szintű Storage képes a rendelkezésre álló terület a régióban, ahol a virtuális gép. Tekintse át a Virtuálisgép-méretek, amelyek a prémium szintű Storage-kompatibilis. Az Azure virtuális gép mérete paramétereknek szereplő [virtuális gépek méretei](sizes.md).
Tekintse át a virtuális gépek, amely együttműködik a prémium szintű Storage, és válassza ki a leginkább megfelelő virtuális gép méretét, amely a legjobban megfelel a számítási feladatok teljesítményétől. Győződjön meg arról, hogy nincs elegendő sávszélesség érhető el a virtuális Gépet, a lemez forgalom alapjául.

### <a name="disk-sizes"></a>Lemezméretek

**Prémium szintű felügyelt lemez**

Hét különböző típusú premium felügyelt lemezek, amelyek együtt a virtuális Gépet, és mindegyik rendelkezik-e adott iops-érték és átviteli korlátok. Vegye figyelembe a működés felső korlátjának kiválasztása a prémium szintű lemez a virtuális gép a kapacitás, a teljesítmény, méretezhetőség az alkalmazás igényeinek megfelelően, és maximális tölti be.

| Prémium szintű lemezek típusa  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Lemezméret           | 128 GB| 512 GB| 128 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS-érték lemezenként       | 120   | 240   | 500   | 2300              | 5000              | 7500              | 7500              | 
| Adattovábbítás lemezenként | 25 MB / s  | 50 MB / s  | 100 MB / s | 150 MB / s | 200 MB / s | 250 MB / s | 250 MB / s | 

**Standard szintű felügyelt lemez**

Standard szintű felügyelt lemez, amely a virtuális gép használható hét típusa van. Azok a különböző kapacitással rendelkeznek, de azonos IOPS és átviteli sebességének korlátai. Válassza ki a standard szintű felügyelt lemez az alkalmazás a kapacitásigények alapján.

| Standard lemez típusa  | S4               | S6               | S10              | S20              | S30              | S40              | S50              | 
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------| 
| Lemezméret           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1024 GB (1 TB)   | 2048 GB (2TB)    | 4095 GB (4 TB)   | 
| IOPS-érték lemezenként       | 500              | 500              | 500              | 500              | 500              | 500             | 500              | 
| Adattovábbítás lemezenként | 60 MB / s | 60 MB / s | 60 MB / s | 60 MB / s | 60 MB / s | 60 MB / s | 60 MB / s | 


### <a name="disk-caching-policy"></a>Lemez gyorsítótárazási házirend 

**Prémium szintű felügyelt lemez**

Alapértelmezés szerint a gyorsítótárazási házirend lemez van *csak olvasható* prémium adatok lemezein, és *írható-olvasható* az a prémium szintű operációsrendszer-lemez csatolva a virtuális gép. A konfigurációs beállítás ajánlott az alkalmazás IOs rendszerhez az optimális teljesítmény eléréséhez. Írási műveleteket vagy a csak írható adatlemezek (köztük SQL Server) tiltsa le a lemezt gyorsítótárazás, hogy az alkalmazás jobb teljesítményt érhet el.

### <a name="pricing"></a>Díjszabás

Tekintse át a [kezelt lemezek árképzési](https://azure.microsoft.com/pricing/details/managed-disks/). Prémium szintű felügyelt lemez árképzési legyen, mint a nem felügyelt Premium lemezek. Azonban a standard szintű felügyelt lemez árképzési más nem felügyelt Standard lemezeknél.


## <a name="checklist"></a>Feladatlista

1.  Ha áttelepítés prémium szintű felügyelt lemez, feltétlenül érhető el a régió telepít át.

2.  Döntse el, az új Virtuálisgép-sorozat fog használni. A prémium szintű Storage képes kell, ha az áttelepítés prémium szintű felügyelt lemez.

3.  Döntse el, a pontos Virtuálisgép-méretet fogja használni a régió telepít át a rendelkezésre álló. Virtuálisgép-méretet kell lennie, elég nagy legyen rendelkezik adatlemezek számának támogatásához. Például ha négy adatlemezek, a virtuális gép két vagy több maggal kell rendelkeznie. Fontolja meg is, a feldolgozási kapacitása, memória, és a hálózati sávszélesség igényeinek megfelelően.

4.  Az aktuális virtuális gép adatai lesz szüksége, beleértve a megfelelő VHD-blobok és lemezek listáját rendelkezik.

Készítse elő az állásidő alkalmazását. Egy tiszta az áttelepítés végrehajtásához, akkor állítsa le a feldolgozás az aktuális rendszerben. Csak ezután beszerezheti a konzisztens állapotú. Ez az új platformon is áttelepíthetők. Állásidő időtartama áttelepítéséhez a lemezeken mennyiségétől függ.


## <a name="migrate-the-vm"></a>A virtuális gép áttelepítése

Készítse elő az állásidő alkalmazását. Egy tiszta az áttelepítés végrehajtásához, akkor állítsa le a feldolgozás az aktuális rendszerben. Csak ezután beszerezheti a konzisztens állapotú. Ez az új platformon is áttelepíthetők. Állásidő időtartama áttelepítéséhez a lemezeken mennyiségétől függ.


1.  Első lépésként állítsa be a következő általános paramétereket:

    ```powershell
    $resourceGroupName = 'yourResourceGroupName'
    
    $location = 'your location' 
    
    $virtualNetworkName = 'yourExistingVirtualNetworkName'
    
    $virtualMachineName = 'yourVMName'
    
    $virtualMachineSize = 'Standard_DS3'
    
    $adminUserName = "youradminusername"
    
    $adminPassword = "yourpassword" | ConvertTo-SecureString -AsPlainText -Force
    
    $imageName = 'yourImageName'
    
    $osVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd'
    
    $dataVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk1.vhd'
    
    $dataDiskName = 'dataDisk1'
    ```

2.  Hozzon létre egy felügyelt operációsrendszer-lemez, a klasszikus virtuális gépről a virtuális merevlemez használatával.

    Győződjön meg arról, hogy megadta-e a teljes URI-azonosítója az operációs rendszer VHD-fájlt a $osVhdUri paraméter. Emellett adja meg **- AccountType** , **PremiumLRS** vagy **StandardLRS** alapú lemezek (prémium és Standard) típusú végzi az áttelepítést.

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk (New-AzureRmDiskConfig '
    -AccountType PremiumLRS -Location $location -CreateOption Import -SourceUri $osVhdUri) '
    -ResourceGroupName $resourceGroupName
    ```

3.  Az operációsrendszer-lemezképet csatlakoztatni az új virtuális Gépet.

    ```powershell
    $VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $osDisk.Id '
    -StorageAccountType PremiumLRS -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

4.  Felügyelt adatlemezt készíteni a VHD-fájlt, és adja hozzá az új virtuális Gépet.

    ```powershell
    $dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk (New-AzureRmDiskConfig '
    -AccountType PremiumLRS -Location $location -CreationDataCreateOption Import '
    -SourceUri $dataVhdUri ) -ResourceGroupName $resourceGroupName
    
    $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName '
    -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
    ```

5.  Az új virtuális gép létrehozása úgy, hogy nyilvános IP-, a virtuális hálózat és a hálózati adaptert.

    ```powershell
    $publicIp = New-AzureRmPublicIpAddress -Name ($VirtualMachineName.ToLower()+'_ip') '
    -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
    
    $vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName
    
    $nic = New-AzureRmNetworkInterface -Name ($VirtualMachineName.ToLower()+'_nic') '
    -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id '
    -PublicIpAddressId $publicIp.Id
    
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id
    
    New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
    ```

> [!NOTE]
>További lépésekre lehet szükség az alkalmazás, amely támogatja az útmutató nem vonatkoznak.
>
>

## <a name="next-steps"></a>További lépések

- Csatlakozzon a virtuális géphez. Útmutatásért lásd: [csatlakoztatása, és jelentkezzen be a Windowst futtató Azure virtuális gép](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

