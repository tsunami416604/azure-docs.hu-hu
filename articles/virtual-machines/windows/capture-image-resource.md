---
title: Felügyelt rendszerkép létrehozása az Azure-ban | Microsoft Docs
description: Egy általánosított virtuális gép vagy virtuális merevlemez felügyelt rendszerképének létrehozása az Azure-ban. A rendszerképek használatával több, felügyelt lemezeket használó virtuális gép hozható létre.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 84099a2695d8a26e538f4790b708bf2465ea1a5e
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827690"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Általánosított virtuális gép felügyelt rendszerképének létrehozása az Azure-ban

A felügyelt lemezképek erőforrásai olyan általánosított virtuális gépről (VM) hozhatók létre, amely felügyelt lemezként vagy nem felügyelt lemezként van tárolva egy Storage-fiókban. A rendszerkép ezután több virtuális gép létrehozására is használható. További információ a felügyelt lemezképek számlázásáról: [Managed Disks díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/). 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="generalize-the-windows-vm-using-sysprep"></a>Windows rendszerű virtuális gép általánosítása a Sysprep használatával

A Sysprep eltávolítja az összes személyes fiókot és biztonsági információt, majd előkészíti a gépet képként való használatra. További információ a Sysprep eszközről: a [Sysprep áttekintése](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Győződjön meg arról, hogy a Sysprep támogatja a számítógépen futó kiszolgálói szerepköröket. További információ: a [Sysprep-támogatás a kiszolgálói szerepkörökhöz](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) és a nem [támogatott forgatókönyvekhez](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios).

> [!IMPORTANT]
> Miután futtatta a sysprept egy virtuális gépen, a virtuális gép általánosított minősül, és nem indítható újra. A virtuális gép általánosítása nem vonható vissza. Ha meg kell őriznie az eredeti virtuális gép működését, hozzon létre egy [másolatot a virtuális](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) gépről, és általánosítsa a másolatát. 
>
> Ha a virtuális merevlemez (VHD) első alkalommal történő feltöltése előtt tervezi a Sysprep futtatását, győződjön meg arról, hogy előkészítette [a virtuális gépet](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  
> 
> 

A Windows rendszerű virtuális gép általánosításához kövesse az alábbi lépéseket:

1. Jelentkezzen be a Windows rendszerű virtuális gépre.
   
2. Nyisson meg egy parancssori ablakot rendszergazdaként. Módosítsa a könyvtárat a%WINDIR%\system32\sysprep értékre, majd `sysprep.exe`futtassa a parancsot.
   
3. A rendszerelőkészítő **eszköz** párbeszédpanelen jelölje be a **rendszerszintű felhasználói élmény (OOBE) megadása** jelölőnégyzetet, és jelölje be az **általánosítás** jelölőnégyzetet.
   
4. A leállítási beállításoknál válassza a **Leállítás** **lehetőséget**.
   
5. Kattintson az **OK** gombra.
   
    ![A Sysprep elindítása](./media/upload-generalized-managed/sysprepgeneral.png)

6. A Sysprep befejezésekor a rendszer leállítja a virtuális gépet. Ne indítsa újra a virtuális gépet.


## <a name="create-a-managed-image-in-the-portal"></a>Felügyelt rendszerkép létrehozása a portálon 

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

2. A bal oldali menüben válassza a **virtuális gépek** lehetőséget, majd válassza ki a virtuális gépet a listából.

3. A **virtuális gép virtuálisgép** -lapjának felső menüjében válassza a **rögzítés**lehetőséget.

   Megjelenik a **rendszerkép létrehozása** lap.

4. A **név**mezőben fogadja el az előre megadott nevet, vagy adjon meg egy nevet, amelyet a képhez használni szeretne.

5. Az **erőforráscsoport**területen válassza az **új létrehozása** elemet, adjon meg egy nevet, vagy válassza a **meglévő használata** lehetőséget, és válasszon ki egy erőforráscsoportot, amelyet használni szeretne a legördülő listából.

6. Ha a rendszerkép létrehozása után törölni szeretné a forrás virtuális gépet, jelölje be a **virtuális gép automatikus törlése a rendszerkép létrehozása után**jelölőnégyzetet.

7. Ha azt szeretné, hogy a rendszerképek bármelyik [rendelkezésre állási zónában](../../availability-zones/az-overview.md)használhatók legyenek, válassza **a** be lehetőséget a **zóna rugalmassága**lehetőségnél.

8. A rendszerkép létrehozásához válassza a **Létrehozás** lehetőséget.

9. A rendszerkép létrehozása után képerőforrásként megkeresheti az erőforráscsoport erőforrásainak listáját.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Virtuális gép rendszerképének létrehozása a PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A rendszerkép közvetlenül a virtuális gépről való létrehozása biztosítja, hogy a lemezkép tartalmazza a virtuális géphez társított összes lemezt, beleértve az operációsrendszer-lemezt és az adatlemezeket. Ebből a példából megtudhatja, hogyan hozhat létre felügyelt rendszerképeket egy felügyelt lemezeket használó virtuális gépről.

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a Azure PowerShell modul legújabb verziójával. A verzió megkereséséhez futtassa `Get-Module -ListAvailable Az` a parancsot a PowerShellben. Ha frissítenie kell, tekintse [meg a Azure PowerShell telepítése Windows rendszerre a PowerShellGet](/powershell/azure/install-az-ps)használatával című témakört. Ha helyileg futtatja a PowerShellt, `Connect-AzAccount` futtassa a parancsot az Azure-beli kapcsolatok létrehozásához.


> [!NOTE]
> Ha a rendszerképet a zóna redundáns tárolójában szeretné tárolni, létre kell hoznia egy olyan régióban, amely támogatja a [rendelkezésre állási zónákat](../../availability-zones/az-overview.md) , `-ZoneResilient` és tartalmazza a paramétert a`New-AzImageConfig` rendszerkép-konfigurációban (parancs).

Virtuálisgép-rendszerkép létrehozásához kövesse az alábbi lépéseket:

1. Hozzon létre néhány változót.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Győződjön meg arról, hogy a virtuális gép fel van foglalva.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Állítsa a virtuális gép állapotát **általánosítva**értékre. 
   
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

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Rendszerkép létrehozása felügyelt lemezről a PowerShell használatával

Ha csak az operációsrendszer-lemez lemezképét szeretné létrehozni, akkor a felügyelt lemez AZONOSÍTÓját állítsa be operációsrendszer-lemezként:

    
1. Hozzon létre néhány változót. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. Szerezze be a virtuális gépet.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. A felügyelt lemez AZONOSÍTÓjának beolvasása.

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


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Rendszerkép létrehozása pillanatképből a PowerShell használatával

A következő lépések végrehajtásával hozhat létre felügyelt rendszerképet egy általánosított virtuális gépről készült pillanatképből:

    
1. Hozzon létre néhány változót. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. A pillanatkép beszerzése.

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


## <a name="create-an-image-from-a-vhd-in-a-storage-account"></a>Rendszerkép létrehozása egy virtuális merevlemezről egy Storage-fiókban

Felügyelt rendszerkép létrehozása egy általános operációsrendszer-VHD-fájlból egy Storage-fiókban. Szüksége lesz a virtuális merevlemez URI-azonosítójához a Storage-fiókban, amely a következő formátumban van: https://*mystorageaccount*. blob.Core.Windows.net/*vhdcontainer*/*vhdfilename. vhd*. Ebben a példában a VHD a *mystorageaccount*, egy *vhdcontainer*nevű TÁROLÓban, a VHD-fájl pedig *vhdfilename. vhd*.


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
    
3. A virtuális gépet általánosítva kell megjelölnie.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Hozza létre a rendszerképet az általánosított operációs rendszer VHD-fájljának használatával.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>További lépések
- [Hozzon létre egy virtuális gépet egy felügyelt rendszerképből](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).    

