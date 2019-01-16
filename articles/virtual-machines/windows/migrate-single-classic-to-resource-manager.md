---
title: Klasszikus virtuális gép egy ARM felügyelt lemezes virtuális gép migrálása |} A Microsoft Docs
description: Egyetlen Azure virtuális gép migrálása a klasszikus üzemi modellből a Managed Disks szolgáltatásba a Resource Manager-alapú üzemi modellben.
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
ms.openlocfilehash: a662a61d737dbb620d07fa6d114649e70c082796
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329769"
---
# <a name="migrate-a-classic-vm-to-use-a-managed-disk"></a>Felügyelt lemez használata a klasszikus virtuális gép áttelepítése 


Ez a szakasz segítséget nyújt a meglévő Azure virtuális gépek áttelepítése a klasszikus üzemi modellből [Managed Disks](managed-disks-overview.md) a Resource Manager-alapú üzemi modellben.


## <a name="plan-for-the-migration-to-managed-disks"></a>Tervezze meg a migrálás a Managed Disks szolgáltatásba

Ez a szakasz segít, hogy a legjobb döntést a virtuális gép és a lemez típusa.


### <a name="location"></a>Hely

Jelöljön ki egy helyet, ha a Managed Disks rendelkezésre állnak. Ha az áttelepítés a Premium storage-alapú felügyelt lemezre, is győződjön meg arról, hogy a Premium storage az adott régióban érhető el. Lásd: [Azure-szolgáltatások byRegion](https://azure.microsoft.com/regions/#services) naprakész információk az elérhető helyek.

### <a name="vm-sizes"></a>A virtuális gépek mérete

Ha az áttelepítés a Premium Storage tárolást használ felügyelt lemezeket, akkor frissítse a virtuális gép méretét a Premium Storage képes a méret elérhető a régióban, ahol a virtuális gép is található. Tekintse át a Premium Storage képes a a Virtuálisgép-méretek. Az Azure virtuális gép mérete előírások felsorolt [virtuális gépek méretei](sizes.md).
Tekintse át a teljesítményt nyújt, amely a Premium Storage működnek, és válassza ki a leginkább megfelelő Virtuálisgép-méretet a számítási feladathoz leginkább megfelelő virtuális gépek. Győződjön meg arról, hogy van-e elegendő sávszélesség érhető el a meghajtó a lemez forgalmat a virtuális gép.

### <a name="disk-sizes"></a>Lemezméretek

**Prémium**

A Premium storage, amely a virtuális gép használható hét típusa van, mindegyik adott IOPs és átviteli sebesség korlátok. A virtuális gép kapacitását, teljesítmény, méretezhetőség tekintetében az alkalmazás igényeinek megfelelően a prémium szintű lemeztípus kiválasztásakor, vegye figyelembe ezeket a korlátokat, és csúcs tölti be.

| Prémium szintű lemezek típusa  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Lemezméret           | 128 GB| 512 GB| 128 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS-érték lemezenként       | 120   | 240   | 500   | 2300              | 5000              | 7500              | 7500              | 
| Adattovábbítás lemezenként | 25 MB / s  | 50 MB / s  | 100 MB / s | 150 MB / s | 200 MB / s | 250 MB / s | 250 MB / s | 

**Standard**

A standard szintű lemezek, amelyek használhatók a virtuális gép hét típusai vannak. Azok a más kapacitásokhoz de azonos IOPS- és feldolgozásisebesség-korlátait. Válassza ki a standard szintű Managed disks kapacitást az alkalmazás igényeinek megfelelően.

| Standard lemez típusa  | S4               | S6               | S10              | S20              | S30              | S40              | S50              | 
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------| 
| Lemezméret           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1024 GB (1 TB)   | 2048 GB (2 TB)    | 4095 GB (4 TB)   | 
| IOPS-érték lemezenként       | 500              | 500              | 500              | 500              | 500              | 500             | 500              | 
| Adattovábbítás lemezenként | 60 MB / s | 60 MB / s | 60 MB / s | 60 MB / s | 60 MB / s | 60 MB / s | 60 MB / s | 


### <a name="disk-caching-policy"></a>Lemez gyorsítótárazási házirend 

**Premium Managed Disks**

Alapértelmezés szerint a lemez gyorsítótárazási házirend a *csak olvasható* minden a prémium szintű adatlemezek esetén és *olvasási és írási* a prémium szintű operációsrendszer-lemez a virtuális Géphez csatlakoztatva. Ezt a konfigurációs beállítást az optimális teljesítmény érdekében az alkalmazás IOs-hez javasolt. Írási vagy csak írási adatlemezek (például az SQL Server-naplófájlok) tiltsa le a lemezek gyorsítótárazása, így jobb alkalmazásteljesítményt érhet el.

### <a name="pricing"></a>Díjszabás

Tekintse át a [a felügyelt lemezek díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/). Prémium szintű felügyelt lemezek díjszabása megegyezik a nem felügyelt prémium szintű lemezek. Azonban a standard szintű felügyelt lemezek díjszabása eltér attól a standard szintű nem felügyelt lemezek.


## <a name="checklist"></a>Ellenőrzőlista

1.  Ha az áttelepítés a Premium Managed Disks szolgáltatásba, róla, hogy elérhető a régióban végzi az áttelepítést.

2.  Döntse el, az új Virtuálisgép-sorozatok fog használni. Ha az áttelepítés a Premium Managed Disks szolgáltatásba egy prémium szintű Storage képes a kell lennie.

3.  Döntse el, a pontos virtuális gép mérete fogja használni az áttelepítés régióban érhető el. Virtuálisgép-méretet kell lennie, elég nagy legyen támogatja a több adatlemez van. Például ha négy adatlemezeket, a virtuális gép két vagy több mag kell rendelkeznie. Emellett érdemes feldolgozási, memória és a hálózati sávszélesség-igény.

4.  Rendelkezik a kényelmes, beleértve a lemezek és a megfelelő VHD-blobok listáját az aktuális virtuális gép adatait.

Az alkalmazás előkészítése az állásidő. Egy tiszta az áttelepítés végrehajtásához, akkor az összes feldolgozó leállítása a jelenlegi rendszerben. Csak ezután beszerezheti azt konzisztens állapotba, amelyhez áttelepíthet az új platformra. Állásidő időtartama attól függ, hogy áttelepíteni a lemezeken lévő adatok mennyisége.


## <a name="migrate-the-vm"></a>A virtuális gép áttelepítése

Az alkalmazás előkészítése az állásidő. Egy tiszta az áttelepítés végrehajtásához, akkor az összes feldolgozó leállítása a jelenlegi rendszerben. Csak ezután beszerezheti azt áttelepítheti az új platformra a konzisztens állapotba. Állásidő időtartama attól függ, hogy áttelepíteni a lemezeken lévő adatok mennyisége.

Ez a rész az Azure PowerShell-modul verzióját 6.0.0 igényel vagy újabb. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.


Az általános paraméterek változók létrehozása.

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

Hozzon létre egy felügyelt operációsrendszer-lemez a VHD-t a klasszikus virtuális gép használatával. Győződjön meg arról, hogy a teljes URI-ját a rendszert tartalmazó virtuális Merevlemezt $osVhdUri paraméteréhez megadott. Emellett adja meg **- AccountType** , **Premium_LRS** vagy **Standard_LRS** alapú lemezek (prémium vagy standard) típusú végzi az áttelepítést.

```powershell
$osDisk = New-AzureRmDisk -DiskName $osDiskName '
   -Disk (New-AzureRmDiskConfig '
   -AccountType Premium_LRS '
   -Location $location '
   -CreateOption Import '
   -SourceUri $osVhdUri) '
   -ResourceGroupName $resourceGroupName
```

Az operációsrendszer-lemez csatolása az új virtuális Gépet.

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize
$VirtualMachine = Set-AzureRmVMOSDisk '
   -VM $VirtualMachine '
   -ManagedDiskId $osDisk.Id '
   -StorageAccountType Premium_LRS '
   -DiskSizeInGB 128 '
   -CreateOption Attach -Windows
```

Hozzon létre egy felügyelt adatlemezre az adatokat VHD-fájlból, és adja hozzá az új virtuális Gépet.

```powershell
$dataDisk1 = New-AzureRmDisk '
   -DiskName $dataDiskName '
   -Disk (New-AzureRmDiskConfig '
   -AccountType Premium_LRS '
   -Location $location '
   -CreationOption Import '
   -SourceUri $dataVhdUri ) '
   -ResourceGroupName $resourceGroupName
    
$VirtualMachine = Add-AzureRmVMDataDisk '
   -VM $VirtualMachine '
   -Name $dataDiskName '
   -CreateOption Attach '
   -ManagedDiskId $dataDisk1.Id '
   -Lun 1
```

Hozzon létre az új virtuális gép nyilvános IP-Címek, virtuális hálózatot és hálózati beállításával

```powershell
$publicIp = New-AzureRmPublicIpAddress '
   -Name ($VirtualMachineName.ToLower()+'_ip') '
   -ResourceGroupName $resourceGroupName '
   -Location $location '
   -AllocationMethod Dynamic
    
$vnet = Get-AzureRmVirtualNetwork '
   -Name $virtualNetworkName '
   -ResourceGroupName $resourceGroupName
    
$nic = New-AzureRmNetworkInterface '
   -Name ($VirtualMachineName.ToLower()+'_nic') '
   -ResourceGroupName $resourceGroupName '
   -Location $location '
   -SubnetId $vnet.Subnets[0].Id '
   -PublicIpAddressId $publicIp.Id
    
$VirtualMachine = Add-AzureRmVMNetworkInterface '
   -VM $VirtualMachine '
   -Id $nic.Id
    
New-AzureRmVM -VM $VirtualMachine '
   -ResourceGroupName $resourceGroupName '
   -Location $location
```

> [!NOTE]
>Előfordulhat, hogy további lépéseket, amely az alkalmazása támogatásához szükséges ez az útmutató nem vonatkoznak.
>
>

## <a name="next-steps"></a>További lépések

- Csatlakozzon a virtuális géphez. Útmutatásért lásd: [csatlakoztatása és bejelentkezés Windows rendszert futtató Azure virtuális gép](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

