---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: 368f08272173b019873dfe20e1164d6baf72ff5e
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542661"
---
#### <a name="additional-premium-file-share-level-limits"></a>További prémium szintű fájl megosztási szint korlátok

|Terület  |Target  |
|---------|---------|
|Minimális méret növelése vagy csökkentése    |1 GiB      |
|Alap IOPS-érték    |1 GB, legfeljebb 100 000 IOPS|
|Tartalékkapacitás IOPS    |/ GB, legfeljebb 100 000 3 x IOPS|
|Kilépő üzenetek gyakorisága         |60 MiB/s + 0,06 * kiosztott GiB        |
|Bejövő forgalom| 40 MiB/s + 0,04 * kiosztott GiB |

#### <a name="file-level-limits"></a>Fájl szintű korlátok

|Terület  |Prémium szintű fájl  |Standard szintű fájl |
|---------|---------|---------|
|Size                  |1 TiB         |1 TiB|
|Fájlonkénti maximális iops-érték     |5,000         |1,000|
|Egyidejű kezeli    |2,000         |2,000|
|Bejövő forgalom  |300 MiB/sec|      Standard szintű fájl átviteli értékek|
|Kimenő forgalom   |200 Mib/mp| Standard szintű fájl átviteli értékek|
|Teljesítmény| Prémium szintű fájl bejövő/kimenő forgalom értékek| Akár 60 MiB/mp|