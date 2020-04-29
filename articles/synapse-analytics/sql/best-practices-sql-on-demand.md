---
title: Ajánlott eljárások az SQL igény szerinti használatára (előzetes verzió) az Azure szinapszis Analytics szolgáltatásban
description: Javaslatok és ajánlott eljárások az SQL igény szerinti használata esetén (előzetes verzió).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 1d4203141973c10fe7673f6ab9dedbc3bfdc8999
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429069"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Ajánlott eljárások az SQL igény szerinti használatára (előzetes verzió) az Azure szinapszis Analytics szolgáltatásban

Ebben a cikkben megtalálja az ajánlott eljárások gyűjteményét az SQL on-demand (előzetes verzió) használatához. Az SQL on-demand egy további erőforrás az Azure szinapszis Analyticsben.

## <a name="general-considerations"></a>Általános megfontolások

Az SQL on-demand lehetővé teszi fájlok lekérdezését az Azure Storage-fiókokban. Nem rendelkezik helyi tárolási vagy betöltési képességekkel. Ennek megfelelően az összes olyan fájl, amely a lekérdezés célja, az SQL igény szerint kívül van. A fájlok tárterületről való olvasásával kapcsolatos minden művelet hatással lehet a lekérdezés teljesítményére.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Az Azure Storage-fiók és az SQL igény szerinti elhelyezése

A késés csökkentése érdekében helyezze el az Azure Storage-fiókját és az SQL igény szerinti végpontját. A munkaterület létrehozása során kiépített Storage-fiókok és-végpontok ugyanabban a régióban találhatók.

Az optimális teljesítmény érdekében, ha más Storage-fiókokhoz is hozzáfér az SQL igény szerint, ügyeljen arra, hogy ugyanabban a régióban legyenek. Ha nem ugyanabban a régióban találhatók, az adatok hálózati átvitele nagyobb késéssel jár a távoli és a végpont régiói között.

## <a name="azure-storage-throttling"></a>Azure Storage-szabályozás

Több alkalmazás és szolgáltatás is hozzáférhet a Storage-fiókhoz. A tárolási szabályozás akkor fordul elő, ha az alkalmazások, szolgáltatások és az SQL igény szerinti munkaterhelése által generált kombinált IOPS vagy átviteli sebesség meghaladja a Storage-fiók korlátait. Ennek eredményeképpen jelentős negatív hatást tapasztal a lekérdezési teljesítményre.

A szabályozás észlelése után az SQL on-demand beépített kezeléssel rendelkezik ebben a forgatókönyvben. Az SQL igény szerint lassabban, a szabályozás feloldása után kéri a tárterületet.

> [!TIP]
> Az optimális lekérdezési műveletek végrehajtásához a Storage-fiókot más számítási feladatokkal kell kihangsúlyozni a lekérdezés végrehajtása során.

## <a name="prepare-files-for-querying"></a>Fájlok előkészítése lekérdezéshez

Ha lehetséges, készíthet fájlokat a jobb teljesítmény érdekében:

- CSV konvertálása a parkettára – a parketta oszlopos formátumú. Mivel tömörítve van, a fájlméretek kisebbek, mint a CSV-fájlok, amelyek ugyanazokat az adatmennyiségeket használják. Az SQL on-demand kevesebb időt és tárolási kérést igényel a beolvasáshoz.
- Ha egy lekérdezés egyetlen nagyméretű fájlt céloz meg, akkor a több kisebb fájlra is kihasználhatja.
- Próbálja meg a CSV-fájl méretét 10 GB alatt tartani.
- Jobb, ha azonos méretű fájlokat szeretne egy OPENROWSET elérési úthoz vagy egy külső tábla HELYéhez.
- Particionálja az adatait úgy, hogy a partíciókat különböző mappákba vagy fájlnevekre tárolja, [majd a fájlnév és a filepath függvények használatával adja meg az adott partíciókat](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Fileinfo és filepath függvények használata adott partíciók célzásához

Az adathalmazok gyakran partíciókban vannak rendszerezve. Az SQL igény szerint kérhető az adott mappák és fájlok lekérdezésére. Ez a függvény csökkenti a fájlok számát és az adatmennyiséget, amelyet a lekérdezésnek olvasni és feldolgoznia kell. A hozzáadott bónusz az, hogy jobb teljesítményt érhet el.

További információért olvassa el a [filename](develop-storage-files-overview.md#filename-function) és a [filepath](develop-storage-files-overview.md#filepath-function) függvények és példák című témakört a [megadott fájlok lekérdezéséhez](query-specific-files.md).

Ha a tárolt adatok nincsenek particionálva, érdemes particionálni, hogy ezek a függvények a fájlokra irányuló lekérdezések optimalizálására legyenek optimalizálva. Ha a [particionált Spark-táblákat](develop-storage-files-spark-tables.md) SQL-igény alapján kérdezi le, a lekérdezés automatikusan csak a szükséges fájlokat fogja megcélozni.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>A CETAS használata a lekérdezések teljesítményének és illesztésének növeléséhez

A [CETAS](develop-tables-cetas.md) az SQL igény szerint elérhető legfontosabb funkcióinak egyike. A CETAS egy párhuzamos művelet, amely létrehozza a külső tábla metaadatait, és exportálja a SELECT lekérdezési eredményeket a Storage-fiókban lévő fájlok készletére.

A CETAS használatával a lekérdezések gyakran használt részeit (például az összekapcsolt hivatkozási táblákat) a fájlok új készletéhez is tárolhatja. Ezután csatlakozhat ehhez az egyetlen külső táblához, és nem kell ismétlődő közös illesztéseket használnia több lekérdezésben.

Ahogy a CETAS a parketta-fájlokat hozza létre, a statisztikák automatikusan létrejönnek, amikor az első lekérdezés ezt a külső táblázatot célozza meg, ami jobb teljesítményt eredményez.

## <a name="next-steps"></a>További lépések

Tekintse át a gyakori problémákkal és megoldásokkal kapcsolatos [hibaelhárítási](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) cikket. Ha SQL-készlet helyett SQL-készlettel dolgozik, tekintse meg az [ajánlott eljárásokat az SQL-készlethez](best-practices-sql-pool.md) című cikkben a konkrét útmutatásért.
