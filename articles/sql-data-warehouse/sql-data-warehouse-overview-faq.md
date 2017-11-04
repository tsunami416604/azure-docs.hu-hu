---
title: "Azure SQL Data Warehouse kapcsolatos gyakori kérdések |} Microsoft Docs"
description: "Ez a cikk felsorolja, ügyfelek és a fejlesztők Azure SQL Data Warehouse kapcsolatos gyakran ismételt kérdések"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: 812CA525-3BF3-49DF-8DF3-FB4342464F4F
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: overview
ms.date: 3/1/2017
ms.author: elbutter;barbkess
ms.openlocfilehash: 4c00710ecc0c91f8407eca81b78176075fcbd6ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="sql-data-warehouse-frequently-asked-questions"></a>SQL Data Warehouse gyakran ismételt kérdések

## <a name="general"></a>Általános kérdések

Q. Mi nem SQL DW ajánlat adatbiztonságot?

A. SQL DW TDE például az adatok védelmének és naplózási számos megoldást kínál. További információkért lásd: [biztonsági].

Q. Hol található, milyen jogi vagy üzleti szabványok SQL DW megfelelő?

A. Látogasson el a [Microsoft Compliance] például SOC és ISO termék különböző megfelelőségi ajánlatok lapját. Először megfelelőségi cím akkor válassza ki, majd bontsa ki az Azure a Microsoft a hatókör felhő szolgáltatások szakaszában jobb oldalán a lapon, hogy milyen szolgáltatásokat az Azure-szolgáltatások megfelelőek.

Q. Kapcsolódhatnak a powerbi-ban?

A. Igen! Bár a Power bi támogatja a közvetlen lekérdezés SQL DW, nem célja a felhasználók vagy a valós idejű adatok nagy számú. A Power bi üzemi használatra ajánlott Azure Analysis Services vagy az Analysis Service IaaS Power bi használatával. 

Q. SQL Data Warehouse kapacitás korlátai

A. Tekintse meg az aktuális [kapacitáskorlátait] lap. 

Q. Miért a méretezési / / szüneteltet tart sokáig?

A. Különböző tényezők befolyásolhatják a számítási műveletek ideje. Egy közös hosszú ideig futó műveletek: a tranzakciós visszaállítási eset. A skála vagy a szüneteltetési művelet elindításakor az összes bejövő munkamenetek le vannak tiltva, és lekérdezések merül vannak-e le. Ahhoz, hogy a rendszer stabil állapotban hagyásához tranzakciók kell állítható vissza a művelet megkezdése előtt. A nagyobb számának és nagyobb naplóméret tranzakciók, minél hosszabb a művelet fog elakadt a rendszer visszaállítása egy stabil állapotot.

## <a name="user-support"></a>Felhasználói támogatás

Q. Van a szolgáltatás kérelmet, ahol küldje el?

A. Ha egy szolgáltatás kérése, küldje el a a [UserVoice] lap

Q. Hogyan tehetem x?

A. Segítségre van az SQL Data Warehouse szolgáltatással fejleszt, a kérdéseit teheti fel a [Stack Overflow] lap. 

Q. Hogyan küldhetek egy támogatási jegy?

A. [Támogatási jegy megjelenítése] nyilvántartásához Azure portálon keresztül.

## <a name="sql-languagefeature-support"></a>SQL-nyelv/szolgáltatás támogatás 

Q. Mely adattípusokat támogatja az SQL Data Warehouse?

A. Tekintse meg az SQL Data Warehouse [adattípusok].

Q. Milyen táblában funkciók támogatásához?

A. Míg az SQL Data Warehouse számos funkcióját támogatja, néhány nem támogatottak, és vannak dokumentálva [tábla nem támogatott funkciókat].

## <a name="tooling-and-administration"></a>Tooling és felügyelet

Q. Támogatott az adatbázis-projekteket a Visual Studio.

A. Jelenleg nem támogatott adatbázis-projekteket a Visual Studio az SQL Data Warehouse. Ha szeretné leadott szavazattal az ezt a szolgáltatást, látogasson el a User Voice [adatbázisprojekteket funkció kérés].

Q. Támogatja az SQL Data Warehouse REST API-kat?

A. Igen. A legtöbb REST funkciót az SQL Database használható érhető el az SQL Data Warehouse szolgáltatással. Belül dokumentációs oldalakat REST API információkat vagy [MSDN].


## <a name="loading"></a>Betöltés

Q. Milyen ügyfél-illesztőprogram támogatja?

A. DW illesztőprogram támogatása található meg a [kapcsolati karakterláncok] lap

K: milyen fájlformátumok az SQL Data Warehouse PolyBase által támogatott?

V: Orc, RC, Parquet és strukturálatlan tagolt szövegfájl

K: Mit tud kapcsolódni a PolyBase használatával SQL DW? 

V: [az Azure Data Lake Store] és [Azure Storage blobs szolgáltatásban]

K: van számítási leküldéses közzététele lehetőség, amikor csatlakozik az Azure Storage Blobsba vagy ADLS? 

A: SQL DW PolyBase nem, csak a tárolási összetevőinek együttműködik. 

K: kapcsolódni HDI?

V: HDI a HDFS rétegként ADLS vagy WASB is használhatja. Ha a HDFS rétegként vagy, majd akkor is, hogy adatok betöltése az SQL DW. Azonban nem hozható létre leküldéses közzététele számítási HDI-példányához. 

## <a name="next-steps"></a>Következő lépések
A teljes SQL Data warehouse további információkért lásd: a [áttekintése] lap.


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[kapcsolati karakterláncok]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Támogatási jegy megjelenítése]: ./sql-data-warehouse-get-started-create-support-ticket.md
[biztonsági]: ./sql-data-warehouse-overview-manage-security.md
[Microsoft Compliance]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[kapacitáskorlátait]: ./sql-data-warehouse-service-capacity-limits.md
[adattípusok]: ./sql-data-warehouse-tables-data-types.md
[tábla nem támogatott funkciókat]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[az Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Azure Storage blobs szolgáltatásban]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[adatbázisprojekteket funkció kérés]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/en-us/library/azure/mt163685.aspx
[áttekintése]: ./sql-data-warehouse-overview-faq.md