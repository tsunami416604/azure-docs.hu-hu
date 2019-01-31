---
title: Az Azure SQL Data Warehouse kibocsátási megjegyzések a 2018 December |} A Microsoft Docs
description: Kibocsátási megjegyzések az Azure SQL Data warehouse-hoz.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 12/12/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 8e82e352ebea4634b1b99864245adcf606352657
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469336"
---
# <a name="whats-new-in-azure-sql-data-warehouse-version---100101060"></a>What's new in Azure SQL Data Warehouse verzió - 10.0.10106.0?
Az Azure SQL Data Warehouse (az SQL DW) folyamatos javítása. Ez a cikk ismerteti az új szolgáltatásokat és változásokat tartalmazza, amelyek az SQL DW 10.0.10106.0 verzió.

## <a name="query-restartability---ctas-and-insertselect"></a>Lekérdezés Restartability – valamint CTA- és Insert/kiválasztása
Ritka esetekben (vagyis időszakos hálózati kapcsolati problémák, csomóponthibák) lekérdezések végrehajtása az Azure SQL DW sikertelen lehet. Már fut az utasításokat, például [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-ctas) és INSERT-válasszon operations, több lehetséges probléma vannak kitéve. Ezzel a kiadással az Azure SQL DW CTAS és INSERT-válasszon (mellett válassza ki a korábban bejelentett utasítások) utasítások, így a rendszer transzparens módon kezeli az ilyen átmeneti problémák és lekérdezések meggátolja, hogy sikertelen újrapróbálkozási logikát valósítja meg. Az újrapróbálkozások számát és az átmeneti hibák kezelése listája olyan rendszer konfigurálva.

## <a name="return-order-by-optimization"></a>Optimalizálás által visszaadott sorrend
VÁLASSZON... Az ORDER BY lekérdezések get teljesítménynek ebben a kiadásban.  Korábban a lekérdezést végrehajtó motor lenne rendelési eredmények minden számítási csomópont, majd azok streamelésére az a vezérlő csomópont, amely kellene majd egyesítse az eredményeket. Ez a fejlesztés, az összes számítási csomópontok inkább küldése az eredményeiket egyetlen számítási csomópont, amely majd összefűzi azokat, és a rendezett eredményeket ad vissza a felhasználónak a számítási csomóponton keresztül.  Ez jelentős teljesítménybeli nyereség kínál, ha a lekérdezés eredményhalmazában nagy számú sort tartalmaz.

## <a name="data-movement-enhancements-for-partitionmove-and-broadcastmove"></a>Adatok áthelyezése fejlesztések PartitionMove és BroadcastMove
Az Azure SQL Data Warehouse Gen2 ShuffleMove típusú adatok mozgását lépéseket azonnali data módszerekkel történő leírt kihasználhatja a [teljesítményének fejlesztései itt blog](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/).  Ebben a kiadásban az adatáthelyezési adattípusok PartitionMove és BroadcastMove most is működteti az azonos azonnali data módszerekkel történő.  Felhasználói lekérdezések, amelyek ténylegesen használják az ilyen típusú adatok mozgását lépéseket teljesítménynek jelenik meg.  Kód változatlan ezek teljesítménynövekedést kihasználásához szükséges.

## <a name="next-steps"></a>További lépések
Most, hogy jobban megismerte az SQL Data warehouse, megtudhatja, hogyan lehet gyorsan [hozzon létre egy SQL Data Warehouse][create a SQL Data Warehouse]. Ha most ismerkedik az Azure-ba, hasznosnak találhatja a [Azure szószedet] [ Azure glossary] hasznos megtudhatja az új fogalmakkal ismerkedik. Vagy tekintsen meg néhányat a többi SQL Data Warehouse-erőforrás közül.  

* [Ügyfelek sikertörténetei]
* [Blogok]
* [Funkciókérések]
* [Videók]
* [Az ügyféltanácsadói csapat blogjai]
* [Stack Overflow-fórum]
* [Twitter]


[Blogok]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Az ügyféltanácsadói csapat blogjai]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Ügyfelek sikertörténetei]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Funkciókérések]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow-fórum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videók]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
