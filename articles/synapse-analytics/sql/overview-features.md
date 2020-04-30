---
title: A T-SQL szolgáltatásbeli különbségek az Azure szinapszis SQL-ben
description: A szinapszis SQL-ben elérhető Transact-SQL-funkciók listája.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: b3e4d705bea7243966959038cf15373097c73ebc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424894"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Az Azure szinapszis SQL által támogatott Transact-SQL-funkciók

Az Azure szinapszis SQL egy big data analitikus szolgáltatás, amely lehetővé teszi, hogy a T-SQL nyelv használatával lekérdezéseket és elemzéseket lehessen készíteni az adatairól. Az adatelemzéshez a SQL Server és Azure SQL Database által használt SQL-nyelv szabványos ANSI-kompatibilis dialektusát használhatja. 

Az SQL Server nélküli és a kiépített modellben a Transact-SQL nyelv használata különböző objektumokra hivatkozhat, és a támogatott funkciók némelyike eltérő lehet. Ezen az oldalon magas szintű Transact-SQL nyelvi különbségeket talál a szinapszis SQL felhasználási modelljei között.

## <a name="database-objects"></a>Adatbázis-objektumok

A szinapszis SQL felhasználási modelljei lehetővé teszik különböző adatbázis-objektumok használatát. A támogatott objektumtípusok összehasonlítása az alábbi táblázatban látható:

