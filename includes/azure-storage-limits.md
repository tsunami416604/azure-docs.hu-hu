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
ms.openlocfilehash: 1dd11e22361e25721effe2ed919f175d9cb1b9e4
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2019
ms.locfileid: "54249619"
---
A következő táblázat az Azure Storage alapértelmezett korlátokat ismerteti. A *bejövő* korlát vonatkozik az összes adatot (kérést) küld egy tárfiókba. A *kimenő* korlát vonatkozik egy tárfiók felől fogadott összes adatot (választ).

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Storage-fiókok régiónként és előfizetésenként, beleértve a standard és prémium szintű fiókok száma | 250 |
| Tárfiókok kapacitásával maximális száma | 2 PB Egyesült Államok és Európa, az összes többi régió, többek között az Egyesült Királyság esetében 500 TB |
| Blob-tárolók, blobok, fájlmegosztások, táblák, üzenetsorok, entitások vagy tárfiókonként üzenetek maximális száma | Korlátlan |
| Maximális<sup>1</sup> tárfiókonként | másodpercenként 20 000 kérelem |
| Maximális bejövő<sup>1</sup> tárfiókonként (USA régió) | Ha az RA-GRS/GRS engedélyezve van, 20 GB/s az LRS és zrs esetén 10 GB/s<sup>2</sup> |
| Maximális bejövő<sup>1</sup> tárfiókonként (Amerikai Egyesült régió) | Ha az RA-GRS/GRS engedélyezve van, 10 GB/s az LRS és zrs esetén 5 GB/s<sup>2</sup> |
| Maximális kimenő forgalom, általános célú v2 és Blob storage-fiókok (az összes régióban) | 50 GB/s |
| Maximális kimenő forgalom, általános célú v1-tárfiókok (USA régióban) | Ha az RA-GRS/GRS engedélyezése esetén 30 GB/s az LRS és zrs esetén 20 GB/s <sup>2</sup> |
| Maximális kimenő forgalom, általános célú v1-tárfiókok (Amerikai Egyesült régió) | Ha az RA-GRS/GRS engedélyezve van, 15 GB/s az LRS és zrs esetén 10 GB/s <sup>2</sup> |

<sup>1</sup> kérés által az azure storage-fiókok támogatják az a bejövő és iops-érték magasabb korlátok. A korlátok növelését, lépjen kapcsolatba [Azure-támogatási](https://azure.microsoft.com/support/faq/).

<sup>2</sup> [azure Storage replikáció](https://docs.microsoft.com/azure/storage/common/storage-redundancy) lehetőségek a következők:
* **RA-GRS**: Írásvédett georedundáns tárolás. RA-GRS engedélyezése esetén a másodlagos hely kimenő célok megegyeznek az elsődleges helyen.
* **GRS**: Georedundáns tárolás. 
* **A ZRS**: Zónaredundáns tárolás.
* **LRS**: Helyileg redundáns tárolás. 

> [!NOTE]
> A Microsoft azt javasolja, általános célú v2-tárfiókok használata a legtöbb forgatókönyvhöz. Könnyedén frissíthet egy általános célú v1- vagy Blob storage-fiók egy általános célú v2 fiók üzemkimaradás nélkül, valamint az adatok másolása nélkül.
>
> Az Azure Storage-fiókokról további információért lásd: [Tárfiók áttekintésének](../articles/storage/common/storage-account-overview.md). 

Ha az alkalmazása igényeit meghaladja a skálázhatósági célokat, az egy tárfiókban, hozhat létre az alkalmazás több tárfiók használata. Ezután az adatobjektumok particionáló ezen a tárfiókon keresztül. Lásd: [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/) számára a mennyiségi díjszabásról.

Az összes storage-fiókok egy egybesimított hálózati topológia fut, és támogatja a következő cikkben ismertetett, függetlenül attól, ha létrehozták őket a méretezhetőségi és teljesítménycéljai. További információ az Azure Storage egybesimított hálózati architektúra és a méretezhetőség: [a Microsoft Azure Storage: Erős konzisztencia magas rendelkezésre állású Felhőbeli Tárolószolgáltatásba](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

