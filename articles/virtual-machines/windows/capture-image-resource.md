---
title: Hozzon létre egy felügyelt képre az Azure-ban |} Microsoft Docs
description: Egy felügyelt képre egy általánosított virtuális gép vagy egy virtuális merevlemez létrehozása az Azure-ban. Lemezképek, a felügyelt lemezeket használó több virtuális gépek létrehozásához használható.
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
ms.date: 03/06/2018
ms.author: cynthn
ms.openlocfilehash: 0b0bd48b95ad9393b4cd82081436e561326df6da
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Egy felügyelt képre egy általánosított virtuális gép létrehozása az Azure-ban

Egy felügyelt képre erőforrás vagy egy felügyelt lemezes, vagy egy nem felügyelt lemezek tárfiókokban tárolt általánosított virtuális gép alapján hozhatók létre. A kép majd több virtuális gépek létrehozásához használható. 

## <a name="generalize-the-windows-vm-using-sysprep"></a>A Windows virtuális gép Sysprep generalize

A Sysprep eltávolítja a személyes adatok, többek között, és előkészíti a számítógépet, hogy képként használni. A Sysprep kapcsolatos részletekért lásd: [hogyan használja a Sysprep: Bevezetés](http://technet.microsoft.com/library/bb457073.aspx).

Győződjön meg arról, hogy a Sysprep által a gépen futó kiszolgálói szerepkörök támogatottak. További információkért lásd: [Sysprep támogatási kiszolgálói szerepköre tekintetében](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Miután a sysprep futtatása egy virtuális gépen, akkor tekinthető *általánosítva* nem indítható újra. A folyamat a virtuális gép normalizálása nincs visszafejthető. Veheti kell tartani az eredeti virtuális gép működik, ha egy [a virtuális gép másolata](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) és a példány generalize. 
>
> Ha előtt a virtuális merevlemez feltöltése az Azure-bA az első alkalommal futtatja a Sysprep, győződjön meg arról, hogy [a virtuális gép előkészített](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a Sysprep futtatása előtt.  
> 
> 

1. Jelentkezzen be a Windows rendszerű virtuális gép.
2. Nyissa meg a parancssort rendszergazdaként. Lépjen be **%windir%\system32\sysprep**, majd futtassa a `sysprep.exe`.
3. Az a **rendszer-előkészítő eszköz** párbeszédpanelen jelölje ki **adja meg a rendszer Out-of-Box élmény (OOBE)**, és győződjön meg arról, hogy a **Generalize** jelölőnégyzet be van jelölve.
4. A **leállítási beállítások**, jelölje be **leállítási**.
5. Kattintson az **OK** gombra.
   
    ![Indítsa el a Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. A Sysprep befejezését követően a virtuális gép leáll. Ne indítsa újra a virtuális Gépet.


## <a name="create-a-managed-image-in-the-portal"></a>Hozzon létre egy felügyelt képre a portálon 

1. Nyissa meg a [portal](https://portal.azure.com).
2. A bal oldali menüben kattintson a virtuális gépeket, és válassza ki a virtuális Gépet a listából.
3. Kattintson a lap a virtuális géphez, a felső menüben **rögzítése**.
3. A **neve**, írja be szeretné használni a lemezkép nevét.
4. A **erőforráscsoport** válassza **hozzon létre új** , és adjon meg egy nevet, vagy válasszon **meglévő** , és válasszon egy erőforráscsoportot használni a legördülő listából.
5. Ha azt szeretné, hogy a forrás virtuális gép törlése után a kép létrehozott, jelölje be **automatikusan törli a lemezkép létrehozása után a virtuális gép**.
6. Ha végzett, kattintson a **Létrehozás** gombra.
16. A lemezkép létrehozása után látni fogja, hogy egy **kép** erőforrás az erőforráscsoport erőforrásainak listája.



## <a name="create-an-image-of-a-vm-using-powershell"></a>A Powershell használatával virtuális gépek lemezkép létrehozása

Lemezkép létrehozása a közvetlenül a virtuális gépről biztosítja, hogy a kép tartalmazza a virtuális gép, beleértve az operációs rendszer lemezének és adatlemezeket társított lemezeket. Ez a példa bemutatja, hogyan hozzon létre egy felügyelt képre egy virtuális gép által használt lemezek kezelését.


Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik-e a legújabb verzióját a AzureRM.Compute PowerShell-modult. A következő parancsot a telepítéshez. (Használata `Get-Module` ellenőrzéséhez melyik verzió van.)

```azurepowershell-interactive
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
További információkért lásd: [Azure PowerShell Versioning](/powershell/azure/overview).


1. Bizonyos változókat létrehozni.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Győződjön meg arról, hogy a virtuális gép fel lettek szabadítva.

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. A virtuális gép állapotának beállítása **Generalized**. 
   
    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Helyezze a virtuális gépet. 

    ```azurepowershell-interactive
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName
    ```

5. A lemezkép-konfiguráció létrehozása.

    ```azurepowershell-interactive
    $image = New-AzureRmImageConfig -Location $location -SourceVirtualMachineId $vm.ID 
    ```
6. A lemezkép létrehozása.

    ```azurepowershell-interactive
    New-AzureRmImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 
## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Lemezkép létrehozása a PowerShell használatával felügyelt lemezes

Ha szeretné létrehozni az operációsrendszer-lemezképet képe, is létrehozhat lemezkép az operációs rendszer lemezeként a felügyelt Lemezazonosítóban megadásával.

    
1. Bizonyos változókat létrehozni. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. A virtuális gép beolvasása.

   ```azurepowershell-interactive
   $vm = Get-AzureRmVm -Name $vmName -ResourceGroupName $rgName
   ```

3. A felügyelt lemezes Azonosítójának beolvasása.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. A lemezkép-konfiguráció létrehozása.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. A lemezkép létrehozása.

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Lemezkép létrehozása egy pillanatképből Powershell használatával

Létrehozhat egy felügyelt képre egy általánosított virtuális gép pillanatképe.

    
1. Bizonyos változókat létrehozni. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. A pillanatkép beolvasása.

   ```azurepowershell-interactive
   $snapshot = Get-AzureRmSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. A lemezkép-konfiguráció létrehozása.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. A lemezkép létrehozása.

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-image-from-a-vhd-in-a-storage-account"></a>Kép készítése a tárfiókban lévő virtuális merevlemez

Hozzon létre egy felügyelt képre tárfiókokban általánosított az operációs rendszer virtuális Merevlemezt. Az URI-azonosítója a VHD-n a tárfiókot, amely a formátum https:// kell*mystorageaccount*.blob.core.windows.net/*tároló*/*vhd_filename.vhd*. A jelen példában használjuk a virtuális merevlemez van *mystorageaccount* nevű tároló *vhdcontainer* és a virtuális merevlemez Fájlnév *osdisk.vhd*.


1.  Első lépésként állítsa be a következő általános paramétereket:

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```
2. A virtuális gép Step\deallocate.

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. A virtuális gép megjelölése általánosítva van.

    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized 
    ```
4.  A lemezkép az operációs rendszer általánosított példányát VHD létrehozása.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>További lépések
- Most is [virtuális gép létrehozása a általánosított felügyelt lemezképből](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

