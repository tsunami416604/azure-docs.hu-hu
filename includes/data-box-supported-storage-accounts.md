---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/git14/2020
ms.author: alkohli
ms.openlocfilehash: 91f91b1260cc445f90c2608fc5259ad61acd37ac
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90533197"
---
Itt látható a támogatott Storage-fiókok listája, valamint a Data Box eszköz tárolási típusai. A különböző típusú tárolási fiókok és azok teljes képességeinek teljes listáját a [Storage-fiókok típusai](/azure/storage/common/storage-account-overview#types-of-storage-accounts)című részben tekintheti meg.

Importálási megrendelések esetén a következő táblázat a támogatott Storage-fiókokat mutatja be.

| **Storage-fiók/támogatott tárolási típusok** | **Blokkblob** |**Oldal blobja*** |**Azure-fájlok** |**Megjegyzések**|
| --- | --- | -- | -- | -- |
| Klasszikus standard | Y | Y | Y |
| Általános célú v1 standard  | Y | Y | Y | A gyakori és a ritka elérés is támogatott.|
| Általános célú v1 Premium  |  | Y| | |
| Általános célú v2 standard  | Y | Y | Y | A gyakori és a ritka elérés is támogatott.|
| Általános célú v2 prémium  |  |Y | | |
| BLOB Storage standard |Y | | |A gyakori és a ritka elérés is támogatott. |

\**– Az oldal blobokra feltöltött adatmennyiségnek 512 bájtnak kell lennie, például VHD-k.*

Az exportálási megrendelések esetében a következő táblázat a támogatott Storage-fiókokat mutatja be.

| **Storage-fiók/támogatott tárolási típusok** | **Blokkblob** |**Oldal blobja*** |**Azure-fájlok** |**Támogatott hozzáférési szintek**|
| --- | --- | -- | -- | -- |
| Klasszikus standard | Y | Y | Y | |
| Általános célú v1 standard  | Y | Y | Y | Gyors elérésű, ritka|
| Általános célú v1 Premium  |  | Y| | |
| Általános célú v2 standard  | Y | Y | Y | Gyors elérésű, ritka|
| Általános célú v2 prémium  |  |Y | | |
| BLOB Storage standard |Y | | |Gyors elérésű, ritka |
| A blob Storage Premium letiltása |Y | | |Gyors elérésű, ritka |
| Oldal blob Storage Premium | |Y | | |

> [!IMPORTANT]
> - Az általános célú fiókok esetében a Data Box nem támogatja az importálási rendelésekhez tartozó üzenetsor-, tábla-és lemez-tárolási típusokat. Az exportálási megrendelések esetében Data Box nem támogatja az üzenetsor-kezelést, a táblát, a lemezt és a Azure Data Lake 2. generációs tárolási típusokat az általános célú fiókokhoz.
> - A Data Box nem támogatja a Blobok hozzáfűzését a Blob Storagehoz és a Blob Storage fiókok blokkolásához.
> - A Data Box nem támogatja a prémium szintű File Storage fiókokat.
> - Az oldal blobokra feltöltött adatmennyiségnek 512 bájtnak kell lennie, például VHD-k.
> - Legfeljebb 80 TB lehet exportálni.
> - A rendszer nem exportálja a fájl előzményeit és a blob-pillanatképeket.


