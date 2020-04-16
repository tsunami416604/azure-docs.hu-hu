---
title: Azure Synapse Analytics (korábban SQL DW) – gyakori kérdések
description: Ez a cikk felsorolja az Azure Synapse Analytics (korábban SQL DW) kapcsolatos gyakori kérdéseket az ügyfelektől és fejlesztőktől
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 260086d186f7e2b2d6f76c437057f5fbc8af39b5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416074"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Azure Synapse Analytics (korábban SQL DW) gyakori kérdések

## <a name="general"></a>Általános kérdések

K. Mi az az Azure Synapse?

A. Az Azure Synapse egy korlátlan elemzési szolgáltatás, amely egyesíti az adattárházat és a Big Data-elemzéseket. Ez lehetővé teszi, hogy lekérdezze az adatokat a feltételek, akár kiszolgáló nélküli on-demand vagy kiépített erőforrások - nagy méretekben. Az Azure Synapse egyesíti ezt a két világot egy egységes felhasználói felülettel, amely lenyeli, előkészíti, kezeli és kiszolgálja az adatokat az azonnali BI és gépi tanulási igényekhez. További információ: [Mi az Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

K. Mi történt az Azure SQL Data Warehouse?Mi történt az Azure SQL Data Warehouse?

A. Az Azure Synapse az Azure SQL Data Warehouse (SQL DW) fejlődött. Ugyanazt az iparágvezető adatraktárt a teljesítmény és a képességek egy teljesen új szintjére vittük. Folytathatja a meglévő adattárház-számítási feladatok futtatását éles környezetben az Azure Synapse segítségével, és automatikusan élvezheti az új képességek előnyeit, amelyek előzetes verzióban vannak. További információ: [Mi az Azure Synapse Analytics.](sql-data-warehouse-overview-what-is.md)

K. Mi a Synapse SQL-készlet?

A. A Synapse SQL-készlet az Azure Synapse-val általánosan elérhető vállalati adattárház-funkciókra hivatkozik. További információ: [Mi az Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

K. Hogyan kezdhetem el az Azure Synapse?

A. További információkért ismerkedhet meg egy [ingyenes Azure-fiókkal,](https://azure.microsoft.com/free/sql-data-warehouse/) vagy [kapcsolatba léphet az értékesítéssel.](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html)

K. Mit kínál az Azure Synapse az adatbiztonságért?

A. Az Azure Synapse számos megoldást kínál az adatok, például a TDE és a naplózás védelmére. További információ: [Security](sql-data-warehouse-overview-manage-security.md).

K. Hol találhatom meg, hogy az Azure Synapse milyen jogi vagy üzleti szabványoknak felel meg?

A. Látogasson el a [Microsoft megfelelőségi](https://www.microsoft.com/trustcenter/compliance/complianceofferings) oldalára, ahol termékenkénti megfelelőségi ajánlatokat, például SOC-t és ISO-t keres.
Először válasszon a Megfelelőségi cím szerint. Ezután bontsa ki az Azure-t a Microsoft hatókörön kívül lévő felhőszolgáltatások szakaszban a lap jobb oldalán, hogy milyen szolgáltatásoknak felelmeg az Azure Synapse.

K. Csatlakoztatható a Power BI?

A. Igen! Bár a Power BI támogatja a közvetlen lekérdezést az Azure Synapse segítségével, nem nagyszámú felhasználó nak vagy valós idejű adatnak készült. A Power BI teljesítményének további optimalizálásához fontolja meg a Power BI használatát az Azure Analysis Services vagy az Analysis Service IaaS mellett.

K. Mik a Synapse SQL-készlet kapacitáskorlátai?

A. Tekintse meg az aktuális [kapacitáskorlátok](sql-data-warehouse-service-capacity-limits.md) oldalt.

K. Miért tart ilyen sokáig a Mérleg/Szünet/Önéletrajz?

A. Számos tényező befolyásolhatja a számítási felügyeleti műveletek idejét. A hosszú ideig futó műveletek gyakori esete a tranzakciós visszaállítás. Méretezési vagy szüneteltetési művelet indításakor a rendszer minden bejövő munkamenetet blokkol, és a lekérdezéseket kiüríti. Annak érdekében, hogy a rendszer stabil állapotban legyen, a tranzakciókat vissza kell görgetni, mielőtt a művelet megkezdődhetne. Minél nagyobb a tranzakciók száma és nagyobb a naplómérete, annál hosszabb ideig tart a művelet a rendszer stabil állapotba való visszaállításának.

## <a name="user-support"></a>Felhasználói támogatás

K. Funkciókérésem van, hol küldhetem be?

A. Ha funkciókérése van, küldje el a [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse) oldalunkon

K. Hogyan tudom x?

A. Ha segítségre van szüksége az Azure Synapse használatával kapcsolatos fejlesztéshez, kérdéseket tehet fel a [Veremtúlcsordulás](https://stackoverflow.com/questions/tagged/azure-sqldw) oldalon.

K. Hogyan küldhetek be támogatási jegyet?

A. [Támogatási jegyek](sql-data-warehouse-get-started-create-support-ticket.md) az Azure Portalon keresztül is benyújthatók.

## <a name="sql-languagefeature-support"></a>SQL nyelv/szolgáltatás támogatása

K. Milyen adattípusok támogatottak?

A. Lásd: [adattípusok](sql-data-warehouse-tables-data-types.md).

K. Milyen táblázatfunkciókat támogat?

A. Számos funkció támogatott. A nem támogatott szolgáltatások a [nem támogatott táblaszolgáltatások](sql-data-warehouse-tables-data-types.md)ban találhatók.

## <a name="tooling-and-administration"></a>Szerszámozás és adminisztráció

K. A Synapse SQL-készlet támogatja a REST API-kat?

A. Igen. Az SQL Database-ben használható LEGTÖBB REST-funkció a Synapse SQL-készlettel is elérhető. Az API-információk a REST [dokumentációs](/rest/api/sql/databases?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)oldalain vagy az adatbázisokban találhatók.

## <a name="loading"></a>Betöltése

K. Milyen ügyfél-illesztőprogramokat támogat?

A. A Synapse SQL-készlet illesztőprogram-támogatása megtalálható a [Kapcsolati karakterláncok](../sql/connection-strings.md) lapon

K: Milyen fájlformátumokat támogat a PolyBase?

V: Ork, RC, Parketta és sík tagolt szöveg

K: Milyen adatforrásokhoz csatlakozhatok a PolyBase használatával?

V: [Azure Data Lake Storage](sql-data-warehouse-load-from-azure-data-lake-store.md) és Azure Storage [Blobs](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

K: Lehetséges a számítási leküldéses leküldéses leküldéses leküldéses leküldéses lehetőség az Azure Storage Blobs vagy az ADLS-hez való csatlakozáskor?

A: Nem, a PolyBase csak a tárolóösszetevőkkel kommunikál.

K: Csatlakozhatok a HDI-hez?

A: A HDI az ADLS vagy a WASB hdfs-rétegként használható. Ha van bármelyik a HDFS-réteg, betöltheti az adatokat egy Synapse SQL-készletbe. A HDI-példányhoz azonban nem hozhat létre leküldéses számítást.

## <a name="next-steps"></a>További lépések

Az Azure Synapse egészére vonatkozó további információkért tekintse meg [áttekintési oldalunkat.](sql-data-warehouse-overview-faq.md)
