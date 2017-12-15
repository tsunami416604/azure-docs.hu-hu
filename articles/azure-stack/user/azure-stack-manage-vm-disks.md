---
title: "Virtuális gépek lemezei Azure verem kezelése |} Microsoft Docs"
description: "A virtuális gépek lemezek létrehozásához Azure verem."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/14/2017
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: 0c36e2eaaf2d266842b2b7de0b0c8dc0ed1e0145
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2017
---
# <a name="virtual-machine-disk-storage-for-azure-stack"></a>Virtuális gép lemezes tárolás Azure verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Az Azure verem használatát támogatja [lemezek nem felügyelt](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#unmanaged-disks) egy virtuális gépen (operációs rendszer) operációsrendszer-lemez és adatlemez is. Nem felügyelt lemezeket használni, hozzon létre egy [tárfiók](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) és a lemezek majd tárolót, mint a tárfiókon belül tárolókban lévő lapblobokat. Ezek a lemezek majd virtuális lemezeket nevezik.

Javítsa a teljesítményt, és az Azure-verem rendszer a felügyeleti költségek csökkentése, ajánlott minden egyes Virtuálisgép-lemez külön helyezze el. Egy tároló, egy operációsrendszer-lemez vagy egy adatlemez, de nem mindkettőt egyszerre rendelkezzen. Van azonban nincs korlátozva, amely megakadályozza a üzembe mindkettő ugyanabban a tárolóban.

Legalább egy adatlemezt ad hozzá egy virtuális Gépet, ha szeretne további tárolók használata olyan hely, a lemezek tárolására. Adatlemezek, például az operációs rendszer lemezének további virtuális gépek is kell a saját különálló tárolók.

Több virtuális gép létrehozásakor ugyanazt a tárfiókot az egyes új virtuális gépek is felhasználhatja. Csak a tárolók létrehozása egyedinek kell lennie.  

Lemezek hozzáadása a virtuális gépek, a felhasználói portál vagy PowerShell használja.

