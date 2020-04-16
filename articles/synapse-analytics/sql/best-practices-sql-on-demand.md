---
title: Gyakorlati tanácsok az SQL igény szerinti (előzetes verzióhoz) az Azure Synapse Analytics szolgáltatásban
description: Javaslatok és gyakorlati tanácsok, amelyeket az SQL igény szerinti (előzetes verzió) rendszerrel való munka közben ismernie kell.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429069"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Gyakorlati tanácsok az SQL igény szerinti (előzetes verzióhoz) az Azure Synapse Analytics szolgáltatásban

Ebben a cikkben az SQL igény szerinti (előzetes verzió) használatával kapcsolatos gyakorlati tanácsok gyűjteményét találja. Az SQL igény szerinti egy további erőforrás az Azure Synapse Analytics szolgáltatásban.

## <a name="general-considerations"></a>Általános megfontolások

SQL igény szerinti lehetővé teszi, hogy az Azure storage-fiókok ban fájlok lekérdezése. Nem rendelkezik helyi tároló- vagy betöltési képességekkel. Mint ilyen, minden olyan fájl, amelya lekérdezési célok külső SQL on-demand. A fájlok tárolásból való olvasásával kapcsolatos minden hatással lehet a lekérdezés teljesítményére.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Az Azure Storage-fiók és az SQL igény szerinti közös áthelyezése

A késés minimalizálása érdekében helyezze el az Azure-tárfiókot és az SQL igény szerinti végpontját. A munkaterület létrehozása során kiépített tárfiókok és végpontok ugyanabban a régióban találhatók.

Az optimális teljesítmény érdekében, ha az SQL igény szerinti más tárfiókokhoz is hozzáfér, győződjön meg arról, hogy ugyanabban a régióban vannak. Ha nem ugyanabban a régióban, az adatok közötti hálózati adatátvitel a távoli és a végpont régiói között nagyobb lesz.

## <a name="azure-storage-throttling"></a>Az Azure Storage szabályozása

Több alkalmazás és szolgáltatás is hozzáférhet a tárfiókhoz. A tárolási szabályozás akkor történik, amikor az alkalmazások, szolgáltatások és az SQL-alapú számítási feladatok által létrehozott kombinált IOPS vagy átviteli kapacitás meghaladja a tárfiók korlátait. Ennek eredményeképpen jelentős negatív hatással lesz a lekérdezés teljesítményére.

A szabályozás észlelése után az SQL igény szerinti rendszer rendelkezik a forgatókönyv beépített kezelésével. SQL igény szerinti lesz, hogy a kérelmeket a tárolás lassabb ütemben, amíg a szabályozás feloldása.

> [!TIP]
> Az optimális lekérdezés-végrehajtás érdekében ne terhelje ki a tárfiókot más számítási feladatokkal a lekérdezés végrehajtása során.

## <a name="prepare-files-for-querying"></a>Fájlok előkészítése lekérdezésre

Ha lehetséges, előkészítheti a fájlokat a jobb teljesítmény érdekében:

- A CSV átalakítása parketta - A parketta oszlopos formátum. Mivel tömörített, a fájlméretek kisebbek, mint az azonos adatokkal rendelkező CSV-fájlok. Az SQL igény szerinti olvasásához kevesebb időre és tárolási kérelmekre van szükség.
- Ha egy lekérdezés egyetlen nagy fájlt céloz meg, előnyös, ha több kisebb fájlra osztja fel.
- Próbálja meg a CSV fájlméretét 10 GB alatt tartani.
- Jobb, ha azonos méretű fájlokat egyetlen OPENROWSET elérési úthoz vagy egy külső tábla HELYÉHEZ.
- Partisítsa az adatokat a partíciók különböző mappákba vagy fájlnevekbe való tárolásával - ellenőrizze [a fájlnév és a filepath függvények használatát adott partíciók célzásához.](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Adott partíciók célzásához használjon fileinfo és filepath függvényeket

Az adatok gyakran partíciókba vannak rendezve. Utasíthatja az SQL-t, hogy bizonyos mappákat és fájlokat kérdezzen le. Ez a funkció csökkenti a lekérdezés olvasásához és feldolgozásához szükséges fájlok számát és adatmennyiségét. A hozzáadott bónusz az, hogy jobb teljesítményt érhet el.

További információért ellenőrizze [a fájlnév-](develop-storage-files-overview.md#filename-function) és [fájlelérési funkciókat,](develop-storage-files-overview.md#filepath-function) valamint példákat arra vonatkozóan, hogyan [lehet lekérdezni bizonyos fájlokat.](query-specific-files.md)

Ha a tárolt adatok nincsenek particionálva, fontolja meg a particionálást, hogy ezekkel a függvényekkel optimalizálhassa a fájlokat célzó lekérdezéseket. [Particionált Spark-táblák lekérdezésekor](develop-storage-files-spark-tables.md) az SQL igény szerint, a lekérdezés automatikusan csak a szükséges fájlokat célozza meg.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>A CETAS használata a lekérdezési teljesítmény növeléséhez és az illesztéshez

A [CETAS](develop-tables-cetas.md) az SQL igény szerinti egyik legfontosabb szolgáltatása. A CETAS egy párhuzamos művelet, amely külső táblametaadatokat hoz létre, és a SELECT lekérdezés eredményeit a tárfiókban lévő fájlok készletébe exportálja.

A CETAS segítségével a lekérdezések gyakran használt részeit, például az illesztett referenciatáblákat egy új fájlkészletben tárolhatja. Ezután csatlakozhat ehhez az egyetlen külső táblához ahelyett, hogy több lekérdezésben megismételné a gyakori illesztéseket.

Mivel a CETAS parattfájlokat hoz létre, a statisztika automatikusan létrejön, amikor az első lekérdezés ezt a külső táblát célozza meg, ami jobb teljesítményt eredményez.

## <a name="next-steps"></a>További lépések

Tekintse át a [hibaelhárítási](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) cikket a gyakori problémákért és megoldásokért. Ha az SQL-készlet telje, nem pedig az SQL-on-demand, tekintse meg az [SQL-készlet gyakorlati tanácsai](best-practices-sql-pool.md) cikket a konkrét útmutatást.
