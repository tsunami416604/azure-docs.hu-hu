---
title: VHD feltöltése az Azure-ba a Azure PowerShell használatával
description: Megtudhatja, hogyan tölthet fel egy virtuális merevlemezt egy Azure felügyelt lemezre, és hogyan másolhat a felügyelt lemezeket régiók között a Azure PowerShell használatával.
author: roygara
ms.author: rogarana
ms.date: 05/06/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: de9975151270ccce8d4a7abd58210c6550d40464
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720343"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>VHD feltöltése az Azure-ba a Azure PowerShell használatával

Ez a cikk azt ismerteti, hogyan tölthetők fel virtuális merevlemezek a helyi gépről egy Azure-beli felügyelt lemezre. Korábban egy olyan folyamatot kellett követnie, amely a Storage-fiókban tárolt adatait és a Storage-fiók kezelését is magában foglalja. Most már nem kell kezelnie a Storage-fiókot, vagy nem kell megadnia azokat a virtuális merevlemezek feltöltéséhez. Ehelyett létre kell hoznia egy üres felügyelt lemezt, és fel kell töltenie egy virtuális merevlemezt. Ez leegyszerűsíti a helyszíni virtuális gépek Azure-ba való feltöltését, és lehetővé teszi, hogy a virtuális merevlemezt akár 32 TiB-ra feltöltse közvetlenül egy nagy felügyelt lemezre.

Ha biztonsági mentési megoldást biztosít a IaaS virtuális gépek számára az Azure-ban, javasoljuk, hogy a közvetlen feltöltés használatával állítsa vissza az ügyfelek biztonsági mentését a felügyelt lemezekre. Ha a virtuális merevlemezt az Azure-on kívüli gépről tölti fel, a sebesség a helyi sávszélességtől függ. Ha Azure-beli virtuális gépet használ, a sávszélesség ugyanaz lesz, mint a standard HDD-k.

A közvetlen feltöltés jelenleg a standard HDD, a standard SSD és a prémium szintű SSD által felügyelt lemezek esetében támogatott. Ultra SSD-k esetén még nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

- Töltse le a [AzCopy v10 legújabb verzióját](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Azure PowerShell modul telepítése](/powershell/azure/install-Az-ps).
- Ha a virtuális merevlemezt a PEM-ról szeretné feltölteni: Az Azure-hoz [készült](prepare-for-upload-vhd-image.md), helyileg tárolt virtuális merevlemez.
- Vagy egy felügyelt lemezt az Azure-ban, ha egy másolási műveletet kíván végrehajtani.

## <a name="create-an-empty-managed-disk"></a>Üres felügyelt lemez létrehozása

A virtuális merevlemez Azure-ba való feltöltéséhez létre kell hoznia egy üres felügyelt lemezt, amely ehhez a feltöltési folyamathoz van konfigurálva. Mielőtt létrehoz egyet, néhány további információra van szüksége a lemezekről.

Az ilyen felügyelt lemez két egyedi állapottal rendelkezik:

- ReadToUpload, ami azt jelenti, hogy a lemez készen áll a feltöltésre, de nem jött létre [biztonságos hozzáférési aláírás](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS).
- ActiveUpload, ami azt jelenti, hogy a lemez készen áll a feltöltés fogadására, és a SAS létrejött.

Ezen állapotok bármelyikében a felügyelt lemez a [standard szintű HDD díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/)alapján kerül kiszámlázásra, a lemez tényleges típusától függetlenül. Egy P10 például egy S10-ként lesz kiszámlázva. Ez csak `revoke-access` akkor lesz érvényes, ha a felügyelt lemezre van meghívva, ami szükséges ahhoz, hogy csatlakoztatni lehessen a lemezt egy virtuális géphez.

Mielőtt üres szabványos HDD-t hozna létre a feltöltéshez, szüksége lesz a feltölteni kívánt vhd-fájl mérete bájtban. A példában a kód a következőt fogja használni: `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`. Ez az érték a **-UploadSizeInBytes** paraméter megadásakor használatos.

