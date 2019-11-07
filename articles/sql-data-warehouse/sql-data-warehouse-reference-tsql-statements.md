---
title: T-SQL-utasítások
description: A Azure SQL Data Warehouse által támogatott T-SQL-utasításokhoz tartozó dokumentációra mutató hivatkozások.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 05/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 861675941693697a20da189edebe33d91918782c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685516"
---
# <a name="t-sql-statements-supported-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse támogatott T-SQL-utasítások
A Azure SQL Data Warehouse által támogatott T-SQL-utasításokhoz tartozó dokumentációra mutató hivatkozások.

## <a name="data-definition-language-ddl-statements"></a>Az adatdefiníciós nyelv (DDL) utasításai
* [ALTER DATABASE](https://msdn.microsoft.com/library/mt204042.aspx)
* [ALTER INDEX](https://msdn.microsoft.com/library/ms188388.aspx)
* [Módosítható anyagú nézet](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest) (előzetes verzió) 
* [MÓDOSÍTÁSI ELJÁRÁS](https://msdn.microsoft.com/library/ms189762.aspx)
* [SÉMA MÓDOSÍTÁSA](https://msdn.microsoft.com/library/ms173423.aspx)
* [ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)
* [OSZLOPCENTRIKUS INDEX LÉTREHOZÁSA](https://msdn.microsoft.com/library/gg492153.aspx)
* [CREATE DATABASE](https://msdn.microsoft.com/library/mt204021.aspx)
* [ADATBÁZIS-HATÓKÖRŰ HITELESÍTŐ ADAT LÉTREHOZÁSA](https://msdn.microsoft.com/library/mt270260.aspx)
* [KÜLSŐ ADATFORRÁS LÉTREHOZÁSA](https://msdn.microsoft.com/library/dn935022.aspx)
* [CREATE EXTERNAL FILE FORMAT](https://msdn.microsoft.com/library/dn935026.aspx)
* [KÜLSŐ TÁBLA LÉTREHOZÁSA](https://msdn.microsoft.com/library/dn935021.aspx)
* [FÜGGVÉNY LÉTREHOZÁSA](https://msdn.microsoft.com/library/mt203952.aspx)
* [INDEX LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms188783.aspx)
* [Anyagbeli nézet létrehozása Select](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (előzetes verzió) nézetben 
* [ELJÁRÁS LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms187926.aspx)
* [SÉMA LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms189462.aspx)
* [CREATE STATISTICS](https://msdn.microsoft.com/library/ms188038.aspx)
* [CREATE TABLE](https://msdn.microsoft.com/library/mt203953.aspx)
* [CREATE TABLE AS SELECT](https://msdn.microsoft.com/library/mt204041.aspx)
* [NÉZET LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms187956.aspx)
* [SZÁMÍTÁSI FELADATOK BESOROLÁSÁNAK LÉTREHOZÁSA](/sql/t-sql/statements/create-workload-classifier-transact-sql)
* [KÜLSŐ ADATFORRÁS ELDOBÁSA](https://msdn.microsoft.com/library/mt146367.aspx)
* [KÜLSŐ FÁJLFORMÁTUM ELDOBÁSA](https://msdn.microsoft.com/library/mt146379.aspx)
* [KÜLSŐ TÁBLA ELDOBÁSA](https://msdn.microsoft.com/library/mt130698.aspx)
* [DROP INDEX](https://msdn.microsoft.com/library/ms176118.aspx)
* [MŰVELET ELDOBÁSA](https://msdn.microsoft.com/library/ms174969.aspx)
* [STATISZTIKÁK ELDOBÁSA](https://msdn.microsoft.com/library/ms175075.aspx)
* [TÁBLÁZAT ELDOBÁSA](https://msdn.microsoft.com/library/ms173790.aspx)
* [SÉMA ELDOBÁSA](https://msdn.microsoft.com/library/ms186751.aspx)
* [NÉZET ELDOBÁSA](https://msdn.microsoft.com/library/ms173492.aspx)
* [ELDOBÁSI SZÁMÍTÁSI FELADATOK BESOROLÁSA](/sql/t-sql/statements/drop-workload-classifier-transact-sql)
* [ÁTNEVEZÉSE](https://msdn.microsoft.com/library/mt631611.aspx)
* [RESULT_SET_CACHING BEÁLLÍTÁSA](/sql/t-sql/statements/set-result-set-caching-transact-sql) 
* [TRUNCATE TABLE](https://msdn.microsoft.com/library/ms177570.aspx)
* [UPDATE STATISTICS](https://msdn.microsoft.com/library/ms187348.aspx)

## <a name="data-manipulation-language-dml-statements"></a>Adatmanipulációs nyelvi (DML) utasítások
* [DELETE](https://msdn.microsoft.com/library/ms189835.aspx)
* [INSERT](https://msdn.microsoft.com/library/ms174335.aspx)
* [FRISSÍTÉSE](https://msdn.microsoft.com/library/ms177523.aspx)

## <a name="database-console-commands"></a>Adatbázis-konzol parancsai
* [DBCC DROPCLEANBUFFERS](https://msdn.microsoft.com/library/ms187762.aspx)
* [DBCC-DROPRESULTSETCACHE](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest) (előzetes verzió)
* [DBCC FREEPROCCACHE](https://msdn.microsoft.com/library/mt204018.aspx)
* [DBCC SHRINKLOG](https://msdn.microsoft.com/library/mt204020.aspx)
* [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)
* [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest)
* [DBCC-SHOWRESULTCACHESPACEUSED](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql) (előzetes verzió)
* [DBCC PDW_SHOWPARTITIONSTATS](https://msdn.microsoft.com/library/mt204013.aspx)
* [DBCC PDW_SHOWSPACEUSED](https://msdn.microsoft.com/library/mt204028.aspx)
* [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/mt204043.aspx)

## <a name="query-statements"></a>Lekérdezési utasítások
* [Válassza](https://msdn.microsoft.com/library/ms189499.aspx)
* [Common_table_expression](https://msdn.microsoft.com/library/ms175972.aspx)
* [KIVÉVE és METSZeni](https://msdn.microsoft.com/library/ms188055.aspx)
* [MEGMAGYARÁZNI](https://msdn.microsoft.com/library/mt631615.aspx)
* [A](https://msdn.microsoft.com/library/ms177634.aspx)
* [A PIVOT és az unpivot használata](https://msdn.microsoft.com/library/ms177410.aspx)
* [CSOPORTOSÍTÁSI ALAPJA](https://msdn.microsoft.com/library/ms177673.aspx)
* [TEKINTETTEL](https://msdn.microsoft.com/library/ms180199.aspx)
* [RENDEZÉSI SORREND](https://msdn.microsoft.com/library/ms188385.aspx)
* [OPTION](https://msdn.microsoft.com/library/ms190322.aspx)
* [UNION](https://msdn.microsoft.com/library/ms180026.aspx)
* [AHOL](https://msdn.microsoft.com/library/ms188047.aspx)
* [TOP](https://msdn.microsoft.com/library/ms189463.aspx)
* [Aliasing](https://msdn.microsoft.com/library/mt631614.aspx)
* [Keresési feltétel](https://msdn.microsoft.com/library/ms173545.aspx)
* [Allekérdezéseket](https://msdn.microsoft.com/library/mt631613.aspx)

## <a name="security-statements"></a>Biztonsági utasítások
* Engedélyek: [Engedélyezés](https://msdn.microsoft.com/library/ms187965.aspx), [Megtagadás](https://msdn.microsoft.com/library/ms188338.aspx), [Visszavonás](https://msdn.microsoft.com/library/ms187728.aspx)
* [MÓDOSÍTÁS ENGEDÉLYEZÉSE](https://msdn.microsoft.com/library/ms187359.aspx)
* [TANÚSÍTVÁNY MÓDOSÍTÁSA](https://msdn.microsoft.com/library/ms189511.aspx)
* [ADATBÁZIS TITKOSÍTÁSI KULCSÁNAK MÓDOSÍTÁSA](https://msdn.microsoft.com/library/bb630389.aspx)
* [MÓDOSÍTÁSI BEJELENTKEZÉS](https://msdn.microsoft.com/library/ms189828.aspx)
* [ALTER MASTER KEY](https://msdn.microsoft.com/library/ms186937.aspx)
* [SZEREPKÖR MÓDOSÍTÁSA](https://msdn.microsoft.com/library/ms189775.aspx)
* [FELHASZNÁLÓ MÓDOSÍTÁSA](https://msdn.microsoft.com/library/ms176060.aspx)
* [BIZTONSÁGI MENTÉSI TANÚSÍTVÁNY](https://msdn.microsoft.com/library/ms178578.aspx)
* [FŐKULCS LEZÁRÁSA](https://msdn.microsoft.com/library/ms188387.aspx)
* [TANÚSÍTVÁNY LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms187798.aspx)
* [ADATBÁZIS-TITKOSÍTÁSI KULCS LÉTREHOZÁSA](https://msdn.microsoft.com/library/bb677241.aspx)
* [BEJELENTKEZÉS LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms189751.aspx)
* [FŐKULCS LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms174382.aspx)
* [SZEREPKÖR LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms187936.aspx)
* [FELHASZNÁLÓ LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms173463.aspx)
* [TANÚSÍTVÁNY ELDOBÁSA](https://msdn.microsoft.com/library/ms179906.aspx)
* [ADATBÁZIS-TITKOSÍTÁSI KULCS ELDOBÁSA](https://msdn.microsoft.com/library/bb630256.aspx)
* [BEJELENTKEZÉS ELDOBÁSA](https://msdn.microsoft.com/library/ms188012.aspx)
* [FŐ KULCS ELDOBÁSA](https://msdn.microsoft.com/library/ms180071.aspx)
* [SZEREPKÖR ELDOBÁSA](https://msdn.microsoft.com/library/ms174988.aspx)
* [FELHASZNÁLÓ ELDOBÁSA](https://msdn.microsoft.com/library/ms189438.aspx)
* [FŐKULCS MEGNYITÁSA](https://msdn.microsoft.com/library/ms174433.aspx)

## <a name="next-steps"></a>További lépések
További információ: a [T-SQL nyelvi elemei Azure SQL Data Warehouseban](sql-data-warehouse-reference-tsql-language-elements.md)és a [rendszernézetek a Azure SQL Data Warehouseban](sql-data-warehouse-reference-tsql-system-views.md).
