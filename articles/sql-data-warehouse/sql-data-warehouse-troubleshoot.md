---
title: Hibakeresés
description: Az Azure szinapszis Analytics hibaelhárítása.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/04/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 085b907b6a848fb534df63b5465948864048cc19
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256483"
---
# <a name="troubleshooting-sql-analytics-in-azure-synapse"></a>Az SQL Analytics hibaelhárítása Az Azure Szinapszisban
Ez a cikk a gyakori hibaelhárítási kérdéseit sorolja fel.

## <a name="connecting"></a>Csatlakozás
| Probléma                                                        | Megoldás:                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Az „NT AUTHORITY\ANONYMOUS LOGON” felhasználó bejelentkezése nem sikerült. (Microsoft SQL Server, hiba: 18456) | Ez a hiba akkor fordul elő, ha egy AAD-felhasználó megpróbál csatlakozni a master adatbázishoz, de nem rendelkezik felhasználóval a master adatbázisban.  A probléma megoldásához adja meg azt az SQL-készletet, amelyhez csatlakozni szeretne, vagy adja hozzá a felhasználót a Master adatbázishoz.  További részletekért tekintse meg a [Biztonság áttekintése](sql-data-warehouse-overview-manage-security.md) című cikket. |
| A „MyUserName” kiszolgálói tag a jelenlegi biztonsági környezetben nem tud hozzáférni a „master” adatbázishoz. Nem lehet megnyitni a felhasználói alapértelmezett adatbázist. A bejelentkezés sikertelen volt. A következő felhasználó bejelentkezése nem sikerült: „MyUserName”. (Microsoft SQL Server, hiba: 916) | Ez a hiba akkor fordul elő, ha egy AAD-felhasználó megpróbál csatlakozni a master adatbázishoz, de nem rendelkezik felhasználóval a master adatbázisban.  A probléma megoldásához adja meg azt az SQL-készletet, amelyhez csatlakozni szeretne, vagy adja hozzá a felhasználót a Master adatbázishoz.  További részletekért tekintse meg a [Biztonság áttekintése](sql-data-warehouse-overview-manage-security.md) című cikket. |
| CTAIP hiba                                                  | Ez a hiba akkor fordulhat elő, ha az SQL Server főadatbázisán egy bejelentkezési azonosítót hoztak létre, de nem az SQL Database-ben.  Ha ezt a hibát tapasztalja, tekintse meg a [biztonsági áttekintést ismertető](sql-data-warehouse-overview-manage-security.md) cikket.  Ez a cikk azt ismerteti, hogyan hozható létre egy bejelentkezés és egy felhasználó a főkiszolgálón, majd hogyan hozható létre felhasználó az SQL Database-ben. |
| Tűzfal blokkolja                                          | Az SQL-készleteket tűzfalak védik, hogy csak az ismert IP-címek férhessenek hozzá egy adatbázishoz. Alapértelmezés szerint a tűzfalak biztonságosak, ami azt jelenti, hogy a kapcsolat megkezdése előtt explicit módon engedélyeznie kell és meg kell adnia az IP-címet vagy a címtartományt.  A tűzfal hozzáférésre való konfigurálásához kövesse a [kiszolgáló tűzfal-hozzáférés konfigurálása az ügyfél IP](sql-data-warehouse-get-started-provision.md) -címére című témakör lépéseit a [létesítési utasításokban](sql-data-warehouse-get-started-provision.md). |
| Nem lehet kapcsolatot létesíteni az eszközzel vagy az illesztőprogrammal                           | Az SQL Analytics a [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15), [a SSDT for Visual Studio](sql-data-warehouse-install-visual-studio.md)vagy a [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) használatát javasolja az adatai lekérdezéséhez. További információ az illesztőprogramokról és az Azure Szinapszishoz való csatlakozásról: [Az Azure szinapszis-illesztőprogramok](sql-data-warehouse-connection-strings.md) és [Az Azure szinapszis-cikkek csatlakoztatása](sql-data-warehouse-connect-overview.md) . |

