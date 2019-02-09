---
title: Létrehozhat egy felügyelt rendszerképet az Azure-ban |} A Microsoft Docs
description: Hozzon létre egy felügyelt rendszerképet egy általánosított virtuális gép vagy VHD-t az Azure-ban. Lemezképek több felügyelt lemezeket használó virtuális gépek létrehozásához használható.
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
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: a5e3fbc3369f19af8d93e23d669a4449ab3d414c
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55980584"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Az Azure-beli általánosított virtuális gép felügyelt rendszerképének létrehozása

Egy felügyelt rendszerképet erőforrás az általánosított virtuális gép (VM) tárolt felügyelt lemez vagy a storage-fiókban lévő nem felügyelt lemez hozható létre. A lemezkép ezután több virtuális gép létrehozására használható. Információk a felügyelt lemezképek számlázása, lásd: [Managed Disks díjszabását ismertető](https://azure.microsoft.com/pricing/details/managed-disks/). 

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="generalize-the-windows-vm-using-sysprep"></a>Windows rendszerű virtuális gép általánosítása a Sysprep használatával

A Sysprep eltávolítja a személyes fiókot és a biztonsági adatokat, és ezután előkészíti a gép képként használni. További információ a Sysprep eszközről: [Sysprep áttekintése](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Ellenőrizze, hogy a Sysprep a gépen futó kiszolgálói szerepkörök támogatottak. További információkért lásd: [kiszolgálói szerepkörök támogatása a Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> A virtuális gép a Sysprep futtatása után a virtuális gép számít *általánosítva* és nem indítható újra. A virtuális gép általánosítása nem vonható vissza. Ha kell tartani az eredeti virtuális gép működik, létre kell hoznia egy [a virtuális gép másolatának](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) és általánosítása példányát. 
>
> Ha azt tervezi, mielőtt feltöltené a virtuális merevlemez (VHD) az Azure-ba, először futtassa a Sysprep eszközt, ellenőrizze, hogy [készíteni a virtuális gép](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  
> 
> 

A Windows virtuális gép általánosításához, kövesse az alábbi lépéseket:

1. Jelentkezzen be a Windows virtuális gép.
   
2. Nyisson meg egy parancssori ablakot rendszergazdaként. Lépjen a % windir%\system32\sysprep, és futtassa `sysprep.exe`.
   
3. Az a **rendszer-előkészítő eszköz** párbeszédpanelen jelölje ki **adja meg System Out-of-Box élmény (OOBE)** , és válassza ki a **Generalize** jelölőnégyzetet.
   
4. A **leállítási beállítások**válassza **leállítási**.
   
5. Kattintson az **OK** gombra.
   
    ![Indítsa el a Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. A Sysprep befejezését követően a virtuális gép leáll. Ne indítsa újra a virtuális Gépet.


## <a name="create-a-managed-image-in-the-portal"></a>Létrehozhat egy felügyelt rendszerképet, a portálon 

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

2. A bal oldali menüben válassza **virtuális gépek** , és válassza ki a virtuális Gépet a listából.

3. Az a **virtuális gép** lapon a virtuális gép, a felső menüben válassza ki **rögzítése**.

   A **kép létrehozása** lap jelenik meg.

4. A **neve**, vagy fogadja el az előre megadott nevet, vagy adjon meg egy nevet, amely a lemezképet használni szeretne.

5. A **erőforráscsoport**, válassza ki **új létrehozása** és adjon meg egy nevet, vagy bejelölheti **meglévő használata** és a egy erőforráscsoportot, használja a legördülő listából válassza ki.

6. Ha azt szeretné, törölje a forrás virtuális Gépet, ha a rendszerkép lett létrehozva, jelölje be **automatikus törlése a rendszerkép létrehozása után a virtuális gép**.

7. Ha azt szeretné, hogy a kép használata bármely [rendelkezésre állási zónában](../../availability-zones/az-overview.md)válassza **a** a **zónarugalmasságot**.

8. Válassza ki **létrehozás** rendszerkép létrehozásához.

9. A lemezkép létrehozása után annak legyen egy **kép** erőforrásra a listában, az erőforráscsoportban lévő erőforrásokat.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Hozzon létre egy rendszerképet a virtuális gépek Powershell-lel

Lemezkép létrehozása a virtuális gépről közvetlenül biztosítja, hogy a rendszerkép tartalmazza a virtuális Géphez, többek között az operációsrendszer-lemez és bármely adatlemez társított lemezeket. Ez a példa bemutatja, hogyan hozhat létre egy felügyelt rendszerképet, hogy a használt felügyelt lemezek virtuális gépből.


Mielőtt elkezdené, győződjön meg arról, hogy a legújabb verzióját az AzureRM.Compute PowerShell-modul, amely 5.7.0 verziójúnak kell lennie, vagy később. A verzió megkereséséhez futtassa `Get-Module -ListAvailable AzureRM.Compute` a PowerShellben. Ha frissíteni szeretne, olvassa el [Azure PowerShell telepítése a Windows a Powershellgettel](/powershell/azure/azurerm/install-az-ps). Ha Ön helyileg futtatja a Powershellt, futtassa `Connect-AzAccount` kapcsolat létrehozása az Azure-ral.


> [!NOTE]
> Ha szeretné tárolni a rendszerképet a zónaredundáns tárolás, egy régióban, amely támogatja a létrehozásához szükséges [rendelkezésre állási zónák](../../availability-zones/az-overview.md) , és tartalmazzák a `-ZoneResilient` paramétert a rendszerkép-konfiguráció (`New-AzImageConfig` parancsot).

Virtuálisgép-rendszerkép létrehozásához kövesse az alábbi lépéseket:

1. Hozzon létre változókat.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Győződjön meg arról, hogy a virtuális gép fel lett szabadítva.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. A virtuális gép állapotának beállítása **Általánosítottra**. 
   
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

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Rendszerkép létrehozása egy felügyelt lemezről a PowerShell használatával

Ha azt szeretné, hozzon létre egy rendszerképet az operációsrendszer-lemez, adja meg a felügyelt lemez azonosítója az operációsrendszer-lemez:

    
1. Hozzon létre változókat. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. A virtuális gép lekérése.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. A felügyelt lemez Azonosítójának lekéréséhez.

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


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Rendszerkép létrehozása pillanatképből a Powershell használatával

Egy általánosított virtuális gép pillanatképe a következő lépésekkel hozhat létre egy felügyelt rendszerképet:

    
1. Hozzon létre változókat. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. A pillanatkép beolvasása.

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


## <a name="create-an-image-from-a-vhd-in-a-storage-account"></a>Rendszerkép létrehozása a tárfiókban lévő virtuális merevlemezből

Hozzon létre egy felügyelt rendszerképet általános virtuális merevlemezből operációsrendszer-storage-fiókban. Szükséges URI-ját a VHD-t a tárfiókban, amely a következő formátumban: https://*mystorageaccount*.blob.core.windows.net/*vhdcontainer* /  *vhdfilename.vhd*. Ebben a példában a virtuális merevlemez van *mystorageaccount*, nevű tárolóban *vhdcontainer*, és a virtuális merevlemez Fájlnév *vhdfilename.vhd*.


1.  Hozzon létre változókat.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Állítsa le és felszabadítja a virtuális Gépet.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. A virtuális gép megjelölése általánosítva.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  A rendszerkép létrehozása a operációs rendszer általánosított virtuális merevlemez használatával.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>További lépések
- [Virtuális gép létrehozása felügyelt rendszerképből](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).    

