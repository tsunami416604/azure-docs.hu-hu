---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78941012"
---
Az alábbi táblázat a kapacitás-, méretezhetőség- és teljesítménycélokat ismerteti a Table Storage esetében.

| Erőforrás | Cél |
|----------|---------------|
| Az Azure-tárfiókban található táblázatok száma | Csak a tárfiók kapacitása korlátozza |
| A partíciók száma a táblázatban | Csak a tárfiók kapacitása korlátozza |
| Entitások száma egy partíción belül | Csak a tárfiók kapacitása korlátozza |
| Egyetlen táblázat maximális mérete | 500 TiB |
| Egyetlen entitás maximális mérete, az összes tulajdonságértéket beleértve | 1 MiB |
| Tulajdonságok maximális száma egy táblázatentitásban | 255 (a három rendszertulajdonságot – **PartitionKey**, **RowKey** és **Timestamp** – is beleértve) |
| Egyedi tulajdonság maximális teljes mérete egy entitásban | Tulajdonságtípustól függően változik. További információt a [Table Service adatmodelljét](/rest/api/storageservices/understanding-the-table-service-data-model) ismertető témakör **tulajdonságtípusokkal** foglalkozó részében talál. |
| A **PartitionKey** mérete | Legfeljebb 1 KiB méretű sztring |
| A **RowKey** mérete | Legfeljebb 1 KiB méretű sztring |
| Az entitáscsoport-tranzakció mérete | Egy tranzakció legfeljebb 100 entitást tartalmazhat, és a hasznos adat méretének 4 MiB értéknél kisebbnek kell lennie. Egy entitáscsoport-tranzakció egy entitást legfeljebb egyszer frissíthet. |
| Tárolt hozzáférési szabályzatok táblázatonkénti maximális száma | 5 |
| Maximális kérelemmennyiség tárfiókonként | 20 000 tranzakció másodpercenként, 1 KiB entitásméretet feltételezve |
| Az átviteli sebesség célértéke egyetlen táblázat-partíció esetében (1 KiB méretű entitások) | Legfeljebb 2000 entitás másodpercenként |