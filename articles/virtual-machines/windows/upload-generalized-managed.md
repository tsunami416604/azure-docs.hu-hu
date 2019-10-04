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
ms.openlocfilehash: be3ccfd0c562763d0968398ddb042dc5f07dbdcf
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101561"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Töltse fel az általános VHD-t, és használja az új virtuális gépek létrehozásához az Azure-ban

Ez a cikk végigvezeti a PowerShell használatával egy általánosított virtuális gép Azure-ba való feltöltéséhez, egy rendszerkép létrehozásához a VHD-ből, és létrehoz egy új virtuális gépet a rendszerképből. Egy helyszíni virtualizációs eszközről vagy egy másik felhőből exportált virtuális merevlemezt is feltölthet. A [Managed Disks](managed-disks-overview.md) használata az új virtuális géphez leegyszerűsíti a virtuális gépek felügyeletét, és jobb rendelkezésre állást biztosít, ha a virtuális gép egy rendelkezésre állási csoportba kerül. 

A minta parancsfájlt a [virtuális merevlemezek Azure-ba való feltöltéséhez és egy új virtuális gép létrehozásához](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md)című témakörben tekintheti meg.

## <a name="before-you-begin"></a>Előkészületek

- A virtuális merevlemezek Azure-ba való feltöltése előtt kövesse az Azure-ba való feltöltéshez szükséges [Windows VHD vagy VHDX előkészítését](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ismertető témakört.
- A Migrálás megkezdése előtt tekintse át [a Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) áttelepítésének tervét [Managed Disks](managed-disks-overview.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="generalize-the-source-vm-by-using-sysprep"></a>A forrás virtuális gép általánosítása a Sysprep használatával

A Sysprep többek között minden személyes fiókadatot eltávolít, a gépet pedig előkészíti rendszerképként való használatra. A Sysprep eszközről a [Sysprep áttekintése](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)című témakörben olvashat bővebben.

Győződjön meg arról, hogy a Sysprep támogatja a számítógépen futó kiszolgálói szerepköröket. További információ: a [Sysprep-támogatás a kiszolgálói szerepkörökhöz](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Ha azt tervezi, hogy először a virtuális merevlemezt az Azure-ba való feltöltés előtt szeretné futtatni, győződjön meg arról, hogy előkészítette [a virtuális gépet](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Jelentkezzen be a Windows rendszerű virtuális gépre.
2. Nyissa meg a parancsablakot rendszergazdaként. Módosítsa a könyvtárat a%WINDIR%\system32\sysprep értékre, majd `sysprep.exe`futtassa a parancsot.
3. A **rendszer-előkészítő eszköz** párbeszédpanelen jelölje be a rendszerindítási folyamat **megadása (OOBE)** jelölőnégyzetet, és győződjön meg arról, hogy az **általánosítás** jelölőnégyzet be van jelölve.
4. A leállítási beállításoknál válassza a **Leállítás** **lehetőséget**.
5. Kattintson az **OK** gombra.
   
    ![A Sysprep elindítása](./media/upload-generalized-managed/sysprepgeneral.png)
6. A Sysprep befejezésekor a rendszer leállítja a virtuális gépet. Ne indítsa újra a virtuális gépet.


## <a name="get-a-storage-account"></a>Storage-fiók beszerzése

Szüksége lesz egy Storage-fiókra az Azure-ban a feltöltött VM-rendszerkép tárolásához. Használhat meglévő Storage-fiókot, vagy létrehozhat egy újat. 

Ha a VHD-t a virtuális gép felügyelt lemezének létrehozására fogja használni, a Storage-fiók helyének meg kell egyeznie a virtuális gép létrehozásához használt hellyel.

A rendelkezésre álló Storage-fiókok megjelenítéséhez írja be a következőt:

```azurepowershell
Get-AzStorageAccount | Format-Table
```

## <a name="upload-the-vhd-to-your-storage-account"></a>Töltse fel a VHD-t a Storage-fiókjába

Az [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) parancsmag használatával töltse fel a VHD-t a Storage-fiókjában lévő tárolóba. Ez a példa feltölti a *myVHD. vhd* fájlt a *C:\Users\Public\Documents\Virtual\\ merevlemezekről* a *myResourceGroup* erőforráscsoport *mystorageaccount* nevű Storage-fiókjába. A fájl a *mycontainer* nevű tárolóba kerül, és az új fájlnév *myUploadedVHD. vhd*lesz.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Ha a művelet sikeres, a következőhöz hasonló választ kap:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

A hálózati kapcsolatban és a VHD-fájl méretétől függően ez a parancs hosszabb időt is igénybe vehet.

### <a name="other-options-for-uploading-a-vhd"></a>A virtuális merevlemezek feltöltésének egyéb lehetőségei
 
A VHD-t a következők egyikével is feltöltheti a Storage-fiókjába:

- [AzCopy](https://aka.ms/downloadazcopy)
- [Azure Storage másolási blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Blobok feltöltése Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)
- [Tároló importálási/exportálási szolgáltatásának REST API referenciája](https://msdn.microsoft.com/library/dn529096.aspx)
-   Javasoljuk, hogy az import/export szolgáltatást használja, ha a becsült feltöltési idő hosszabb, mint hét nap. A [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) segítségével megbecsülheti az időt az adatok méretétől és az adatátviteli egységtől. 
    Az importálási/exportálási szolgáltatás a standard Storage-fiókba való másolásra használható. A standard Storage-ból prémium szintű Storage-fiókra kell másolnia egy olyan eszköz használatával, mint a AzCopy.

> [!IMPORTANT]
> Ha a AzCopy használatával tölti fel a VHD-t az Azure-ba, győződjön meg róla, hogy a feltöltési parancsfájl futtatása előtt beállította a [ **/BlobType: lapot**](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs#upload-a-file) . Ha a cél egy blob, és ez a beállítás nincs megadva, alapértelmezés szerint a AzCopy létrehoz egy blokk-blobot.
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Felügyelt rendszerkép létrehozása a feltöltött VHD-ből 

Hozzon létre egy felügyelt rendszerképet az általánosított operációs rendszer VHD-ről. Cserélje le a következő értékeket a saját adataira.


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


## <a name="next-steps"></a>További lépések

Jelentkezzen be az új virtuális gépre. További információ: [Kapcsolódás és bejelentkezés egy Windows rendszerű Azure-beli virtuális gépre](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

