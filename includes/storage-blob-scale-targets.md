---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/20/2019
ms.author: tamram
ms.openlocfilehash: d96f400332b7953b34a157b3b52cf00bb20db76e
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60012520"
---
| Erőforrás | Cél        |
|----------|---------------|
| Egy blob-tároló maximális mérete | Ugyanaz, mint a maximális tárolókapacitás fiók |
| Maximális száma blokkokat egy block blob vagy hozzáfűző blob | 50 000 blokkot |
| A blokkblob egy blokk maximális mérete | 100 MiB |
| A blokkblobok maximális mérete | 50 000 x 100 MiB (körülbelül 4,75 TiB) |
| Egy blokkblob egy hozzáfűző BLOB maximális mérete | 4 MiB |
| A hozzáfűző blob maximális mérete | 50 000 x 4 MiB (KB. 195 GB) |
| Egy lapblob maximális mérete | 8 TiB |
| Egy blob-tárolóban tárolt hozzáférési szabályzatok maximális számát | 5 |
|Cél átviteli egyetlen BLOB |Tároló bejövő/kimenő forgalom korlátai legfeljebb<sup>1</sup> |

<sup>1</sup> számos tényező befolyásolja, többek között, de nem kizárólagosan függ, hogy egyetlen objektum átviteli sebesség: egyidejűségi, a kérelem mérete, a teljesítményszint, a feltöltések a forrás és cél letöltések sebességétől. Kihasználásához [nagy átviteli sebességű blokkblob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) teljesítményt érintő továbbfejlesztés, használjon egy Put Blob- vagy Blokkelraktározási kérés mérete > 4 MiB (> 256 MiB prémium szintű teljesítményt blokkblob típusú tárolás vagy a Data Lake Storage Gen2).