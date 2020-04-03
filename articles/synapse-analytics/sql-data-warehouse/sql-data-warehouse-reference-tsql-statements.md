---
title: T-SQL-utasítások
description: A Synapse SQL-készletben támogatott T-SQL utasítások dokumentációjára mutató hivatkozások.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 011a850a44948bf5eee28a40d59dcf7672d866e8
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586369"
---
# <a name="t-sql-statements-supported-in-synapse-sql-pool"></a>A Synapse SQL-készletben támogatott T-SQL utasítások

A Synapse SQL-készletben támogatott T-SQL utasítások dokumentációjára mutató hivatkozások.

## <a name="data-definition-language-ddl-statements"></a>Adatdefiníciós nyelv (DDL) utasítások

* [ADATBÁZIS MÓDOSÍTÁSA](https://msdn.microsoft.com/library/mt204042.aspx)
* [ALTER INDEX](https://msdn.microsoft.com/library/ms188388.aspx)
* [MATERIALIZÁLT NÉZET MÓDOSÍTÁSA](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest) (előnézet) 
* [ELJÁRÁS MÓDOSÍTÁSA](https://msdn.microsoft.com/library/ms189762.aspx)
* [SÉMA MÓDOSÍTÁSA](https://msdn.microsoft.com/library/ms173423.aspx)
* [ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)
* [OSZLOPCENTRIKUS INDEX LÉTREHOZÁSA](https://msdn.microsoft.com/library/gg492153.aspx)
* [ADATBÁZIS LÉTREHOZÁSA](https://msdn.microsoft.com/library/mt204021.aspx)
* [ADATBÁZIS HATÓKÖRE HITELESÍTŐ ADATOK LÉTREHOZÁSA](https://msdn.microsoft.com/library/mt270260.aspx)
* [KÜLSŐ ADATFORRÁS LÉTREHOZÁSA](https://msdn.microsoft.com/library/dn935022.aspx)
* [CREATE EXTERNAL FILE FORMAT](https://msdn.microsoft.com/library/dn935026.aspx)
* [KÜLSŐ TÁBLA LÉTREHOZÁSA](https://msdn.microsoft.com/library/dn935021.aspx)
* [FÜGGVÉNY LÉTREHOZÁSA](https://msdn.microsoft.com/library/mt203952.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [MATERIALIZÁLT NÉZET LÉTREHOZÁSA KIJELÖLÉSKÉNT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (Előzetes verzió) 
* [ELJÁRÁS LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms187926.aspx)
* [SCHEMA LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms189462.aspx)
* [STATISZTIKÁK LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms188038.aspx)
* [TÁBLA LÉTREHOZÁSA](https://msdn.microsoft.com/library/mt203953.aspx)
* [CREATE TABLE AS SELECT](https://msdn.microsoft.com/library/mt204041.aspx)
* [NÉZET LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms187956.aspx)
* [SZÁMÍTÁSI FELADATOK OSZTÁLYOZÓJÁNAK LÉTREHOZÁSA](/sql/t-sql/statements/create-workload-classifier-transact-sql)
* [KÜLSŐ ADATFORRÁS ELDOBÁSA](https://msdn.microsoft.com/library/mt146367.aspx)
* [KÜLSŐ FÁJLFORMÁTUM ELDOBÁSA](https://msdn.microsoft.com/library/mt146379.aspx)
* [KÜLSŐ TÁBLA ELDOBÁSA](https://msdn.microsoft.com/library/mt130698.aspx)
* [DROP INDEX](https://msdn.microsoft.com/library/ms176118.aspx)
* [EJTÉSI ELJÁRÁS](https://msdn.microsoft.com/library/ms174969.aspx)
* [DROP STATISZTIKÁK](https://msdn.microsoft.com/library/ms175075.aspx)
* [TÁBLÁZAT LEDOBÁSA](https://msdn.microsoft.com/library/ms173790.aspx)
* [DROP SCHEMA](https://msdn.microsoft.com/library/ms186751.aspx)
* [LEGÖRDÜLŐ NÉZET](https://msdn.microsoft.com/library/ms173492.aspx)
* [DROP MUNKATERHELÉS-OSZTÁLYOZÓ](/sql/t-sql/statements/drop-workload-classifier-transact-sql)
* [Átnevez](https://msdn.microsoft.com/library/mt631611.aspx)
* [BEÁLLÍTÁS RESULT_SET_CACHING](/sql/t-sql/statements/set-result-set-caching-transact-sql) 
* [TÁBLÁZAT CSONKOLÁSA](https://msdn.microsoft.com/library/ms177570.aspx)
* [UPDATE STATISTICS](https://msdn.microsoft.com/library/ms187348.aspx)

## <a name="data-manipulation-language-dml-statements"></a>Adatkezelési nyelv (DML) utasítások

* [Töröl](https://msdn.microsoft.com/library/ms189835.aspx)
* [Beszúrása](https://msdn.microsoft.com/library/ms174335.aspx)
* [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx)

## <a name="database-console-commands"></a>Adatbázis-konzol parancsai

* [DBCC DROPCLEANBUFFER-EK](https://msdn.microsoft.com/library/ms187762.aspx)
* [DBCC DROPRESULTSETCACHE](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest) (előzetes verzió)
* [DBCC FREEPROCCACHE](https://msdn.microsoft.com/library/mt204018.aspx)
* [DBCC ZSUGORÍTÁS](https://msdn.microsoft.com/library/mt204020.aspx)
* [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)
* [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest)
* [DBCC SHOWRESULTCACHESpaceUsed](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql) (előzetes verzió)
* [DBCC PDW_SHOWPARTITIONSTATS](https://msdn.microsoft.com/library/mt204013.aspx)
* [DBCC PDW_SHOWSPACEUSED](https://msdn.microsoft.com/library/mt204028.aspx)
* [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/mt204043.aspx)

## <a name="query-statements"></a>Utasítások lekérdezése

* [Válassza ki](https://msdn.microsoft.com/library/ms189499.aspx)
* [A common_table_expression](https://msdn.microsoft.com/library/ms175972.aspx)
* [EXCEPT és INTERSECT](https://msdn.microsoft.com/library/ms188055.aspx)
* [Magyarázni](https://msdn.microsoft.com/library/mt631615.aspx)
* [FROM](https://msdn.microsoft.com/library/ms177634.aspx)
* [A PIVOT és az UNPIVOT használata](https://msdn.microsoft.com/library/ms177410.aspx)
* [CSOPORTOSÍTÁS SZERINT](https://msdn.microsoft.com/library/ms177673.aspx)
* [HAVING](https://msdn.microsoft.com/library/ms180199.aspx)
* [ORDER BY](https://msdn.microsoft.com/library/ms188385.aspx)
* [Lehetőség](https://msdn.microsoft.com/library/ms190322.aspx)
* [UNION](https://msdn.microsoft.com/library/ms180026.aspx)
* [Ahol](https://msdn.microsoft.com/library/ms188047.aspx)
* [Top](https://msdn.microsoft.com/library/ms189463.aspx)
* [Aliasok használata](https://msdn.microsoft.com/library/mt631614.aspx)
* [Keresési feltétel](https://msdn.microsoft.com/library/ms173545.aspx)
* [Segédlekérdezések](https://msdn.microsoft.com/library/mt631613.aspx)

## <a name="security-statements"></a>Biztonsági utasítások

* Engedélyek: [GRANT](https://msdn.microsoft.com/library/ms187965.aspx), [DENY](https://msdn.microsoft.com/library/ms188338.aspx), [VISSZAVONÁS](https://msdn.microsoft.com/library/ms187728.aspx)
* [ENGEDÉLYEZÉSI MÓDOSÍTOTT](https://msdn.microsoft.com/library/ms187359.aspx)
* [TANÚSÍTVÁNY MÓDOSÍTÁSA](https://msdn.microsoft.com/library/ms189511.aspx)
* [ADATBÁZIS-TITKOSÍTÁSI KULCS MÓDOSÍTÁSA](https://msdn.microsoft.com/library/bb630389.aspx)
* [BEJELENTKEZÉS MÓDOSÍTÁSA](https://msdn.microsoft.com/library/ms189828.aspx)
* [FŐKULCS MÓDOSÍTÁSA](https://msdn.microsoft.com/library/ms186937.aspx)
* [SZEREP MÓDOSÍTÁSA](https://msdn.microsoft.com/library/ms189775.aspx)
* [FELHASZNÁLÓ MÓDOSÍTÁSA](https://msdn.microsoft.com/library/ms176060.aspx)
* [BIZTONSÁGI MÁSOLAT](https://msdn.microsoft.com/library/ms178578.aspx)
* [FŐKULCS BEZÁRÁSA](https://msdn.microsoft.com/library/ms188387.aspx)
* [TANÚSÍTVÁNY LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms187798.aspx)
* [ADATBÁZIS-TITKOSÍTÁSI KULCS LÉTREHOZÁSA](https://msdn.microsoft.com/library/bb677241.aspx)
* [BEJELENTKEZÉS LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms189751.aspx)
* [FŐKULCS LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms174382.aspx)
* [SZEREPKÖR LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms187936.aspx)
* [FELHASZNÁLÓ LÉTREHOZÁSA](https://msdn.microsoft.com/library/ms173463.aspx)
* [DROP TANÚSÍTVÁNY](https://msdn.microsoft.com/library/ms179906.aspx)
* [ADATBÁZIS-TITKOSÍTÁSI KULCS ELVETÉSE](https://msdn.microsoft.com/library/bb630256.aspx)
* [BEJELENTKEZÉS BEDOBÁSA](https://msdn.microsoft.com/library/ms188012.aspx)
* [FŐKULCS ELVETÉSE](https://msdn.microsoft.com/library/ms180071.aspx)
* [SZEREPKÖR ELDOBÁSA](https://msdn.microsoft.com/library/ms174988.aspx)
* [FELHASZNÁLÓ ELDOBÁSA](https://msdn.microsoft.com/library/ms189438.aspx)
* [FŐKULCS MEGNYITÁSA](https://msdn.microsoft.com/library/ms174433.aspx)

## <a name="next-steps"></a>További lépések

További információt a [Synapse SQL-készlet T-SQL nyelvi elemei](sql-data-warehouse-reference-tsql-language-elements.md)és [a Rendszernézetek a Synapse SQL-készletben című témakörben talál.](sql-data-warehouse-reference-tsql-system-views.md)
