---
title: Hibaelhárítás
description: Az Azure Synapse Analytics hibaelhárítása.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: b1b841c7532e975f9a1ad928ffd0559231207fb2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350009"
---
# <a name="troubleshooting-sql-analytics-in-azure-synapse"></a>Az SQL Analytics hibaelhárítása az Azure Synapse-ban
Ez a cikk a gyakori hibaelhárítási kérdéseket sorolja fel.

## <a name="connecting"></a>Csatlakozó
| Probléma                                                        | Megoldás:                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Az „NT AUTHORITY\ANONYMOUS LOGON” felhasználó bejelentkezése nem sikerült. (Microsoft SQL Server, Hiba: 18456) | Ez a hiba akkor fordul elő, ha egy AAD-felhasználó megpróbál csatlakozni a master adatbázishoz, de nem rendelkezik felhasználóval a master adatbázisban.  A probléma megoldásához adja meg azt az SQL-készletet, amelyhez a kapcsolat idején csatlakozni kíván, vagy adja hozzá a felhasználót a fő adatbázishoz.  További részletek a [Biztonság áttekintése](sql-data-warehouse-overview-manage-security.md) című cikkben találhatók. |
| A „MyUserName” kiszolgálói tag a jelenlegi biztonsági környezetben nem tud hozzáférni a „master” adatbázishoz. Nem lehet megnyitni a felhasználói alapértelmezett adatbázist. A bejelentkezés sikertelen volt. A következő felhasználó bejelentkezése nem sikerült: „MyUserName”. (Microsoft SQL Server, Hiba: 916) | Ez a hiba akkor fordul elő, ha egy AAD-felhasználó megpróbál csatlakozni a master adatbázishoz, de nem rendelkezik felhasználóval a master adatbázisban.  A probléma megoldásához adja meg azt az SQL-készletet, amelyhez a kapcsolat idején csatlakozni kíván, vagy adja hozzá a felhasználót a fő adatbázishoz.  További részletek a [Biztonság áttekintése](sql-data-warehouse-overview-manage-security.md) című cikkben találhatók. |
| CTAIP hiba                                                  | Ez a hiba akkor fordulhat elő, ha az SQL-kiszolgáló főadatbázisában létrehozott bejelentkezést hoztak létre, de az SQL-adatbázisban nem.  Ha ez a hiba, tekintse meg a [Biztonság áttekintése](sql-data-warehouse-overview-manage-security.md) cikket.  Ez a cikk bemutatja, hogyan hozhat létre bejelentkezési és felhasználói főkiszolgálót, majd hogyan hozhat létre felhasználót az SQL-adatbázisban. |
| Tűzfal blokkolja                                          | Az SQL-készleteket tűzfalak védik, így csak az ismert IP-címek férnek hozzá az adatbázishoz. A tűzfalak alapértelmezés szerint biztonságosak, ami azt jelenti, hogy a csatlakozás előtt explicit módon engedélyeznie kell az IP-címet vagy címtartományt.  A tűzfal hozzáférésre való beállításához kövesse a [Kiszolgáló tűzfal-hozzáférésének konfigurálása az ügyfél IP-címéhez](create-data-warehouse-portal.md) című, [a kiépítési utasításokban](create-data-warehouse-portal.md)leírt lépéseket. |
| Nem lehet csatlakozni az eszközhöz vagy az illesztőprogramhoz                           | Az SQL Analytics az [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15), [ssdt for Visual Studio](sql-data-warehouse-install-visual-studio.md)vagy [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) használatát javasolja az adatok lekérdezéséhez. Az illesztőprogramokkal és az Azure Synapse-hoz való csatlakozásról az [Azure Synapse-illesztőprogramok](sql-data-warehouse-connection-strings.md) és [az Azure Synapse-cikkek csatlakoztatása](sql-data-warehouse-connect-overview.md) című témakörben talál további információt. |

