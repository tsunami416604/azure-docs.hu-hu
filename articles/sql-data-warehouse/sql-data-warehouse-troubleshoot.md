---
title: Hibaelhárítás
description: Hibaelhárítási Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/25/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: ce57c48e568e840f3a651a5530f3fba6c0be60b7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721047"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Hibaelhárítási Azure SQL Data Warehouse
Ez a cikk a gyakori hibaelhárítási kérdéseit sorolja fel.

## <a name="connecting"></a>Csatlakozás
| Probléma                                                        | Felbontás                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Az „NT AUTHORITY\ANONYMOUS LOGON” felhasználó bejelentkezése nem sikerült. (Microsoft SQL Server, hiba: 18456) | Ez a hiba akkor fordul elő, ha egy AAD-felhasználó megpróbál csatlakozni a master adatbázishoz, de nem rendelkezik felhasználóval a master adatbázisban.  A hiba kijavítása érdekében adja meg azt az SQL Data Warehouse-t, amelyhez csatlakozni szeretne a kapcsolódáskor, vagy adja hozzá a felhasználót a master adatbázishoz.  További részletekért tekintse meg a [Biztonság áttekintése](sql-data-warehouse-overview-manage-security.md) című cikket. |
| A „MyUserName” kiszolgálói tag a jelenlegi biztonsági környezetben nem tud hozzáférni a „master” adatbázishoz. Nem lehet megnyitni a felhasználói alapértelmezett adatbázist. A bejelentkezés sikertelen volt. A következő felhasználó bejelentkezése nem sikerült: „MyUserName”. (Microsoft SQL Server, hiba: 916) | Ez a hiba akkor fordul elő, ha egy AAD-felhasználó megpróbál csatlakozni a master adatbázishoz, de nem rendelkezik felhasználóval a master adatbázisban.  A hiba kijavítása érdekében adja meg azt az SQL Data Warehouse-t, amelyhez csatlakozni szeretne a kapcsolódáskor, vagy adja hozzá a felhasználót a master adatbázishoz.  További részletekért tekintse meg a [Biztonság áttekintése](sql-data-warehouse-overview-manage-security.md) című cikket. |
| CTAIP hiba                                                  | Ez a hiba akkor fordulhat elő, ha az SQL Server főadatbázisán egy bejelentkezési azonosítót hoztak létre, de nem szerepelnek a SQL Data Warehouse adatbázisban.  Ha ezt a hibát tapasztalja, tekintse meg a [biztonsági áttekintést ismertető](sql-data-warehouse-overview-manage-security.md) cikket.  Ez a cikk azt ismerteti, hogyan hozható létre egy bejelentkezési azonosító és egy felhasználó a főkiszolgálón, majd hogyan hozható létre felhasználó a SQL Data Warehouse adatbázisban. |
| Tűzfal blokkolja                                          | Az Azure SQL Database-adatbázisokat kiszolgáló-és adatbázis-szintű tűzfalak védik, hogy csak az ismert IP-címek férhessenek hozzá egy adatbázishoz. Alapértelmezés szerint a tűzfalak biztonságosak, ami azt jelenti, hogy a kapcsolat megkezdése előtt explicit módon engedélyeznie kell és meg kell adnia az IP-címet vagy a címtartományt.  A tűzfal hozzáférésre való konfigurálásához kövesse a [kiszolgáló tűzfal-hozzáférés konfigurálása az ügyfél IP](sql-data-warehouse-get-started-provision.md) -címére című témakör lépéseit a [létesítési utasításokban](sql-data-warehouse-get-started-provision.md). |
| Nem lehet kapcsolatot létesíteni az eszközzel vagy az illesztőprogrammal                           | SQL Data Warehouse a [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15), [a SSDT for Visual Studio](sql-data-warehouse-install-visual-studio.md)vagy a [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) használatát javasolja az adatai lekérdezéséhez. Az illesztőprogramokkal és a SQL Data Warehousehoz való csatlakozással kapcsolatos további információkért lásd: [illesztőprogramok Azure SQL Data Warehouse](sql-data-warehouse-connection-strings.md) és [Csatlakozás Azure SQL Data Warehouse](sql-data-warehouse-connect-overview.md) cikkekhez. |

## <a name="tools"></a>Eszközök
| Probléma                                                        | Felbontás                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| A Visual Studio Object Explorerből hiányzik a HRE-felhasználók           | Ez egy ismert probléma.  Megkerülő megoldásként tekintse meg a [sys. database_principals](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?view=sql-server-ver15)felhasználóit.  A Azure Active Directory és a SQL Data Warehouse használatával kapcsolatos további tudnivalókért tekintse meg a [Azure SQL Data Warehouse hitelesítés](sql-data-warehouse-authentication.md) című témakört. |
| A manuális parancsfájlok, a parancsfájlkezelés varázsló használata vagy a SSMS-on keresztüli csatlakozás lassú, nem válaszol, vagy hibákat állít elő. | Győződjön meg arról, hogy a felhasználók létre lettek hozva a Master adatbázisban. A parancsfájl-kezelési beállítások területen győződjön meg arról, hogy a motor kiadása "Microsoft Azure SQL Data Warehouse Edition", a motor típusa pedig "Microsoft Azure SQL Database". |
| A szkriptek előállítása meghiúsul a SSMS                               | A SQL Data Warehouse parancsfájl létrehozása meghiúsul, ha a "függő objektumok létrehozásához szükséges parancsfájl létrehozása" beállítás értéke "true" (igaz). Megkerülő megoldásként a felhasználóknak manuálisan kell megadniuk az **eszközök-> Options-> SQL Server Object Explorer-> parancsfájlt létrehozni a függő beállításokhoz, és false** értékre kell állítani. |

