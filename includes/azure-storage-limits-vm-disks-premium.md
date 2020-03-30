---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: ef18feb10dabc6a77e6512c6a32ad44b32c6e832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334787"
---
**Prémium szintű nem felügyelt virtuálisgép-lemezek: Fiókonkénti korlátok**

| Erőforrás | Korlát |
| --- | --- |
| Fiókonkénti teljes lemezkapacitás |35 TB |
| Fiókonkénti teljes pillanatkép-kapacitás |10 TB |
| Maximális sávszélesség fiókonként (be- és kilépés)<sup>1</sup> |<=50 Gbps |

<sup>1</sup>*A be- és visszafordítás* a tárfiókba küldött kérelmekösszes adatára vonatkozik. *A kimenő forgalom* a tárfiókból érkező válaszokból származó összes adatra vonatkozik.

**Prémium szintű nem felügyelt virtuálisgép-lemezek: Lemezenkénti korlátok**

| Prémium szintű tárolólemezek típusai | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Lemezméret |128 GiB |512 GiB |1024 GiB (1 TB) |2048 GiB (2 TB)|4095 GiB (4 TB)|
| Maximális IOPS lemezenként |500 |2300 |5000 |7500 |7500 |
| Maximális átviteli teljesítmény lemezenként |100 MB/s | 150 MB/s |200 MB/s |250 MB/s |250 MB/s |
| A lemezek maximális száma tárfiókonként |280 |70 |35 | 17 | 8 |

**Prémium szintű nem felügyelt virtuálisgép-lemezek: Virtuálisgép-per-virtuális gép korlát**

| Erőforrás | Korlát |
| --- | --- |
| Maximális IOPS virtuális gépenként |80 000 IOPS GS5 virtuális géptel |
| Maximális átviteli teljesítmény virtuális gépenként |2000 MB/mp GS5 virtuális géptel |