## <a name="tools"></a>Eszközök
| Probléma                                                        | Megoldás:                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| A Visual Studio objektumkezelőből hiányoznak az AAD-felhasználók           | Ez egy ismert probléma.  Kerülő megoldásként tekintse meg a felhasználókat a [sys.database_principals.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?view=sql-server-ver15)  Az Azure Active Directory SQL Analytics szolgáltatással való használatáról az [Azure Synapse hitelesítése](sql-data-warehouse-authentication.md) című témakörben olvashat bővebben. |
| A kézi parancsfájlok használata, a parancsfájlkészítő varázsló használata vagy az SSMS-en keresztüli csatlakozás lassú, nem válaszol, vagy hibákat okoz | Győződjön meg arról, hogy felhasználók lettek létrehozva a fő adatbázisban. A parancsfájlkezelési beállításokban győződjön meg arról is, hogy a motorkiadás "Microsoft Azure SQL Data Warehouse Edition" és "Microsoft Azure SQL Database" típusú. |
| Parancsfájlok létrehozása sikertelen az SSMS-ben                               | Parancsfájl létrehozása az SQL Analytics szolgáltatáshoz sikertelen, ha a "Parancsfájl létrehozása függő objektumokhoz" beállítás értéke "True" lesz. Kerülő megoldásként a felhasználóknak manuálisan kell megkerülniük **az Eszközök -> Beállítások ->SQL Server Object Explorer -> A parancsfájl létrehozása a függő beállításokhoz, és hamis** |