## <a name="tools"></a>Eszközök
| Probléma                                                        | Megoldás:                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| A Visual Studio Object Explorerből hiányzik a HRE-felhasználók           | Ez egy ismert probléma.  Megkerülő megoldásként tekintse meg a [sys. database_principals](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?view=sql-server-ver15)felhasználóit.  További információ az SQL Analytics szolgáltatással való Azure Active Directory használatáról: az [Azure szinapszis hitelesítése](sql-data-warehouse-authentication.md) . |
| A manuális parancsfájlok, a parancsfájlkezelés varázsló használata vagy a SSMS-on keresztüli csatlakozás lassú, nem válaszol, vagy hibákat állít elő. | Győződjön meg arról, hogy a felhasználók létre lettek hozva a Master adatbázisban. A parancsfájl-kezelési beállítások területen győződjön meg arról, hogy a motor kiadása "Microsoft Azure SQL Data Warehouse Edition", a motor típusa pedig "Microsoft Azure SQL Database". |
| A szkriptek előállítása meghiúsul a SSMS                               | Ha az SQL Analyticshez parancsfájlt hoz létre, akkor sikertelen, ha "a parancsfájl létrehozása a függő objektumok számára" beállítás értéke "true" (igaz). Megkerülő megoldásként a felhasználóknak manuálisan kell megadniuk az **eszközök-> Options-> SQL Server Object Explorer-> parancsfájlt létrehozni a függő beállításokhoz, és false** értékre kell állítani. |

