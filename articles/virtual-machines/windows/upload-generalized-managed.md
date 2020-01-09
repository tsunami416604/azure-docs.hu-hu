---
title: Virtuális gép létrehozása feltöltött, általánosított virtuális merevlemezről
description: Töltsön fel egy általánosított virtuális merevlemezt az Azure-ba, és használja az új virtuális gépek létrehozásához a Resource Manager-alapú üzemi modellben.
services: virtual-machines-windows
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/12/2019
ms.author: cynthn
ms.openlocfilehash: 3c482caf2407c89ffdb6c55c9184c31e2e3197c4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464949"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Töltse fel az általános VHD-t, és használja az új virtuális gépek létrehozásához az Azure-ban

Ez a cikk végigvezeti a PowerShell használatával egy általánosított virtuális gép Azure-ba való feltöltéséhez, egy rendszerkép létrehozásához a VHD-ből, és létrehoz egy új virtuális gépet a rendszerképből. Egy helyszíni virtualizációs eszközről vagy egy másik felhőből exportált virtuális merevlemezt is feltölthet. A [Managed Disks](managed-disks-overview.md) használata az új virtuális géphez leegyszerűsíti a virtuális gépek felügyeletét, és jobb rendelkezésre állást biztosít, ha a virtuális gép egy rendelkezésre állási csoportba kerül. 

A minta parancsfájlt a [virtuális merevlemezek Azure-ba való feltöltéséhez és egy új virtuális gép létrehozásához](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md)című témakörben tekintheti meg.

## <a name="before-you-begin"></a>Előzetes teendők

- A virtuális merevlemezek Azure-ba való feltöltése előtt kövesse az Azure-ba való [feltöltéshez szükséges Windows VHD vagy VHDX előkészítését](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ismertető témakört.
- A Migrálás megkezdése előtt tekintse át [a Managed Disks áttelepítésének tervét](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) [Managed Disks](managed-disks-overview.md).

 
## <a name="generalize-the-source-vm-by-using-sysprep"></a>A forrás virtuális gép általánosítása a Sysprep használatával

Ha még nem tette meg, az Azure-ba való feltöltés előtt telepítenie kell a virtuális gépet. A Sysprep többek között minden személyes fiókadatot eltávolít, a gépet pedig előkészíti rendszerképként való használatra. A Sysprep eszközről a [Sysprep áttekintése](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)című témakörben olvashat bővebben.

Győződjön meg arról, hogy a Sysprep támogatja a számítógépen futó kiszolgálói szerepköröket. További információ: a [Sysprep-támogatás a kiszolgálói szerepkörökhöz](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Ha azt tervezi, hogy először a virtuális merevlemezt az Azure-ba való feltöltés előtt szeretné futtatni, győződjön meg arról, hogy [előkészítette a virtuális gépet](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Jelentkezzen be a Windows rendszerű virtuális gépre.
2. Nyissa meg a parancsablakot rendszergazdaként. Módosítsa a könyvtárat a%WINDIR%\system32\sysprep értékre, majd futtassa a `sysprep.exe`.
3. A **rendszer-előkészítő eszköz** párbeszédpanelen jelölje be a rendszerindítási folyamat **megadása (OOBE)** jelölőnégyzetet, és győződjön meg arról, hogy az **általánosítás** jelölőnégyzet be van jelölve.
4. A **leállítási beállításoknál**válassza a **Leállítás**lehetőséget.
5. Kattintson az **OK** gombra.
   
    ![A Sysprep elindítása](./media/upload-generalized-managed/sysprepgeneral.png)
6. A Sysprep befejezésekor a rendszer leállítja a virtuális gépet. Ne indítsa újra a virtuális gépet.


## <a name="upload-the-vhd"></a>A VHD feltöltése 

Most már közvetlenül is feltölthet egy virtuális merevlemezt egy felügyelt lemezre. Útmutatásért lásd: [virtuális merevlemez feltöltése az Azure-ba Azure PowerShell használatával](disks-upload-vhd-to-managed-disk-powershell.md).



Miután feltöltötte a VHD-t a felügyelt lemezre, a [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk) használatával kell beolvasnia a felügyelt lemezt.

```azurepowershell-interactive
$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="create-the-image"></a>A rendszerkép létrehozása
Hozzon létre egy felügyelt lemezképet az általánosított operációs rendszer felügyelt lemezéről. Cserélje le a következő értékeket a saját adataira.

Először állítson be néhány változót:

```powershell
$location = 'East US'
$imageName = 'myImage'
$rgName = 'myResourceGroup'
```

Hozza létre a lemezképet a felügyelt lemez használatával.

```azurepowershell-interactive
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsState Generalized `
   -OsType Windows `
   -ManagedDiskId $disk.Id
```

Hozza létre a rendszerképet.

```azurepowershell-interactive
$image = New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```

## <a name="create-the-vm"></a>Virtuális gép létrehozása

Most, hogy már van egy rendszerképe, létrehozhat belőle egy vagy több új virtuális gépet. Ez a példa egy *myVM* nevű virtuális gépet hoz létre a *MyImage*, a *myResourceGroup*-ben.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Image $image.Id `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Következő lépések

Jelentkezzen be az új virtuális gépre. További információ: [Kapcsolódás és bejelentkezés egy Windows rendszerű Azure-beli virtuális gépre](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

