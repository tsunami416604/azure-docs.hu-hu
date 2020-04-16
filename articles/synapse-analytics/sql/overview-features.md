---
title: A T-SQL szolgáltatáskülönbségei az Azure Synapse SQL-ben
description: A Synapse SQL-ben elérhető Transact-SQL-szolgáltatások listája.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: b3e4d705bea7243966959038cf15373097c73ebc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424894"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Az Azure Synapse SQL által támogatott Transact-SQL-funkciók

Az Azure Synapse SQL egy big data analitikus szolgáltatás, amely lehetővé teszi az adatok lekérdezését és elemzését a T-SQL nyelv használatával. Az SQL Server és az Azure SQL Database szabványos ANSI-kompatibilis dialektust használhat az adatok elemzéséhez. 

A Transact-SQL nyelvet a Synapse SQL kiszolgáló nélküli és kiépített modell különböző objektumokra hivatkozhat, és a támogatott szolgáltatások készletében van néhány eltérés. Ezen az oldalon a Synapse SQL felhasználási modelljei közötti magas szintű Transact-SQL nyelvi különbségeket talál.

## <a name="database-objects"></a>Adatbázis-objektumok

A Synapse SQL felhasználási modelljei lehetővé teszik a különböző adatbázis-objektumok használatát. A támogatott objektumtípusok összehasonlítása az alábbi táblázatban látható:

