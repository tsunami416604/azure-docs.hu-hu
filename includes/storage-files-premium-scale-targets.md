---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2019
ms.locfileid: "71180052"
---
#### <a name="additional-premium-file-share-level-limits"></a>A prémium szintű fájlmegosztás további korlátai

|Terület  |Target  |
|---------|---------|
|Minimális méret növelése/csökkentése    |1 GiB      |
|Alap IOPS-érték    |1 IOPS/GiB, legfeljebb 100 000|
|IOPS-kitörés    |3x IOPS/GiB, akár 100 000|
|Kimenő forgalom aránya         |60 MiB/s + 0,06 * kiépített GiB        |
|Bejövő forgalom aránya| 40 MiB/s + 0,04 * kiépített GiB |

#### <a name="file-level-limits"></a>Fájl szintű korlátok

|Terület  |Prémium fájl  |Standard fájl |
|---------|---------|---------|
|Size                  |1 TiB         |1 TiB|
|IOPS maximális száma     |5,000         |1,000|
|Egyidejű kezelők    |2,000         |2,000|
|Kimenő forgalom  |300 MiB/mp|      Lásd a szabványos fájl átviteli sebességének értékeit|
|Bejövő forgalom  |200 MiB/mp| Lásd a szabványos fájl átviteli sebességének értékeit|
|Teljesítmény| Lásd a prémium szintű fájlok bejövő/kimenő értékeit| Akár 60 MiB/mp|
