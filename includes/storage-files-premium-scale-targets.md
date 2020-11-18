---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: 8dcb58499113b0b7ae0814419f0a76965a0ed945
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94680848"
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
|IOPS maximális száma      |1,000         |Akár 8 000 *         |
|Egyidejű kezelők     |2000         |2000         |
|Kimenő forgalom     |Lásd a szabványos fájl átviteli sebességének értékeit         |300 MiB/mp (legfeljebb 1 GiB/s SMB többcsatornás előzetes verzióval) * *         |
|Bejövő forgalom     |Lásd a szabványos fájl átviteli sebességének értékeit         |200 MiB/mp (legfeljebb 1 GiB/s SMB többcsatornás előzetes verzióval) * *        |
|Teljesítmény     |Akár 60 MiB/mp         |Lásd a prémium szintű fájlok bejövő/kimenő értékeit         |

\*<sup>Az olvasási és írási iOS-re vonatkozik (általában kisebb i/o-méretek <= 64 kb). Az olvasási és írási műveletektől eltérő metaadat-műveletek lehetnek alacsonyabbak. </sup>

\*\*<sup>A számítógép hálózati korlátai, a rendelkezésre álló sávszélesség, az i/o-méretek, a várólista mélysége és egyéb tényezők is érvényesek. További részletek: [többcsatornás SMB-teljesítmény](../articles/storage/files/storage-files-smb-multichannel-performance.md). </sup>
