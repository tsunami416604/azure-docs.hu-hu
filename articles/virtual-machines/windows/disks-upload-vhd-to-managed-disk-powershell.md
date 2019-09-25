---
title: VHD feltöltése az Azure-ba a Azure PowerShell használatával
description: Megtudhatja, hogyan tölthet fel egy virtuális merevlemezt egy Azure-beli felügyelt lemezre Azure PowerShell használatával.
author: roygara
ms.author: rogarana
ms.date: 05/06/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 98c0316a3fa513f98031b79eefcedea5a1111539
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266626"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>VHD feltöltése az Azure-ba a Azure PowerShell használatával

Ez a cikk azt ismerteti, hogyan tölthetők fel virtuális merevlemezek a helyi gépről egy Azure-beli felügyelt lemezre. Korábban egy olyan folyamatot kellett követnie, amely a Storage-fiókban tárolt adatait és a Storage-fiók kezelését is magában foglalja. Most már nem kell kezelnie a Storage-fiókot, vagy nem kell megadnia azokat a virtuális merevlemezek feltöltéséhez. Ehelyett létre kell hoznia egy üres felügyelt lemezt, és fel kell töltenie egy virtuális merevlemezt. Ez leegyszerűsíti a helyszíni virtuális gépek Azure-ba való feltöltését, és lehetővé teszi, hogy a virtuális merevlemezt akár 32 TiB-ra feltöltse közvetlenül egy nagy felügyelt lemezre.

Ha biztonsági mentési megoldást biztosít a IaaS virtuális gépek számára az Azure-ban, javasoljuk, hogy a közvetlen feltöltés használatával állítsa vissza az ügyfelek biztonsági mentését a felügyelt lemezekre. Ha a virtuális merevlemezt az Azure-on kívüli gépről tölti fel, a sebesség a helyi sávszélességtől függ. Ha Azure-beli virtuális gépet használ, a sávszélesség ugyanaz lesz, mint a standard HDD-k.

A közvetlen feltöltés jelenleg a standard HDD, a standard SSD és a prémium szintű SSD által felügyelt lemezek esetében támogatott. Ultra SSD-k esetén még nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

- Töltse le a [AzCopy v10 legújabb verzióját](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Azure PowerShell modul telepítése](/powershell/azure/install-Az-ps).
- Helyileg tárolt vhd-fájl.

## <a name="create-an-empty-managed-disk"></a>Üres felügyelt lemez létrehozása

A virtuális merevlemez Azure-ba való feltöltéséhez létre kell hoznia egy üres felügyelt lemezt, amely kifejezetten ehhez a feltöltési folyamathoz van konfigurálva. Mielőtt létrehoz egyet, néhány további információra van szüksége a lemezekről.

Az ilyen felügyelt lemez két egyedi állapottal rendelkezik:

- ReadToUpload, ami azt jelenti, hogy a lemez készen áll a feltöltésre, de nem jött létre [biztonságos hozzáférési aláírás](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS).
- ActiveUpload, ami azt jelenti, hogy a lemez készen áll a feltöltés fogadására, és a SAS létrejött.

Ezen állapotok bármelyikében a felügyelt lemez a [standard szintű HDD díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/)alapján kerül kiszámlázásra, a lemez tényleges típusától függetlenül. Egy P10 például egy S10-ként lesz kiszámlázva. Ez csak `revoke-access` akkor lesz érvényes, ha a felügyelt lemezre van meghívva, ami szükséges ahhoz, hogy csatlakoztatni lehessen a lemezt egy virtuális géphez.

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

## <a name="next-steps"></a>További lépések

Most, hogy sikeresen feltöltött egy virtuális merevlemezt egy felügyelt lemezre, csatlakoztathatja a lemezt egy virtuális géphez, és megkezdheti a használatát.

Ha szeretné megtudni, hogyan csatolhat lemezeket egy virtuális géphez, tekintse meg a tárgyban található cikket: [Adatlemez csatlakoztatása egy Windows rendszerű virtuális géphez a PowerShell használatával](attach-disk-ps.md).
