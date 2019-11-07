---
title: Azure szinapszis Analytics (korábban SQL DW) – gyakori kérdések
description: Ez a cikk az Azure szinapszis Analytics (korábban SQL DW) ügyfelektől és fejlesztőktől származó gyakori kérdéseit sorolja fel.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: f7b4f926bb9611d87c67276f754a6b596850b59d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645594"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Azure szinapszis Analytics (korábban SQL DW) – gyakori kérdések

## <a name="general"></a>Általános kérdések

K. Mi az Azure szinapszis?

A. Az Azure szinapszis egy korlátlan elemzési szolgáltatás, amely egyesíti az adattárházat és a Big adatelemzést. Lehetővé teszi, hogy a saját feltételei szerint, kiszolgáló nélküli, igény szerinti vagy kiépített erőforrásokkal lekérdezze az adatait. Az Azure szinapszis az azonnali BI-és gépi tanulási igényekhez kapcsolódóan egységes felhasználói élményt nyújt az adatgyűjtéshez, előkészítéséhez, kezeléséhez és kiszolgálásához. További információ: [Mi az az Azure szinapszis Analytics](sql-data-warehouse-overview-what-is.md).

K. Mi történt a Azure SQL Data Warehouse?

A. Az Azure szinapszis Azure SQL Data Warehouse (SQL DW) lett kifejlesztve. Az iparág vezető adattárháza a teljesítmény és a képességek teljesen új szintjére került. Továbbra is futtathatja meglévő adattárház-számítási feladatait az éles környezetben az Azure szinapszis szolgáltatással, és automatikusan kihasználhatja az előzetes verzióban elérhető új funkciók előnyeit. További információ: [Mi az az Azure szinapszis Analytics](sql-data-warehouse-overview-what-is.md).

K. Mi az SQL Analytics?

A. Az SQL Analytics az Azure szinapszis szolgáltatással általánosan elérhető nagyvállalati adattárház-funkciókra utal. További információ: [Mi az az Azure szinapszis Analytics](sql-data-warehouse-overview-what-is.md).

K. Hogyan az Azure szinapszis megkezdése?