## <a name="performance"></a>Teljesítmény
| Probléma                                                        | Megoldás:                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Lekérdezési teljesítmény – hibaelhárítás                            | Ha egy adott lekérdezést próbál elhárítani, kezdje a [Lekérdezések figyelésének elsajátításával.](sql-data-warehouse-manage-monitor.md#monitor-query-execution) |
| TempDB-területproblémák | [TempDB-területhasználat figyelése.](sql-data-warehouse-manage-monitor.md#monitor-tempdb)  A TempDB terület kifutásának gyakori okai a következők:<br>- Nincs elég erőforrás a lekérdezéshez, amely adatokat juttat a TempDB-nek.  Lásd: [Munkaterhelés-kezelés](resource-classes-for-workload-management.md) <br>- A statisztikák hiányoznak vagy elavultak, ami túlzott adatmozgást okoz.  A statisztika létrehozásának részleteit [lásd: Táblastatisztikák karbantartása](sql-data-warehouse-tables-statistics.md)<br>- A TempDB terület szolgáltatási szintenként van lefoglalva.  [Az SQL-készlet](sql-data-warehouse-manage-compute-overview.md#scaling-compute) magasabb DWU-beállításra való méretezése több TempDB-területet foglal le.|
| A gyenge lekérdezési teljesítmény és a tervek gyakran a hiányzó statisztikák eredménye | A gyenge teljesítmény leggyakoribb oka a statisztikák hiánya a táblákon.  A statisztikák létrehozásának és a teljesítmény szempontjából kritikus fontosságúk módjának részleteit a [Táblastatisztikák karbantartása](sql-data-warehouse-tables-statistics.md) című témakörben találja. |
| Alacsony egyidejűség / lekérdezések várólistára                             | A [számítási feladatok kezelésének](resource-classes-for-workload-management.md) ismertetése fontos annak megértéséhez, hogyan lehet egyensúlyt teremteni a memóriafoglalás és az egyidejűség között. |
| A bevált módszerek megvalósítása                              | A legjobb kiindulópont a lekérdezési teljesítmény javításának megkezdéséhez az [SQL Analytics ajánlott eljárásokról](sql-data-warehouse-best-practices.md) szóló cikk. |
| A teljesítmény javítása méretezéssel                      | Néha a teljesítmény javítása érdekében egyszerűen nagyobb számítási teljesítményt ad a lekérdezésekhez [az SQL-készlet méretezésével.](sql-data-warehouse-manage-compute-overview.md) |
| Gyenge lekérdezési teljesítmény a gyenge indexminőség miatt     | A lekérdezések egy része lelassulhat az [oszlopcentrikus index minőségének romlása](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)miatt.  Ebben a cikkben további információt talál, és az [indexek újraépítéséről a szegmensek minőségének javítása érdekében.](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality) |

## <a name="system-management"></a>Rendszerkezelés
| Probléma                                                        | Megoldás:                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: Nem lehetett végrehajtani a műveletet, mert a kiszolgáló túllépte a 45000-es adatbázistranzakciós egység megengedett kvótáját. | Csökkentse a létrehozni kívánt adatbázis [DWU-ját,](what-is-a-data-warehouse-unit-dwu-cdwu.md) vagy [kérje kvótanövelést.](sql-data-warehouse-get-started-create-support-ticket.md) |
| Az űrhasználat vizsgálata                              | A rendszer helykihasználásának megismeréséhez tekintse meg a [Táblázatméretek]( ../../sql-data-warehouse/sql-data-warehouse-tables-overview.md#table-size-queries) című témakört. |
| Súgó a táblák kezeléséhez                                    | A táblázatok kezelésével kapcsolatos segítséget a [Táblázat áttekintése](sql-data-warehouse-tables-overview.md) című cikkben találja.  Ez a cikk részletesebb témakörökre mutató hivatkozásokat is tartalmaz , például [táblaadattípusokat](sql-data-warehouse-tables-data-types.md), [tábla terjesztése](sql-data-warehouse-tables-distribute.md), [tábla indexelése](sql-data-warehouse-tables-index.md), [tábla particionálása](sql-data-warehouse-tables-partition.md), [Táblastatisztikák és](sql-data-warehouse-tables-statistics.md) ideiglenes [táblák](sql-data-warehouse-tables-temporary.md)karbantartása . |
| Az átlátszó adattitkosítás (TDE) folyamatjelzője nem frissül az Azure Portalon | A TDE állapotát a [powershell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption)en keresztül tekintheti meg. |


## <a name="differences-from-sql-database"></a>Különbségek az SQL-adatbázistól
| Probléma                                 | Megoldás:                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Nem támogatott SQL Database-szolgáltatások     | Lásd: [Nem támogatott táblaszolgáltatások](sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Nem támogatott SQL Database-adattípusok   | Lásd: [Nem támogatott adattípusok](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| DELETE és UPDATE korlátozások         | Lásd: [UPDATE kerülő megoldások](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements), DELETE [kerülő megoldások](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements) és a [CTAS használata a nem támogatott UPDATE és DELETE szintaxis kerülőmegoldása kerülő megoldásként.](sql-data-warehouse-develop-ctas.md) |
| A MERGE utasítás nem támogatott      | Lásd: [MERGE kerülő megoldások](sql-data-warehouse-develop-ctas.md#replace-merge-statements).                  |
| Tárolt eljárás korlátai          | A tárolt eljárások egyes korlátainak megértéséhez lásd: [Tárolt eljáráskorlátozások.](sql-data-warehouse-develop-stored-procedures.md#limitations) |
| Az UDF-ek nem támogatják a SELECT utasításokat | Ez az UDF-ek jelenlegi korlátozása.  Az általunk támogatott szintaxis létrehozásáról lásd: [FÜGGVÉNY LÉTREHOZÁSA.](https://docs.microsoft.com/sql/t-sql/statements/create-function-sql-data-warehouse?view=aps-pdw-2016-au7) |

## <a name="next-steps"></a>További lépések
Ha további segítségre van szüksége a probléma megoldásához, itt talál néhány további erőforrást, amelyet kipróbálhat.

* [Blogok](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Szolgáltatáskérések](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Videók](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Támogatási jegy létrehozása](sql-data-warehouse-get-started-create-support-ticket.md)
* [MSDN fórum](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse)
* [Stack Overflow-fórum](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)
