---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: e7e57c6a821731874dcb1d99a3133b6ede1da26e
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "67179108"
---
**Prémium nem felügyelt virtuálisgép-lemezek: Felhasználónkénti korlátok**

| Resource | Alapértelmezett korlát |
| --- | --- |
| Fiókonkénti teljes lemezkapacitás |35 TB |
| Fiókonkénti teljes pillanatkép-kapacitás |10 TB |
| Maximális sávszélesség/fiók (bejövő és kimenő forgalom)<sup>1</sup> |<=50 Gbps |

<sup>1</sup> A *bejövő* forgalom a Storage-fiókba küldött kérelmekből származó összes adatokra vonatkozik. A *kimenő* forgalom a Storage-fiókból kapott válaszok összes adatértékére vonatkozik.

**Prémium nem felügyelt virtuálisgép-lemezek: Lemezterület-korlátok**

| Prémium szintű tárolólemezek típusai | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Lemezméret |128 GiB |512 GiB |1 024 GiB (1 TB) |2 048 GiB (2 TB)|4 095 GiB (4 TB)|
| IOPS maximális száma lemezenként |500 |2,300 |5000 |7500 |7,500 |
| Maximális átviteli sebesség (lemez) |100 MB/s | 150 MB/s |200 MB/s |250 MB/s |250 MB/s |
| Lemezek maximális száma Storage-fiókban |280 |70 |35 | 17 | 8 |

**Prémium nem felügyelt virtuálisgép-lemezek: VIRTUÁLIS gépekre vonatkozó korlátok**

| Resource | Alapértelmezett korlát |
| --- | --- |
| IOPS maximális száma virtuális gépenként |80 000 IOPS GS5 virtuális géppel |
| Maximális átviteli sebesség virtuális gépenként |2 000 MB/mp GS5 virtuális géppel |

