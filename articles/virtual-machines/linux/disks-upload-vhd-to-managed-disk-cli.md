---
title: Virtuális merevlemez feltöltése az Azure CLI használatával
description: Ismerje meg, hogyan tölthet fel egy vhd-t egy Azure felügyelt lemezre, és hogyan másolhat egy felügyelt lemezt a régiók között az Azure CLI használatával, közvetlen feltöltés útján.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 03/13/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: d89a4279d425e4b12e92aae81edfd6c1514c3eef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062667"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>Vhd feltöltése az Azure CLI használatával az Azure-ba

Ez a cikk bemutatja, hogyan tölthet fel egy vhd a helyi gépről egy Azure felügyelt lemezre. Korábban egy nagyobb részt vevő folyamatot kellett követnie, amely magában foglalta az adatok tárfiókban való előkészítését és a tárfiók kezelését. Most már nem kell kezelnie egy tárfiókot, vagy a fázis adatokat, hogy feltölt egy vhd. Ehelyett hozzon létre egy üres felügyelt lemezt, és töltsön fel egy vhd-t közvetlenül. Ez leegyszerűsíti a helyszíni virtuális gépek feltöltését az Azure-ba, és lehetővé teszi, hogy egy legfeljebb 32 TiB-alapú virtuális merevlemezt töltsön fel közvetlenül egy nagy felügyelt lemezre.

Ha az Azure-beli IaaS virtuális gépek biztonsági mentési megoldását biztosítja, javasoljuk, hogy közvetlen feltöltéssel állítsa vissza az ügyfelek biztonsági mentését a felügyelt lemezekre. Ha egy virtuális merevlemezt tölt fel az Azure-on kívüli gépről, a sebesség a helyi sávszélességtől függ. Ha egy Azure virtuális gép, majd a sávszélesség ugyanaz lesz, mint a szabványos HDD-k.

Jelenleg a közvetlen feltöltés támogatott szabványos HDD, standard SSD és prémium szintű SSD felügyelt lemezek. Ez még nem támogatott ultra SSD.It is yet supported for ultra SSD.

## <a name="prerequisites"></a>Előfeltételek

- Töltse le az [AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)legújabb verzióját.
- [Telepítse az Azure CLI](/cli/azure/install-azure-cli).
- Helyileg tárolt vhd fájl
- Ha egy vhd-t kíván feltölteni a helyszíni: Az [Azure-hoz készített](../windows/prepare-for-upload-vhd-image.md)rögzített méretű vhd, helyileg tárolva.
- Vagy egy felügyelt lemez az Azure-ban, ha másolási műveletet kíván végrehajtani.

## <a name="create-an-empty-managed-disk"></a>Üres felügyelt lemez létrehozása

A vhd azure-ba való feltöltéséhez létre kell hoznia egy üres felügyelt lemezt, amely ehhez a feltöltési folyamathoz van konfigurálva. Mielőtt létrehozna egyet, további információkat is tudnia kell ezekről a lemezekről.

Ez a fajta felügyelt lemez két egyedi állapottal rendelkezik:

- ReadToUpload, ami azt jelenti, hogy a lemez készen áll a feltöltésfogadására, de nem jött létre [biztonságos hozzáférési aláírás](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS).
- ActiveUpload, ami azt jelenti, hogy a lemez készen áll a feltöltésre, és a SAS jött létre.

