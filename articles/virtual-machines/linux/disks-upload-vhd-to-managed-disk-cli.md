---
title: VHD feltöltése az Azure-ba az Azure CLI használatával
description: Megtudhatja, hogyan tölthet fel egy virtuális merevlemezt egy Azure-beli felügyelt lemezre, és hogyan másolhat egy felügyelt lemezt régiók között az Azure CLI használatával.
services: virtual-machines-linux,storage
author: roygara
ms.author: rogarana
ms.date: 09/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: dfcf9ea61a1f0fb5fd2d3b613c2449480753b3a1
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595098"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>VHD feltöltése az Azure-ba az Azure CLI használatával

Ez a cikk azt ismerteti, hogyan tölthetők fel virtuális merevlemezek a helyi gépről egy Azure-beli felügyelt lemezre. Korábban egy olyan folyamatot kellett követnie, amely a Storage-fiókban tárolt adatait és a Storage-fiók kezelését is magában foglalja. Most már nem kell kezelnie a Storage-fiókot, vagy nem kell megadnia azokat a virtuális merevlemezek feltöltéséhez. Ehelyett létre kell hoznia egy üres felügyelt lemezt, és fel kell töltenie egy virtuális merevlemezt. Ez leegyszerűsíti a helyszíni virtuális gépek Azure-ba való feltöltését, és lehetővé teszi, hogy a virtuális merevlemezt akár 32 TiB-ra feltöltse közvetlenül egy nagy felügyelt lemezre.

Ha biztonsági mentési megoldást biztosít a IaaS virtuális gépek számára az Azure-ban, javasoljuk, hogy a közvetlen feltöltés használatával állítsa vissza az ügyfelek biztonsági mentését a felügyelt lemezekre. Ha a virtuális merevlemezt az Azure-on kívüli gépről tölti fel, a sebesség a helyi sávszélességtől függ. Ha Azure-beli virtuális gépet használ, a sávszélesség ugyanaz lesz, mint a standard HDD-k.

A közvetlen feltöltés jelenleg a standard HDD, a standard SSD és a prémium szintű SSD által felügyelt lemezek esetében támogatott. Ultra SSD-k esetén még nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

- Töltse le a [AzCopy v10 legújabb verzióját](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Telepítse az Azure CLI](/cli/azure/install-azure-cli)-t.
- Helyileg tárolt vhd-fájl
- Ha a virtuális merevlemezt a PEM-ról kívánja feltölteni: az Azure-hoz [készült](../windows/prepare-for-upload-vhd-image.md), helyileg tárolt virtuális merevlemezt.
- Vagy egy felügyelt lemezt az Azure-ban, ha egy másolási műveletet kíván végrehajtani.

## <a name="create-an-empty-managed-disk"></a>Üres felügyelt lemez létrehozása

A virtuális merevlemez Azure-ba való feltöltéséhez létre kell hoznia egy üres felügyelt lemezt, amely ehhez a feltöltési folyamathoz van konfigurálva. Mielőtt létrehoz egyet, néhány további információra van szüksége a lemezekről.

Az ilyen felügyelt lemez két egyedi állapottal rendelkezik:

- ReadToUpload, ami azt jelenti, hogy a lemez készen áll a feltöltésre, de nem jött létre [biztonságos hozzáférési aláírás](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS).
- ActiveUpload, ami azt jelenti, hogy a lemez készen áll a feltöltés fogadására, és a SAS létrejött.

Ezen állapotok bármelyikében a felügyelt lemez a [standard szintű HDD díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/)alapján kerül kiszámlázásra, a lemez tényleges típusától függetlenül. Egy P10 például egy S10-ként lesz kiszámlázva. Ez csak akkor lesz igaz, ha `revoke-access` a felügyelt lemezen van meghívva, amely ahhoz szükséges, hogy a lemezt egy virtuális géphez csatlakoztassa.

Ahhoz, hogy a feltöltéshez üres szabványos HDD-t hozzon létre, bájtban kell megadnia a feltölteni kívánt vhd-fájl méretét. Ehhez `wc -c <yourFileName>.vhd` vagy `ls -al <yourFileName>.vhd` is használhatja. Ez az érték a **--upload-size-Bytes** paraméter megadásakor használatos.

