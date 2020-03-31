---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "71180052"
---
#### <a name="additional-premium-file-share-level-limits"></a>További prémium fájlmegosztási szint-korlátozások

|Terület  |Cél  |
|---------|---------|
|Minimális méretnövekedés/-csökkenés    |1 GiB      |
|Kiindulási IOPS    |1 IOPS/GiB, 100 000-ig|
|IOPS-felszakítás    |3x IOPS per GiB, akár 100 000|
|Kimenő forgalom aránya         |60 MiB/s + 0,06 * kiépített GiB        |
|Be- és visszakamatemelési arány| 40 MiB/s + 0,04 * kiépített GiB |

#### <a name="file-level-limits"></a>Fájlszint-korlátok

|Terület  |Prémium fájl  |Szabványos fájl |
|---------|---------|---------|
|Méret                  |1 TiB         |1 TiB|
|Maximális IOPS fájlonként     |5000         |1,000|
|Egyidejű fogantyúk    |2000         |2000|
|Kimenő forgalom  |300 MiB/mp|      Normál fájlátviteli értékek megtekintése|
|Bejövő forgalom  |200 MiB/mp| Normál fájlátviteli értékek megtekintése|
|Teljesítmény| Prémium fájlbe- és kimenő forgalom értékeinek megtekintése| Akár 60 MiB/mp|
