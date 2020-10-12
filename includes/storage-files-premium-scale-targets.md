---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: d704c6026e9d007a7365a3b72649ca509585da4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88057757"
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

|Terület  |Standard fájl  |Prémium fájl  |
|---------|---------|---------|
|Méret     |1 TiB         |4 TiB         |
|IOPS maximális száma      |1,000         |5000         |
|Egyidejű kezelők     |2000         |2000         |
|Kimenő forgalom     |Lásd a szabványos fájl átviteli sebességének értékeit         |300 MiB/mp         |
|Bejövő forgalom     |Lásd a szabványos fájl átviteli sebességének értékeit         |200 MiB/mp         |
|Teljesítmény     |Akár 60 MiB/mp         |Lásd a prémium szintű fájlok bejövő/kimenő értékeit         |