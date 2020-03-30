---
title: Vhd feltöltése az Azure PowerShell használatával az Azure-ba
description: Megtudhatja, hogyan tölthet fel egy vhd-t egy Azure által felügyelt lemezre, és hogyan másolhat egy felügyelt lemezt régiók között az Azure PowerShell használatával, közvetlen feltöltés útján.
author: roygara
ms.author: rogarana
ms.date: 03/13/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 883fea1e25ded26c35e96d11edd8f417e96db30e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369556"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>Vhd feltöltése az Azure PowerShell használatával az Azure-ba

Ez a cikk bemutatja, hogyan tölthet fel egy vhd a helyi gépről egy Azure felügyelt lemezre. Korábban egy nagyobb részt vevő folyamatot kellett követnie, amely magában foglalta az adatok tárfiókban való előkészítését és a tárfiók kezelését. Most már nem kell kezelnie egy tárfiókot, vagy a fázis adatokat, hogy feltölt egy vhd. Ehelyett hozzon létre egy üres felügyelt lemezt, és töltsön fel egy vhd-t közvetlenül. Ez leegyszerűsíti a helyszíni virtuális gépek feltöltését az Azure-ba, és lehetővé teszi, hogy egy legfeljebb 32 TiB-alapú virtuális merevlemezt töltsön fel közvetlenül egy nagy felügyelt lemezre.

Ha az Azure-beli IaaS virtuális gépek biztonsági mentési megoldását biztosítja, javasoljuk, hogy közvetlen feltöltéssel állítsa vissza az ügyfelek biztonsági mentését a felügyelt lemezekre. Ha egy virtuális merevlemezt tölt fel az Azure-on kívüli gépről, a sebesség a helyi sávszélességtől függ. Ha egy Azure virtuális gép, majd a sávszélesség ugyanaz lesz, mint a szabványos HDD-k.

Jelenleg a közvetlen feltöltés támogatott szabványos HDD, standard SSD és prémium szintű SSD felügyelt lemezek. Ez még nem támogatott ultra SSD.It is yet supported for ultra SSD.

## <a name="prerequisites"></a>Előfeltételek

- Töltse le az [AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)legújabb verzióját.
- [Telepítse az Azure PowerShell-modult.](/powershell/azure/install-Az-ps)
- Ha a helyszíni: Az Azure számára [előkészített](prepare-for-upload-vhd-image.md)rögzített méretű virtuális merevlemez, helyileg tárolt virtuális merevlemez.
- Vagy egy felügyelt lemez az Azure-ban, ha másolási műveletet kíván végrehajtani.

## <a name="create-an-empty-managed-disk"></a>Üres felügyelt lemez létrehozása

A vhd azure-ba való feltöltéséhez létre kell hoznia egy üres felügyelt lemezt, amely ehhez a feltöltési folyamathoz van konfigurálva. Mielőtt létrehozna egyet, további információkat is tudnia kell ezekről a lemezekről.

Ez a fajta felügyelt lemez két egyedi állapottal rendelkezik:

- ReadToUpload, ami azt jelenti, hogy a lemez készen áll a feltöltésfogadására, de nem jött létre [biztonságos hozzáférési aláírás](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS).
- ActiveUpload, ami azt jelenti, hogy a lemez készen áll a feltöltésre, és a SAS jött létre.

