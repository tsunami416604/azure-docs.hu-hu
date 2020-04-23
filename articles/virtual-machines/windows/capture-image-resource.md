---
title: Felügyelt lemezkép létrehozása az Azure-ban
description: Általános virtuális gép vagy virtuális merevlemez felügyelt lemezképét hoz létre az Azure-ban. A lemezképek segítségével több felügyelt lemezt használó virtuális gépet hozhat létre.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 258bddec85e4ab182ff0b07c49cdc93f92264f95
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084464"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Felügyelt rendszerkép létrehozása általánosított Azure-beli virtuális gépből

Az olyan általánosított virtuális gépekből (VM-ekből), amelyek felügyelt lemezként vagy nem felügyelt lemezként vannak tárolva egy tárfiókban, létrehozható egy felügyelt rendszerkép-erőforrás. A rendszerkép ezután több virtuális gép létrehozásához is használható. A felügyelt lemezképek számlázásáról a [Felügyelt lemezek díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/)című témakörben talál további információt. 

 

## <a name="generalize-the-windows-vm-using-sysprep"></a>Windows rendszerű virtuális gép általánosítása a Sysprep használatával

A Sysprep eltávolítja az összes személyes fiók- és biztonsági információt, majd előkészíti a gépet, hogy képként lehessen használni. A Sysprep programról a [Sysprep – áttekintés című témakörben](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)olvashat.

Győződjön meg arról, hogy a sysprep támogatja a számítógépen futó kiszolgálói szerepköröket. További információt a [Sysprep kiszolgálói szerepkörök és](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) nem támogatott forgatókönyvek támogatása című [témakörben talál.](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios)

> [!IMPORTANT]
> Miután futtatta a Sysprep programot egy virtuális számítógépen, a virtuális gép *általánosnak* minősül, és nem indítható újra. A virtuális gép általánosítása nem vonható vissza. Ha meg kell tartani az eredeti virtuális gép működését, hozzon létre egy [másolatot a virtuális gép,](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) és általánosítani a másolatot. 
>
> Ha azt tervezi, hogy a Virtuális merevlemez (VHD) első alkalommal azure-ba való feltöltése előtt futtatja a Sysprep programot, győződjön meg arról, hogy [előkészítette a virtuális gépet.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)  
> 
> 

A Windows virtuális gép általánosításához kövesse az alábbi lépéseket:

1. Jelentkezzen be a Windows virtuális gépbe.
   
2. Nyisson meg egy parancssorablakot rendszergazdaként. Módosítsa a könyvtárat %windir%\system32\sysprep `sysprep.exe`értékre, majd futtassa a parancsot.
   
3. A **Rendszerelőkészítő eszköz** párbeszédpanelen jelölje be **a Beépített rendszerélmény (OOBE) jelölőnégyzetet,** és jelölje be az **Általánosítás** jelölőnégyzetet.
   
4. A **Leállítási beállítások párbeszédpanelen**válassza a **Leállítás**lehetőséget.
   
5. Válassza **az OK gombot.**
   
    ![A Sysprep indítása](./media/upload-generalized-managed/sysprepgeneral.png)

6. Amikor a Sysprep befejeződik, leállítja a virtuális gép leállítását. Ne indítsa újra a virtuális gépet.

> [!TIP]
> **Nem kötelező** A [DISM](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) használatával optimalizálhatja a lemezképet, és csökkentheti a virtuális gép első rendszerindítási idejét.
>
> A lemezkép optimalizálásához csatlakoztassa a virtuális merevlemezt úgy, hogy duplán kattint rá `/optimize-image` a Windows Intézőben, majd futtassa a DISM-et a paraméterrel.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> Ahol D: a csatlakoztatott VHD elérési útja.
>
> A `DISM /optimize-image` futás nak a virtuális merevlemezen végzett utolsó módosításnak kell lennie. Ha a virtuális merevlemez en az üzembe helyezés előtt módosítja a számítógépet, újra kell futtatnia. `DISM /optimize-image`

## <a name="create-a-managed-image-in-the-portal"></a>Felügyelt lemezkép létrehozása a portálon 

