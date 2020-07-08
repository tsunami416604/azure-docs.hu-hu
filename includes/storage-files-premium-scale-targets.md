---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "71180052"
---
#### <a name="additional-premium-file-share-level-limits"></a>A prémium szintű fájlmegosztás további korlátai

|Terület  |Cél  |
|---------|---------|
|Minimális méret növelése/csökkentése    |1 GiB      |
|Alapterv IOPS    |1 IOPS/GiB, legfeljebb 100 000|
|IOPS-kitörés    |3x IOPS/GiB, akár 100 000|
|Kimenő forgalom aránya         |60 MiB/s + 0,06 * kiépített GiB        |
|Bejövő forgalom aránya| 40 MiB/s + 0,04 * kiépített GiB |

#### <a name="file-level-limits"></a>Fájl szintű korlátok

|Terület  |Prémium fájl  |Standard fájl |
|---------|---------|---------|
|Méret                  |1 TiB         |1 TiB|
|IOPS maximális száma     |5000         |1,000|
|Egyidejű kezelők    |2000         |2000|
|Kimenő forgalom  |300 MiB/mp|      Lásd a szabványos fájl átviteli sebességének értékeit|
|Bejövő forgalom  |200 MiB/mp| Lásd a szabványos fájl átviteli sebességének értékeit|
|Teljesítmény| Lásd a prémium szintű fájlok bejövő/kimenő értékeit| Akár 60 MiB/mp|
