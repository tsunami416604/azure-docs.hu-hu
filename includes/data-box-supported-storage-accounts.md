---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 06/08/2020
ms.author: alkohli
ms.openlocfilehash: da36e2bbf358a1c61d2b9b3f7ede592ac88fd427
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200327"
---
Itt látható a támogatott Storage-fiókok listája, valamint a Data Box eszköz tárolási típusai. A különböző típusú tárolási fiókok és azok teljes képességeinek teljes listáját a [Storage-fiókok típusai](/azure/storage/common/storage-account-overview#types-of-storage-accounts)című részben tekintheti meg.

Importálási megrendelések esetén a következő táblázat a támogatott Storage-fiókokat mutatja be.

| **Storage-fiók/támogatott tárolási típusok** | **Blokkblob** |**Oldal blobja*** |**Azure-fájlok** |**Megjegyzések**|
| --- | --- | -- | -- | -- |
| Klasszikus standard | I | I | I |
| Általános célú v1 standard  | I | I | I | A gyakori és a ritka elérés is támogatott.|
| Általános célú v1 Premium  |  | I| | |
| Általános célú v2 standard  | I | I | I | A gyakori és a ritka elérés is támogatott.|
| Általános célú v2 prémium  |  |I | | |
| BLOB Storage standard |I | | |A gyakori és a ritka elérés is támogatott. |

\**– Az oldal blobokra feltöltött adatmennyiségnek 512 bájtnak kell lennie, például VHD-k.*

Az exportálási megrendelések esetében a következő táblázat a támogatott Storage-fiókokat mutatja be.

| **Storage-fiók/támogatott tárolási típusok** | **Blokkblob** |**Oldal blobja*** |**Azure-fájlok** |**Támogatott hozzáférési szintek**|
| --- | --- | -- | -- | -- |
| Klasszikus standard | I | I | I | |
| Általános célú v1 standard  | I | I | I | Gyors elérésű, ritka|
| Általános célú v1 Premium  |  | I| | |
| Általános célú v2 standard  | I | I | I | Gyors elérésű, ritka|
| Általános célú v2 prémium  |  |I | | |
| BLOB Storage standard |I | | |Gyors elérésű, ritka |
| A blob Storage Premium letiltása |I | | |Gyors elérésű, ritka |
| Oldal blob Storage Premium | |I | | |

> [!IMPORTANT]
> - Az általános célú fiókok esetében a Data Box nem támogatja az üzenetsor, a tábla, a lemez és a Azure Data Lake 2. generációs tárolási típust.
> - A Data Box nem támogatja a Blobok hozzáfűzését a Blob Storagehoz és a Blob Storage fiókok blokkolásához.
> - A Data Box nem támogatja a prémium szintű File Storage fiókokat.
> - Az oldal blobokra feltöltött adatmennyiségnek 512 bájtnak kell lennie, például VHD-k.
> - Legfeljebb 80 TB lehet exportálni.
> - A rendszer nem exportálja a fájl előzményeit és a blob-pillanatképeket.


