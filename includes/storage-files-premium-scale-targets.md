---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: 86bf4911026e46c997469b956f9e7c75c4f17164
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697976"
---
#### <a name="additional-premium-file-share-level-limits"></a>A prémium szintű fájlmegosztás további korlátai

|Terület  |Cél  |
|---------|---------|
|Minimális méret növelése/csökkentése    |1 GiB      |
|Alapterv IOPS    |400 + 1 IOPS/GiB, legfeljebb 100 000|
|IOPS-kitörés    |Max (4000, 3x IOPS/GiB), akár 100 000|
|Kimenő forgalom aránya         |60 MiB/s + 0,06 * kiépített GiB        |
|Bejövő forgalom aránya| 40 MiB/s + 0,04 * kiépített GiB |

#### <a name="file-level-limits"></a>Fájl szintű korlátok

|Terület  |Standard fájl  |Prémium fájl  |
|---------|---------|---------|
|Méret     |1 TiB         |4 TiB         |
|IOPS maximális száma      |1,000         |Akár 8 000 *         |
|Egyidejű kezelők     |2000         |2000         |
|Kimenő forgalom     |Lásd a szabványos fájl átviteli sebességének értékeit         |300 MiB/mp (legfeljebb 1 GiB/s SMB többcsatornás előzetes verzióval) * *         |
|Bejövő forgalom     |Lásd a szabványos fájl átviteli sebességének értékeit         |200 MiB/mp (legfeljebb 1 GiB/s SMB többcsatornás előzetes verzióval) * *        |
|Teljesítmény     |Akár 60 MiB/mp         |Lásd a prémium szintű fájlok bejövő/kimenő értékeit         |

\*<sup>Az olvasási és írási iOS-re vonatkozik (általában kisebb i/o-méretek <= 64 kb). Az olvasási és írási műveletektől eltérő metaadat-műveletek lehetnek alacsonyabbak. </sup>

\*\*<sup>A számítógép hálózati korlátai, a rendelkezésre álló sávszélesség, az i/o-méretek, a várólista mélysége és egyéb tényezők is érvényesek. További részletek: [többcsatornás SMB-teljesítmény](../articles/storage/files/storage-files-smb-multichannel-performance.md). </sup>