1. A virtuális gép lemezképének kezeléséhez az [Azure Portalon](https://portal.azure.com) kezelheti. Keressen és válasszon **virtuális gépek**lehetőséget.

2. Válassza ki a virtuális gép a listából.

3. A **virtuális gép virtuális géplapján** a felső menüben válassza a **Rögzítés parancsot.**

   Megjelenik **a Kép létrehozása** lap.

4. A **Név mezőbe**fogadja el az előre kitöltött nevet, vagy adja meg a lemezképhez használni kívánt nevet.

5. Az **Erőforráscsoport csoportban**válassza az **Új létrehozása** lehetőséget, és adjon meg egy nevet, vagy válasszon ki egy erőforráscsoportot a legördülő listából.

6. Ha a rendszerkép létrehozása után törölni szeretné a forrásvirtuális gépet, válassza **a virtuális gép automatikus törlése a lemezkép létrehozása után**lehetőséget.

7. Ha azt szeretné, hogy a kép bármely [rendelkezésre állási zónában](../../availability-zones/az-overview.md)használható legyen, válassza **a Be** lehetőséget a **Zóna rugalmasságához.**

8. A kép létrehozásához válassza a **Létrehozás** gombot.

A lemezkép létrehozása után az erőforráscsoport erőforrásainak listájában **image** erőforrásként találhatja meg.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Virtuális gép lemezének létrehozása a PowerShell használatával

 

A lemezkép közvetlenül a virtuális gépről való létrehozása biztosítja, hogy a lemezkép tartalmazza a virtuális géphez társított összes lemezt, beleértve az operációs rendszer lemezét és az adatlemezeket. Ez a példa bemutatja, hogyan hozhat létre felügyelt lemezképet felügyelt lemezeket használó virtuális gépről.

Mielőtt elkezdené, győződjön meg arról, hogy az Azure PowerShell-modul legújabb verzióját használja. A verzió megkereséséhez `Get-Module -ListAvailable Az` futtassa a PowerShellben. Ha frissítenie kell, olvassa [el az Azure PowerShell telepítése Windowson a PowerShellGet segítségével című témakört.](/powershell/azure/install-az-ps) Ha helyileg futtatja a `Connect-AzAccount` PowerShellt, futtassa a kapcsolatot az Azure-ral.


> [!NOTE]
> Ha a lemezképet zónaredundáns tárolóban szeretné tárolni, olyan régióban kell létrehoznia, `-ZoneResilient` amely támogatja a`New-AzImageConfig` [rendelkezésre állási zónákat,](../../availability-zones/az-overview.md) és a paramétert a lemezkép konfigurációjában ( parancs).

Virtuálisgép-lemezkép létrehozásához kövesse az alábbi lépéseket:

1. Hozzon létre néhány változót.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Győződjön meg arról, hogy a virtuális gép fel van terhelve.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Állítsa a virtuális gép állapotát **Generalized**állásba. 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Töltse be a virtuális gépet. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Hozza létre a rendszerkép-konfigurációt.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. Hozza létre a rendszerképet.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Lemezkép létrehozása felügyelt lemezről a PowerShell használatával

Ha csak az operációs rendszer lemezéről szeretne lemezt létrehozni, adja meg a felügyelt lemezazonosítót operációsrendszer-lemezként:

    
1. Hozzon létre néhány változót. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. Szerezd meg a virtuális gép.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. A felügyelt lemez azonosítójának beszereznie.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Hozza létre a rendszerkép-konfigurációt.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Hozza létre a rendszerképet.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Kép létrehozása pillanatképből a Powershell használatával

Felügyelt lemezképet hozhat létre egy általános gép pillanatképéből az alábbi lépésekkel:

    
1. Hozzon létre néhány változót. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Szerezd meg a pillanatfelvételt.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Hozza létre a rendszerkép-konfigurációt.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Hozza létre a rendszerképet.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>Tárfiókot használó virtuális gépről való lemezkép létrehozása

Ha felügyelt lemezképet szeretne létrehozni egy olyan virtuális gépről, amely nem használ felügyelt lemezeket, a storage-fiókban az operációs rendszer virtuális merevlemezének URI-jára van szüksége a következő formátumban: https://*mystorageaccount*.blob.core.windows.net//*vhdfilename.vhd.**vhdcontainer* Ebben a példában a virtuális merevlemez a *mystorageaccount*-, egy *vhdcontainer*nevű tárolóban található, a VHD fájlnév *pedig vhdfilename.vhd.*


1.  Hozzon létre néhány változót.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. A virtuális gép leállítása/felszabadítása.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Jelölje meg a virtuális gép általános.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Hozza létre a lemezképet az általános operációsrendszer-virtuális merevlemez használatával.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>További lépések
- [Virtuális gép létrehozása felügyelt lemezképből.](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)    

