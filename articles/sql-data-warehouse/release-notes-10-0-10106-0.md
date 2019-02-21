---
title: Az Azure SQL Data Warehouse-kibocsátási megjegyzései |} A Microsoft Docs
description: Kibocsátási megjegyzések az Azure SQL Data warehouse-hoz.
services: sql-data-warehouse
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/09/2019
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
manager: craigg
ms.openlocfilehash: 2de7c335e56117f2a99db5150575ed94616467e9
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56455585"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Az Azure SQL Data Warehouse kibocsátási megjegyzései
Ez a cikk összefoglalja a új funkciókat és fejlesztéseket a legutóbbi kiadásaiban [Azure SQL Data Warehouse](sql-data-warehouse-overview-what-is.md). A cikk nem közvetlenül kapcsolódó kiadására, de az időkeretből közzétett jelentős tartalomfrissítéseket is megjeleníti. Más Azure-szolgáltatások fejlesztései, lásd: [szolgáltatási hírek](https://azure.microsoft.com/updates)

## <a name="sql-data-warehouse-version-100101060-january"></a>Az SQL Data Warehouse verzió 10.0.10106.0 (január)

### <a name="service-improvements"></a>Szolgáltatás fejlesztései

| Szolgáltatás fejlesztései | Részletek |
| --- | --- |
|**Optimalizálás által visszaadott sorrend**|VÁLASSZON... Az ORDER BY lekérdezések get teljesítménynek ebben a kiadásban.   Most számítási csomópontok küldése az eredményeiket egyetlen számítási csomópont, amely egyesíti, valamint rendezi az eredményeket, amely a rendszer visszairányítja a felhasználót a számítási csomóponton keresztül.  Egyesítése egy egyetlen számítási csomópont eredményeket jelentős teljesítménybeli nyereség keresztül, ha a lekérdezés eredményhalmazában nagy számú sort tartalmaz. Korábban a lekérdezést végrehajtó motor lenne rendelési eredmények minden számítási csomópont, majd azok streamelésére az a vezérlő csomópont, amely kellene majd egyesítse az eredményeket.|
|**Adatok áthelyezése fejlesztések PartitionMove és BroadcastMove**|Az Azure SQL Data Warehouse Gen2, adatok mozgását lépéseket típusú ShuffleMove, használja a azonnali data módszerekkel történő leírt a [teljesítményének fejlesztései blog](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/). Ebben a kiadásban az adatáthelyezési adattípusok PartitionMove és BroadcastMove most is működteti az azonos azonnali data módszerekkel történő. Felhasználói lekérdezések, amelyek ténylegesen használják az ilyen típusú adatok mozgását lépéseket javult teljesítményű fog futni. A teljesítménnyel kapcsolatos fejlesztések kihasználása kód változatlan van szükség.|
|**Jelentős hibák**|Azure SQL Data Warehouse verziója nem kompatibilis - ' @ VÁLASSZA@VERSION"a megfelelő verziót, 10.0.9999.0 adhatnak vissza. A jelenlegi kiadásban helyes verziószáma 10.0.10106.0. Ezt a hibát jelentett és felülvizsgálat alatt áll.

### <a name="documentation-improvements"></a>Dokumentáció fejlesztései

| Dokumentáció fejlesztései | Részletek |
| --- | --- |
|nincs | |
| | |

## <a name="next-steps"></a>További lépések
- [SQL Data Warehouse létrehozása](./create-data-warehouse-portal.md)

## <a name="more-information"></a>További információ
- [Blog – az Azure SQL Data warehouse-bA](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Az ügyféltanácsadói csapat blogjai](https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/)
- [Ügyfelek sikertörténetei](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Stack Overflow-fórum](http://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [Videók](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Azure szószedet](../azure-glossary-cloud-terminology.md)