## <a name="performance"></a>Teljesítmény
| Probléma                                                        | Felbontás                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| A lekérdezés teljesítményével kapcsolatos hibaelhárítás                            | Ha egy adott lekérdezést próbál elhárítani, kezdje a [megtanulni a lekérdezések figyelését](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md#monitor-query-execution). |
| TempDB lemezterülettel kapcsolatos problémák | [Figyelje a tempdb](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md#monitor-tempdb) terület használatát.  A TempDB-területek futtatásának gyakori okai a következők:<br>– Nincs elég erőforrás a lekérdezés számára, ami az adatvesztést okozó TempDB.  Lásd: [munkaterhelés-kezelés](resource-classes-for-workload-management.md) <br>– A statisztikák hiányoznak vagy elavultak, ami túlzott adatáthelyezést okoz.  A statisztikák létrehozásával kapcsolatos részletekért tekintse meg a [táblák statisztikáinak fenntartását](sql-data-warehouse-tables-statistics.md) ismertető témakört.<br>– A TempDB terület kiosztása szolgáltatási szintenként történik.  [A SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#scaling-compute) nagyobb DWU-beállításra méretezése több tempdb területet foglal le.|
| A hiányos lekérdezési teljesítmény és a tervek gyakran a hiányzó statisztikák eredménye. | A gyenge teljesítmény leggyakoribb oka a táblák statisztikáinak hiánya.  A statisztikák létrehozásával és a teljesítmény szempontjából kritikus fontosságú információkkal kapcsolatos további információkért tekintse meg a [táblák statisztikáinak fenntartását](sql-data-warehouse-tables-statistics.md) ismertető témakört. |
| Alacsony Egyidejűség/lekérdezések várólistán                             | A számítási [feladatok kezelésének](resource-classes-for-workload-management.md) megismerése fontos annak megismerése érdekében, hogy miként lehet kiegyensúlyozni a memória elosztását a párhuzamosságtal. |
| Az ajánlott eljárások megvalósítása                              | A legjobb megoldás, ha a lekérdezési teljesítmény javításának módszereit [SQL Data Warehouse ajánlott eljárásokat](sql-data-warehouse-best-practices.md) ismertető cikkben találja. |
| A teljesítmény növelése a skálázással                      | Előfordulhat, hogy a megoldás javítja a teljesítményt, hogy egyszerűen vegyen fel több számítási teljesítményt a lekérdezésekhez a [SQL Data Warehouse skálázásával](sql-data-warehouse-manage-compute-overview.md). |
| Gyenge lekérdezési teljesítmény a gyenge index minősége miatt     | Bizonyos időpontokban a lekérdezések lelassulnak a [gyenge oszlopcentrikus indexe](../sql-data-warehouse/sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)miatt.  Ebből a cikkből megtudhatja, hogyan [hozhat létre az indexeket a szegmensek minőségének javítása érdekében](../sql-data-warehouse/sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). |

## <a name="system-management"></a>Rendszerkezelés
| Probléma                                                        | Felbontás                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: nem végezhető el a művelet, mert a kiszolgáló túllépi az engedélyezett adatbázis-tranzakciós egység (45000) kvótáját. | Csökkentse a létrehozni kívánt adatbázis [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) vagy a [kvóta növelését](sql-data-warehouse-get-started-create-support-ticket.md). |
| A terület kihasználtságának vizsgálata                              | A rendszerek lemezterület-kihasználtságának megismeréséhez tekintse meg a [táblázat méretét]( ../sql-data-warehouse/sql-data-warehouse-tables-overview.md#table-size-queries) . |
| Súgó a táblázatok kezeléséhez                                    | A táblák kezelésével kapcsolatos segítségért tekintse meg a [táblázat áttekintése] [áttekintés] cikket.  A cikk a részletes témakörökre mutató hivatkozásokat is tartalmaz, például a [táblázat adattípusait](sql-data-warehouse-tables-data-types.md), [a táblázatok terjesztését](sql-data-warehouse-tables-distribute.md), [a táblák indexelését](sql-data-warehouse-tables-index.md), [a táblák particionálását, a](sql-data-warehouse-tables-partition.md)táblák [statisztikáit](sql-data-warehouse-tables-statistics.md) és az [ideiglenes táblákat](sql-data-warehouse-tables-temporary.md). |
| Az transzparens adattitkosítás (TDE) folyamatjelző sáv nem frissül a Azure Portal | A TDE állapotát a [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption)segítségével tekintheti meg. |


## <a name="differences-from-sql-database"></a>Különbségek a SQL Database
| Probléma                                 | Felbontás                                                   |
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