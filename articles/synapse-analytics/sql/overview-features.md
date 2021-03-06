---
title: A T-SQL szolgáltatásbeli különbségek a szinapszis SQL-ben
description: A szinapszis SQL-ben elérhető Transact-SQL-funkciók listája.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 6bb54cf6a5545a49b3c84df59a9ee1294b788846
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462679"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Az Azure szinapszis SQL által támogatott Transact-SQL-funkciók

Az Azure szinapszis SQL egy big data analitikus szolgáltatás, amely lehetővé teszi, hogy a T-SQL nyelv használatával lekérdezéseket és elemzéseket lehessen készíteni az adatairól. Az adatelemzéshez a SQL Server és Azure SQL Database által használt SQL-nyelv szabványos ANSI-kompatibilis dialektusát használhatja. 

A Transact-SQL nyelv használata a kiszolgáló nélküli SQL-készletben és a dedikált modellben különböző objektumokra hivatkozhat, és a támogatott funkciók némelyike eltérő lehet. Ezen az oldalon magas szintű Transact-SQL nyelvi különbségeket talál a szinapszis SQL felhasználási modelljei között.

## <a name="database-objects"></a>Adatbázis-objektumok

A szinapszis SQL felhasználási modelljei lehetővé teszik különböző adatbázis-objektumok használatát. A támogatott objektumtípusok összehasonlítása az alábbi táblázatban látható:

|   | Dedikált | Kiszolgáló nélküli |
| --- | --- | --- |
| **Táblák** | [Igen](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Nem, a kiszolgáló nélküli modell csak az [Azure Storage](#storage-options) -ba helyezett külső adatforrásokat kérdezheti le |
| **Nézetek** | [Igen](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). A nézetek a dedikált modellben elérhető [lekérdezési nyelvi elemeket](#query-language) is használhatják. | [Igen](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). A nézetek a kiszolgáló nélküli modellben elérhető [lekérdezési nyelvi elemeket](#query-language) is használhatják. |
| **Sémák** | [Igen](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | [Igen](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| **ideiglenes táblákkal** | [Igen](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Nem |
| **Eljárások** | [Igen](/sql/t-sql/statements/create-procedure-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Igen |
| **Functions** | [Igen](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Igen, csak a beágyazott táblázat értékű függvények. |
| **Eseményindítók** | Nem | Nem |
| **Külső táblák** | [Igen](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Lásd: támogatott [adatformátumok](#data-formats). | [Igen](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Lásd: támogatott [adatformátumok](#data-formats). |
| **Gyorsítótárazási lekérdezések** | Igen, több űrlap (SSD-alapú gyorsítótárazás, memóriabeli, eredményhalmazt gyorsítótárazás). Emellett a rendszer az anyagbeli nézetet is támogatja | Nem |
| **Ideiglenes változók** | [Nem](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), ideiglenes táblák használata | Nem |
| **[Tábla eloszlása](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**               | Igen | Nem |
| **[Tábla indexei](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                           | Igen | Nem |
| **[Tábla partíciói](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                     | Igen | Nem |
| **[Statisztika](develop-tables-statistics.md)**            | Igen | Igen |
| **[Számítási feladatok kezelése, erőforrás-osztályok és Egyidejűség-vezérlés](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Igen    | Nem |
| **Cost Control** | Igen, vertikális Felskálázási és leskálázási műveletek használatával. | Igen, [a Azure Portal vagy a T-SQL eljárás](https://docs.microsoft.com/azure/synapse-analytics/sql/data-processed#cost-control)használatával. |

## <a name="query-language"></a>Lekérdezés nyelve

A szinapszis SQL-ben használt lekérdezési nyelvek különböző támogatott funkciókat tartalmazhatnak a használati modelltől függően. Az alábbi táblázat a Transact-SQL-dialektusokban a legfontosabb lekérdezési nyelvi különbségeket ismerteti:

|   | Dedikált | Kiszolgáló nélküli |
| --- | --- | --- |
| **SELECT utasítás** | Igen. A Transact-SQL-lekérdezési záradékok nem támogatottak [az XML/for JSON](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)esetében, és a [Match egyezés](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) nem támogatott. | Igen. A Transact-SQL-lekérdezési záradékok nem támogatottak [az XML](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)-, a [egyezési](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), a [prediktív](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)és a lekérdezési mutatóhoz. |
| **INSERT utasítás** | Igen | Nem |
| **FRISSÍTÉSI utasítás** | Igen | Nem |
| **Utasítás törlése** | Igen | Nem |
| **MERGE utasítás** | Nem | Nem |
| **[Tranzakciók](develop-transactions.md)** | Igen | Igen, a meta-adatobjektumokra alkalmazható. |
| **[Címkék](develop-label.md)** | Igen | Nem |
| **Adatterhelés** | Igen. Az előnyben részesített segédprogram [másolási](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) utasítás, de a rendszer a tömeges betöltést (BCP) és a [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) is támogatja az adatok betöltéséhez. | Nem |
| **Adatexportálás** | Igen. A [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)használata. | Igen. A [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)használata. |
| **Típusok** | Igen, az összes Transact-SQL típus, kivéve a [kurzort](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), a [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), az [ntext, a Text és a képet](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), a [ROWVERSION](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), a [térbeli típusokat](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), az [SQL- \_ változatot](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)és az [XML](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Igen, az összes Transact-SQL-típus a [kurzor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), a [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), az [ntext, a Text és a képek](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), a [ROWVERSION](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), a [térbeli típusok](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), az [SQL \_ Variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), az [XML](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)és a Table típus kivételével |
| **Adatbázisközi lekérdezések** | Nem | Igen, beleértve a [use](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) utasítást is. |
| **Beépített függvények (elemzés)** | Igen, az összes Transact-SQL [analitikai](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), átalakítási, [dátum és idő](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), logikai, [matematikai](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) függvény, kivéve a választás, az [IIf](/sql/t-sql/functions/logical-functions-iif-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)és az [elemzés](/sql/t-sql/functions/parse-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [lehetőséget](/sql/t-sql/functions/logical-functions-choose-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Igen, az összes Transact-SQL [analitikai](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), átalakítási, [dátum és idő](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), logikai és [matematikai](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) függvény. |
| **Beépített függvények (szöveg)** | Igen. Minden Transact-SQL [karakterlánc](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)-, [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)-és rendezési függvény, kivéve a [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) és a [fordítást](/sql/t-sql/functions/translate-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Igen. Minden Transact-SQL [sztring](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)-, [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)-és rendezési funkció. |
| **Beépített Table-Value függvények** | Igen, a [Transact-SQL sorhalmaz függvények](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), kivéve a [OpenXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), a [index](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), a [LekérdezésMegnyitása](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)és a [OpenRowset](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Igen, a [Transact-SQL sorhalmaz függvények](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), kivéve a [OpenXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), a [index](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)és a [LekérdezésMegnyitása](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  |
| **Összesítések** |  Transact-SQL beépített összesítések, kivéve [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) és [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | A Transact-SQL beépített összesítései. |
| **Operátorok** | Igen, az összes [Transact-SQL operátor](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) , kivéve a [! >](/sql/t-sql/language-elements/not-greater-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) és [! <](/sql/t-sql/language-elements/not-less-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Igen, az összes [Transact-SQL operátor](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  |
| **A folyamat vezérlése** | Igen. Minden [Transact-SQL-vezérlési utasítás a](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [Folytatás](/sql/t-sql/language-elements/continue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), a [goto](/sql/t-sql/language-elements/goto-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), a [Return](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), a [use](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)és a [waitfor](/sql/t-sql/language-elements/waitfor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) kivételével | Igen. Az összes [Transact-SQL Control-flow utasítás](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) kiválaszthatja a lekérdezést a `WHILE (...)` feltételben |
| **DDL-utasítások (létrehozás, módosítás, eldobás)** | Igen. Minden olyan Transact-SQL DDL-utasítás, amely a támogatott objektumtípusok esetében érvényes | Igen. Minden olyan Transact-SQL DDL-utasítás, amely a támogatott objektumtípusok esetében érvényes |

## <a name="security"></a>Biztonság

A szinapszis SQL lehetővé teszi, hogy az adatai védelméhez és a hozzáférés szabályozásához beépített biztonsági funkciókat használjon. Az alábbi táblázat összehasonlítja a szinapszis SQL-felhasználási modellek közötti magas szintű különbségeket.

|   | Dedikált | Kiszolgáló nélküli |
| --- | --- | --- |
| **Bejelentkezések** | N/A (csak a benne foglalt felhasználók támogatottak az adatbázisokban) | Igen |
| **Felhasználók** |  N/A (csak a benne foglalt felhasználók támogatottak az adatbázisokban) | Igen |
| **[Tartalmazott felhasználók](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)** | Igen. **Megjegyzés:** csak egy Azure ad-felhasználó lehet korlátlan rendszergazda | Nem |
| **SQL Felhasználónév/jelszó hitelesítése**| Igen | Igen |
| **Azure Active Directory (Azure AD) hitelesítés**| Igen, az Azure AD-felhasználók | Igen, Azure AD-bejelentkezések és felhasználók |
| **Storage Azure Active Directory (Azure AD) továbbító hitelesítés** | Igen | Igen |
| **Storage SAS-jogkivonat hitelesítése** | Nem | Igen, az [adatbázis-hatókörrel rendelkező hitelesítő adatok](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) használata [külső adatforrásban](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) vagy példány-szintű [hitelesítő](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)adatban. |
| **Tárterület-hozzáférési kulcs hitelesítése** | Igen, [adatbázis-hatókörű hitelesítő](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) adat használata a [külső adatforrásban](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Nem |
| **Storage [felügyelt identitások](../security/synapse-workspace-managed-identity.md) hitelesítése** | Igen, [Managed Service Identity hitelesítő adat](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) használatával | Igen, a `Managed Identity` hitelesítő adatok használatával. |
| **Storage-alkalmazás identitásának hitelesítése** | [Igen](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Nem |
| **Engedélyek – objektum szintű** | Igen, beleértve a felhasználók engedélyeinek megadására, megtagadására és visszavonására vonatkozó képességet. | Igen, beleértve a felhasználók/bejelentkezések engedélyének ENGEDÉLYEZÉSét, megtagadását és visszavonását a támogatott rendszerobjektumokon |
| **Engedélyek – séma szintű** | Igen, beleértve a sémában a felhasználókra/bejelentkezésekre vonatkozó engedélyek megadásának, megtagadásának és visszavonásának képességét. | Igen, beleértve a sémában a felhasználókra/bejelentkezésekre vonatkozó engedélyek megadásának, megtagadásának és visszavonásának képességét. |
| **Engedélyek – [adatbázis szintű](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)** | Igen | Igen |
| **Engedélyek – [kiszolgáló szintű](/sql/relational-databases/security/authentication-access/server-level-roles)** | Nem | Igen, a sysadmin és más kiszolgálói szerepkörök támogatottak |
| **Engedélyek – [oszlop szintű biztonság](/azure/synapse-analytics/sql-data-warehouse/column-level-security?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Igen | Igen |
| **Szerepkörök/csoportok** | Igen (az adatbázis hatóköre) | Igen (a kiszolgáló és az adatbázis hatóköre is) |
| **Biztonsági &amp; azonosító függvények** | Egyes Transact-SQL biztonsági függvények és operátorok:,,,,  `CURRENT_USER` `HAS_DBACCESS` `IS_MEMBER` `IS_ROLEMEMBER` `SESSION_USER` , `SUSER_NAME` , `SUSER_SNAME` , `SYSTEM_USER` , `USER` , `USER_NAME` `EXECUTE AS` ,, `OPEN/CLOSE MASTER KEY` | Egyes Transact-SQL biztonsági függvények és operátorok:,,,,,,,,,,,,,  `CURRENT_USER` `HAS_DBACCESS` `HAS_PERMS_BY_NAME` `IS_MEMBER', 'IS_ROLEMEMBER` `IS_SRVROLEMEMBER` `SESSION_USER` `SESSION_CONTEXT` `SUSER_NAME` `SUSER_SNAME` `SYSTEM_USER` `USER` `USER_NAME` `EXECUTE AS` és `REVERT` . A biztonsági függvények nem használhatók külső adatok lekérdezésére (a lekérdezésben használható, változóban tárolt eredmény tárolására).  |
| **ADATBÁZIS-HATÓKÖRŰ HITELESÍTŐ ADAT** | Igen | Igen |
| **KISZOLGÁLÓ HATÓKÖRŰ HITELESÍTŐ ADATAI** | Nem | Igen |
| **Sorszintű biztonság** | [Igen](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&view=sql-server-ver15) | Nem |
| **Transzparens adattitkosítás (TDE)** | [Igen](/azure/sql-database/transparent-data-encryption-azure-sql?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&tabs=azure-portal) | Nem | 
| **Adatfelderítés és -besorolás** | [Igen](/azure/sql-database/sql-database-data-discovery-and-classification?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Nem |
| **Biztonsági rések felmérése** | [Igen](/azure/sql-database/sql-vulnerability-assessment?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Nem |
| **Advanced Threat Protection** | [Igen](/azure/sql-database/sql-database-threat-detection-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
| **Naplózás** | [Igen](/azure/sql-database/sql-database-auditing?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Nem |
| **[Tűzfalszabályok](../security/synapse-workspace-ip-firewall.md)**| Igen | Igen |
| **[Privát végpont](../security/synapse-workspace-managed-private-endpoints.md)**| Igen | Igen |

A dedikált SQL-készlet és a kiszolgáló nélküli SQL-készlet szabványos Transact-SQL nyelvet használ az adat lekérdezéséhez. A részletes különbségekért tekintse meg a [Transact-SQL nyelvi referenciáját](/sql/t-sql/language-reference).

## <a name="tools"></a>Eszközök

Az adatlekérdezéshez különféle eszközöket használhat a szinapszis SQL-hez való kapcsolódáshoz.

|   | Dedikált | Kiszolgáló nélküli |
| --- | --- | --- |
| **Synapse Studio** | Igen, SQL-parancsfájlok | Igen, SQL-parancsfájlok |
| **Power BI** | Igen | [Igen](tutorial-connect-power-bi-desktop.md) |
| **Azure Analysis Service** | Igen | Igen |
| **Azure Data Studio** | Igen | Igen, 1.18.0 vagy újabb verzió. Az SQL-parancsfájlok és az SQL-jegyzetfüzetek támogatottak. |
| **Az SQL Server Management Studio** | Igen | Igen, 18,5-es vagy újabb verzió |

> [!NOTE]
> A SSMS segítségével csatlakozhat a kiszolgáló nélküli SQL-készlethez és a lekérdezéshez. Részlegesen támogatott a 18,5-es verziótól kezdődően, csak a kapcsolódáshoz és a lekérdezésekhez használható.

Az alkalmazások többsége szabványos Transact-SQL nyelvet használ a szinapszis SQL dedikált és kiszolgáló nélküli használati modelljeinek lekérdezéséhez.

## <a name="storage-options"></a>Tárolási lehetőségek

Az elemzett adattípusok különböző tárolási típusok tárolására használhatók. A következő táblázat felsorolja az összes rendelkezésre álló tárolási beállítást:

|   | Dedikált | Kiszolgáló nélküli |
| --- | --- | --- |
| **Belső tárterület** | Igen | Nem |
| **Azure Data Lake v2** | Igen | Igen |
| **Azure Blob Storage** | Igen | Igen |
| **Azure SQL (távoli)** | Nem | Nem |
| **Azure CosmosDB tranzakciós tároló** | Nem | Nem |
| **Azure CosmosDB analitikai tároló** | Nem | Igen, a [szinapszis hivatkozás használatával (előzetes verzió)](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ([nyilvános előzetes](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#limitations)verzió) |
| **Táblák Apache Spark (a munkaterületen)** | Nem | PARKETTA-táblázatok csak [metaadat-szinkronizálás](develop-storage-files-spark-tables.md) használatával |
| **Apache Spark táblák (távoli)** | Nem | Nem |
| **Databricks táblák (távoli)** | Nem | Nem |

## <a name="data-formats"></a>Adatformátumok

Az elemzett adattárak különböző tárolási formátumokban tárolhatók. A következő táblázat felsorolja az összes elemezhető adatformátumot:

|   | Dedikált | Kiszolgáló nélküli |
| --- | --- | --- |
| **Tagolt** | [Igen](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | [Igen](query-single-csv-file.md) |
| **CSV** | Igen (a több karakterből álló határolójelek nem támogatottak) | [Igen](query-single-csv-file.md) |
| **Parquet** | [Igen](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | [Igen](query-parquet-files.md), beleértve a [beágyazott típusokkal](query-parquet-nested-types.md) rendelkező fájlokat is |
| **Kaptári ork** | [Igen](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Nem |
| **Struktúra RC** | [Igen](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Nem |
| **JSON** | Igen | [Igen](query-json-files.md) |
| **Avro** | Nem | Nem |
| **[Delta – Lake](https://delta.io/)** | Nem | Nem |
| **[CDM](https://docs.microsoft.com/common-data-model/)** | Nem | Nem |

## <a name="next-steps"></a>Következő lépések
A dedikált SQL-készlet és a kiszolgáló nélküli SQL-készlet ajánlott eljárásaival kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Ajánlott eljárások a dedikált SQL-készlethez](best-practices-sql-pool.md)
- [Ajánlott eljárások kiszolgáló nélküli SQL-készlethez](best-practices-sql-on-demand.md)
