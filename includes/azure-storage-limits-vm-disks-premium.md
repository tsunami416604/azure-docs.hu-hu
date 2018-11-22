---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: 5ac7982d306125804fc5b7873e537f9381f717cb
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279687"
---
**Prémium nem felügyelt virtuálisgép-lemezek: fiókonkénti korlátok**

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Fiókonkénti teljes lemezkapacitás |35 TB |
| Fiókonkénti teljes pillanatkép-kapacitás |10 TB |
| Fiókonkénti maximális sávszélesség (bejövő + kimenő forgalom<sup>1</sup>) |<=50 Gbps |

<sup>1</sup>*Bejövő forgalom* alatt egy tárfiók felé elküldött összes adatot (kérést) értjük. *Kimenő forgalom* alatt egy tárfiók felől fogadott összes adatot (választ) értjük.

**Prémium nem felügyelt virtuálisgép-lemezek: lemezenkénti korlátok**

| Premium Storage-lemez típusa | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Lemezméret |128 GiB |512 GiB |1024 GiB (1 TB) |2048 GiB (2 TB)|4095 GiB (4 TB)|
| Lemezenkénti maximális IOPS-érték |500 |2300 |5000 |7500 |7500 |
| Lemezenkénti maximális átviteli sebesség |100 MB/s | 150 MB/s |200 MB/s |250 MB/s |250 MB/s |
| Lemezek tárfiókonkénti maximális száma |280 |70 |35 | 17 | 8 |

**Prémium nem felügyelt virtuálisgép-lemezek: virtuális gépenkénti korlátok**

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Virtuális gépenkénti maximális IOPS |80 000 IOPS GS5 virtuális géppel |
| Virtuális gépenkénti maximális átviteli sebesség |2000 MB/s GS5 virtuális géppel |