Hozzon létre egy üres szabványos HDD-t a feltöltéshez a **-– for-upload** paraméter és a **--upload-size-Bytes** paraméter megadásával a [lemez létrehozása](/cli/azure/disk#az-disk-create) parancsmagban:

```bash
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Ha prémium szintű SSD-t vagy standard SSD-t szeretne feltölteni, cserélje le a **standard_lrs** -t a **premium_LRS** vagy a **standardssd_lrs**értékre. A ultra SSD még nem támogatott.

Ezzel létrehozott egy üres felügyelt lemezt, amely a feltöltési folyamathoz van konfigurálva. A virtuális merevlemezek lemezre való feltöltéséhez írható SAS szükséges, hogy a feltöltés céljának megfelelően hivatkozzon legyen rá.

Az üres felügyelt lemezre írható SAS létrehozásához használja a következő parancsot:

```bash
az disk grant-access -n mydiskname -g resourcegroupname --access-level Write --duration-in-seconds 86400
```

Minta visszaadott értéke:

```
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-vhd"></a>VHD feltöltése

Most, hogy rendelkezik egy SAS-vel az üres felügyelt lemezhez, használhatja azt a felügyelt lemez beállításához a feltöltési parancs céljának megfelelően.

A AzCopy v10 használatával töltse fel a helyi VHD-fájlt egy felügyelt lemezre úgy, hogy megadja a létrehozott SAS URI-t.

Ez a feltöltés azonos átviteli sebességgel rendelkezik, mint a [szabványos HDD](disks-types.md#standard-hdd). Ha például egy olyan mérettel rendelkezik, amely megfelel az S4 értéknek, akkor akár 60 MiB/s sebességű átviteli sebességgel fog rendelkezni. Ha azonban egy olyan mérettel rendelkezik, amely megfelel a S70, akkor akár 500 MiB/s sebességű átviteli sebességgel fog rendelkezni.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Ha a SAS lejár a feltöltés során, és még nem nevezte meg `revoke-access`, akkor új SAS-t kaphat a feltöltés folytatásához `grant-access` használatával.

Miután a feltöltés befejeződött, és többé nem kell további adatokra írnia a lemezt, vonja vissza a SAS-t. Az SAS visszavonása megváltoztatja a felügyelt lemez állapotát, és lehetővé teszi a lemez csatlakoztatását egy virtuális géphez.

```bash
az disk revoke-access -n mydiskname -g resourcegroupname
```

## <a name="copy-a-managed-disk"></a>Felügyelt lemez másolása

A közvetlen feltöltés emellett leegyszerűsíti a felügyelt lemezek másolásának folyamatát. A másolást elvégezheti ugyanazon a régión vagy régión belül (egy másik régióba).

A következő szkript ezt elvégzi Önnek, a folyamat hasonló a korábban ismertetett lépésekhez, néhány különbséggel, mivel meglévő lemezzel dolgozik.

> [!IMPORTANT]
> Ha az Azure-ból felügyelt lemez mérete bájtban van megadva, akkor 512 eltolást kell hozzáadnia. Ennek az az oka, hogy az Azure kihagyja a láblécet a lemez méretének visszaadása során. Ha ezt nem teszi meg, a másolás sikertelen lesz. A következő szkript ezt már elvégezte Önnek.

Cserélje le a `<sourceResourceGroupHere>`, `<sourceDiskNameHere>`, `<targetDiskNameHere>`, `<targetResourceGroupHere>` és `<yourTargetLocationHere>` (például a uswest2) értékeit, majd futtassa a következő parancsfájlt a felügyelt lemez másolásához.

```bash
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

## <a name="next-steps"></a>Következő lépések

Most, hogy sikeresen feltöltött egy virtuális merevlemezt egy felügyelt lemezre, csatlakoztathatja a lemezt egy virtuális géphez, és megkezdheti a használatát.

Ha meg szeretné tudni, hogyan lehet lemezeket csatlakoztatni egy virtuális géphez, tekintse meg a tárgyat ismertető cikket: [lemez hozzáadása Linux rendszerű virtuális géphez](add-disk.md).
