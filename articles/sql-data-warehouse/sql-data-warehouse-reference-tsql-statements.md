---
title: T-SQL-utasítások – Azure SQL Data Warehouse |} A Microsoft Docs
description: Az Azure SQL Data Warehouse támogatott T-SQL-utasítások dokumentációját mutató hivatkozásokat tartalmaz.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 06/13/2018
ms.author: twounder
ms.reviewer: igorstan
ms.openlocfilehash: 8922d4399f52937710f111c3c8ad4c9af91b6bb7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57890100"
---
# <a name="t-sql-statements-supported-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse támogatott T-SQL-utasítások
Az Azure SQL Data Warehouse támogatott T-SQL-utasítások dokumentációját mutató hivatkozásokat tartalmaz.

## <a name="data-definition-language-ddl-statements"></a>Adatok definíciós nyelv (DDL) utasítások
* [ALTER DATABASE](https://msdn.microsoft.com/library/mt204042.aspx)
* [AZ ALTER INDEX](https://msdn.microsoft.com/library/ms188388.aspx)
* [AZ ALTER ELJÁRÁS](https://msdn.microsoft.com/library/ms189762.aspx)
* [AZ ALTER SCHEMA](https://msdn.microsoft.com/library/ms173423.aspx)
* [ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)
* [AZ OSZLOPCENTRIKUS INDEX LÉTREHOZÁSA](https://msdn.microsoft.com/library/gg492153.aspx)
* [CREATE DATABASE](https://msdn.microsoft.com/library/mt204021.aspx)
* [HOZZON LÉTRE AZ ADATBÁZISHOZ KÖTŐDŐ HITELESÍTŐ ADATOK](https://msdn.microsoft.com/library/mt270260.aspx)
* [KÜLSŐ ADATFORRÁS LÉTREHOZÁSA](https://msdn.microsoft.com/library/dn935022.aspx)
* [CREATE EXTERNAL FILE FORMAT](https://msdn.microsoft.com/library/dn935026.aspx)
* [KÜLSŐ TÁBLA LÉTREHOZÁSA](https://msdn.microsoft.com/library/dn935021.aspx)
* [FÜGGVÉNY LÉTREHOZÁSA](https://msdn.microsoft.com/library/mt203952.aspx)
* [INDEX LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms188783.aspx)
* [HOZZON LÉTRE ELJÁRÁST](https://msdn.microsoft.com/library/ms187926.aspx)
* [HOZZON LÉTRE SÉMÁT](https://msdn.microsoft.com/library/ms189462.aspx)
* [CREATE STATISTICS](https://msdn.microsoft.com/library/ms188038.aspx)
* [CREATE TABLE](https://msdn.microsoft.com/library/mt203953.aspx)
* [CREATE TABLE AS SELECT](https://msdn.microsoft.com/library/mt204041.aspx)
* [NÉZET LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms187956.aspx)
* [MUNKATERHELÉS-OSZTÁLYOZÓ létrehozása](/sql/t-sql/statements/create-workload-classifier-transact-sql) (előzetes verzió)
* [DOBJA EL A KÜLSŐ ADATFORRÁS](https://msdn.microsoft.com/library/mt146367.aspx)
* [DOBJA EL A KÜLSŐ FÁJLFORMÁTUM](https://msdn.microsoft.com/library/mt146379.aspx)
* [DOBJA EL A KÜLSŐ TÁBLA](https://msdn.microsoft.com/library/mt130698.aspx)
* [A DROP INDEX](https://msdn.microsoft.com/library/ms176118.aspx)
* [A DROP ELJÁRÁST](https://msdn.microsoft.com/library/ms174969.aspx)
* [A DROP STATISTICS](https://msdn.microsoft.com/library/ms175075.aspx)
* [DROP TABLE](https://msdn.microsoft.com/library/ms173790.aspx)
* [DROP SCHEMA](https://msdn.microsoft.com/library/ms186751.aspx)
* [KÖZVETLEN MEGTEKINTÉSE](https://msdn.microsoft.com/library/ms173492.aspx)
* [KÖZVETLEN MUNKATERHELÉS OSZTÁLYOZÓ](/sql/t-sql/statements/drop-workload-classifier-transact-sql) (előzetes verzió)
* [RENAME](https://msdn.microsoft.com/library/mt631611.aspx)
* [TRUNCATE TABLE](https://msdn.microsoft.com/library/ms177570.aspx)
* [UPDATE STATISTICS](https://msdn.microsoft.com/library/ms187348.aspx)

## <a name="data-manipulation-language-dml-statements"></a>Adatok adatkezelési nyelvű (DML) utasítások
* [DELETE](https://msdn.microsoft.com/library/ms189835.aspx)
* [INSERT](https://msdn.microsoft.com/library/ms174335.aspx)
* [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx)

## <a name="database-console-commands"></a>Adatbázis-konzol parancsok
* [DBCC DROPCLEANBUFFERS](https://msdn.microsoft.com/library/ms187762.aspx)
* [DBCC FREEPROCCACHE](https://msdn.microsoft.com/library/mt204018.aspx)
* [DBCC SHRINKLOG](https://msdn.microsoft.com/library/mt204020.aspx)
* [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)
* [DBCC PDW_SHOWPARTITIONSTATS](https://msdn.microsoft.com/library/mt204013.aspx)
* [DBCC PDW_SHOWSPACEUSED](https://msdn.microsoft.com/library/mt204028.aspx)
* [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/mt204043.aspx)

## <a name="query-statements"></a>Lekérdezés utasítások
* [SELECT](https://msdn.microsoft.com/library/ms189499.aspx)
* [A common_table_expression](https://msdn.microsoft.com/library/ms175972.aspx)
* [INTERSECT és kivételével](https://msdn.microsoft.com/library/ms188055.aspx)
* [EXPLAIN](https://msdn.microsoft.com/library/mt631615.aspx)
* [FROM](https://msdn.microsoft.com/library/ms177634.aspx)
* [PIVOT és az UNPIVOT használatával](https://msdn.microsoft.com/library/ms177410.aspx)
* [GROUP BY](https://msdn.microsoft.com/library/ms177673.aspx)
* [HAVING](https://msdn.microsoft.com/library/ms180199.aspx)
* [ORDER BY](https://msdn.microsoft.com/library/ms188385.aspx)
* [OPTION](https://msdn.microsoft.com/library/ms190322.aspx)
* [UNION](https://msdn.microsoft.com/library/ms180026.aspx)
* [WHERE](https://msdn.microsoft.com/library/ms188047.aspx)
* [TOP](https://msdn.microsoft.com/library/ms189463.aspx)
* [Aliasing](https://msdn.microsoft.com/library/mt631614.aspx)
* [Keresési feltétel](https://msdn.microsoft.com/library/ms173545.aspx)
* [Segédlekérdezések](https://msdn.microsoft.com/library/mt631613.aspx)

## <a name="security-statements"></a>Biztonsági utasítások
* Engedélyek: [ENGEDÉLYEZÉS](https://msdn.microsoft.com/library/ms187965.aspx), [MEGTAGADÁS](https://msdn.microsoft.com/library/ms188338.aspx), [VISSZAVONÁSA](https://msdn.microsoft.com/library/ms187728.aspx)
* [AZ ALTER ENGEDÉLYEZÉSI](https://msdn.microsoft.com/library/ms187359.aspx)
* [AZ ALTER TANÚSÍTVÁNY](https://msdn.microsoft.com/library/ms189511.aspx)
* [AZ ALTER DATABASE ENCRYPTION KEY](https://msdn.microsoft.com/library/bb630389.aspx)
* [AZ ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
* [AZ ALTER FŐKULCS](https://msdn.microsoft.com/library/ms186937.aspx)
* [SZEREPKÖR MÓDOSÍTÁSA](https://msdn.microsoft.com/library/ms189775.aspx)
* [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)
* [BIZTONSÁGI MENTÉSI TANÚSÍTVÁNY](https://msdn.microsoft.com/library/ms178578.aspx)
* [ZÁRJA BE A FŐKULCS](https://msdn.microsoft.com/library/ms188387.aspx)
* [TANÚSÍTVÁNY LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms187798.aspx)
* [ADATBÁZIS-TITKOSÍTÁSI KULCS LÉTREHOZÁSA](https://msdn.microsoft.com/library/bb677241.aspx)
* [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx)
* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx)
* [SZEREPKÖR LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms187936.aspx)
* [FELHASZNÁLÓ LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms173463.aspx)
* [DOBJA EL A TANÚSÍTVÁNY](https://msdn.microsoft.com/library/ms179906.aspx)
* [DROP DATABASE ENCRYPTION KEY](https://msdn.microsoft.com/library/bb630256.aspx)
* [DROP LOGIN](https://msdn.microsoft.com/library/ms188012.aspx)
* [DOBJA EL A FŐKULCSOT](https://msdn.microsoft.com/library/ms180071.aspx)
* [KÖZVETLEN SZEREPKÖR](https://msdn.microsoft.com/library/ms174988.aspx)
* [DROP USER](https://msdn.microsoft.com/library/ms189438.aspx)
* [NYISSA MEG A FŐKULCS](https://msdn.microsoft.com/library/ms174433.aspx)

## <a name="next-steps"></a>További lépések
Hivatkozás kapcsolatos további információkért lásd: [az Azure SQL Data Warehouse a T-SQL nyelvi elemei](sql-data-warehouse-reference-tsql-language-elements.md), és [rendszernézetek az Azure SQL Data Warehouse](sql-data-warehouse-reference-tsql-system-views.md).