|   | Kiépítve | Kiszolgáló nélküli |
| --- | --- | --- |
| Táblák | [Igen](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nem, a kiszolgáló nélküli modell csak az [Azure Storage-ban](#storage-options) elhelyezett külső adatokat kérdezheti le |
| Nézetek | [Igen,](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)ez az. A nézetek [lekérdezési nyelvi elemeket](#query-language) használhatnak, amelyek a kiépített modellben érhetők el. | [Igen,](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)ez az. A nézetek kiszolgáló nélküli modellben elérhető [lekérdezési nyelvi elemeket](#query-language) használhatnak. |
| Sémák | [Igen](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Igen](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| Ideiglenes táblák | [Igen](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Nem |
| Eljárások | [Igen](/sql/t-sql/statements/create-procedure-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nem |
| Functions | [Igen](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nem |
| Eseményindítók | Nem | Nem |
| Külső táblák | [Igen,](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)ez az. Lásd a támogatott [adatformátumokat](#data-formats). | [Igen,](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)ez az. Lásd a támogatott [adatformátumokat](#data-formats). |
| Lekérdezések gyorsítótárazása | Igen, több űrlap (SSD-alapú gyorsítótárazás, memórián belüli, eredményalapú gyorsítótárazás). Ezenkívül a Materializált nézet támogatott | Nem |
| Táblaváltozók | [Nem](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), használjon ideiglenes táblákat | Nem |

## <a name="query-language"></a>Lekérdezés nyelve

A Synapse SQL-ben használt lekérdezési nyelvek a felhasználási modelltől függően különböző támogatott funkciókkal rendelkezhetnek. Az alábbi táblázat a Transact-SQL nyelvjárások legfontosabb lekérdezési nyelvi különbségeit ismerteti:

|   | Kiépítve | Kiszolgáló nélküli |
| --- | --- | --- |
| SELECT utasítás | Igen. Az [XML/FOR JSON,](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) és [PREDICT](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) transact-SQL lekérdezési záradékok nem támogatottak. | Igen. Az [XML,](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [PREDICT](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)és query tippek transact-SQL lekérdezési záradékai nem támogatottak. [Az ELTOLÁS/LEHÍVÁS](/sql/t-sql/queries/select-order-by-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#using-offset-and-fetch-to-limit-the-rows-returned) és [a KIMUTATÁS/UNPIVOT](/sql/t-sql/queries/from-using-pivot-and-unpivot?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) csak ideiglenes táblákban lévő adatok lekérdezésére használható (külső adatok nem). |
| INSERT utasítás | Igen | Nem |
| UPDATE utasítás | Igen | Nem |
| DELETE utasítás | Igen | Nem |
| EGYESÍTÉSi utasítás | Igen | Nem |
| Adatterhelés | Igen. Az előnyben részesített segédprogram a [COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) utasítás, de a rendszer támogatja mind a TÖMEGES terhelést (BCP), mind a [CETAS-t](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) az adatok betöltéséhez. | Nem |
| Adatexportálás | Igen. A [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)használatával. | Igen. A [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)használatával. |
| Típusok | Igen, minden Transact-SQL típus, kivéve [a kurzort,](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [a hierarchyid,](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [ntext, szöveg és kép,](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [sorverzió](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [Térbeli típusok](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [sql\_változat](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)és [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Igen, minden Transact-SQL típus, kivéve [a kurzort,](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [a hierarchiát](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [a szöveget és](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)a képet, [a sorverziót,](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [a Térbeli típusokat,](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)az sql [\_variantot](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), az [xml-t](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)és a Táblázat típust |
| Adatbázisközi lekérdezések | Nem | Igen, beleértve [a USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) utasítást is. |
| Beépített függvények (elemzés) | Igen, minden Transact-SQL [Analytic](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), Conversion, [Date and Time](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), Logikai, [Matematikai](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) függvény, kivéve [a CHOOSE,](/sql/t-sql/functions/logical-functions-choose-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [IIF](/sql/t-sql/functions/logical-functions-iif-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)és [PARSE](/sql/t-sql/functions/parse-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Igen, minden Transact-SQL [Analytic](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), Conversion, [Date and Time](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), Logikai, [Matematikai](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) függvény. |
| Beépített függvények (szöveg) | Igen. Minden Transact-SQL [string](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)és Collation függvény, kivéve [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) és [a TRANSLATE](/sql/t-sql/functions/translate-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Igen. Az összes Transact-SQL [string](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)és Collation függvény. |
| Beépített táblaérték-függvények | Igen, [a Transact-SQL Rowset függvények](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), kivéve [az OPENXML,](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)és [OPENROWSET függvényeket](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Igen, [a Transact-SQL Rowset függvények](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), kivéve [az OPENXML,](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)és [OPENQUERY függvényeket](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| Összesítések |  Transact-SQL beépített aggregátumok, [kivéve,](/sql/t-sql/functions/checksum-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) kivéve CHECKSUM_AGG és [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Transact-SQL beépített aggregátumok a [\_STRING AGG](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) kivételével |
| Operátorok | Igen, az összes [Transact-SQL operátor,](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) kivéve [a !>](/sql/t-sql/language-elements/not-greater-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) és [a !<](/sql/t-sql/language-elements/not-less-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Igen, az összes [Transact-SQL operátor](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| Az áramlás szabályozása | Igen. Minden [Transact-SQL Flow vezérlő utasítás,](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) kivéve [a CONTINUE,](/sql/t-sql/language-elements/continue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [GOTO,](/sql/t-sql/language-elements/goto-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [RETURN](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)és [WAITFOR utasítást](/sql/t-sql/language-elements/waitfor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Igen. Minden [Transact-SQL Flow-vezérlő utasítás,](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) kivéve `WHILE (...)` a [RETURN](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) és a SELECT lekérdezést állapotban |
| DDL utasítások (CREATE, ALTER, DROP) | Igen. A támogatott objektumtípusokra vonatkozó összes Transact-SQL DDL utasítás | Igen. A támogatott objektumtípusokra vonatkozó összes Transact-SQL DDL utasítás |

## <a name="security"></a>Biztonság

A Synapse SQL lehetővé teszi, hogy beépített biztonsági funkciókat használjon az adatok védelméhez és a hozzáférés szabályozásához. Az alábbi táblázat a Synapse SQL-felhasználási modellek közötti magas szintű különbségeket hasonlítja össze.

|   | Kiépítve | Kiszolgáló nélküli |
| --- | --- | --- |
| Bejelentkezések | N/A (az adatbázisokban csak a tartalmazott felhasználókat támogatják) | Igen |
| Felhasználók |  N/A (az adatbázisokban csak a tartalmazott felhasználókat támogatják) | Igen |
| [Tartalmazott felhasználók](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Igen. **Megjegyzés:** csak egy AAD-felhasználó lehet korlátlan rendszergazda | Igen |
| Azure Active Directory (AAD) hitelesítés | Igen, AAD-felhasználók | Igen, AAD bejelentkezések és felhasználók |
| Storage AAD áthaladási hitelesítés | Igen | Igen |
| Storage SAS-token hitelesítése | Nem | Igen, [az ADATBÁZIS HATÓKÖRE hitelesítő adatok](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) használata [külső adatforrásban](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) vagy példányszintű [hitelesítő adatokban.](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| Tárolási hozzáférési kulcs hitelesítése | Igen, [DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) használata [külső adatforrásban](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nem |
| Storage felügyelt identitáshitelesítés | Igen, [a felügyelt szolgáltatás identitásának hitelesítő adatainak](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) használatával | Igen, `Managed Identity` hitelesítő adatokkal. |
| A tárolóalkalmazás identitásának hitelesítése | [Igen](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nem |
| Engedélyek – Objektumszintű | Igen, beleértve a felhasználóknak szóló GRANT, DENY és REVOKE engedélyek et is | Igen, beleértve a támogatott rendszerobjektumok felhasználóinak/bejelentkezéseinek engedélyezéset, megtagadását és visszavonását |
| Engedélyek – Sémaszint | Igen, beleértve a séma felhasználóinak/bejelentkezéseinek engedélyezéseit is | Igen, beleértve a séma felhasználóinak/bejelentkezéseinek engedélyezéseit is |
| Engedélyek - [Adatbázis-szintű](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Igen | Igen |
| Engedélyek – [Kiszolgálószintű](/sql/relational-databases/security/authentication-access/server-level-roles) | Nem | Igen, a rendszergazda és más kiszolgálói szerepkörök támogatottak |
| Szerepkörök/csoportok | Igen (adatbázis hatóköre) | Igen (kiszolgáló- és adatbázishatókörrel) |
| Biztonsági &amp; identitásfüggvények | Egyes Transact-SQL biztonsági funkciók `CURRENT_USER`és `HAS_DBACCESS` `IS_MEMBER`operátorok: `SUSER_SNAME`, `SYSTEM_USER` `USER`, `USER_NAME` `IS_ROLEMEMBER` `EXECUTE AS`, `SESSION_USER`, `SUSER_NAME`, , , , , , ,`OPEN/CLOSE MASTER KEY` | Egyes Transact-SQL biztonsági funkciók `CURRENT_USER`és `HAS_DBACCESS` `HAS_PERMS_BY_NAME`operátorok: `SESSION_USER` `SUSER_NAME`, `SUSER_SNAME` `SYSTEM_USER`, `USER` `IS_MEMBER', 'IS_ROLEMEMBER` `IS_SRVROLEMEMBER`, `EXECUTE AS`, `REVERT`, , , , `USER_NAME`, , és . A biztonsági függvények nem használhatók külső adatok lekérdezésére (az eredményt a lekérdezésben használható változóban tárolhatja).  |
| ADATBÁZIS HATÓKÖRÉNEK HITELESÍTŐ ADATAI | Igen | Igen |

Az SQL-készlet és az IGÉNY szerinti SQL szabványos Transact-SQL nyelvet használ az adatok lekérdezéséhez. A részletes különbségeket a [Transact-SQL nyelvi útmutatóban lehet felkeresni.](/sql/t-sql/language-reference)

## <a name="tools"></a>Eszközök

Különböző eszközök kelhetnek a Synapse SQL-hez való csatlakozáshoz az adatok lekérdezéséhez.

|   | Kiépítve | Kiszolgáló nélküli |
| --- | --- | --- |
| Szinapszis Stúdió | Igen, SQL-parancsfájlok | Igen, SQL-parancsfájlok |
| Power BI | Igen | [Igen](tutorial-connect-power-bi-desktop.md) |
| Azure elemző szolgáltatás | Igen | Igen |
| Azure Data Studio | Igen | Igen, 1.14-es vagy újabb verzió. Az SQL-parancsfájlok és az SQL-jegyzetfüzetek támogatottak. |
| SQL Server Management Studio | Igen | Igen, 18.4-es vagy újabb verzió |

A legtöbb alkalmazás szabványos Transact-SQL nyelvet használ, lekérdezheti a Synapse SQL kiépített és kiszolgáló nélküli fogyasztási modelljeit.

## <a name="storage-options"></a>Tárolási lehetőségek

Az elemzett adatok különböző tárolótípusokon tárolhatók. Az alábbi táblázat az összes rendelkezésre álló tárolási lehetőséget sorolja fel:

|   | Kiépítve | Kiszolgáló nélküli |
| --- | --- | --- |
| Belső tárolás | Igen | Nem |
| Azure Data Lake v2 | Igen | Igen |
| Azure Blob Storage | Igen | Igen |

## <a name="data-formats"></a>Adatformátumok

Az elemzett adatok különböző tárolási formátumokban tárolhatók. Az alábbi táblázat az összes elemezhető adatformátumot sorolja fel:

|   | Kiépítve | Kiszolgáló nélküli |
| --- | --- | --- |
| Tagolt | [Igen](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Igen](query-single-csv-file.md) |
| CSV | Igen (a többkarakteres határolók nem támogatottak) | [Igen](query-single-csv-file.md) |
| Parketta | [Igen](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Igen](query-parquet-files.md), beleértve a [beágyazott típusú fájlokat is](query-parquet-nested-types.md) |
| Hive ORC | [Igen](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nem |
| Kaptár RC | [Igen](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nem |
| JSON | Igen | [Igen](query-json-files.md) |
| [Delta-tó](https://delta.io/) | Nem | Nem |
| [Cdm](https://docs.microsoft.com/common-data-model/) | Nem | Nem |

## <a name="next-steps"></a>További lépések
Az SQL-készlet és az SQL on-demand ajánlott eljárásokról az alábbi cikkekben talál további információkat:

- [Gyakorlati tanácsok az SQL-készlethez](best-practices-sql-pool.md)
- [Gyakorlati tanácsok az SQL igény szerinti](best-practices-sql-on-demand.md)