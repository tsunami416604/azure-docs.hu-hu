---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 2ed88d8abb7cbe96093b68d89030e6e464a35541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75392289"
---
| Erőforrás | Cél        |
|----------|---------------|
| Egyetlen blobtároló maximális mérete | Megegyezik a tárfiók maximális kapacitással |
| A blokkblobban vagy hozzáfűző blobban lévő blokkok maximális száma | 50 000 háztömb |
| Blokkméretének maximális mérete egy blokkblobban | 100 mib |
| Egy blokkblob maximális mérete | 50 000 X 100 MiB (kb. 4,75 TiB) |
| Egy blokk maximális mérete hozzáfűző blobban | 4 MiB |
| Hozzáfűző blob maximális mérete | 50 000 x 4 Millió (kb. 195 GiB) |
| Egy lapblob maximális mérete | 8 TiB |
| A tárolt hozzáférési házirendek maximális száma blobtárolónként | 5 |
|Egyetlen blob célkérelem-aránya | Másodpercenként legfeljebb 500 kérelem |
|Egyetlen lapblob átviteli átviteli célja | Akár 60 MiB másodpercenként |
|Egyetlen blokkblob célátviteli-átatot használ |A tárfiók be- és kilépési<sup>korlátaiig 1</sup> |

<sup>1</sup> Egy blob átviteli sebessége több tényezőtől függ, többek között, de nem kizárólagosan: egyidejűség, kérelem mérete, teljesítményszint, a feltöltések forrásának sebessége és a letöltések célja. A [nagy átviteli sebességű blokkblobok](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)teljesítménybeli javításai nak kihasználásához töltsön fel nagyobb blobokat vagy blokkokat. Pontosabban hívja meg a [Put Blob](/rest/api/storageservices/put-blob) vagy [put block](/rest/api/storageservices/put-block) műveletet egy blob vagy blokk mérete, amely nagyobb, mint 4 MiB a standard szintű tárfiókok. Prémium szintű blokkblobhoz vagy Data Lake Storage Gen2 storage-fiókokhoz használjon 256 KiB-nél nagyobb blokk- vagy blobméretet.
