---
title: Virtuális merevlemez feltöltése az Azure-ba vagy lemez másolása régiók között – Azure CLI
description: Ismerje meg, hogyan tölthet fel virtuális merevlemezt egy Azure által felügyelt lemezre, és hogyan másolhat egy felügyelt lemezt a régiók között az Azure CLI használatával, közvetlen feltöltés útján.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 03/27/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 6813206aebe67e4e6d2afd0d9c78d03f0c20c952
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420971"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>Virtuális merevlemez feltöltése az Azure-ba, vagy felügyelt lemez másolása egy másik régióba – Azure CLI

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Előfeltételek

- Töltse le az [AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)legújabb verzióját.
- [Telepítse az Azure CLI](/cli/azure/install-azure-cli).
- Ha a helyszíni: Az Azure számára [előkészített](../windows/prepare-for-upload-vhd-image.md)rögzített méretű virtuális merevlemez, helyileg tárolt virtuális merevlemez.
- Vagy egy felügyelt lemez az Azure-ban, ha másolási műveletet kíván végrehajtani.

## <a name="getting-started"></a>Első lépések

Ha szeretné feltölteni a lemezeket egy gui-n keresztül, ezt megteheti az Azure Storage Explorer használatával. További részletek: [Az Azure felügyelt lemezeinek kezeléséhez használja az Azure Storage Explorert](disks-use-storage-explorer-managed-disks.md)

A virtuális merevlemez Azure-ba való feltöltéséhez létre kell hoznia egy üres felügyelt lemezt, amely ehhez a feltöltési folyamathoz van konfigurálva. Mielőtt létrehozna egyet, további információkat is tudnia kell ezekről a lemezekről.

Ez a fajta felügyelt lemez két egyedi állapottal rendelkezik:

- ReadToUpload, ami azt jelenti, hogy a lemez készen áll a feltöltésfogadására, de nem jött létre [biztonságos hozzáférési aláírás](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS).
- ActiveUpload, ami azt jelenti, hogy a lemez készen áll a feltöltésre, és a SAS jött létre.

> [!NOTE]
> Míg ezen állapotok bármelyikében a felügyelt lemez számlázása [a szabványos HDD-díjszabás](https://azure.microsoft.com/pricing/details/managed-disks/)szerint történik, függetlenül a lemez tényleges típusától. Például egy P10 s10-ként kerül számlázásra. Ez addig igaz, amíg meg nem `revoke-access` hívják a felügyelt lemezen, amely szükséges ahhoz, hogy a lemezt egy virtuális géphez csatolja.

## <a name="create-an-empty-managed-disk"></a>Üres felügyelt lemez létrehozása

Mielőtt létrehozhatna egy üres szabványos merevlemezt a feltöltéshez, szüksége lesz a feltölteni kívánt virtuális merevlemez fájlméretére bájtban. Ahhoz, hogy ezt, `wc -c <yourFileName>.vhd` `ls -al <yourFileName>.vhd`akkor vagy vagy . Ez az érték a **--upload-size-bytes** paraméter megadásakor használatos.

Hozzon létre egy üres szabványos merevlemezt a feltöltéshez a **--for-upload** paraméter és a [lemezlétrehozási](/cli/azure/disk#az-disk-create) parancsmag **--upload-size-byte** paraméterének megadásával:

Cserélje `<yourdiskname>` `<yourresourcegroupname>`ki `<yourregion>` a , , értékeket az Ön által választott. A `--upload-size-bytes` paraméter a példaértékét `34359738880`tartalmazza, cserélje le az Önnek megfelelő értékre.

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Ha prémium SSD-t vagy szabványos SSD-t szeretne feltölteni, cserélje ki **standard_lrs** **premium_LRS** vagy **standardssd_lrs.** Az ultralemezek egyelőre nem támogatottak.

Most, hogy létrehozott egy üres felügyelt lemezt, amely a feltöltési folyamathoz van konfigurálva, feltölthet egy virtuális merevlemezt. Ha egy virtuális merevlemezt szeretne feltölteni a lemezre, írható SAS-ra van szüksége, hogy a feltöltés célhelyeként hivatkozhassrá.

Az üresen kezelt lemez írható SAS-ének létrehozásához cserélje le, `<yourdiskname>`majd `<yourresourcegroupname>`használja a következő parancsot:

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

A minta visszaadott értéke:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>VHD feltöltése

Most, hogy rendelkezik egy SAS-sel az üres felügyelt lemezhez, használhatja a felügyelt lemez beállításához a feltöltési parancs célhelyeként.

Az AzCopy v10 használatával feltöltheti a helyi virtuális merevlemez-fájlt egy felügyelt lemezre a létrehozott SAS URI megadásával.

Ez a feltöltés ugyanaz az átviteli, mint az egyenértékű [szabványos HDD](disks-types.md#standard-hdd). Ha például az S4-nek megfelelő mérettel rendelkezik, legfeljebb 60 MiB/s átviteli fokkal rendelkezik. De ha van egy méret, amely megegyezik az S70, akkor egy átviteli akár 500 MiB / s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Miután a feltöltés befejeződött, és már nem kell több adatot írnia a lemezre, vonja vissza a SAS-t. A SAS visszavonása módosítja a felügyelt lemez állapotát, és lehetővé teszi a lemez virtuális géphez való csatolását.

Cserélje `<yourdiskname>` `<yourresourcegroupname>`ki, majd a következő paranccsal tegye használhatóvá a lemezt:

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
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

Most, hogy sikeresen feltöltött egy virtuális merevlemezt egy felügyelt lemezre, csatlakoztathatja a lemezt [adatlemezként egy meglévő virtuális géphez,](add-disk.md) vagy a [lemezt egy virtuális géphez csatlakoztathatja operációs rendszerlemezként,](upload-vhd.md#create-the-vm)hogy hozzon létre egy új virtuális gép. 

