---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: bc156b8c18f46cccf6fc775b82f76383b8c43861
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "66242143"
---
Itt látható a támogatott Storage-fiókok listája, valamint a Data Box eszköz tárolási típusai. A különböző típusú tárolási fiókok és azok teljes képességeinek teljes listáját a [Storage-fiókok típusai](/azure/storage/common/storage-account-overview#types-of-storage-accounts)című részben tekintheti meg.

| **Storage-fiók/támogatott tárolási típusok** | **Blokkblob** |**Oldal blobja*** |**Azure-fájlok** |**Megjegyzések**|
| --- | --- | -- | -- | -- |
| Klasszikus standard | I | I | I |
| Általános célú v1 standard  | I | I | I | A gyakori és a ritka elérés is támogatott.|
| Általános célú v1 Premium  |  | I| | |
| Általános célú v2 standard  | I | I | I | A gyakori és a ritka elérés is támogatott.|
| Általános célú v2 prémium  |  |I | | |
| BLOB Storage standard |I | | |A gyakori és a ritka elérés is támogatott. |

\**– Az oldal blobokra feltöltött adatmennyiségnek 512 bájtnak kell lennie, például VHD-k.*

>[!NOTE]
> Az Azure Data Lake Storage Gen2-fiókok nem támogatottak.
