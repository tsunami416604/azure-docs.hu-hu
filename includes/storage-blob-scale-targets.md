---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/11/2019
ms.author: tamram
ms.openlocfilehash: b3e2f018a3f1ba2563ba8cf2df6dfd4959be592e
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59737114"
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

<sup>1</sup> számos tényező befolyásolja, többek között, de nem kizárólagosan függ, hogy egyetlen objektum átviteli sebesség: egyidejűségi, a művelet mérete, a teljesítményszint, a feltöltéshez forrás és cél letöltések sebessége.