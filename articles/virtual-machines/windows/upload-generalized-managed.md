---
title: Felügyelt Azure virtuális gép létrehozása a helyszíni általánosított virtuális merevlemezből |} A Microsoft Docs
description: Általános VHD feltöltése az Azure-ba, és ezzel hozzon létre új virtuális gépeket, a Resource Manager-alapú üzemi modellben.
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
ms.date: 09/25/2018
ms.author: cynthn
ms.openlocfilehash: 93d5cbdb44d3014b547141d59ce96cf607276846
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234612"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Általános VHD feltöltése és ezzel hozzon létre új virtuális gépeket az Azure-ban

Ez a cikk végigvezeti egy általánosított virtuális gép VHD feltöltése az Azure-ba, a virtuális Merevlemezt a rendszerkép létrehozása és egy új virtuális gép létrehozása a rendszerképből PowerShell használatával. Feltölthet egy VHD-t egy helyszíni virtualizálási eszközből vagy egy másik felhőben exportált. Használatával [Managed Disks](managed-disks-overview.md) az új virtuális gép egyszerűbbé teszi a virtuális gép kezelése és jobb rendelkezésre állást biztosít, amikor a virtuális Gépet helyez egy rendelkezésre állási csoportban. 

Mintaparancsprogram lásd [mintaparancsfájl VHD feltöltése az Azure-ba, és hozzon létre egy új virtuális Gépet](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Előkészületek

- Minden olyan VHD feltöltése az Azure-ba, mielőtt követendő [készítse elő a Windows VHD vagy VHDX, az Azure-bA feltöltendő](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Felülvizsgálat [tervezze meg a migrálás a Managed Disks szolgáltatásba](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) az áttelepítés megkezdése előtt [Managed Disks](managed-disks-overview.md).
- Ez a cikk az AzureRM-modul, 5.6-os vagy újabb verziója szükséges. Futtatás ` Get-Module -ListAvailable AzureRM.Compute` a verzió megkereséséhez. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.


## <a name="generalize-the-source-vm-by-using-sysprep"></a>A forrásoldali virtuális gép általánosításához használja a Sysprep

A Sysprep többek között minden személyes fiókadatot eltávolít, a gépet pedig előkészíti rendszerképként való használatra. További információ a Sysprepről: a [Sysprep áttekintése](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Ellenőrizze, hogy a Sysprep a gépen futó kiszolgálói szerepkörök támogatottak. További információkért lásd: [kiszolgálói szerepkörök támogatása a Sysprep](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Ha azt tervezi, a virtuális merevlemez feltöltése az Azure-ban először előtt futtassa a Sysprep eszközt, ellenőrizze, hogy [készíteni a virtuális gép](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Jelentkezzen be a Windows virtuális gép.
2. Nyissa meg a parancsablakot rendszergazdaként. Lépjen a % windir%\system32\sysprep, és futtassa `sysprep.exe`.
3. Az a **rendszer-előkészítő eszköz** párbeszédpanelen jelölje ki **adja meg System Out-of-Box élmény (OOBE)**, és ellenőrizze, hogy a **Generalize** jelölőnégyzet.
4. A **leállítási beállítások**válassza **leállítási**.
5. Kattintson az **OK** gombra.
   
    ![Indítsa el a Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Amikor a Sysprep befejezte a működést a virtuális gép leáll. Ne indítsa újra a virtuális Gépet.


## <a name="get-a-storage-account"></a>Storage-fiók létrehozása

Szüksége lesz egy storage-fiókot az Azure-ban, a feltöltött Virtuálisgép-lemezkép tárolásához. Használjon egy meglévő tárfiókot, vagy hozzon létre egy újat. 

Fogja használni a virtuális merevlemez létrehozása egy felügyelt lemezt egy virtuális gép, ha a tárfiók helye, akkor hoz létre a virtuális gép ugyanazon a helyen kell lennie.

A rendelkezésre álló tár fiókokat jeleníti meg, adja meg:

```azurepowershell
Get-AzureRmStorageAccount | Format-Table
```

## <a name="upload-the-vhd-to-your-storage-account"></a>A virtuális lemezek feltöltése a storage-fiókba

Használja a [Add-AzureRmVhd](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd) parancsmaggal töltse fel a VHD-t a tárfiókban lévő tárolóba. Ez a példa feltölti a fájlt *myVHD.vhd* a *C:\Users\Public\Documents\Virtual merevlemezek\\*  tárfiókhoz nevű *mystorageaccount* a a *myResourceGroup* erőforráscsoportot. A fájlt a tárolóba nevű kerülnek *mycontainer* és az új fájl neve lesz *myUploadedVHD.vhd*.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Sikeres művelet esetén olyan ehhez hasonló választ kap:

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

A hálózati kapcsolat és a VHD-fájl méretétől függően ez a parancs is igénybe vehet igénybe.

### <a name="other-options-for-uploading-a-vhd"></a>Más beállításokat a virtuális merevlemez feltöltése
 
Emellett feltölthet egy virtuális Merevlemezt a tárfiókhoz, használja az alábbi lehetőségek közül:

- [AzCopy](https://aka.ms/downloadazcopy)
- [Az Azure Storage Blob másolásához API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Az Azure Storage Explorer Blobok feltöltése](https://azurestorageexplorer.codeplex.com/)
- [Storage Import/Export szolgáltatás REST API-referencia](https://msdn.microsoft.com/library/dn529096.aspx)
-   Az Import/Export szolgáltatás használatát, ha a 7 napnál hosszabb idő feltöltése, becsült javasoljuk. Használhat [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) megbecsülni az adatok mérete és átviteli egység időpontját. 
    Importálási/exportálási átmásolása a standard szintű tárfiók is használható. Standard szintű storage-ból másolása a premium storage-fiók egy eszköz, például az AzCopy használatával kell.

> [!IMPORTANT]
> Ha a VHD feltöltése az Azure-bA az AzCopy használ, ellenőrizze, hogy meg van [ **/BlobType:page** ](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#blobtypeblock--page--append) a feltöltési parancsfájl futtatása előtt. Ha a cél egy blobot, és ez a beállítás nincs megadva, a AzCopy alapértelmezés szerint a blokkblobok hoz létre.
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Létrehozhat egy felügyelt rendszerképet, a feltöltött virtuális merevlemezből 

Hozzon létre egy felügyelt rendszerképet az általánosított operációs rendszer virtuális merevlemezből. A következő értékeket cserélje le a saját adatait.


Először állítsa be az egyes paraméterek:

```powershell
$location = "East US" 
$imageName = "myImage"
```

A lemezkép a operációs rendszer általánosított virtuális merevlemez létrehozása.

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

Most, hogy már van egy rendszerképe, létrehozhat belőle egy vagy több új virtuális gépet. Ez a példa létrehoz egy virtuális gép nevű *myVM* a *myImage*, a *myResourceGroup*.


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

Jelentkezzen be az új virtuális gépet. További információkért lásd: [csatlakoztatása és bejelentkezés Windows rendszert futtató Azure virtuális gép](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