## <a name="performance"></a>Teljesítmény
| Probléma                                                        | Megoldás:                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| A lekérdezés teljesítményével kapcsolatos hibaelhárítás                            | Ha egy adott lekérdezést próbál elhárítani, kezdje a [megtanulni a lekérdezések figyelését](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md#monitor-query-execution). |
| TempDB lemezterülettel kapcsolatos problémák | [Figyelje a tempdb](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md#monitor-tempdb) terület használatát.  A TempDB-területek futtatásának gyakori okai a következők:<br>– Nincs elég erőforrás a lekérdezés számára, ami az adatvesztést okozó TempDB.  Lásd: [munkaterhelés-kezelés](resource-classes-for-workload-management.md) <br>– A statisztikák hiányoznak vagy elavultak, ami túlzott adatáthelyezést okoz.  A statisztikák létrehozásával kapcsolatos részletekért tekintse meg a [táblák statisztikáinak fenntartását](sql-data-warehouse-tables-statistics.md) ismertető témakört.<br>– A TempDB terület kiosztása szolgáltatási szintenként történik.  [Az SQL-készlet](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#scaling-compute) nagyobb DWU-beállításra skálázása több tempdb területet foglal le.|
| A hiányos lekérdezési teljesítmény és a tervek gyakran a hiányzó statisztikák eredménye. | A gyenge teljesítmény leggyakoribb oka a táblák statisztikáinak hiánya.  A statisztikák létrehozásával és a teljesítmény szempontjából kritikus fontosságú információkkal kapcsolatos további információkért tekintse meg a [táblák statisztikáinak fenntartását](sql-data-warehouse-tables-statistics.md) ismertető témakört. |
| Alacsony Egyidejűség/lekérdezések várólistán                             | A számítási [feladatok kezelésének](resource-classes-for-workload-management.md) megismerése fontos annak megismerése érdekében, hogy miként lehet kiegyensúlyozni a memória elosztását a párhuzamosságtal. |
| Az ajánlott eljárások megvalósítása                              | A legjobb megoldás, ha a lekérdezési teljesítmény javításának módszereit az [SQL Analytics ajánlott eljárásait](sql-data-warehouse-best-practices.md) ismertető cikkben találja. |
| A teljesítmény növelése a skálázással                      | Előfordulhat, hogy a megoldás javítja a teljesítményt, hogy egyszerűen vegyen fel további számítási teljesítményt a lekérdezésekhez az [SQL-készlet skálázásával](sql-data-warehouse-manage-compute-overview.md). |
| Gyenge lekérdezési teljesítmény a gyenge index minősége miatt     | Bizonyos időpontokban a lekérdezések lelassulnak a [gyenge oszlopcentrikus indexe](../sql-data-warehouse/sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)miatt.  Ebből a cikkből megtudhatja, hogyan [hozhat létre az indexeket a szegmensek minőségének javítása érdekében](../sql-data-warehouse/sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). |

## <a name="system-management"></a>Rendszerkezelés
| Probléma                                                        | Megoldás:                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: nem végezhető el a művelet, mert a kiszolgáló túllépi az engedélyezett adatbázis-tranzakciós egység (45000) kvótáját. | Csökkentse a létrehozni kívánt adatbázis [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) vagy a [kvóta növelését](sql-data-warehouse-get-started-create-support-ticket.md). |
| A terület kihasználtságának vizsgálata                              | A rendszerek lemezterület-kihasználtságának megismeréséhez tekintse meg a [táblázat méretét]( ../sql-data-warehouse/sql-data-warehouse-tables-overview.md#table-size-queries) . |
| Súgó a táblázatok kezeléséhez                                    | A táblák kezelésével kapcsolatos segítségért tekintse meg a [táblázat áttekintését](../sql-data-warehouse/sql-data-warehouse-tables-overview.md) ismertető cikket.  A cikk a részletes témakörökre mutató hivatkozásokat is tartalmaz, például a [táblázat adattípusait](sql-data-warehouse-tables-data-types.md), [a táblázatok terjesztését](sql-data-warehouse-tables-distribute.md), [a táblák indexelését](sql-data-warehouse-tables-index.md), [a táblák particionálását, a](sql-data-warehouse-tables-partition.md)táblák [statisztikáit](sql-data-warehouse-tables-statistics.md) és az [ideiglenes táblákat](sql-data-warehouse-tables-temporary.md). |
| Az transzparens adattitkosítás (TDE) folyamatjelző sáv nem frissül a Azure Portal | A TDE állapotát a [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption)segítségével tekintheti meg. |


## <a name="differences-from-sql-database"></a>Különbségek a SQL Database
| Probléma                                 | Megoldás:                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Nem támogatott SQL Database funkciók     | Lásd: nem [támogatott táblázatos funkciók](../sql-data-warehouse/sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Nem támogatott SQL Database adattípusok   | Lásd: nem [támogatott adattípusok](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| TÖRLÉSi és frissítési korlátozások         | [A nem támogatott frissítési és törlési szintaxissal](sql-data-warehouse-develop-ctas.md)kapcsolatban tekintse meg a [megoldások frissítése](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements), a [megkerülő megoldások törlése](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements) és a CTAS használata című témakört. |
| A MERGE utasítás nem támogatott      | Lásd: [egyesítési Áthidaló megoldás](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md#replace-merge-statements).                  |
| Tárolt eljárások korlátai          | A tárolt eljárások korlátainak megismeréséhez tekintse meg a [tárolt eljárásokra vonatkozó korlátozásokat](../sql-data-warehouse/sql-data-warehouse-develop-stored-procedures.md#limitations) . |
| A UDF nem támogatja a SELECT utasítások használatát | Ez a UDF jelenlegi korlátozása.  Az általunk támogatott szintaxishoz lásd: [create Function](https://docs.microsoft.com/sql/t-sql/statements/create-function-sql-data-warehouse?view=aps-pdw-2016-au7) . |

## <a name="next-steps"></a>Következő lépések
Ha további segítségre van szüksége a probléma megoldásához, néhány további erőforrást is kipróbálhat.

* [Blogok](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Funkciókérések](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Videók](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Támogatási jegy létrehozása](sql-data-warehouse-get-started-create-support-ticket.md)
* [MSDN-fórum](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse)
* [Stack Overflow-fórum](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)