| Módszer | Beállítások
|-|-|
|[Felhasználói portál](#use-the-portal-to-add-additional-disks-to-a-vm)|-Az új adatok lemezek hozzáadása, amely korábban lett kiépítve. Új lemezek Azure verem jönnek létre. </br> </br>-Adjon hozzá egy meglévő .vhd fájlra egy lemezt egy virtuális géphez, amely korábban lett kiépítve. Ehhez először készítse elő, és töltse fel a .vhd fájlt Azure verem. |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | – Hozzon létre egy új virtuális gép operációsrendszer-lemezzel, majd egy időben adja hozzá egy vagy több adatlemezek ezt a virtuális Gépet. |


## <a name="use-the-portal-to-add-additional-disks-to-a-vm"></a>A portál használatával további lemezek hozzáadása a virtuális gép
Alapértelmezés szerint a portál használatával hozzon létre egy virtuális Gépet a legtöbb Piactéri elemek, ha csak egy operációsrendszer-lemez jön létre. Az Azure-ban létrehozott lemezeket felügyelt lemezeket nevezik.

A virtuális gép kiépítése után a portál segítségével egy új adatokat a lemez vagy egy meglévő adatok hozzáadása a virtuális gép. Minden további lemezhez külön kell rendezni. Nem felügyelt lemezek ad hozzá egy virtuális lemezeket nevezik.

### <a name="use-the-portal-to-attach-a-new-data-disk-to-a-vm"></a>A portál használatával új adatlemezt csatolni egy virtuális géphez

1.  Kattintson a portál **virtuális gépek**.    
    ![Példa: Virtuális gép irányítópult](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2.  Válassza ki a korábban kiosztott virtuális gépeket.   
    ![Példa: Válassza ki a virtuális gépek az irányítópulton](media/azure-stack-manage-vm-disks/select-a-vm.png)

3.  Kattintson a virtuális gép **lemezek** > **új Attach**.       
    ![Példa: Egy új lemezt csatolni a virtuális gép](media/azure-stack-manage-vm-disks/Attach-disks.png)    

4.  Az a **új lemez csatolása** ablaktáblán kattintson a **hely**. Alapértelmezés szerint a hely értéke ugyanabban a tárolóban, amely az operációsrendszer-lemezképet tárolja.      
    ![Példa: Set a lemez helye](media/azure-stack-manage-vm-disks/disk-location.png)

5.  Válassza ki a **tárfiók** használatára. Ezután válassza ki a **tároló** helyezze az adatok helyét. Az a **tárolók** lapon létrehozhat egy új tároló Ha azt szeretné. A saját tároló majd módosítsa a helyet az új lemez. Az egyes lemezek külön használata esetén az adatok lemezre, amely a jobb teljesítmény érdekében elhelyezését terjesztése. Kattintson a **válasszon** a mentéshez.     
    ![Példa: Egy tároló kiválasztása](media/azure-stack-manage-vm-disks/select-container.png)

6.  Az a **új lemez csatolása** lapon, a frissítés a **neve**, **típus**, **mérete**, és **állomás-gyorsítótárazása** beállítások a lemezen. Kattintson a **OK** az új lemez konfigurációját a virtuális gép mentéséhez.  
    ![Példa: Teljes lemezek melléklet](media/azure-stack-manage-vm-disks/complete-disk-attach.png)  

7.  Után Azure verem a lemezt hoz létre, és csatolja azt a virtuális gép, az új lemez szerepel-e a virtuális gép lemezbeállításokat alatt **ADATLEMEZEK**.   
    ![Példa: Lemez megtekintése](media/azure-stack-manage-vm-disks/view-data-disk.png)


### <a name="attach-an-existing-data-disk-to-a-vm"></a>Adatok meglévő lemez csatolása a virtuális géphez
1.  [.Vhd fájl előkészítése](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd) adatlemez használni a virtuális gépek. A .vhd fájlt feltölteni egy tárfiókot, amelyet a virtuális Gépet, amely a .vhd fájlt csatolni szeretné.

  Ahhoz, hogy a .vhd fájlt, mint a tároló, amely az operációs rendszer lemezének különböző tárolót használni szeretne.   
  ![Példa: A VHD-fájl feltöltése](media/azure-stack-manage-vm-disks/upload-vhd.png)

2.  A .vhd fájl feltöltése, után készen áll a virtuális merevlemez csatlakoztatása egy virtuális Gépet. A bal oldali menüben kattintson a **virtuális gépek**.  
 ![Példa: Válassza ki a virtuális gépek az irányítópulton](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3.  Válassza ki a virtuális gépet a listából.    
  ![Példa: Válassza ki a virtuális gépek az irányítópulton](media/azure-stack-manage-vm-disks/select-a-vm.png)

4.  Kattintson a lap a virtuális gép **lemezek** > **Csatolás meglévő**.   
  ![Példa: Meglévő lemez csatolása](media/azure-stack-manage-vm-disks/attach-disks2.png)

5.  Az a **meglévő lemez csatolása** kattintson **VHD-fájl**. A **tárfiókok** lap megnyitásakor.    
  ![Példa: Válasszon egy VHD-fájl](media/azure-stack-manage-vm-disks/select-vhd.png)

6.  A **tárfiókok**, jelölje ki a fiókot használja, és válassza ki a tároló, amely a .vhd fájl, melyet korábban töltött fel. Válassza ki a .vhd fájlt, és kattintson **válasszon** a mentéshez.    
  ![Példa: Egy tároló kiválasztása](media/azure-stack-manage-vm-disks/select-container2.png)

7.  A **meglévő lemez csatolása**, a kiválasztott fájl megtalálható-e **VHD-fájl**. Frissítés a **állomás-gyorsítótárazása** beállítása a lemezen, és kattintson a **OK** az új lemez konfigurációját a virtuális gép mentéséhez.    
  ![Példa: A VHD-fájl csatolása](media/azure-stack-manage-vm-disks/attach-vhd.png)

8.  Után Azure verem a lemezt hoz létre, és csatolja azt a virtuális gép, az új lemez szerepel-e a virtuális gép lemezbeállításokat alatt **adatlemezek**.   
  ![Példa: Végezze el a lemez csatolása](media/azure-stack-manage-vm-disks/complete-disk-attach.png)


## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>Több nem felügyelt lemezeket adhat egy virtuális Gépet a PowerShell használatával
PowerShell segítségével a virtuális gép kiépítése és egy új adatlemez hozzáadása vagy csatlakoztatása egy korábban létező **.vhd** adatlemezt fájlt.

A **Add-AzureRmVMDataDisk** parancsmag adatlemezt ad egy virtuális gépet. Amikor egy virtuális gépet hoz létre, vagy egy meglévő virtuális gép adhat hozzá adatlemezt adhat hozzá adatlemezt. Adja meg a **VhdUri** terjeszteni a lemezek különböző tárolók paraméter.

### <a name="add-data-disks-to-a-new-virtual-machine"></a>Adatlemez hozzáadása egy új virtuális gép
Az alábbi példák a PowerShell-parancsok segítségével három adatlemezekkel rendelkező virtuális gép létrehozása, minden egyes elhelyezni egy másik tárolóban.

Az első parancs a virtuálisgép-objektumot hoz létre, és tárolják a a *$VirtualMachine* változó. A parancs nevét és méretét rendel a virtuális gép.
  ```
  $VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"
  ```

A következő három parancsok rendelje hozzá olyan három adatok lemezek elérési útjait a *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, és *$DataDiskVhdUri03* változók. Adja meg egy másik elérési utat az URL-címben a lemezek különböző tárolók elosztása.     
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```

  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```

  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```

Az utolsó három parancsok adatok lemezek hozzáadása a virtuális gép tárolható *$VirtualMachine*. Minden egyes parancsa a nevét, helyét és a lemez további tulajdonságainak megadása Az egyes lemezek URI Azonosítóját tárolja *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, és *$DataDiskVhdUri03*.
  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                  -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                  -VhdUri $DataDiskVhdUri01 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                 -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                 -VhdUri $DataDiskVhdUri02 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                  -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                  -VhdUri $DataDiskVhdUri03 -CreateOption Empty
  ```

A következő PowerShell-parancsok segítségével az operációs rendszer lemez és hálózat konfigurációja hozzáadása a virtuális Gépet, és indítsa el az új virtuális Gépet.
  ```
  #set variables
  $rgName = "myResourceGroup"
  $location = "local"
  #Set OS Disk
  $osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
  $osDiskName = "osDisk"

  $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
      -CreateOption FromImage -Windows

  # Create a subnet configuration
  $subnetName = "mySubNet"
  $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24

  # Create a vnet configuration
  $vnetName = "myVnetName"
  $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
      -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet

  # Create a public IP
  $ipName = "myIP"
  $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
      -AllocationMethod Dynamic

  # Create a network security group cnfiguration
  $nsgName = "myNsg"
  $rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
      -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
      -SourceAddressPrefix Internet -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
      -Name $nsgName -SecurityRules $rdpRule

  # Create a NIC configuration
  $nicName = "myNicName"
  $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
  -Location $location -SubnetId $vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

  #Create the new VM
  $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine | `
      Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
      -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
  New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
  ```



### <a name="add-data-disks-to-an-existing-virtual-machine"></a>Adatlemez hozzáadása egy meglévő virtuális gépet
Az alábbi példák PowerShell-parancsok segítségével három adatlemezek hozzáadása egy meglévő virtuális gépre.
Az első parancs beolvassa a virtuális gép nevű VirtualMachine a **Get-AzureRmVM** parancsmag. A parancs tárolja a virtuális gép a *$VirtualMachine* változó.
  ```
  $VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                  -Name "VirtualMachine"
  ```
A következő három parancsok három adatlemezek-elérési útvonalakhoz a $DataDiskVhdUri01 $DataDiskVhdUri02 és $DataDiskVhdUri03 változók rendelje hozzá.  A másik elérési utat a vhduri neveknek azt jelzi, hogy a lemez elhelyezésre különböző tárolók.
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```
  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```
  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```


  A következő három parancsok a adatok lemezek hozzáadása a virtuális gép tárolja a *$VirtualMachine* változó. Minden egyes parancsa a nevét, helyét és a lemez további tulajdonságainak megadása Az egyes lemezek URI Azonosítóját tárolja *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, és *$DataDiskVhdUri03*.
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" `
                        -VhdUri $DataDiskVhdUri01 -LUN 0 `
                        -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk2" `
                        -VhdUri $DataDiskVhdUri02 -LUN 1 `
                        -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk3" `
                        -VhdUri $DataDiskVhdUri03 -LUN 2 `
                        -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
  ```


  A záró parancs frissíti a tárolt virtuális gép állapotának *$VirtualMachine* a -*ResourceGroupName*.
  ```
  Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
  ```
<!-- Pending scripts  

## Distribute the data disks of an existing VM
If you have a VM with more than one disk in the same container, the service operator of the Azure Stack deployment might ask you to redistribute the disks into individual containers.

To do so, use the scripts from the following location in GitHub. These scripts can be used to move the data disks to different containers.
-->

## <a name="next-steps"></a>Következő lépések
Virtuális gépek Azure-verem kapcsolatos további információkért lásd: [szempontok a virtuális gépek Azure-készletben](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-considerations).
