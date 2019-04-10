---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 2957b45a0a26c590e2db1b402f4956ae9c3b8000
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59291873"
---
A következő táblázat az Azure Storage alapértelmezett korlátokat ismerteti. A *bejövő* korlát a tárfiókhoz küldött kérések hivatkozik az összes adat. A *kimenő* korlát hivatkozik az összes adat, amely egy storage-fiók érkező válaszok.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Storage-fiókok régiónként és előfizetésenként, beleértve a standard és prémium szintű fiókok száma | 250 |
| Maximális tárolókapacitás fiók | 2 PB Egyesült Államok és Európa, az összes többi régió esetében 500 TB, amely tartalmazza az Egyesült Királyság |
| Blob-tárolók, blobok, fájlmegosztások, táblák, üzenetsorok, entitások vagy tárfiókonként üzenetek maximális száma | Korlátlan |
| FileStorage (előzetes verzió) tárfiókok maximális mérete | 100 000 giB |
| Maximális IOPS FileStorage (előzetes verzió) storage-fiókok | 100 000 |
| Maximális sávszélesség (előzetes verzió) FileStorage storage-fiókok | 5 GB/s |
| Maximális<sup>1</sup> tárfiókonként | másodpercenként 20 000 kérelem |
| Maximális bejövő<sup>1</sup> tárfiókonként (Egyesült Államokbeli régiókhoz) | Ha engedélyezve van az RA-GRS/GRS 10 GB/s, 20 GB/s az LRS és zrs esetén<sup>2</sup> |
| Maximális bejövő<sup>1</sup> tárfiókonként (USA-régió) | Ha engedélyezve van az RA-GRS/GRS 5 GB/s, 10 GB/s az LRS és zrs esetén<sup>2</sup> |
| Maximális bejövő FileStorage (előzetes verzió) tárfiókonként (Egyesült Államokbeli régiókhoz) | 20 GB/s (csak az LRS esetén) |
| Maximális bejövő FileStorage (előzetes verzió) tárfiókonként (USA-régió) | 10 GB/s (csak az LRS esetén)|
| Maximális kimenő forgalom az általános célú v2, FileStorage (előzetes verzió) és a Blob storage-fiókok (az összes régióban) | 50 GB/s |
| Általános célú v1-tárfiókok (Egyesült Államokbeli régiókhoz) maximális kimenő forgalom | Ha engedélyezve van az RA-GRS/GRS 20 GB/s, 30 GB/s az LRS és zrs esetén<sup>2</sup> |
| Általános célú v1-tárfiókok (USA-régió) maximális kimenő forgalom | Ha engedélyezve van az RA-GRS/GRS 10 GB/s, 15 GB/s az LRS és zrs esetén<sup>2</sup> |

<sup>1</sup>azure standard szintű Storage-fiókok támogatása magasabb korlátok bejövő kérelem által. A bejövő forgalom korlátok növelését, lépjen kapcsolatba a [Azure-támogatási](https://azure.microsoft.com/support/faq/).

<sup>2</sup> [azure Storage replikáció](https://docs.microsoft.com/azure/storage/common/storage-redundancy) lehetőségek a következők:
* **RA-GRS**: Írásvédett georedundáns tárolás. RA-GRS engedélyezése esetén a másodlagos hely kimenő célok megegyeznek az elsődleges helyen.
* **GRS**: Georedundáns tárolás. 
* **ZRS**: Zónaredundáns tárolás.
* **LRS**: Helyileg redundáns tárolás. 

> [!NOTE]
> Javasoljuk, hogy a legtöbb esetben használjon egy általános célú v2-tárfiók. Könnyedén frissíthet egy általános célú V1-es vagy egy Azure Blob storage-fiók egy általános célú v2 fiók üzemkimaradás nélkül, valamint az adatok másolása nélkül.
>
> Az Azure Storage-fiókokról további információért lásd: [Tárfiók áttekintésének](../articles/storage/common/storage-account-overview.md). 

Ha az alkalmazása igényeit meghaladja a skálázhatósági célokat, az egy tárfiókban, hozhat létre az alkalmazás több tárfiók használata. Ezután az adatobjektumok particionáló ezen a tárfiókon keresztül. A mennyiségi díjszabásról további információkért lásd: [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/).

Az összes storage-fiókok egy egybesimított hálózati topológia fut, és támogatja a következő cikkben ismertetett, függetlenül attól, ha létrehozták őket a méretezhetőségi és teljesítménycéljai. További információ az Azure Storage egybesimított hálózati architektúra és a méretezhetőség: [a Microsoft Azure Storage: Erős konzisztencia magas rendelkezésre állású Felhőbeli Tárolószolgáltatásba](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

