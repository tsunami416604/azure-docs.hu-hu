---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 83c391c0d92f8d4a0ed4b44bc3a90273db51b412
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81539307"
---
A növekményes pillanatképek olyan felügyelt lemezek időbeli biztonsági másolatai, amelyek készítése esetén csak az utolsó pillanatkép óta végrehajtott összes változást tartalmazják. Amikor megpróbál letölteni, vagy más módon használja a növekményes pillanatképet, a teljes virtuális merevlemez t használja. Ez az új képesség a felügyelt lemez pillanatképek potenciálisan lehetővé teszi számukra, hogy költséghatékonyabb, mivel, ha úgy dönt, hogy nem kell tárolni a teljes lemezt minden egyes pillanatkép. Csakúgy, mint a rendszeres pillanatképek, növekményes pillanatképek segítségével vagy hozzon létre egy teljes felügyelt lemezt, vagy rendszeres pillanatképet.

Van néhány különbség a növekményes pillanatkép és a rendszeres pillanatkép között. A növekményes pillanatképek mindig szabványos HDD-tárolót használnak, függetlenül a lemez tárolási típusától, míg a rendszeres pillanatképek prémium szintű SSD-ket használhatnak. Ha a prémium szintű storage-ban rendszeres pillanatképeket használ a virtuális gép-telepítések méretezéséhez, azt javasoljuk, hogy egyéni lemezképeket használjon a megosztott képtár szabványos [tárhelyén.](../articles/virtual-machines/linux/shared-image-galleries.md) Ez segít elérni egy nagyobb méretű alacsonyabb költségek. Emellett a növekményes pillanatképek potenciálisan nagyobb megbízhatóságot kínálnak [a zónaredundáns tárolás](../articles/storage/common/storage-redundancy-zrs.md) (ZRS) segítségével. Ha a ZRS elérhető a kiválasztott régióban, a növekményes pillanatkép automatikusan használja a ZRS-t. Ha a ZRS nem érhető el a régióban, majd a pillanatkép alapértelmezés szerint [a helyileg redundáns tárolás](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Felülbírálhatja ezt a viselkedést, és manuálisan is kijelölhet egyet, de ezt nem javasoljuk.

A növekményes pillanatképek különbözeti képességet is kínálnak, csak a felügyelt lemezek számára érhető el. Lehetővé teszik, hogy a módosításokat két növekményes pillanatképek ugyanazon felügyelt lemezek, le a blokk szintre. Ezzel a funkcióval csökkentheti az adatlábnyomot a pillanatképek régiók közötti másolásakor.  Például letöltheti az első növekményes pillanatképet egy másik régióban alapblobként. A későbbi növekményes pillanatképek csak a módosításokat, mivel az utolsó pillanatkép az alap blob. A módosítások másolása után pillanatképeket készíthet az alapblobon, amely a lemez egy másik régióban lévő időbeli biztonsági mentését képviseli. A lemez t alapblobból vagy egy másik régióban lévő alapblob ról származó pillanatképből állíthatja vissza.

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="A régiók között másolt növekményes pillanatképeket ábrázoló diagram. A pillanatképek különböző API-hívásokat kezdeményeznek, amíg végül oldalblobokat nem alakítanak ki minden pillanatképenként.":::

A pillanatképek használt méretét az [Azure-használati jelentés](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)ből láthatja. Ha például egy pillanatkép használt adatmérete 10 GiB, a **napi** használati jelentés 10 GiB/(31 nap) = 0,3226 lesz a felhasznált mennyiség.
