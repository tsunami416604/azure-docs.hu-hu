---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78941012"
---
Az alábbi táblázat a table storage kapacitását, méretezhetőségét és teljesítménycéljait ismerteti.

| Erőforrás | Cél |
|----------|---------------|
| Táblák száma egy Azure-tárfiókban | Csak a tárfiók kapacitása korlátozza |
| A táblában lévő partíciók száma | Csak a tárfiók kapacitása korlátozza |
| A partíción lévő entitások száma | Csak a tárfiók kapacitása korlátozza |
| Egyetlen táblázat maximális mérete | 500 TiB |
| Egyetlen entitás maximális mérete, az összes tulajdonságértéktel együtt | 1 MiB |
| A táblaentitás tulajdonságainak maximális száma | 255 (beleértve a három rendszertulajdonságot, **a PartitionKey,** **a RowKey**és **az Időbélyeg**) |
| Egy adott gazdálkodó egység egyedi tulajdonságának maximális összmérete | A tulajdonság típusától függően változik. További információt a [Táblaszolgáltatás adatmodelljének ismertetése](/rest/api/storageservices/understanding-the-table-service-data-model) **tulajdonságtípusok** című témakörben talál. |
| A **partíciókulcs** mérete | Legfeljebb 1 KiB méretű karakterlánc |
| A **sorkulcs** mérete | Legfeljebb 1 KiB méretű karakterlánc |
| Egy entitáscsoport-tranzakció mérete | Egy tranzakció legfeljebb 100 entitást tartalmazhat, és a hasznos tehernek 4 MiB-nél kisebbnek kell lennie. Az entitáscsoport-tranzakció csak egyszer tartalmazhat egy entitás frissítését. |
| A tárolt hozzáférési házirendek maximális száma táblázatonként | 5 |
| Maximális kérelemarány tárfiókonként | 20 000 tranzakció másodpercenként, amely 1 KiB-es gazdálkodó egység méretét feltételezi |
| Egyetlen táblapartíció célátviteli-átalója (1 KiB-entitás) | Másodpercenként legfeljebb 2000 entitás |