A. További információért forduljon az [ingyenes Azure-fiókhoz](https://azure.microsoft.com/free/sql-data-warehouse/) , vagy [vegye fel a kapcsolatot az értékesítéssel](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html). 

K. Mit kínál az Azure szinapszis az adatbiztonsághoz?

A. Az Azure szinapszis számos megoldást kínál az adatok védelmére, például a TDE és a naplózásra. További információ: [Biztonság].

K. Honnan tudhatom meg, hogy az Azure szinapszis milyen jogi vagy üzleti szabványoknak felel meg?

A. Tekintse meg a [Microsoft-megfelelőség] oldalát, ahol különböző megfelelőségi ajánlatokat talál, például a SoC és az ISO terméket. Először válassza a megfelelőségi cím lehetőséget, majd az oldal jobb oldalán az Azure elemet a Microsoft hatókörű Cloud Services szakaszában, ahol megtekintheti, hogy az Azure szinapszis-kompatibilis szolgáltatások milyen szolgáltatásokat biztosítanak.

K. Csatlakozhatok Power BIhoz?

A. Igen! Habár Power BI támogatja az Azure szinapszis közvetlen lekérdezését, nem sok felhasználónak vagy valós idejű adatmennyiségnek a célja. A Power BI teljesítményének további optimalizálása érdekében érdemes lehet Power BIt használni a Azure Analysis Services vagy az Analysis Service IaaS.

K. Mik az SQL Analytics kapacitásának korlátai?

A. Tekintse meg a jelenlegi [Kapacitási korlátok] lapot. 

K. Miért van a méretezési/szüneteltetési vagy folytatási művelet?

A. Számos tényező befolyásolhatja a számítási felügyeleti műveletek idejét. A hosszú ideig futó műveletek esetében gyakori eset a tranzakciós visszaállítás. Ha egy méretezési vagy szüneteltetési műveletet kezdeményeznek, az összes bejövő munkamenet le lesz tiltva, és a rendszer leállítja a lekérdezéseket. Ahhoz, hogy a rendszer stabil állapotban maradjon, a tranzakciókat vissza kell állítani a művelet megkezdése előtt. Minél nagyobb a szám, és nagyobb a tranzakciók naplózási mérete, annál hosszabb a művelet, mert a rendszer stabil állapotba állítja vissza a műveletet.

## <a name="user-support"></a>Felhasználói támogatás

K. Van egy szolgáltatási kérésem, hová tudom elküldeni?

A. Ha van szolgáltatási kérése, küldje el a [UserVoice-on] oldalon

K. Hogyan tehetek x-t?

A. Ha segítségre van szüksége az Azure szinapszis használatával való fejlesztéshez, kérdéseket tehet fel [stack overflow] oldalunkon. 

K. Hogyan támogatási jegyet küldeni?

A. A [Támogatási jegyek] Azure Portalon keresztül lehet benyújtani.

## <a name="sql-languagefeature-support"></a>SQL nyelv/funkció támogatása 

K. Milyen adattípusok támogatottak?

A. Lásd [az adattípusokat].

K. Milyen funkciókat támogat?

A. Számos funkció támogatott, azok, amelyek nem támogatottak, és nincsenek dokumentálva a nem támogatott [Nem támogatott táblázat-funkciók].

## <a name="tooling-and-administration"></a>Eszközök és felügyelet

K. Támogatja az adatbázis-projekteket a Visual Studióban.

A. Jelenleg nem támogatjuk az adatbázis-projekteket a Visual Studióban. Ha szavazást szeretne kapni a funkció beszerzéséhez, látogasson el a felhasználói hang [Adatbázis-projektekhez tartozó szolgáltatás kérése].

K. Támogatja az SQL Analytics a REST API-kat?

A. Igen. Az SQL Database használatával használható legtöbb REST-funkció az SQL Analytics szolgáltatásban is elérhető. A REST dokumentációs oldalain vagy az [MSDN]-en találhat API-információkat.


## <a name="loading"></a>Betöltés

K. Milyen ügyfél-illesztőprogramokat támogat?

A. A DW illesztőprogram-támogatása a [Kapcsolati sztringek] oldalon található.

K: milyen fájlformátumokat támogat a Base?

A: ork, RC, Parquet és laposan tagolt szöveg

K: milyen adatforrásokhoz csatlakozhatok a Base használatával? 

A: [Azure Data Lake Store] és [Azure Storage-Blobok]

K: az Azure Storage-Blobokhoz vagy-ADLS való csatlakozáskor lehetséges a számítási pushdown? 

A: nem, a csak a Storage-összetevőkkel kommunikáló 

K: csatlakozhatok a HDI szolgáltatáshoz?

A: a HDI a ADLS vagy a WASB is használhatja a HDFS rétegként. Ha a HDFS rétege van, akkor az adatait az SQL DW-be töltheti be. Azonban nem hozhatja pushdown számítást a HDI-példányra. 

## <a name="next-steps"></a>További lépések
Az Azure szinapszis egészére vonatkozó további információkért tekintse meg az [Áttekintés] oldalt.


<!-- Article references -->
[UserVoice-on]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Kapcsolati sztringek]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Támogatási jegyek]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Biztonság]: ./sql-data-warehouse-overview-manage-security.md
[Microsoft-megfelelőség]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[Kapacitási korlátok]: ./sql-data-warehouse-service-capacity-limits.md
[az adattípusokat]: ./sql-data-warehouse-tables-data-types.md
[Nem támogatott táblázat-funkciók]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Azure Storage-Blobok]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Adatbázis-projektekhez tartozó szolgáltatás kérése]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/library/azure/mt163685.aspx
[Áttekintés]: ./sql-data-warehouse-overview-faq.md
