---
title: Felügyelt Azure-beli virtuális gép létrehozása általánosított helyszíni virtuális merevlemezről | Microsoft Docs
description: Töltsön fel egy általánosított virtuális merevlemezt az Azure-ba, és használja az új virtuális gépek létrehozásához a Resource Manager-alapú üzemi modellben.
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
ms.topic: article
ms.date: 09/25/2018
ms.author: cynthn
ms.openlocfilehash: 6382a39e67805eb9bddb356a7b76205a82f3f7c2
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553466"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Töltse fel az általános VHD-t, és használja az új virtuális gépek létrehozásához az Azure-ban

Ez a cikk végigvezeti a PowerShell használatával egy általánosított virtuális gép Azure-ba való feltöltéséhez, egy rendszerkép létrehozásához a VHD-ből, és létrehoz egy új virtuális gépet a rendszerképből. Egy helyszíni virtualizációs eszközről vagy egy másik felhőből exportált virtuális merevlemezt is feltölthet. A [Managed Disks](managed-disks-overview.md) használata az új virtuális géphez leegyszerűsíti a virtuális gépek felügyeletét, és jobb rendelkezésre állást biztosít, ha a virtuális gép egy rendelkezésre állási csoportba kerül. 

A minta parancsfájlt a [virtuális merevlemezek Azure-ba való feltöltéséhez és egy új virtuális gép létrehozásához](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md)című témakörben tekintheti meg.

## <a name="before-you-begin"></a>Előzetes teendők

- A virtuális merevlemezek Azure-ba való feltöltése előtt kövesse az Azure-ba való [feltöltéshez szükséges Windows VHD vagy VHDX előkészítését](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ismertető témakört.
- A Migrálás megkezdése előtt tekintse át [a Managed Disks áttelepítésének tervét](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) [Managed Disks](managed-disks-overview.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="generalize-the-source-vm-by-using-sysprep"></a>A forrás virtuális gép általánosítása a Sysprep használatával

A Sysprep többek között minden személyes fiókadatot eltávolít, a gépet pedig előkészíti rendszerképként való használatra. A Sysprep eszközről a [Sysprep áttekintése](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)című témakörben olvashat bővebben.

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


## <a name="upload-the-vhd-to-your-storage-account"></a>Töltse fel a VHD-t a Storage-fiókjába

Most már közvetlenül is feltölthet egy virtuális merevlemezt egy felügyelt lemezre. Útmutatásért lásd: [virtuális merevlemez feltöltése az Azure-ba Azure PowerShell használatával](disks-upload-vhd-to-managed-disk-powershell.md).


## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Felügyelt rendszerkép létrehozása a feltöltött VHD-ből 

Hozzon létre egy felügyelt lemezképet az általánosított operációs rendszer felügyelt lemezéről. Cserélje le a következő értékeket a saját adataira.


Először állítsa be a paramétereket:

```powershell
$location = "East US" 
$imageName = "myImage"
```

Hozza létre a rendszerképet a általánosított operációs rendszer VHD-fájljának használatával.

```powershell
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzImage `
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
    -ImageName $imageName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Következő lépések

Jelentkezzen be az új virtuális gépre. További információ: [Kapcsolódás és bejelentkezés egy Windows rendszerű Azure-beli virtuális gépre](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

