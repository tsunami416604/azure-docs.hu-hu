---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 5ab03b682dd0ed1dc7b198e89c86e7a74c6275cd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67457374"
---
| Resource | Target |
|----------|---------------|
| Egyetlen tábla maximális mérete | 500 TiB |
| Egy tábla entitás maximális mérete | 1 MiB |
| Legfeljebb egy table-entitás tulajdonságai | 255, amely három rendszertulajdonsággal tartalmazza: PartitionKey, RowKey és időbélyeg |
| Egy entitásban tulajdonságértékek maximális teljes mérete | 1 MiB |
| Egy entitásban egy adott tulajdonság maximális teljes mérete | A tulajdonság típusa szerint változó. További információkért lásd: **tulajdonságtípus** a [a Table szolgáltatás adatmodelljének ismertetése](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Táblánként tárolt hozzáférési szabályzatok maximális számát | 5 |
| Kérelem maximális díj / storage-fiók | 20 000 tranzakció / másodperc, amely feltételezi, hogy egy 1 – KiB entitás mérete |
| Egyetlen tábla partíciója (1 KiB-entitások) a célként megadott átviteli sebesség | Másodpercenként legfeljebb 2000 entitások |