Most a helyi rendszerhéjon hozzon létre egy üres szabványos HDD-t a feltöltéshez, és adja meg a **feltöltési** beállítást a **-CreateOption** paraméterben, valamint a **-UploadSizeInBytes** paramétert a [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) parancsmagban. Ezután hívja a [New-AzDisk-](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) t a lemez létrehozásához:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location 'West US' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName' -Disk $diskconfig
```

Ha prémium szintű SSD-t vagy standard SSD-t szeretne feltölteni, cserélje le a **Standard_LRS** -t a **Premium_LRS** vagy a **StandardSSD_LRS**értékre. A ultra SSD még nem támogatott.

Ezzel létrehozott egy üres felügyelt lemezt, amely a feltöltési folyamathoz van konfigurálva. A virtuális merevlemezek lemezre való feltöltéséhez írható SAS szükséges, hogy a feltöltés céljának megfelelően hivatkozzon legyen rá.

Az üres felügyelt lemezre írható SAS létrehozásához használja a következő parancsot:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName 'myResouceGroup' -DiskName 'myDiskName' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="upload-vhd"></a>VHD feltöltése

Most, hogy rendelkezik egy SAS-vel az üres felügyelt lemezhez, használhatja azt a felügyelt lemez beállításához a feltöltési parancs céljának megfelelően.

A AzCopy v10 használatával töltse fel a helyi VHD-fájlt egy felügyelt lemezre úgy, hogy megadja a létrehozott SAS URI-t.

Ez a feltöltés azonos átviteli sebességgel rendelkezik, mint a [szabványos HDD](disks-types.md#standard-hdd). Ha például egy olyan mérettel rendelkezik, amely megfelel az S4 értéknek, akkor akár 60 MiB/s sebességű átviteli sebességgel fog rendelkezni. Ha azonban egy olyan mérettel rendelkezik, amely megfelel a S70, akkor akár 500 MiB/s sebességű átviteli sebességgel fog rendelkezni.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas --blob-type PageBlob
```

Ha a sas lejár a feltöltés során, és még nem hívta `revoke-access` meg, egy új sas-t is beszerezhet a feltöltés folytatásához a használatával. `grant-access`

Miután a feltöltés befejeződött, és többé nem kell további adatokra írnia a lemezt, vonja vissza a SAS-t. Az SAS visszavonása megváltoztatja a felügyelt lemez állapotát, és lehetővé teszi a lemez csatlakoztatását egy virtuális géphez.

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="copy-a-managed-disk"></a>Felügyelt lemez másolása

A közvetlen feltöltés emellett leegyszerűsíti a felügyelt lemezek másolásának folyamatát. A másolást elvégezheti ugyanazon a régión vagy régión belül (egy másik régióba).

A következő szkript ezt elvégzi Önnek, a folyamat hasonló a korábban ismertetett lépésekhez, néhány különbséggel, mivel meglévő lemezzel dolgozik.

> [!IMPORTANT]
> Ha az Azure-ból felügyelt lemez mérete bájtban van megadva, akkor 512 eltolást kell hozzáadnia. Ennek az az oka, hogy az Azure kihagyja a láblécet a lemez méretének visszaadása során. Ha ezt nem teszi meg, a másolás sikertelen lesz. A következő szkript ezt már elvégezte Önnek.

Cserélje le a `<sourceResourceGroupHere>`, `<sourceDiskNameHere>`, `<targetDiskNameHere>`, `<targetResourceGroupHere>`, `<yourOSTypeHere>` és `<yourTargetLocationHere>` (például a uswest2) értékeit, majd futtassa a következő szkriptet a felügyelt lemez másolásához.

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

Most, hogy sikeresen feltöltött egy virtuális merevlemezt egy felügyelt lemezre, csatlakoztathatja a lemezt egy virtuális géphez, és megkezdheti a használatát.

Ha szeretné megtudni, hogyan csatolhat lemezeket egy virtuális géphez, tekintse meg a tárgyban található cikket: [Adatlemez csatlakoztatása egy Windows rendszerű virtuális géphez a PowerShell használatával](attach-disk-ps.md).
