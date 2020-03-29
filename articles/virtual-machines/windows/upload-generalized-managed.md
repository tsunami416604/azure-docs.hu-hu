---
title: Virtuális gép létrehozása feltöltött általános virtuális merevlemezről
description: Töltsön fel egy általános virtuális merevlemezt az Azure-ba, és használja azt új virtuális gépek létrehozásához az Erőforrás-kezelő telepítési modelljében.
services: virtual-machines-windows
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/12/2019
ms.author: cynthn
ms.openlocfilehash: 3c482caf2407c89ffdb6c55c9184c31e2e3197c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464949"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Általános VHD feltöltése, majd azzal új Azure-beli virtuális gép létrehozása

Ez a cikk bemutatja a PowerShell használatával egy általános virtuális gép virtuális merevlemezének feltöltésével az Azure-ba, hozzon létre egy lemezképet a virtuális merevlemezről, és hozzon létre egy új virtuális gép az adott lemezképből. Feltölthet egy helyszíni virtualizációs eszközről vagy egy másik felhőből exportált virtuális merevlemezt. [Felügyelt lemezek](managed-disks-overview.md) használata az új virtuális gép leegyszerűsíti a virtuális gép kezelése, és jobb rendelkezésre állást biztosít, ha a virtuális gép egy rendelkezésre állási csoportba kerül. 

Mintaparancsfájlról a [Mintaparancsfájl az Azure-ba való feltöltéshez és egy új virtuális gép létrehozásáról című témakörben látható.](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md)

## <a name="before-you-begin"></a>Előkészületek

- Mielőtt bármilyen virtuális merevlemezt feltöltene az Azure-ba, kövesse [a Windows VHD vagy VHDX előkészítése az Azure-ba való feltöltéshez.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
- Tekintse át [a Felügyelt lemezekre való áttelepítés tervét,](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) mielőtt a Felügyelt lemezekre való [áttelepítést elkezdené.](managed-disks-overview.md)

 
## <a name="generalize-the-source-vm-by-using-sysprep"></a>A forrásvirtuális gép általánosítása a Sysprep programmal

Ha még nem tette meg, a virtuális gép feltöltése előtt a virtuális merevlemez az Azure-ba kell Sysprep. A Sysprep többek között minden személyes fiókadatot eltávolít, a gépet pedig előkészíti rendszerképként való használatra. A Sysprep programról a [Sysprep – áttekintés című témakörben olvashat.](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)

Győződjön meg arról, hogy a sysprep támogatja a számítógépen futó kiszolgálói szerepköröket. További információt a [Sysprep kiszolgálói szerepkörök támogatása című témakörben talál.](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Ha azt tervezi, hogy a Virtuális merevlemez első feltöltése előtt futtatja a Sysprep programot, győződjön meg arról, hogy [előkészítette a virtuális gépét.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 
> 
> 

1. Jelentkezzen be a Windows rendszerű virtuális gépre.
2. Nyissa meg a parancsablakot rendszergazdaként. Módosítsa a könyvtárat %windir%\system32\sysprep `sysprep.exe`értékre, majd futtassa a parancsot.
3. A **Rendszerelőkészítő eszköz** párbeszédpanelen jelölje be a Kezdő élmény **megadása (OOBE) jelölőnégyzetet,** és győződjön meg arról, hogy a **Generalize** jelölőnégyzet engedélyezve van.
4. A **Leállítási beállítások párbeszédpanelen**válassza a **Leállítás**lehetőséget.
5. Válassza **az OK gombot.**
   
    ![A Sysprep indítása](./media/upload-generalized-managed/sysprepgeneral.png)
6. Amikor a Sysprep befejeződik, leállítja a virtuális gépet. Ne indítsa újra a virtuális gépet.


## <a name="upload-the-vhd"></a>A virtuális merevlemez feltöltése 

Most már feltöltheti a Virtuális merevlemezt közvetlenül egy felügyelt lemezre. További információt a [VHD feltöltése az Azure-ba az Azure PowerShell használatával (VHD feltöltése) témakörben talál.](disks-upload-vhd-to-managed-disk-powershell.md)



Miután a virtuális merevlemez t a felügyelt lemezre feltöltötte, a felügyelt lemez bekéseléséhez a [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk) segítségével kell beszereznie.

```azurepowershell-interactive
$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="create-the-image"></a>A rendszerkép létrehozása
Felügyelt lemezképet hozhat létre az általános operációsrendszer-kezelt lemezről. Cserélje le a következő értékeket a saját adataira.

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

Most, hogy már van egy rendszerképe, létrehozhat belőle egy vagy több új virtuális gépet. Ez a példa létrehoz egy *myVM* nevű virtuális gép a *myImage*, a *myResourceGroup*.


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


## <a name="next-steps"></a>További lépések

Jelentkezzen be az új virtuális gépre. További információt a [Csatlakozás és bejelentkezés Windows rendszerű Azure-alapú virtuális gépre](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)című témakörben talál. 

