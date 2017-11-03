---
title: "Az Azure PowerShell-parancsfájl minta - kiszámításához blob-tároló mérete |} Microsoft Docs"
description: "Az Azure Blob storage-tároló mérete kiszámítása a blobjai méretétől összegzésével."
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 10/23/2017
ms.author: fryu
ms.openlocfilehash: 46f3eac0129da41062caba2da090f9e532505d67
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>A Blob storage-tároló méretének kiszámítása

Ez a parancsfájl az Azure Blob storage-tároló mérete a tárolóban lévő blobok méretének összegzésével számítja ki.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="understand-the-size-of-blob-storage-container"></a>A Blob-tároló mérete ismertetése

Teljes Blob storage tárolót is tartalmazza a tároló maga és a mérete, az összes BLOB a tárolóban.

Az alábbiak azt ismertetik, hogyan kiszámítása a Blob-tárolók és Blobok a tárolási kapacitást. Az az alábbi Len(X) a karakterláncban szereplő karakterek számát jelenti.

### <a name="blob-containers"></a>BLOB tárolók

A következő, hogyan becslésére egy blob-tároló felhasznált tárhely:

`
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
`

A következő egy bontásban tartalmazza:
* minden egyes tároló terheléssel 48 bájt tartalmazza az utolsó módosítás dátuma, engedélyek, nyilvános beállításai és bizonyos rendszer metaadatait.
* A tároló neve van tárolva Unicode így igénybe vehet a karakterek számát, és megszorozza a 2.
* Minden egyes blob tároló metaadatait tárolja tároljuk, a név (ASCII tárolt), valamint a karakterlánc hosszát.
* Az 512 bájt / aláírt azonosítója aláírt azonosító neve, az idő, a lejárat időpontjának és az engedélyek.

### <a name="blobs"></a>Blobok

A következő, hogyan becslésére felhasznált egy blob storage:

* Elérésű Blokkblob (alap blob vagy pillanatkép)

`
124 bytes + Len(BlobName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
SizeInBytes(data in unique committed data blocks stored) +
SizeInBytes(data in uncommitted data blocks)
`

* Az oldalakra vonatkozó Blob (alap blob vagy pillanatkép)

`
124 bytes + Len(BlobName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
number of nonconsecutive page ranges with data * 12 bytes +
SizeInBytes(data in unique pages stored)
`

A következő egy bontásban tartalmazza:

* terheléssel blob, beleértve az utolsó módosítás dátuma, a 124 bájt mérete, a Cache-Control, Content-Type, a tartalom-nyelv, Content-Encoding, Content-MD5, engedélyek, pillanatkép információt, bérleti és bizonyos rendszer metaadatait.
* A blob neve Unicode ezért tegye karakterek és a 2 többszöröse száma van tárolva.
* Minden metaadat ott, a név (ASCII tárolt), valamint a karakterlánc hosszát.
* Ezután a Blokkblobokhoz
    * 8 bájtos a tiltólistához
    * A blokkméret azonosító bájtban időpontokban blokkok száma
    * Továbbá az összes lefoglalt és a nem véglegesített blokkok adatok méretét. Vegye figyelembe, pillanatképek használata esetén ez a méret csak tartalmazza az e alap vagy pillanatkép blob a egyedi adatokat. A nem véglegesített blokkok nem használják a hét után, ha szemétgyűjtő lesznek, és majd akkori azok is már nem beleszámít számlázási ezt követően.
* Ezután a Lapblobokat
    * Az adatok kijelöléséhez tartományokat száma alkalommal 12 bájt. Ez az egyedi akkor látható, amikor a GetPageRanges API felület meghívásakor tartományokat száma.
    * Plusz bájtban összes tárolt adatok méretét. Megjegyzés, a pillanatképek használata, ez a méret az alap blob vagy a pillanatkép blob megszámolni egyedi lapokat csak tartalmazza.

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Következő lépések

Az Azure Storage számlázási további információkért lásd: [ismertetése a Windows Azure Storage számlázási](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

További tárhely PowerShell parancsfájl minták található [Azure Storage PowerShell-példák](../blobs/storage-samples-blobs-powershell.md).
