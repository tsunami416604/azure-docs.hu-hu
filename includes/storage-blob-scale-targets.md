---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/20/2019
ms.author: tamram
ms.openlocfilehash: aab17966862c57a52f252b3c4e9b757673078b0a
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "67179331"
---
| Resource | Target        |
|----------|---------------|
| Egy blob-tároló maximális mérete | Ugyanaz, mint a Storage-fiókok maximális kapacitása |
| Blokkok maximális száma egy blokk blobban vagy a blob hozzáfűzése | 50 000 blokk |
| Blokk maximális mérete egy blokk blobban | 100 MiB |
| Blokk blob maximális mérete | 50 000 X 100 MiB (körülbelül 4,75 TiB) |
| Blokk maximális mérete egy hozzáfűző blobban | 4 MiB |
| Hozzáfűző blob maximális mérete | 50 000 x 4 MiB (körülbelül 195 GiB) |
| Oldal blobjának maximális mérete | 8 TiB |
| Tárolt hozzáférési szabályzatok maximális száma blob-tárolón | 5 |
|Cél átviteli sebesség egyetlen blobhoz |Legfeljebb Storage-fiók bejövő/kimenő korlátja<sup>1</sup> |

<sup>1</sup> az egyetlen objektum átviteli sebessége több tényezőtől függ, többek között a következőktől: Egyidejűség, kérelmek mérete, teljesítményszint, a feltöltések forrása, valamint a letöltések célja. A [nagy átviteli sebességű blokk Blobok](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) teljesítményének kihasználásához használjon egy Put blobot, vagy > 4 MIB-t (> 256 KiB a prémium szintű teljesítményű blob Storage-hoz vagy a Data Lake Storage Gen2-hoz).
