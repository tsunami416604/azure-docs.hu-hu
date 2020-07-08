---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 83c391c0d92f8d4a0ed4b44bc3a90273db51b412
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81539307"
---
A növekményes Pillanatképek olyan felügyelt lemezek időpontos biztonsági mentései, amelyek az utolsó pillanatkép óta csak az összes változást tartalmazzák. Ha egy növekményes pillanatkép letöltését vagy más módon történő használatát kísérli meg, a rendszer a teljes VHD-t használja. Ez az új képesség a felügyelt lemezes Pillanatképek számára potenciálisan költséghatékony lehet, mivel, ha nem választja ki, nem kell a teljes lemezt az egyes pillanatképekkel együtt tárolnia. A normál pillanatképekhez hasonlóan a növekményes Pillanatképek is használhatók teljes felügyelt lemez létrehozásához vagy rendszeres pillanatkép készítéséhez.

A növekményes pillanatfelvétel és a rendszeres Pillanatképek között néhány különbség van. A növekményes Pillanatképek mindig a standard HDD-tárolót használják, a lemez tárolási típusától függetlenül, míg a rendszeres Pillanatképek a prémium SSD-ket használhatják. Ha a virtuális gépek központi telepítésének vertikális felskálázásához Premium Storage rendszeresen pillanatképeket használ, javasoljuk, hogy egyéni rendszerképeket használjon a [megosztott lemezképek](../articles/virtual-machines/linux/shared-image-galleries.md)katalógusában a standard szintű tárolóban. Ezzel a megoldással sokkal nagyobb léptékben érhet el alacsonyabb költségeket. Emellett a növekményes Pillanatképek is jobb megbízhatóságot biztosítanak a [Zone-redundáns tárolással](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Ha a ZRS elérhető a kiválasztott régióban, a növekményes pillanatkép automatikusan a ZRS-t fogja használni. Ha a ZRS nem érhető el a régióban, akkor a pillanatkép alapértelmezett értéke [helyileg redundáns tárolás](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Felülbírálhatja ezt a viselkedést, és kiválaszthat egyet manuálisan, de nem ajánlott.

A növekményes Pillanatképek szintén különbözeti képességet biztosítanak, csak a felügyelt lemezek számára. Lehetővé teszik az azonos felügyelt lemezek két növekményes pillanatképének változását a blokk szintjére. Ezzel a képességgel csökkentheti az adatlábnyomot a pillanatképek régiók közötti másolásakor.  Például letöltheti az első növekményes pillanatképet egy másik régióban lévő alapblobként. A későbbi növekményes Pillanatképek esetében csak az alap blob utolsó pillanatképének változásait másolja át. A módosítások másolása után pillanatképeket készíthet az alap blobon, amely egy másik régióban lévő lemez időpontjának biztonsági mentését jelképezi. A lemezt visszaállíthatja az alap blobból, vagy egy másik régióban lévő alap blobon lévő pillanatképből is.

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="A régiók között másolt növekményes pillanatképeket ábrázoló diagram. A pillanatképek különféle API-hívásokat tesznek elérhetővé, amíg az egyes Pillanatképek során a Blobok egy részét nem":::

A pillanatképek használt mérete az [Azure használati jelentésének](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)megtekintésével tekinthető meg. Ha például egy pillanatkép által használt adatméret 10 GiB, a **napi** használati jelentés 10 GIB/(31 nap) = 0,3226-et jelenít meg a felhasznált mennyiségnek megfelelően.
