---
title: Helyszíni általánosított virtuális Merevlemezt a felügyelt Azure virtuális gép létrehozása |} Microsoft Docs
description: Általánosított virtuális merevlemez feltöltése az Azure-ba, és ennek segítségével létrehozott egy új virtuális gépet, a Resource Manager üzembe helyezési modellben.
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
ms.date: 03/26/2018
ms.author: cynthn
ms.openlocfilehash: 6f2f5eae8a4512595457d92d17832cf462b4bec4
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Egy általánosított virtuális merevlemez feltöltéséhez, és beállítsa, hogy létrehozott egy új virtuális gépet az Azure-ban

Ez a témakör végigvezeti egy általánosított virtuális gép virtuális merevlemez feltöltése az Azure-ba, képet készíteni a VHD-t, és hozzon létre egy új virtuális Gépet erről a képről a PowerShell használatával. Egy helyszíni virtualizálási eszköz vagy egy másik felhőben az exportált virtuális merevlemez feltöltése Használatával [kezelt lemezek](managed-disks-overview.md) VM egyszerűbbé teszi az új virtuális gép, és jobb rendelkezésre állást biztosít, amikor a virtuális gép rendelkezésre állási csoportba helyez. 

Ha egy minta parancsfájlt használni kívánt, lásd: [mintaparancsfájl virtuális merevlemez feltöltése az Azure-ba, és hozzon létre egy új virtuális Gépet](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md)

## <a name="before-you-begin"></a>Előkészületek

- Kövesse a virtuális merevlemez feltöltése az Azure-ba, mielőtt [Windows VHD vagy VHDX az Azure-bA feltöltendő előkészítése](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
- Felülvizsgálati [kezelt lemezek az áttelepítés megtervezése](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) az áttelepítés megkezdése előtt [kezelt lemezek](managed-disks-overview.md).
- Ez a cikk a AzureRM 5.6 vagy újabb verziója szükséges. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM.Compute`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.


## <a name="generalize-the-source-vm-using-sysprep"></a>A virtuális gép – forrásként generalize Sysprep használatával

A Sysprep eltávolítja a személyes adatok, többek között, és előkészíti a számítógépet, hogy képként használni. A Sysprep kapcsolatos részletekért lásd: [hogyan használja a Sysprep: Bevezetés](http://technet.microsoft.com/library/bb457073.aspx).

Győződjön meg arról, hogy a Sysprep által a gépen futó kiszolgálói szerepkörök támogatottak. További információkért lásd: [Sysprep támogatási kiszolgálói szerepköre tekintetében](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
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


## <a name="get-the-storage-account"></a>A storage-fiók beszerzése

Kell egy a feltöltött Virtuálisgép-lemezkép tárolásához Azure storage-fiókot. Meglévő tárfiók használata, vagy hozzon létre egy újat. 

Fog használni a virtuális Merevlemezt a virtuális gépek kezelt lemez létrehozása, ha a tárfiók helyének meg kell egyeznie a hely, ahol létrehozni a virtuális Gépet.

A rendelkezésre álló tárfiókok megjelenítéséhez írja be:

```azurepowershell
Get-AzureRmStorageAccount | Format-Table
```

## <a name="upload-the-vhd-to-your-storage-account"></a>A VHD-fájlt feltölti a tárfiók

Használja a [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) parancsmaggal töltse fel a VHD-t a tárfiókban lévő tárolót. Ebben a példában a fájl feltöltését *myVHD.vhd* a *"C:\Users\Public\Documents\Virtual merevlemezek\"*  tárfiókba nevű *mystorageaccount* a a *myResourceGroup* erőforráscsoportot. A fájl bekerülnek a nevű tárolót *mycontainer* és az új fájlnevet lesz *myUploadedVHD.vhd*.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Sikeres ellenőrzés esetén, a következőhöz hasonló választ kap:

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

Attól függően, hogy a hálózati kapcsolat és a VHD-fájl mérete a parancs is igénybe vehet igénybe

### <a name="other-options-for-uploading-a-vhd"></a>Más beállításokat a virtuális merevlemez feltöltése
 
Feltöltheti a virtuális merevlemez a tárfiókhoz, az alábbiak egyikének használatával:

- [AzCopy](http://aka.ms/downloadazcopy)
- [Az Azure Storage másolási Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Az Azure Storage Explorer feltöltése a BLOB](https://azurestorageexplorer.codeplex.com/)
- [Storage Import/Export szolgáltatás REST API-referencia](https://msdn.microsoft.com/library/dn529096.aspx)
-   Ajánlott Import/Export szolgáltatás használata, ha becsült a 7 napnál hosszabb idő feltöltése. Használhat [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) becsléséhez adatok méretét és átviteli egység időpontját. 
    Importálási/exportálási segítségével másolja egy standard szintű tárfiókot. Szüksége lesz a prémium szintű storage-fiókra egy eszköz, például az AzCopy standard tárolási másolja.

> [!IMPORTANT]
> Ha a virtuális merevlemez feltöltése az Azure-bA AzCopy használ, győződjön meg arról, állított [/BlobType:page](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#blobtypeblock--page--append) futtatása előtt töltse fel a parancsfájlt. Ha a cél egy blobot, és ez a beállítás nincs megadva, alapértelmezés szerint az AzCopy egy blokkblob hoz létre.
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Hozzon létre egy felügyelt képre a feltöltött virtuális merevlemezből 

Hozzon létre egy felügyelt képre az operációs rendszer általánosított példányát VHD használatával. Cserélje le az értékeket a saját adatait.


Első lépésként állítsa be az egyes paraméterek:

```powershell
$location = "East US" 
$imageName = "myImage"
```

A lemezkép az operációs rendszer általánosított példányát VHD létrehozása.

```powershell
$imageConfig = New-AzureRmImageConfig `
   -Location $location
$imageConfig = Set-AzureRmImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzureRmImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>Virtuális gép létrehozása

Most, hogy egy lemezképet, létrehozhat egy vagy több új virtuális gépek a lemezképből. Ez a példa létrehoz egy nevű virtuális gép *myVM* a a *myImage*, a a *myResourceGroup*.


```powershell
New-AzureRmVm `
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

Jelentkezzen be az új virtuális gép. További információkért lásd: [csatlakoztatása, és jelentkezzen be a Windowst futtató Azure virtuális gép](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

