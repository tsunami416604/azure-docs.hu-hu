---
title: A szinapszis SQL hibaelhárítása
description: A szinapszis SQL hibaelhárítása Az Azure szinapszis Analyticsben.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 44755ab13b95db1ffec8183d00a4054e291c5a50
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86039024"
---
# <a name="troubleshooting-synapse-sql-in-azure-synapse-analytics"></a>A szinapszis SQL hibaelhárítása Az Azure szinapszis Analyticsben

Ez a cikk a szinapszis SQL általános hibaelhárítási problémáit sorolja fel.

## <a name="connecting"></a>Csatlakozás

| Probléma                                                        | Megoldás:                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Az „NT AUTHORITY\ANONYMOUS LOGON” felhasználó bejelentkezése nem sikerült. (Microsoft SQL Server, hiba: 18456) | Ez a hiba akkor fordul elő, amikor egy Azure AD-felhasználó megpróbál csatlakozni a Master adatbázishoz, de nem rendelkezik a főkiszolgálóval.  A probléma megoldásához adja meg azt az SQL-készletet, amelyhez csatlakozni szeretne, vagy adja hozzá a felhasználót a Master adatbázishoz.  További részletekért tekintse meg a [Biztonság áttekintése](sql-data-warehouse-overview-manage-security.md) című cikket. |
| A „MyUserName” kiszolgálói tag a jelenlegi biztonsági környezetben nem tud hozzáférni a „master” adatbázishoz. Nem lehet megnyitni a felhasználói alapértelmezett adatbázist. A bejelentkezés sikertelen volt. A következő felhasználó bejelentkezése nem sikerült: „MyUserName”. (Microsoft SQL Server, hiba: 916) | Ez a hiba akkor fordul elő, amikor egy Azure AD-felhasználó megpróbál csatlakozni a Master adatbázishoz, de nem rendelkezik a főkiszolgálóval.  A probléma megoldásához adja meg azt az SQL-készletet, amelyhez csatlakozni szeretne, vagy adja hozzá a felhasználót a Master adatbázishoz.  További részletekért tekintse meg a [Biztonság áttekintése](sql-data-warehouse-overview-manage-security.md) című cikket. |
| CTAIP hiba                                                  | Ez a hiba akkor fordulhat elő, ha a SQL Database Master adatbázisban létrehozott egy bejelentkezési azonosítót, de nem az adott SQL-adatbázisban.  Ha ezt a hibát tapasztalja, tekintse meg a [biztonsági áttekintést ismertető](sql-data-warehouse-overview-manage-security.md) cikket.  Ez a cikk bemutatja, hogyan hozhat létre egy felhasználónevet és egy felhasználót a Master adatbázisban, majd hogyan hozhat létre felhasználót egy SQL-adatbázisban. |
| Tűzfal blokkolja                                          | Az SQL-készleteket tűzfalak védik, hogy csak az ismert IP-címek férhessenek hozzá egy adatbázishoz. Alapértelmezés szerint a tűzfalak biztonságosak, ami azt jelenti, hogy a kapcsolat megkezdése előtt explicit módon engedélyeznie kell és meg kell adnia az IP-címet vagy a címtartományt.  A tűzfal hozzáférésre való konfigurálásához kövesse a [kiszolgáló tűzfal-hozzáférés konfigurálása az ügyfél IP](create-data-warehouse-portal.md) -címére című témakör lépéseit a [létesítési utasításokban](create-data-warehouse-portal.md). |
| Nem lehet kapcsolatot létesíteni az eszközzel vagy az illesztőprogrammal                           | A szinapszis SQL Pool a [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [a SSDT for Visual Studio](sql-data-warehouse-install-visual-studio.md)vagy a [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) használatát javasolja az adatai lekérdezéséhez. További információ az illesztőprogramokról és az Azure Szinapszishoz való csatlakozásról: [Az Azure szinapszis-illesztőprogramok](sql-data-warehouse-connection-strings.md) és [Az Azure szinapszis-cikkek csatlakoztatása](sql-data-warehouse-connect-overview.md) . |

## <a name="tools"></a>Eszközök

| Probléma                                                        | Megoldás:                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| A Visual Studio Object Explorerből hiányzik az Azure AD-felhasználók           | Ez egy ismert probléma.  Megkerülő megoldásként tekintse meg a [sys. database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)felhasználóit.  További információ az Azure Active Directory és a szinapszis SQL-készlet használatával történő használatáról: az [Azure szinapszis hitelesítése](sql-data-warehouse-authentication.md) . |
| A manuális parancsfájlok, a parancsfájlkezelés varázsló használata vagy a SSMS-on keresztüli csatlakozás lassú, nem válaszol, vagy hibákat állít elő. | Győződjön meg arról, hogy a felhasználók létre lettek hozva a Master adatbázisban. A parancsfájl-kezelési beállítások területen győződjön meg arról, hogy a motor kiadása "Microsoft Azure SQL Data Warehouse Edition", a motor típusa pedig "Microsoft Azure SQL Database". |
| A szkriptek előállítása meghiúsul a SSMS                               | Nem sikerül létrehozni egy, a szinapszis SQL-készlethez tartozó parancsfájlt, ha a "függő objektumok parancsfájl létrehozása" beállítás értéke "true" (igaz). Megkerülő megoldásként a felhasználóknak manuálisan kell megadniuk az **eszközök-> Options->SQL Server Object Explorer-> parancsfájlt létrehozni a függő beállításokhoz, és false** értékre kell állítani. |

## <a name="performance"></a>Teljesítmény

| Probléma                                                        | Megoldás:                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| A lekérdezés teljesítményével kapcsolatos hibaelhárítás                            | Ha egy adott lekérdezést próbál elhárítani, kezdje a [megtanulni a lekérdezések figyelését](sql-data-warehouse-manage-monitor.md#monitor-query-execution). |
| TempDB lemezterülettel kapcsolatos problémák | [Figyelje a tempdb](sql-data-warehouse-manage-monitor.md#monitor-tempdb) terület használatát.  A TempDB-területek futtatásának gyakori okai a következők:<br>– Nincs elég erőforrás a lekérdezés számára, ami az adatvesztést okozó TempDB.  Lásd: [munkaterhelés-kezelés](resource-classes-for-workload-management.md) <br>– A statisztikák hiányoznak vagy elavultak, ami túlzott adatáthelyezést okoz.  A statisztikák létrehozásával kapcsolatos részletekért tekintse meg a [táblák statisztikáinak fenntartását](sql-data-warehouse-tables-statistics.md) ismertető témakört.<br>– A TempDB terület kiosztása szolgáltatási szintenként történik.  [Az SQL-készlet](sql-data-warehouse-manage-compute-overview.md#scaling-compute) nagyobb DWU-beállításra skálázása több tempdb területet foglal le.|
| A hiányos lekérdezési teljesítmény és a tervek gyakran a hiányzó statisztikák eredménye. | A gyenge teljesítmény leggyakoribb oka a táblák statisztikáinak hiánya.  A statisztikák létrehozásával és a teljesítmény szempontjából kritikus fontosságú információkkal kapcsolatos további információkért tekintse meg a [táblák statisztikáinak fenntartását](sql-data-warehouse-tables-statistics.md) ismertető témakört. |
| Alacsony Egyidejűség/lekérdezések várólistán                             | A számítási [feladatok kezelésének](resource-classes-for-workload-management.md) megismerése fontos annak megismerése érdekében, hogy miként lehet kiegyensúlyozni a memória elosztását a párhuzamosságtal. |
| Az ajánlott eljárások megvalósítása                              | A legjobb megoldás, ha a lekérdezési teljesítmény javításának módját szeretné megismerni a [SZINAPSZIS SQL Pool ajánlott eljárásaival](sql-data-warehouse-best-practices.md) foglalkozó cikkben. |
| A teljesítmény növelése a skálázással                      | Előfordulhat, hogy a megoldás javítja a teljesítményt, hogy egyszerűen vegyen fel további számítási teljesítményt a lekérdezésekhez az [SQL-készlet skálázásával](sql-data-warehouse-manage-compute-overview.md). |
| Gyenge lekérdezési teljesítmény a gyenge index minősége miatt     | Bizonyos időpontokban a lekérdezések lelassulnak a [gyenge oszlopcentrikus indexe](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)miatt.  Ebből a cikkből megtudhatja, hogyan [hozhat létre az indexeket a szegmensek minőségének javítása érdekében](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). |

## <a name="system-management"></a>Rendszerkezelés

| Probléma                                                        | Megoldás:                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: nem végezhető el a művelet, mert a kiszolgáló túllépi az engedélyezett adatbázis-tranzakciós egység (45000) kvótáját. | Csökkentse a létrehozni kívánt adatbázis [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) vagy a [kvóta növelését](sql-data-warehouse-get-started-create-support-ticket.md). |
| A terület kihasználtságának vizsgálata                              | A rendszerek lemezterület-kihasználtságának megismeréséhez tekintse meg a [táblázat méretét](sql-data-warehouse-tables-overview.md#table-size-queries) . |
| Súgó a táblázatok kezeléséhez                                    | A táblák kezelésével kapcsolatos segítségért tekintse meg a [táblázat áttekintését](sql-data-warehouse-tables-overview.md) ismertető cikket.  A cikk a részletes témakörökre mutató hivatkozásokat is tartalmaz, például a [táblázat adattípusait](sql-data-warehouse-tables-data-types.md), [a táblázatok terjesztését](sql-data-warehouse-tables-distribute.md), [a táblák indexelését](sql-data-warehouse-tables-index.md), [a táblák particionálását, a](sql-data-warehouse-tables-partition.md)táblák [statisztikáit](sql-data-warehouse-tables-statistics.md) és az [ideiglenes táblákat](sql-data-warehouse-tables-temporary.md). |
| Az transzparens adattitkosítás (TDE) folyamatjelző sáv nem frissül a Azure Portal | A TDE állapotát a [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)segítségével tekintheti meg. |

## <a name="differences-from-sql-database"></a>Különbségek a SQL Database

| Probléma                                 | Megoldás:                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Nem támogatott SQL Database funkciók     | Lásd: nem [támogatott táblázatos funkciók](sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Nem támogatott SQL Database adattípusok   | Lásd: nem [támogatott adattípusok](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| TÖRLÉSi és frissítési korlátozások         | [A nem támogatott frissítési és törlési szintaxissal](sql-data-warehouse-develop-ctas.md)kapcsolatban tekintse meg a [megoldások frissítése](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements), a [megkerülő megoldások törlése](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements) és a CTAS használata című témakört. |
| A MERGE utasítás nem támogatott      | Lásd: [egyesítési Áthidaló megoldás](sql-data-warehouse-develop-ctas.md#replace-merge-statements).                  |
| Tárolt eljárások korlátai          | A tárolt eljárások korlátainak megismeréséhez tekintse meg a [tárolt eljárásokra vonatkozó korlátozásokat](sql-data-warehouse-develop-stored-procedures.md#limitations) . |
| A UDF nem támogatja a SELECT utasítások használatát | Ez a UDF jelenlegi korlátozása.  Az általunk támogatott szintaxishoz lásd: [create Function](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) . |

## <a name="next-steps"></a>További lépések

Ha további segítségre van szüksége a probléma megoldásához, néhány további erőforrást is kipróbálhat.

* [Blogok](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Funkciókérések](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Videók](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Támogatási jegy létrehozása](sql-data-warehouse-get-started-create-support-ticket.md)
* [Microsoft Q&egy kérdés oldal](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)
* [Stack Overflow fórum](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)
