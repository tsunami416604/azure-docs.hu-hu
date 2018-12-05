---
title: Az Azure PowerShell-szkript minták – teljes számlázási blobtároló méretének kiszámítása |} A Microsoft Docs
description: Az Azure Blob storage-tároló teljes méretének kiszámítása a számlázás tekintetében.
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
ms.openlocfilehash: 7e28b8938c8c0eb258fbb599dd5765258a4d52e4
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52867375"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>Teljes számlázási blobtároló méretének kiszámítása

Ez a szkript kiszámítja egy tárolót az Azure Blob storage céljából számlázási költségek becslése méretét. A parancsfájl a tárolóban lévő blobok méretének összegzi.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Ez a PowerShell-szkript egy tároló méretének számlázási célból számítja ki. Tároló mérete más célokra számítja ki, ha [kiszámítása a Blob storage-tároló amelyek összméretén](../scripts/storage-blobs-container-calculate-size-powershell.md) becsült biztosító egyszerűbb parancsfájl.

## <a name="determine-the-size-of-the-blob-container"></a>A blob-tároló méretének meghatározásához.

A blob-tároló teljes méretét a maga a tároló és a méretét a tárolóban található összes blobot tartalmaz.

Az alábbi szakaszok azt ismerteti, hogyan kerül kiszámításra a tárolási kapacitás a blob-tárolók és blobok. A következő szakaszban Len(X) azt jelenti, hogy a karakterláncban szereplő karakterek száma.

### <a name="blob-containers"></a>BLOB-tárolók

A következő számítási ismerteti, hogyan lehet megbecsülni egy blobtároló felhasznált tárterület mérete:

`
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
`

A következő táblázat összefoglalja következő:
* az egyes tárolók terheléssel 48 bájt az utolsó módosítás dátuma, engedélyek, nyilvános beállítások és néhány rendszer metaadatokat tartalmaz.

* A tároló neve Unicode-ként tárolja úgy karakterek állapotba, és két szorzása.

* Minden egyes blob tárolóban tárolt metaadatok blokk a neve (ASCII), továbbá a karakterlánc hosszának tárolása.

* Az 512 bájt / aláírt azonosító tartalmazza az aláírt azonosító neve, kezdési idő, lejárati időpontot és engedélyeket.

### <a name="blobs"></a>Blobok

A következő számítások blobonkénti felhasznált tárterület mérete becslése szemléltetik.

* A blokkblobok (alap blob vagy pillanatkép):

   `
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   `

* A lapblobok (alap blob vagy pillanatkép):

   `
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   `

A következő táblázat összefoglalja következő:

* blob, amely tartalmazza a terheléssel 124 bájtok:
    - Utolsó módosítás időpontja
    - Méret
    - A Cache-Control
    - Content-Type
    - Tartalom-Language
    - Tartalom kódolása
    - Tartalom-MD5
    - Engedélyek
    - Pillanatkép-információk
    - Bérlet
    - Egyes metaadatok

* A blob neve Unicode-ként tárolja úgy karakterek állapotba, és két szorzása.

* Minden egyes tárolt metaadatok kódblokkot vegyen fel (ASCII tárolva), a név hosszának plusz a karakterlánc hossza.

* A blokkblobok:
    * a blokkblob blokklistájának 8 bájt.
    * Blokkok száma túllépi a azonosítója blokkméret (bájt).
    * Az összes véglegesítése és a nem véglegesített blokkok az adatok mérete.

    >[!NOTE]
    >A pillanatképek használata esetén ez a méret csak az alap- vagy pillanatkép BLOB egyedi adatokat is tartalmaz. A nem véglegesített blokkok nem használják a hét után, ha azok szemétgyűjtési gyűjtött. Ezt követően azok nem beleszámítanak a számlázás.

* Lapblobok:
    * Az adatok nem egymást követő laptartomány száma túllépi az 12 bájt. Láthatja, mikor történt egyedi laptartomány száma a **GetPageRanges** API-t.

    * Az adatok (bájt) összes tárolt oldalt mérete.

    >[!NOTE]
    >Pillanatképek használata esetén ez a méret a csak az alap blob vagy van megszámolni blob pillanatkép egyedi lapokat tartalmaz.

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>További lépések

- Lásd: [kiszámítása a Blob storage-tároló amelyek összméretén](../scripts/storage-blobs-container-calculate-size-powershell.md) egy egyszerű parancsfájlt, amely biztosítja a tároló méretének becslése.

- Azure Storage szolgáltatás számlázásának kapcsolatos további információkért lásd: [ismertetése Windows Azure Storage számlázási](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

- Az Azure PowerShell-modullal kapcsolatos további információkért lásd: [Azure PowerShell-dokumentáció](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.4.1).

- A további tárolási PowerShell szkriptminták annak [PowerShell-minták az Azure Storage](../blobs/storage-samples-blobs-powershell.md).
