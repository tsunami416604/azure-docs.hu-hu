---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 2ed88d8abb7cbe96093b68d89030e6e464a35541
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "75392289"
---
| Erőforrás | Cél        |
|----------|---------------|
| Egy blob-tároló maximális mérete | Ugyanaz, mint a Storage-fiókok maximális kapacitása |
| Blokkok maximális száma egy blokk blobban vagy a blob hozzáfűzése | 50 000 blokk |
| Blokk maximális mérete egy blokk blobban | 100 MiB |
| Blokk blob maximális mérete | 50 000 X 100 MiB (körülbelül 4,75 TiB) |
| Blokk maximális mérete egy hozzáfűző blobban | 4 MiB |
| Hozzáfűző blob maximális mérete | 50 000 x 4 MiB (körülbelül 195 GiB) |
| Oldal blobjának maximális mérete | 8 TiB |
| Tárolt hozzáférési szabályzatok maximális száma blob-tárolón | 5 |
|Egy blobhoz tartozó célalkalmazás-kérelmek sebessége | Legfeljebb 500 kérelem másodpercenként |
|Cél átviteli sebesség egy egyoldalas blobhoz | Akár 60 MiB másodpercenként |
|Cél átviteli sebesség egyetlen blokk blobhoz |Legfeljebb Storage-fiók bejövő/kimenő korlátja<sup>1</sup> |

<sup>1</sup> az egyes Blobok átviteli sebessége több tényezőtől függ, többek között a következőktől: Egyidejűség, kérelmek mérete, teljesítményszint, a feltöltések forrása, valamint a letöltés céljának célja. A [nagy átviteli sebességű blokkos Blobok](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)teljesítményének növelésével kihasználhatja a nagyobb méretű Blobok vagy blokkok feltöltését. Pontosan hívja meg a [put blobot](/rest/api/storageservices/put-blob) vagy a [Letiltás](/rest/api/storageservices/put-block) műveletet egy olyan blob-vagy blokk-mérettel, amely nagyobb, mint 4 MIB a standard Storage-fiókokhoz. A Premium Block blob vagy a Data Lake Storage Gen2 Storage-fiókok esetében használjon 256 KiB-nál nagyobb blokk-vagy blob-méretet.
