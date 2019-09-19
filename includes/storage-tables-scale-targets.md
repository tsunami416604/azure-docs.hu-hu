---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 5ab03b682dd0ed1dc7b198e89c86e7a74c6275cd
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "67457374"
---
| Resource | Target |
|----------|---------------|
| Egyetlen tábla maximális mérete | 500 TiB |
| Egy tábla entitás maximális mérete | 1 MiB |
| Tulajdonságok maximális száma egy tábla entitásban | 255, amely három rendszertulajdonságot tartalmaz: PartitionKey, RowKey és timestamp |
| Az entitásokban lévő tulajdonságértékek maximális teljes mérete | 1 MiB |
| Az entitásban lévő egyes tulajdonságok maximális teljes mérete | A tulajdonság típusa szerint változhat. További információ: **Property types** ( [a Table Service adatmodell ismertetése](/rest/api/storageservices/understanding-the-table-service-data-model)). |
| Tárolt hozzáférési szabályzatok maximális száma táblában | 5 |
| Kérelmek maximális száma Storage-fiókban | 20 000 tranzakció/másodperc, amely egy 1 – KiB értékű entitás méretét feltételezi |
| Cél átviteli sebesség egyetlen tábla partícióhoz (1 KiB – entitások) | Legfeljebb 2 000 entitás másodpercenként |