Míg ezen állapotok bármelyikében a felügyelt lemez számlázása [a szabványos HDD-díjszabás](https://azure.microsoft.com/pricing/details/managed-disks/)szerint történik, függetlenül a lemez tényleges típusától. Például egy P10 s10-ként kerül számlázásra. Ez addig igaz, amíg meg nem `revoke-access` hívják a felügyelt lemezen, amely szükséges ahhoz, hogy a lemezt egy virtuális géphez csatolja.

Mielőtt létrehozna egy üres szabványos HDD-t a feltöltéshez, szüksége lesz a fájlméretre a feltölteni kívánt vhd bájtban. A példa kód lesz, hogy az Ön számára, `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`de, hogy csináld magad használhatja: . Ez az érték a **-UploadSizeInBytes** paraméter megadásakor használatos.

Most, a helyi shell, hozzon létre egy üres szabványos HDD feltöltési megadásával **feltöltési** beállítást a **-CreateOption** paraméter, valamint a **-UploadSizeInBytes** paraméter a [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) parancsmagban. Ezután hívja a [New-AzDisk-et](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) a lemez létrehozásához:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location 'West US' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName' -Disk $diskconfig
```

Ha prémium SSD-t vagy szabványos SSD-t szeretne feltölteni, cserélje ki **Standard_LRS** **Premium_LRS** vagy **StandardSSD_LRS.** Az Ultra SSD még nem támogatott.

Ezzel létrehozott egy üres felügyelt lemezt, amely a feltöltési folyamathoz van konfigurálva. Ha egy vhd-t szeretne feltölteni a lemezre, szüksége lesz egy írható SAS-ra, hogy a feltöltés célhelyeként hivatkozhass rá.

Az üres en kezelt lemez írható SAS-ének létrehozásához használja a következő parancsot:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName 'myResouceGroup' -DiskName 'myDiskName' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="upload-vhd"></a>Vhd feltöltése

Most, hogy rendelkezik egy SAS-sel az üres felügyelt lemezhez, használhatja a felügyelt lemez beállításához a feltöltési parancs célhelyeként.

Az AzCopy v10 használatával feltöltheti a helyi virtuális merevlemez-fájlt egy felügyelt lemezre a létrehozott SAS URI megadásával.

Ez a feltöltés ugyanaz az átviteli, mint az egyenértékű [szabványos HDD](disks-types.md#standard-hdd). Ha például az S4-nek megfelelő mérettel rendelkezik, legfeljebb 60 MiB/s átviteli fokkal rendelkezik. De ha van egy méret, amely megegyezik az S70, akkor egy átviteli akár 500 MiB / s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Miután a feltöltés befejeződött, és már nem kell több adatot írnia a lemezre, vonja vissza a SAS-t. A SAS visszavonása módosítja a felügyelt lemez állapotát, és lehetővé teszi a lemez virtuális géphez való csatolását.

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="copy-a-managed-disk"></a>Felügyelt lemez másolása

A közvetlen feltöltés leegyszerűsíti a felügyelt lemez másolásának folyamatát is. Másolhat ugyanabban a régióban vagy régiók közötti (másik régióba).

A következő szkript ezt az Ön számára, a folyamat hasonló a korábban leírt lépésekhez, némi különbséggel, mivel egy meglévő lemezzel dolgozik.

> [!IMPORTANT]
> 512-es eltolást kell hozzáadnia, amikor egy felügyelt lemez azure-beli lemez méretének bájtban van megadva. Ennek az az oka, hogy az Azure kihagyja az élőlábat a lemezméretének visszaadásakor. A másolat sikertelen lesz, ha ezt nem teszi meg. A következő szkript már ezt az Ön számára.

Cserélje `<sourceResourceGroupHere>`le `<sourceDiskNameHere>` `<targetDiskNameHere>`a `<targetResourceGroupHere>` `<yourOSTypeHere>` , `<yourTargetLocationHere>` , , , és (egy példa a hely értéke lenne uswest2) az ön értékeit, majd futtassa a következő parancsfájlt, hogy másolja a felügyelt lemezt.

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>További lépések

Most, hogy sikeresen feltöltött egy vhd-t egy felügyelt lemezre, csatolhatja a lemezt egy virtuális géphez, és elkezdheti használni.

Ha meg szeretné tudni, hogyan csatolhat adatlemezt egy virtuális géphez, olvassa el a témáról szóló cikkünket: [Adatlemez csatolása Windows virtuális géphez a PowerShell használatával című témakörben.](attach-disk-ps.md) Ha a lemezt operációs rendszerlemezként szeretné használni, olvassa el a Windows virtuális gép létrehozása speciális lemezről című [témakört.](create-vm-specialized.md#create-the-new-vm)
