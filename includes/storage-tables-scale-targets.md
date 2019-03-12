---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: a8b4e3038bfa6a2e937de91804159e340ed13224
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553696"
---
| Erőforrás | Cél |
|----------|---------------|
| Egyetlen tábla maximális mérete | 500 TiB |
| Egy tábla entitás maximális mérete | 1 MiB |
| Legfeljebb egy table-entitás tulajdonságai | 255, amely három rendszertulajdonsággal tartalmazza: PartitionKey, RowKey és időbélyeg |
| Táblánként tárolt hozzáférési szabályzatok maximális számát | 5 |
| Kérelem maximális díj / storage-fiók | 20 000 tranzakció / másodperc, amely feltételezi, hogy egy 1 – KiB entitás mérete |
| Egyetlen tábla partíciója (1 KiB-entitások) a célként megadott átviteli sebesség | Másodpercenként legfeljebb 2000 entitások |