Míg ezen állapotok bármelyikében a felügyelt lemez számlázása [a szabványos HDD-díjszabás](https://azure.microsoft.com/pricing/details/managed-disks/)szerint történik, függetlenül a lemez tényleges típusától. Például egy P10 s10-ként kerül számlázásra. Ez addig igaz, amíg meg nem `revoke-access` hívják a felügyelt lemezen, amely szükséges ahhoz, hogy a lemezt egy virtuális géphez csatolja.

Mielőtt létrehozhatna egy üres szabványos HDD-t a feltöltéshez, bájtban kell rendelkeznie a feltölteni kívánt vhd fájlméretével. Ahhoz, hogy ezt, `wc -c <yourFileName>.vhd` `ls -al <yourFileName>.vhd`akkor vagy vagy . Ez az érték a **--upload-size-bytes** paraméter megadásakor használatos.

Hozzon létre egy üres szabványos merevlemezt a feltöltéshez a **--for-upload** paraméter és a [lemezlétrehozási](/cli/azure/disk#az-disk-create) parancsmag **--upload-size-byte** paraméterének megadásával:

```azurecli
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Ha prémium SSD-t vagy szabványos SSD-t szeretne feltölteni, cserélje ki **standard_lrs** **premium_LRS** vagy **standardssd_lrs.** Az Ultra SSD még nem támogatott.

Ezzel létrehozott egy üres felügyelt lemezt, amely a feltöltési folyamathoz van konfigurálva. Ha egy vhd-t szeretne feltölteni a lemezre, szüksége lesz egy írható SAS-ra, hogy a feltöltés célhelyeként hivatkozhass rá.

Az üres en kezelt lemez írható SAS-ének létrehozásához használja a következő parancsot:

```azurecli
az disk grant-access -n mydiskname -g resourcegroupname --access-level Write --duration-in-seconds 86400
```

A minta visszaadott értéke:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-vhd"></a>Vhd feltöltése

Most, hogy rendelkezik egy SAS-sel az üres felügyelt lemezhez, használhatja a felügyelt lemez beállításához a feltöltési parancs célhelyeként.

Az AzCopy v10 használatával feltöltheti a helyi virtuális merevlemez-fájlt egy felügyelt lemezre a létrehozott SAS URI megadásával.

Ez a feltöltés ugyanaz az átviteli, mint az egyenértékű [szabványos HDD](disks-types.md#standard-hdd). Ha például az S4-nek megfelelő mérettel rendelkezik, legfeljebb 60 MiB/s átviteli fokkal rendelkezik. De ha van egy méret, amely megegyezik az S70, akkor egy átviteli akár 500 MiB / s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Miután a feltöltés befejeződött, és már nem kell több adatot írnia a lemezre, vonja vissza a SAS-t. A SAS visszavonása módosítja a felügyelt lemez állapotát, és lehetővé teszi a lemez virtuális géphez való csatolását.

```azurecli
az disk revoke-access -n mydiskname -g resourcegroupname
```

## <a name="copy-a-managed-disk"></a>Felügyelt lemez másolása

A közvetlen feltöltés leegyszerűsíti a felügyelt lemez másolásának folyamatát is. Másolhat ugyanabban a régióban vagy régiók közötti (másik régióba).

A következő szkript ezt az Ön számára, a folyamat hasonló a korábban leírt lépésekhez, némi különbséggel, mivel egy meglévő lemezzel dolgozik.

> [!IMPORTANT]
> 512-es eltolást kell hozzáadnia, amikor egy felügyelt lemez azure-beli lemez méretének bájtban van megadva. Ennek az az oka, hogy az Azure kihagyja az élőlábat a lemezméretének visszaadásakor. A másolat sikertelen lesz, ha ezt nem teszi meg. A következő szkript már ezt az Ön számára.

Cserélje `<sourceResourceGroupHere>`le `<sourceDiskNameHere>` `<targetDiskNameHere>`a `<targetResourceGroupHere>`, `<yourTargetLocationHere>` , , , és (egy példa a hely értéke lenne uswest2) az ön értékeit, majd futtassa a következő parancsfájlt, hogy másolja a felügyelt lemezt.

```azurecli
sourceDiskName = <sourceDiskNameHere>
sourceRG = <sourceResourceGroupHere>
targetDiskName = <targetDiskNameHere>
targetRG = <targetResourceGroupHere>
targetLocale = <yourTargetLocationHere>

sourceDiskSizeBytes= $(az disk show -g $sourceRG -n $sourceDiskName --query '[uniqueId]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocale --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI = $(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>További lépések

Most, hogy sikeresen feltöltött egy vhd-t egy felügyelt lemezre, csatlakoztathatja a lemezt [adatlemezként egy meglévő virtuális géphez,](add-disk.md) vagy a [lemezt egy virtuális géphez csatlakoztathatja operációs rendszerlemezként,](upload-vhd.md#create-the-vm)hogy hozzon létre egy új virtuális gép. 

