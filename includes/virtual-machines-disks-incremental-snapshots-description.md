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
ms.openlocfilehash: 1f094cd78e6708fec4bda1e5510379b11df14dcd
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299173"
---
A növekményes Pillanatképek olyan felügyelt lemezek időpontos biztonsági mentései, amelyek az utolsó pillanatkép óta csak az összes változást tartalmazzák. Ha egy növekményes pillanatkép letöltését vagy más módon történő használatát kísérli meg, a rendszer a teljes VHD-t használja. Ez az új képesség a felügyelt lemezes Pillanatképek számára potenciálisan költséghatékony lehet, mivel, ha nem választja ki, nem kell a teljes lemezt az egyes pillanatképekkel együtt tárolnia. A normál pillanatképekhez hasonlóan a növekményes Pillanatképek is használhatók teljes felügyelt lemez létrehozásához vagy rendszeres pillanatkép készítéséhez.

A növekményes pillanatfelvétel és a rendszeres Pillanatképek között néhány különbség van. A növekményes Pillanatképek mindig a standard HDD-tárolót használják, a lemez tárolási típusától függetlenül, míg a rendszeres Pillanatképek a prémium SSD-ket használhatják. Ha a virtuális gépek központi telepítésének vertikális felskálázásához Premium Storage rendszeresen pillanatképeket használ, javasoljuk, hogy egyéni rendszerképeket használjon a [megosztott lemezképek](../articles/virtual-machines/linux/shared-image-galleries.md)katalógusában a standard szintű tárolóban. Ezzel a megoldással sokkal nagyobb léptékben érhet el alacsonyabb költségeket. Emellett a növekményes Pillanatképek is jobb megbízhatóságot biztosítanak a [Zone-redundáns tárolással](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Ha a ZRS elérhető a kiválasztott régióban, a növekményes pillanatkép automatikusan a ZRS-t fogja használni. Ha a ZRS nem érhető el a régióban, akkor a pillanatkép alapértelmezett értéke [helyileg redundáns tárolás](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Felülbírálhatja ezt a viselkedést, és kiválaszthat egyet manuálisan, de nem ajánlott.

A növekményes Pillanatképek szintén különbözeti képességet biztosítanak, csak a felügyelt lemezek számára. Lehetővé teszik az azonos felügyelt lemezek két növekményes pillanatképének változását a blokk szintjére. Ezzel a képességgel csökkentheti az adatlábnyomot a pillanatképek régiók közötti másolásakor.
