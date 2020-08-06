---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: c2bd10ab4c98fe2e77332c3cc2566ab2f0c7ad42
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87841868"
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
|Méret                  |4 TiB         |1 TiB|
|IOPS maximális száma     |5000         |1,000|
|Egyidejű kezelők    |2000         |2000|
|Kimenő forgalom  |300 MiB/mp|      Lásd a szabványos fájl átviteli sebességének értékeit|
|Bejövő forgalom  |200 MiB/mp| Lásd a szabványos fájl átviteli sebességének értékeit|
|Átviteli sebesség| Lásd a prémium szintű fájlok bejövő/kimenő értékeit| Akár 60 MiB/mp|