|   | Kiépítve | Kiszolgáló nélküli |
| --- | --- | --- |
| Táblák | [Igen](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nem, a kiszolgáló nélküli modell csak az [Azure Storage](#storage-options) -ba helyezett külső adatforrásokat kérdezheti le |
| Nézetek | [Igen](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). A nézetek használhatják a kiépített modellben elérhető [lekérdezési nyelvi elemeket](#query-language) . | [Igen](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). A nézetek a kiszolgáló nélküli modellben elérhető [lekérdezési nyelvi elemeket](#query-language) is használhatják. |
| Sémák | [Igen](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Igen](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| Ideiglenes táblák | [Igen](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Nem |
| Eljárások | [Igen](/sql/t-sql/statements/create-procedure-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nem |
| Függvény | [Igen](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nem |
| Eseményindítók | Nem | Nem |
| Külső táblák | [Igen](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Lásd: támogatott [adatformátumok](#data-formats). | [Igen](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Lásd: támogatott [adatformátumok](#data-formats). |
| Gyorsítótárazási lekérdezések | Igen, több űrlap (SSD-alapú gyorsítótárazás, memóriabeli, eredményhalmazt gyorsítótárazás). Emellett a rendszer az anyagbeli nézetet is támogatja | Nem |
| Táblázat változói | [Nem](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), ideiglenes táblák használata | Nem |

## <a name="query-language"></a>Lekérdezés nyelve

A szinapszis SQL-ben használt lekérdezési nyelvek különböző támogatott funkciókat tartalmazhatnak a használati modelltől függően. Az alábbi táblázat a Transact-SQL-dialektusokban a legfontosabb lekérdezési nyelvi különbségeket ismerteti:

|   | Kiépítve | Kiszolgáló nélküli |
| --- | --- | --- |
| SELECT utasítás | Igen. A Transact-SQL-lekérdezési záradékok nem támogatottak [az XML/JSON](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)-hoz, a Match és a [prediktív](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [egyeztetéshez](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) . | Igen. A Transact-SQL-lekérdezési záradékok nem támogatottak [az XML](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)-, a [egyezési](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), a [prediktív](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)és a lekérdezési mutatóhoz. Az [eltolás/lekérés](/sql/t-sql/queries/select-order-by-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#using-offset-and-fetch-to-limit-the-rows-returned) és a [PIVOT/unpivot](/sql/t-sql/queries/from-using-pivot-and-unpivot?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) művelettel csak az ideiglenes táblákban (nem külső adatforrásokban) lévő adatlekérdezés használható. |
| INSERT utasítás | Igen | Nem |
| FRISSÍTÉSI utasítás | Igen | Nem |
| Utasítás törlése | Igen | Nem |
| MERGE utasítás | Igen | Nem |
| Adatterhelés | Igen. Az előnyben részesített segédprogram [másolási](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) utasítás, de a rendszer a tömeges betöltést (BCP) és a [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) is támogatja az adatok betöltéséhez. | Nem |
| Adatexportálás | Igen. A [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)használata. | Igen. A [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)használata. |
| Típusok | Igen, az összes Transact-SQL típus, kivéve a [kurzort](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), a [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), az [ntext, a Text és a képet](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), a [ROWVERSION](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), a [térbeli típusokat](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), az [SQL\_-változatot](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)és az [XML](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Igen, az összes Transact-SQL-típus a [kurzor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), a [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), az [ntext, a Text és a képek](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), a [ROWVERSION](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), a [térbeli típusok](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), az [SQL\_Variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), az [XML](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)és a Table típus kivételével |
| Adatbázisközi lekérdezések | Nem | Igen, beleértve a [use](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) utasítást is. |
| Beépített függvények (elemzés) | Igen, az összes Transact-SQL [analitikai](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), átalakítási, [dátum és idő](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), logikai, [matematikai](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) függvény, kivéve a választás, az [IIf](/sql/t-sql/functions/logical-functions-iif-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)és az [elemzés](/sql/t-sql/functions/parse-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [lehetőséget](/sql/t-sql/functions/logical-functions-choose-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Igen, az összes Transact-SQL [analitikai](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), átalakítási, [dátum és idő](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), logikai és [matematikai](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) függvény. |
| Beépített függvények (szöveg) | Igen. Minden Transact-SQL [karakterlánc](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)-, [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)-és rendezési függvény, kivéve a [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) és a [fordítást](/sql/t-sql/functions/translate-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Igen. Minden Transact-SQL [sztring](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)-, [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)-és rendezési funkció. |
| Beépített Table-Value függvények | Igen, a [Transact-SQL sorhalmaz függvények](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), kivéve a [OpenXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), a [index](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), a [LekérdezésMegnyitása](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)és a [OpenRowset](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Igen, a [Transact-SQL sorhalmaz függvények](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), kivéve a [OpenXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), a [index](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)és a [LekérdezésMegnyitása](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| Összesítések |  Transact-SQL beépített összesítések, kivéve [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) és [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Transact-SQL beépített összesítések, kivéve a [String\_Agg](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| Operátorok | Igen, az összes [Transact-SQL operátor](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) , kivéve a [! >](/sql/t-sql/language-elements/not-greater-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) és [! <](/sql/t-sql/language-elements/not-less-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Igen, az összes [Transact-SQL operátor](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| A folyamat vezérlése | Igen. Minden [Transact-SQL-vezérlési utasítás a](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [Folytatás](/sql/t-sql/language-elements/continue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), a [goto](/sql/t-sql/language-elements/goto-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), a [Return](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), a [use](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)és a [waitfor](/sql/t-sql/language-elements/waitfor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) kivételével | Igen. Minden [Transact-SQL-vezérlési utasítás](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) , kivéve a [Return](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) és a Select lekérdezést `WHILE (...)` a feltételben |
| DDL-utasítások (létrehozás, módosítás, eldobás) | Igen. Minden olyan Transact-SQL DDL-utasítás, amely a támogatott objektumtípusok esetében érvényes | Igen. Minden olyan Transact-SQL DDL-utasítás, amely a támogatott objektumtípusok esetében érvényes |

## <a name="security"></a>Biztonság

A szinapszis SQL lehetővé teszi, hogy az adatai védelméhez és a hozzáférés szabályozásához beépített biztonsági funkciókat használjon. Az alábbi táblázat összehasonlítja a szinapszis SQL-felhasználási modellek közötti magas szintű különbségeket.

|   | Kiépítve | Kiszolgáló nélküli |
| --- | --- | --- |
| Bejelentkezések | N/A (csak a benne foglalt felhasználók támogatottak az adatbázisokban) | Igen |
| Felhasználók |  N/A (csak a benne foglalt felhasználók támogatottak az adatbázisokban) | Igen |
| [Tartalmazott felhasználók](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Igen. **Megjegyzés:** csak egy HRE-felhasználó lehet korlátlan rendszergazda | Igen |
| Azure Active Directory (HRE) hitelesítés | Igen, HRE-felhasználók | Igen, HRE bejelentkezések és felhasználók |
| Storage HRE átadó hitelesítés | Igen | Igen |
| Storage SAS-jogkivonat hitelesítése | Nem | Igen, az [adatbázis-hatókörrel rendelkező hitelesítő adatok](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) használata [külső adatforrásban](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) vagy példány-szintű [hitelesítő](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)adatban. |
| Tárterület-hozzáférési kulcs hitelesítése | Igen, [adatbázis-hatókörű hitelesítő](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) adat használata a [külső adatforrásban](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nem |
| Storage felügyelt identitások hitelesítése | Igen, [Managed Service Identity hitelesítő adat](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) használatával | Igen, a `Managed Identity` hitelesítő adatok használatával. |
| Storage-alkalmazás identitásának hitelesítése | [Igen](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nem |
| Engedélyek – objektum szintű | Igen, beleértve a felhasználók engedélyeinek megadására, megtagadására és visszavonására vonatkozó képességet. | Igen, beleértve a felhasználók/bejelentkezések engedélyének ENGEDÉLYEZÉSét, megtagadását és visszavonását a támogatott rendszerobjektumokon |
| Engedélyek – séma szintű | Igen, beleértve a sémában a felhasználókra/bejelentkezésekre vonatkozó engedélyek megadásának, megtagadásának és visszavonásának képességét. | Igen, beleértve a sémában a felhasználókra/bejelentkezésekre vonatkozó engedélyek megadásának, megtagadásának és visszavonásának képességét. |
| Engedélyek – [adatbázis szintű](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Igen | Igen |
| Engedélyek – [kiszolgáló szintű](/sql/relational-databases/security/authentication-access/server-level-roles) | Nem | Igen, a sysadmin és más kiszolgálói szerepkörök támogatottak |
| Szerepkörök/csoportok | Igen (az adatbázis hatóköre) | Igen (a kiszolgáló és az adatbázis hatóköre is) |
| Biztonsági &amp; azonosító függvények | Egyes Transact-SQL biztonsági függvények és operátorok `CURRENT_USER`: `HAS_DBACCESS`, `IS_MEMBER` `IS_ROLEMEMBER`,, `SESSION_USER`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS`,,`OPEN/CLOSE MASTER KEY` | Egyes Transact-SQL biztonsági függvények és operátorok `CURRENT_USER`: `HAS_DBACCESS`, `HAS_PERMS_BY_NAME` `IS_MEMBER', 'IS_ROLEMEMBER` `IS_SRVROLEMEMBER` `SESSION_USER` `SUSER_NAME` `SUSER_SNAME`,,,,,, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS`, és `REVERT`. A biztonsági függvények nem használhatók külső adatok lekérdezésére (a lekérdezésben használható, változóban tárolt eredmény tárolására).  |
| ADATBÁZIS-HATÓKÖRŰ HITELESÍTŐ ADAT | Igen | Igen |

Az SQL-készlet és az SQL on-demand standard Transact-SQL nyelvű használata az adatlekérdezéshez. A részletes különbségekért tekintse meg a [Transact-SQL nyelvi referenciáját](/sql/t-sql/language-reference).

## <a name="tools"></a>Eszközök

Az adatlekérdezéshez különféle eszközöket használhat a szinapszis SQL-hez való kapcsolódáshoz.

|   | Kiépítve | Kiszolgáló nélküli |
| --- | --- | --- |
| Szinapszis Studio | Igen, SQL-parancsfájlok | Igen, SQL-parancsfájlok |
| Power BI | Igen | [Igen](tutorial-connect-power-bi-desktop.md) |
| Azure Analysis Service | Igen | Igen |
| Azure Data Studio | Igen | Igen, 1,14-es vagy újabb verzió. Az SQL-parancsfájlok és az SQL-jegyzetfüzetek támogatottak. |
| SQL Server Management Studio | Igen | Igen, 18,4-es vagy újabb verzió |

Az alkalmazások többsége a standard Transact-SQL nyelvet használja, amely a szinapszis SQL kiépített és kiszolgáló nélküli használati modelljeit is lekérdezheti.

## <a name="storage-options"></a>Tárolási lehetőségek

Az elemzett adattípusok különböző tárolási típusok tárolására használhatók. A következő táblázat felsorolja az összes rendelkezésre álló tárolási beállítást:

|   | Kiépítve | Kiszolgáló nélküli |
| --- | --- | --- |
| Belső tárterület | Igen | Nem |
| Azure Data Lake v2 | Igen | Igen |
| Azure Blob Storage | Igen | Igen |

## <a name="data-formats"></a>Adatformátumok

Az elemzett adattárak különböző tárolási formátumokban tárolhatók. A következő táblázat felsorolja az összes elemezhető adatformátumot:

|   | Kiépítve | Kiszolgáló nélküli |
| --- | --- | --- |
| Tagolt | [Igen](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Igen](query-single-csv-file.md) |
| CSV | Igen (a több karakterből álló határolójelek nem támogatottak) | [Igen](query-single-csv-file.md) |
| Parquet | [Igen](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Igen](query-parquet-files.md), beleértve a [beágyazott típusokkal](query-parquet-nested-types.md) rendelkező fájlokat is |
| Kaptári ork | [Igen](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nem |
| Struktúra RC | [Igen](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nem |
| JSON | Igen | [Igen](query-json-files.md) |
| [Delta – Lake](https://delta.io/) | Nem | Nem |
| [CDM](https://docs.microsoft.com/common-data-model/) | Nem | Nem |

## <a name="next-steps"></a>További lépések
Az SQL Pool és az SQL on-demand ajánlott eljárásaival kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Ajánlott eljárások az SQL-készlethez](best-practices-sql-pool.md)
- [Ajánlott eljárások az SQL igény szerinti kezeléséhez](best-practices-sql-on-demand.md)