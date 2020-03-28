---
title: Azure PowerShell-parancsfájlminta – Egy blobtároló teljes számlázási méretének kiszámítása | Microsoft dokumentumok
description: Számítsa ki egy tároló teljes méretét az Azure Blob storage-ban számlázási célokra.
services: storage
author: fhryo-msft
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: fryu
ms.openlocfilehash: 12b32256c91dfcf93ca55eeb348cc78613ba860e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067109"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>Blob-tároló teljes számlázási méretének kiszámítása

Ez a parancsfájl kiszámítja egy tároló méretét az Azure Blob storage-ban a számlázási költségek becslése céljából. A parancsfájl összegzi a tárolóban lévő blobok méretét.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Ez a PowerShell-parancsfájl számlázási célokra kiszámítja a tároló méretét. Ha más célokra számítja ki a tárolóméretét, olvassa [el a Blob-tároló teljes méretének kiszámítása](../scripts/storage-blobs-container-calculate-size-powershell.md) című témakört egy becslést tartalmazó egyszerűbb parancsfájlhoz.

## <a name="determine-the-size-of-the-blob-container"></a>A blobtároló méretének meghatározása

A blob tároló teljes mérete magában foglalja a tároló méretét, és a tároló alatt lévő összes blob méretét.

A következő szakaszok ismerteti, hogyan számítja ki a tárolási kapacitás blob tárolók és blobok.A következő szakaszban a Len(X) a karakterláncban szereplő karakterek számát jelenti.

### <a name="blob-containers"></a>Blob-tárolók

A következő számítás bemutatja, hogyan becsülhető meg a blobtárolónként felhasznált tárterület mennyiségét:

```
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
```

A következőkben a bontás:
* Az egyes tárolók 48 bájtnyi többletterhelése tartalmazza az utolsó módosítás időpontját, az Engedélyeket, a Nyilvános beállításokat és néhány rendszermetaadatot.

* A tároló neve Unicode formátumban van tárolva, ezért vegye ki a karakterek számát, és szorozza meg kettővel.

* A blobtároló minden tárolt metaadat-blokkja esetében a név (ASCII) hosszát és a karakterlánc-érték hosszát tároljuk.

* Az aláírt azonosítónkénti 512 bájt tartalmazza az aláírt azonosító nevét, kezdési idejét, lejárati idejét és engedélyeit.

### <a name="blobs"></a>Blobok

A következő számítások bemutatják, hogyan becsülje meg a blobonként felhasznált tárterület mennyiségét.

* Blokkblob (alapblob vagy pillanatkép):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   ```

* Lapblob (alapblob vagy pillanatkép):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   ```

A következőkben a bontás:

* 124 bájt rezsi a blobhoz, amely a következőket tartalmazza:
    - Utolsó módosítás időpontja
    - Méret
    - Gyorsítótár-vezérlés
    - Content-Type
    - Tartalomnyelv
    - Tartalomkódolás
    - Tartalom-MD5
    - Engedélyek
    - Pillanatkép-információk
    - Bérleti
    - Egyes rendszermetaadatok

* A blob neve Unicode formátumban van tárolva, ezért vegye ki a karakterek számát, és szorozza meg kettővel.

* Minden tárolt metaadat-blokkhoz adja hozzá a név hosszát (ASCII-ként tárolva), valamint a karakterlánc-érték hosszát.

* A blokkblobok esetében:
  * 8 bájt a tiltólistához.
  * A blokkazonosító méretének szorzata i. blokkok száma bájtban.
  * Az összes véglegesített és nem véglegesített blokk adatainak mérete.

    >[!NOTE]
    >Pillanatképek használata esetén ez a méret csak az alapja vagy pillanatkép blob egyedi adatait tartalmazza. Ha a nem véglegesített blokkokat egy hét után nem használja, akkor a szemetet gyűjtik. Ezután nem számítanak bele a számlázásba.

* Lapblobok esetén:
  * A 12 bájtban lévő adattartományokkal rendelkező nem egymást követő oldaltartományok száma. Ez a **GetPageRanges** API hívásakor látható egyedi oldaltartományok száma.

  * Az összes tárolt oldal bájtban lévő adatainak mérete.

    >[!NOTE]
    >Pillanatképek használata esetén ez a méret csak az alapblob vagy a megszámlált pillanatképblob egyedi lapjait tartalmazza.

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>További lépések

- Lásd: [Blob storage-tároló teljes méretének kiszámítása](../scripts/storage-blobs-container-calculate-size-powershell.md) egy egyszerű parancsfájlhoz, amely a tároló méretének becslését tartalmazza.

- Az Azure Storage-számlázásról a [Windows Azure Storage-számlázás ismertetése című](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/)témakörben talál további információt.

- Az Azure PowerShell-modulról az [Azure PowerShell dokumentációjában](https://docs.microsoft.com/powershell/azure/overview)olvashat bővebben.

- További PowerShell-parancsfájlmintákat találhat az [Azure Storage PowerShell-mintáiban.](../blobs/storage-samples-blobs-powershell.md)
