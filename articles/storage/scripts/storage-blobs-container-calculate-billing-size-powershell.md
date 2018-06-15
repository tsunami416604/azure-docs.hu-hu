---
title: Az Azure PowerShell-parancsfájl minták – a teljes számlázási méretének blob-tároló kiszámítása |} Microsoft Docs
description: A teljes mérete az Azure Blob storage-tároló kiszámítása számlázási célokra.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: fryu
ms.openlocfilehash: c37b416578a76e9b12e29d68e413d851796ccc6f
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
ms.locfileid: "26368544"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>A teljes számlázási méretének blob-tároló kiszámítása

Ez a parancsfájl az Azure Blob storage céljából számlázási költségek megbecsülheti egy tároló mérete számítja ki. A parancsfájl összegzi a tárolóban lévő blobok méretét.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> A PowerShell parancsfájl-tároló mérete számlázási okokból számítja ki. Tároló mérete más célra számítja ki, ha [egy Blob storage tárolót teljes mérete kiszámításához](../scripts/storage-blobs-container-calculate-size-powershell.md) becsült biztosító egyszerűbb parancsfájl.

## <a name="determine-the-size-of-the-blob-container"></a>A blob-tároló méretének meghatározása

A blob-tároló teljes mérete magában foglalja, maga a tároló és a mérete, az összes BLOB a tárolóban.

Az alábbi szakaszok azt ismerteti, hogyan számítja ki a tárolási kapacitás a blob-tárolók és blobok. A következő szakaszban Len(X) azt jelenti, hogy a karakterláncban szereplő karakterek számát.

### <a name="blob-containers"></a>BLOB tárolók

A következő számítási becslésére egy blob-tároló felhasznált tárolási ismerteti:

`
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
`

Az alábbiakban látható bontásban tartalmazza:
* minden egyes tároló terheléssel 48 bájt tartalmazza az utolsó módosítás dátuma, engedélyek, nyilvános beállításai és bizonyos rendszer metaadatait.

* A tároló neve Unicode-ként tárolja, így a karakterek számát figyelembe és szorozza meg két.

* A blob tároló metaadatait tárolt valamennyi blokkja a a név (ASCII), és a karakterlánc hosszát tároljuk.

* Az 512 bájt / aláírt azonosítója aláírt azonosító neve, a kezdési ideje, a lejárati idő és az engedélyek tartalmazza.

### <a name="blobs"></a>Blobok

A következő számítások felhasznált egy blob storage becslésére szemléltetik.

* A blokkblob (alap blob vagy pillanatkép):

   `
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   `

* Az oldalakra vonatkozó blob (alap blob vagy pillanatkép):

   `
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   `

Az alábbiakban látható bontásban tartalmazza:

* 124 bájt terheléssel BLOB, amely tartalmazza:
    - Utolsó módosítás időpontja
    - Méret
    - A Cache-Control
    - Content-Type
    - Tartalom-nyelv
    - Tartalom kódolása
    - Content-MD5
    - Engedélyek
    - Pillanatkép-információk
    - Címbérlet
    - Néhány rendszer metaadatai

* A blob neve Unicode-ként tárolja, így a karakterek számát figyelembe és szorozza meg két.

* Valamennyi blokkja tárolt metaadatok vegye fel (ASCII tárolt), a név hosszát és a karakterlánc hosszát.

* A blokkblobokhoz:
    * 8 bájtos a listából.
    * A blokkméret azonosító bájtban időpontokban blokkok számát.
    * Az összes lefoglalt és a nem véglegesített blokkok adatok méretét. 
    
    >[!NOTE]
    >Pillanatképek használata esetén ez a méret tartalmazza az e alap vagy pillanatkép blob csak a egyedi adatokat. A nem véglegesített blokkok nem használják a hét után, ha azok szemétgyűjtési gyűjti. Ezt követően ezek nem számítanak számlázási felé.

* A lapblobokat:
    * Az adatok kijelöléséhez tartományokat száma alkalommal 12 bájt. Tekintse meg a következő meghívásakor: egyedi tartományokat száma a **GetPageRanges** API.

    * Az összes, a tárolt lapok bájtban adatok méretét. 
    
    >[!NOTE]
    >Pillanatképek használata esetén ez a méret lapjain csak az egyedi az alap blob vagy a pillanatkép-blob, amely leltározás alatt áll.

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Következő lépések

- Lásd: [egy Blob storage tárolót teljes mérete kiszámításához](../scripts/storage-blobs-container-calculate-size-powershell.md) egy egyszerű parancsprogram, amely a tároló méretének becsléséhez alapul.

- Azure Storage számlázással kapcsolatos további információkért lásd: [ismertetése a Windows Azure Storage számlázási](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

- Az Azure PowerShell-modullal kapcsolatos további információkért lásd: [Azure PowerShell dokumentációs](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.4.1).

- A további tárhely PowerShell parancsfájl minták található [Azure Storage PowerShell-példák](../blobs/storage-samples-blobs-powershell.md).
