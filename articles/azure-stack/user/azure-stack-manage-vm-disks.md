---
title: Az Azure Stackben Virtuálisgép-lemezek kezelése |} A Microsoft Docs
description: Virtuális gépek az Azure Stackben lemezek létrehozásához.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/05/2018
ms.author: mabrigg
ms.reviewer: jiahan
ms.openlocfilehash: bdf31c72fbcd8941161e6b9df0a490df7f6a16e0
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44026518"
---
# <a name="provision-virtual-machine-disk-storage-in-azure-stack"></a>Virtuális gép lemezes tárolás az Azure Stack üzembe helyezése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Ez a cikk azt ismerteti, hogyan építheti ki a virtuális gép lemezes tárolás az Azure Stack-portál használatával, vagy a PowerShell használatával.

## <a name="overview"></a>Áttekintés

Azure Stack verziójától kezdve 1808, a virtuális gépek, az operációs rendszer (OS) és a egy adatlemezt is támogatja a felügyelt és a nem felügyelt lemezek használatát. 1808 verziónál régebbi csak a nem felügyelt lemezek használata támogatott. 

**[A Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#managed-disks)**  az Azure IaaS virtuális gépek lemezfelügyelet egyszerűsítése a virtuális gépek lemezeihez társított storage-fiókok kezelésével. Csak meg kell adnia a méretű lemez van szüksége, és az Azure Stack létrehozza és felügyeli a lemezt Ön helyett.

**[Nem felügyelt lemezek](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#unmanaged-disks)**, szükséges, hogy hozzon létre egy [tárfiók](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) a lemezeinek tárolására. A lemezeket hoz létre Virtuálisgép-lemezeket nevezik, és a tárfiókban lévő tárolók vannak tárolva.

 

### <a name="best-practice-guidelines"></a>Gyakorlati előírásai

Javíthatja a teljesítményt, és az általános költségek csökkentése, ajánlott minden egyes Virtuálisgép-lemez helyez egy külön tárolót. Egy tároló, operációsrendszer-lemezt vagy egy adatlemez, de nem mindkettőt egyszerre rendelkezzen. (Azonban nincs akadálya, hogy bármik lehetnek, mindkét típusú lemez ugyanabban a tárolóban.)

Egy vagy több adatlemezt ad hozzá egy virtuális Gépet, ha további tárolók használatával, egy helyen tárolja ezeket a lemezeket. Az operációsrendszer-lemez további virtuális gépeket is kell saját tárolókban.

Amikor több virtuális gépet hoz létre, felhasználhatja ugyanazt a tárfiókot minden egyes új virtuális géphez. Csak a tárolók létrehozása egyedinek kell lennie.

### <a name="adding-new-disks"></a>Új lemezek hozzáadását

Az alábbi táblázat foglalja össze a lemezek hozzáadása a portál használatával, és a PowerShell használatával.

| Módszer | Beállítások
|-|-|
|[Felhasználói portál](#use-the-portal-to-add-additional-disks-to-a-vm)|-Új adatlemezek hozzáadása egy meglévő virtuális Gépet. Azure Stack új lemezeket hoznak létre. </br> </br>– A meglévő lemezt (.vhd) fájl hozzáadása a korábban üzembe helyezett virtuális géphez. Ehhez kell előkészíteni a .vhd és majd feltöltjük a fájlt az Azure Stackhez. |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | – Új virtuális gép létrehozása az operációsrendszer-lemezt, és a egy időben adjon hozzá egy vagy több adatlemezt a virtuális Gépre. |

## <a name="use-the-portal-to-add-disks-to-a-vm"></a>Lemezek hozzáadása egy virtuális Géphez a portál használatával

Alapértelmezés szerint a legtöbb Piactéri elemek, a virtuális gép létrehozása a portál használatakor csak az operációsrendszer-lemez jön létre.

Miután létrehozott egy virtuális Gépet, a portálon is használhatja:
* Hozzon létre egy új adatlemezt, és csatlakoztassa a virtuális Gépet.
* Töltse fel a már meglévő adatlemez, és csatlakoztassa a virtuális Gépet.

Minden nem felügyelt lemez hozzáadása egy külön tárolóban kell elhelyezni.

>[!NOTE]
>Lemezek létrehozása és felügyelete az Azure-ban az úgynevezett [felügyelt lemezek](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).

### <a name="use-the-portal-to-create-and-attach-a-new-data-disk"></a>A portál segítségével létrehozhat, és a egy új adatlemez csatolása

1.  A portálon **virtuális gépek**.    
    ![Példa: Virtuális gép irányítópult](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2.  Válassza ki a korábban kiépített virtuális gépet.   
    ![Példa: Válasszon egy virtuális Gépet az irányítópulton](media/azure-stack-manage-vm-disks/select-a-vm.png)

3.  Válassza ki a virtuális gép **lemezek** > **új Attach**.       
    ![Példa: Egy új lemez csatolása a virtuális géphez](media/azure-stack-manage-vm-disks/Attach-disks.png)    

4.  Az a **új lemez csatolása** ablaktáblán válassza előbb **hely**. Alapértelmezés szerint a hely ugyanazt a tárolót, amely tárolja az operációsrendszer-lemez értéke.      
    ![Példa: Set a lemez helye](media/azure-stack-manage-vm-disks/disk-location.png)

5.  Válassza ki a **tárfiók** használatára. Ezután válassza ki a **tároló** kívánja helyezze az adatokat. Az a **tárolók** oldalon hozhat létre egy új tárolót, ha azt szeretné. A saját tároló majd módosítsa a helyet az új lemez. Az egyes lemezek használatakor egy külön tárolót, terjesztése, javíthatja a teljesítményt az adatlemez elhelyezését. Válasszon **kiválasztása** a mentéshez.     
    ![Példa: Egy tároló kiválasztása](media/azure-stack-manage-vm-disks/select-container.png)

6.  Az a **új lemez csatolása** lapon, a frissítés a **neve**, **típus**, **mérete**, és **gazdagép gyorsítótárazási** beállításai a lemez. Válassza ki **OK** az új lemez konfigurációját a virtuális gép mentéséhez.  
    ![Példa: Teljes lemezek melléklet](media/azure-stack-manage-vm-disks/complete-disk-attach.png)  

7.  Után az Azure Stack a lemezt hoz létre, és csatlakoztatja a virtuális gép, az új lemez szerepel-e a virtuális gép lemezbeállításokat alatt **ADATLEMEZEK**.   
    ![Példa: A lemez megtekintése](media/azure-stack-manage-vm-disks/view-data-disk.png)


### <a name="attach-an-existing-data-disk-to-a-vm"></a>Egy már meglévő adatlemez csatolása a virtuális géphez

1.  [Készítse elő a .vhd fájlt](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd) Virtuálisgép-adatlemez használhatók. A .vhd fájlt feltölteni egy storage-fiókot, amelyet a virtuális gép, amely a .vhd fájlt csatolni szeretné.

  Tervezze meg használni egy másik tárolóba, amely tárolja a .vhd fájlt, mint a tároló, amely az operációsrendszer-lemez.   
  ![Példa: Egy VHD-fájl feltöltése](media/azure-stack-manage-vm-disks/upload-vhd.png)

2.  Miután feltöltötte a .vhd fájlt, készen áll a virtuális merevlemez csatlakoztatása egy virtuális Gépet. A bal oldali menüben válassza **virtuális gépek**.  
 ![Példa: Válasszon egy virtuális Gépet az irányítópulton](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3.  Válassza ki a virtuális gépet a listából.    
  ![Példa: Válasszon egy virtuális Gépet az irányítópulton](media/azure-stack-manage-vm-disks/select-a-vm.png)

4.  Válassza ki a lapon a virtuális gép **lemezek** > **csatolása meglévő**.   
  ![Példa: Meglévő lemez csatlakoztatása](media/azure-stack-manage-vm-disks/attach-disks2.png)

5.  Az a **meglévő lemez csatolása** lapon jelölje be **VHD-fájl**. A **tárfiókok** lap megnyitásakor.    
  ![Példa: Egy VHD-fájl kiválasztása](media/azure-stack-manage-vm-disks/select-vhd.png)

6.  A **tárfiókok**, válassza ki a használni kívánt fiók, és válassza a korábban feltöltött .vhd fájlt tartalmazó tároló. Válassza ki a .vhd fájlt, és válassza **kiválasztása** a mentéshez.    
  ![Példa: Egy tároló kiválasztása](media/azure-stack-manage-vm-disks/select-container2.png)

7.  A **meglévő lemez csatolása**, a kiválasztott fájl részen **VHD-fájl**. Frissítés a **gazdagép gyorsítótárazási** beállítást a lemezen, és válassza ki **OK** az új lemez konfigurációját a virtuális gép mentéséhez.    
  ![Példa: A VHD-fájl csatolása](media/azure-stack-manage-vm-disks/attach-vhd.png)

8.  Után az Azure Stack a lemezt hoz létre, és csatlakoztatja a virtuális gép, az új lemez szerepel-e a virtuális gép lemezbeállításokat alatt **adatlemezek**.   
  ![Példa: Végezze el a lemez csatolása](media/azure-stack-manage-vm-disks/complete-disk-attach.png)


## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>Több felügyelt lemez hozzáadása virtuális Géphez a PowerShell használatával
Virtuális gép létrehozása és a egy új adatlemez hozzáadása vagy egy előzetesen meglévő lemez csatolása használhatja a Powershellt **.vhd** fájl adatlemezként.

A **Add-AzureRmVMDataDisk** parancsmag hozzáadja egy adatlemezt egy virtuális géphez. Hozzáadhat egy adatlemezt hoz létre virtuális gépet, vagy egy adatlemezt is hozzáadhat egy meglévő virtuális gépet. Adja meg a **VhdUri** elosztása a különböző tárolók lemezeket paramétert.

### <a name="add-data-disks-to-a-new-virtual-machine"></a>Adatlemez hozzáadása egy új virtuális gép
Az alábbi példák három adatlemezekkel rendelkező virtuális gép létrehozása PowerShell-parancsok használatával, egy másik tárolóba helyezett minden egyes.

Az első parancs létrehoz egy virtuálisgép-objektumot, és tárolják a a *$VirtualMachine* változó. A parancs hozzárendeli a virtuális gép nevét és méretét.
  ```
  $VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"
  ```

A következő három parancsok rendelje hozzá a három adatlemez, az útvonalak a *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, és *$DataDiskVhdUri03* változókat. Adja meg egy másik elérési utat az URL-címben elosztása a különböző tárolók lemezeket.     
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```

  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```

  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```

Az utolsó három parancsok hozzáadhat adatlemezeket a tárolt virtuális gép *$VirtualMachine*. Minden egyes parancsot adja meg a nevét, helyét és a lemez további tulajdonságok. URI-ját az egyes lemezeken tárolja *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, és *$DataDiskVhdUri03*.
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

A következő PowerShell-parancsok használatával az operációs rendszer lemez és hálózat konfigurációja hozzáadása a virtuális Géphez, és indítsa el az új virtuális Gépet.
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



### <a name="add-data-disks-to-an-existing-virtual-machine"></a>Adatlemez hozzáadása egy meglévő virtuális gép
Az alábbi példák a PowerShell-parancsok használatával három adatlemezek hozzáadása egy meglévő virtuális Gépet.
Az első parancs lekéri a virtuális gép VirtualMachine elnevezve a **Get-AzureRmVM** parancsmagot. A parancs tárolja a virtuális gépet a *$VirtualMachine* változó.
  ```
  $VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                  -Name "VirtualMachine"
  ```
A következő három parancsok $DataDiskVhdUri01 $DataDiskVhdUri02 és $DataDiskVhdUri03 változókhoz rendelje hozzá a három adatlemez útvonalak.  A másik elérési utat a vhduri nevek azt jelzik, hogy a lemez elhelyezésre különböző tárolók.
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```
  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```
  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```


  A következő három parancsokat az adatlemezek hozzáadása a tárolt virtuális gép a *$VirtualMachine* változó. Minden egyes parancsot adja meg a nevét, helyét és a lemez további tulajdonságok. URI-ját az egyes lemezeken tárolja *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, és *$DataDiskVhdUri03*.
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


  Az utolsó parancs frissíti a tárolt virtuális gép *$VirtualMachine* a -*ResourceGroupName*.
  ```
  Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
  ```
<!-- Pending scripts  

## Distribute the data disks of an existing VM
If you have a VM with more than one disk in the same container, the service operator of the Azure Stack deployment might ask you to redistribute the disks into individual containers.

To do so, use the scripts from the following location in GitHub. These scripts can be used to move the data disks to different containers.
-->

## <a name="next-steps"></a>További lépések
Virtuális gépek az Azure Stack kapcsolatos további információkért lásd: [szempontok a virtuális gépek az Azure Stackben](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-considerations).
