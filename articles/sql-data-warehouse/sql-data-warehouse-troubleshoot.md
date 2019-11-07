---
title: Hibaelhárítás
description: Hibaelhárítási Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 7/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2aa7926286be277c7ad0aa7054b4bd6fceb8229f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685403"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Hibaelhárítási Azure SQL Data Warehouse
Ez a cikk a gyakori hibaelhárítási kérdéseit sorolja fel.

## <a name="connecting"></a>Csatlakozás
| Probléma                                                        | Felbontás                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Az „NT AUTHORITY\ANONYMOUS LOGON” felhasználó bejelentkezése nem sikerült. (Microsoft SQL Server, hiba: 18456) | Ez a hiba akkor fordul elő, ha egy AAD-felhasználó megpróbál csatlakozni a master adatbázishoz, de nem rendelkezik felhasználóval a master adatbázisban.  A hiba kijavítása érdekében adja meg azt az SQL Data Warehouse-t, amelyhez csatlakozni szeretne a kapcsolódáskor, vagy adja hozzá a felhasználót a master adatbázishoz.  További részletekért tekintse meg a [Biztonság áttekintése][Security overview] című cikket. |
| A „MyUserName” kiszolgálói tag a jelenlegi biztonsági környezetben nem tud hozzáférni a „master” adatbázishoz. Nem lehet megnyitni a felhasználói alapértelmezett adatbázist. A bejelentkezés sikertelen volt. A következő felhasználó bejelentkezése nem sikerült: „MyUserName”. (Microsoft SQL Server, hiba: 916) | Ez a hiba akkor fordul elő, ha egy AAD-felhasználó megpróbál csatlakozni a master adatbázishoz, de nem rendelkezik felhasználóval a master adatbázisban.  A hiba kijavítása érdekében adja meg azt az SQL Data Warehouse-t, amelyhez csatlakozni szeretne a kapcsolódáskor, vagy adja hozzá a felhasználót a master adatbázishoz.  További részletekért tekintse meg a [Biztonság áttekintése][Security overview] című cikket. |
| CTAIP hiba                                                  | Ez a hiba akkor fordulhat elő, ha az SQL Server főadatbázisán egy bejelentkezési azonosítót hoztak létre, de nem szerepelnek a SQL Data Warehouse adatbázisban.  Ha ezt a hibát tapasztalja, tekintse meg a [biztonsági áttekintést ismertető][Security overview] cikket.  Ez a cikk azt ismerteti, hogyan hozható létre egy bejelentkezési azonosító és egy felhasználó a főkiszolgálón, majd hogyan hozható létre felhasználó a SQL Data Warehouse adatbázisban. |
| Tűzfal blokkolja                                          | Az Azure SQL Database-adatbázisokat kiszolgáló-és adatbázis-szintű tűzfalak védik, hogy csak az ismert IP-címek férhessenek hozzá egy adatbázishoz. Alapértelmezés szerint a tűzfalak biztonságosak, ami azt jelenti, hogy a kapcsolat megkezdése előtt explicit módon engedélyeznie kell és meg kell adnia az IP-címet vagy a címtartományt.  A tűzfal hozzáférésre való konfigurálásához kövesse a [kiszolgáló tűzfal-hozzáférés konfigurálása az ügyfél IP][Configure server firewall access for your client IP] -címére című témakör lépéseit a [létesítési utasításokban][Provisioning instructions]. |
| Nem lehet kapcsolatot létesíteni az eszközzel vagy az illesztőprogrammal                           | SQL Data Warehouse a [SSMS][SSMS], [a SSDT for Visual Studio][SSDT for Visual Studio]vagy a [Sqlcmd][sqlcmd] használatát javasolja az adatai lekérdezéséhez. Az illesztőprogramokkal és a SQL Data Warehousehoz való csatlakozással kapcsolatos további információkért lásd: [illesztőprogramok Azure SQL Data Warehouse][Drivers for Azure SQL Data Warehouse] és [Csatlakozás Azure SQL Data Warehouse][Connect to Azure SQL Data Warehouse] cikkekhez. |

## <a name="tools"></a>Eszközök
| Probléma                                                        | Felbontás                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| A Visual Studio Object Explorerből hiányzik a HRE-felhasználók           | Ez egy ismert probléma.  Megkerülő megoldásként tekintse meg a [sys. database_principals][sys.database_principals]felhasználóit.  A Azure Active Directory és a SQL Data Warehouse használatával kapcsolatos további tudnivalókért tekintse meg a [Azure SQL Data Warehouse hitelesítés][Authentication to Azure SQL Data Warehouse] című témakört. |
| A manuális parancsfájlok, a parancsfájlkezelés varázsló használata vagy a SSMS-on keresztüli csatlakozás lassú, nem válaszol, vagy hibákat állít elő. | Győződjön meg arról, hogy a felhasználók létre lettek hozva a Master adatbázisban. A parancsfájl-kezelési beállítások területen győződjön meg arról, hogy a motor kiadása "Microsoft Azure SQL Data Warehouse Edition", a motor típusa pedig "Microsoft Azure SQL Database". |
| A szkriptek előállítása meghiúsul a SSMS                               | A SQL Data Warehouse parancsfájl létrehozása meghiúsul, ha a "függő objektumok létrehozásához szükséges parancsfájl létrehozása" beállítás értéke "true" (igaz). Megkerülő megoldásként a felhasználóknak manuálisan kell megadniuk az eszközök-> Options-> SQL Server Object Explorer-> parancsfájlt létrehozni a függő beállításokhoz, és false értékre kell állítani. |

## <a name="performance"></a>Teljesítmény
| Probléma                                                        | Felbontás                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| A lekérdezés teljesítményével kapcsolatos hibaelhárítás                            | Ha egy adott lekérdezést próbál elhárítani, kezdje a [megtanulni a lekérdezések figyelését][Learning how to monitor your queries]. |
| A hiányos lekérdezési teljesítmény és a tervek gyakran a hiányzó statisztikák eredménye. | A gyenge teljesítmény leggyakoribb oka a táblák statisztikáinak hiánya.  A statisztikák létrehozásával és a teljesítmény szempontjából kritikus fontosságú információkkal kapcsolatos további információkért tekintse meg a [táblák statisztikáinak fenntartását][Statistics] ismertető témakört. |
| Alacsony Egyidejűség/lekérdezések várólistán                             | A számítási [feladatok kezelésének][Workload management] megismerése fontos annak megismerése érdekében, hogy miként lehet kiegyensúlyozni a memória elosztását a párhuzamosságtal. |
| Az ajánlott eljárások megvalósítása                              | A legjobb megoldás, ha a lekérdezési teljesítmény javításának módszereit [SQL Data Warehouse ajánlott eljárásokat][SQL Data Warehouse best practices] ismertető cikkben találja. |
| A teljesítmény növelése a skálázással                      | Előfordulhat, hogy a megoldás javítja a teljesítményt, hogy egyszerűen vegyen fel több számítási teljesítményt a lekérdezésekhez a [SQL Data Warehouse skálázásával][Scaling your SQL Data Warehouse]. |
| Gyenge lekérdezési teljesítmény a gyenge index minősége miatt     | Bizonyos időpontokban a lekérdezések lelassulnak a [gyenge oszlopcentrikus indexe][Poor columnstore index quality]miatt.  Ebből a cikkből megtudhatja, hogyan [hozhat létre az indexeket a szegmensek minőségének javítása érdekében][Rebuild indexes to improve segment quality]. |

## <a name="system-management"></a>Rendszerkezelés
| Probléma                                                        | Felbontás                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: nem végezhető el a művelet, mert a kiszolgáló túllépi az engedélyezett adatbázis-tranzakciós egység (45000) kvótáját. | Csökkentse a létrehozni kívánt adatbázis [DWU][DWU] vagy a [kvóta növelését][request a quota increase]. |
| A terület kihasználtságának vizsgálata                              | A rendszerek lemezterület-kihasználtságának megismeréséhez tekintse meg a [táblázat méretét][Table sizes] . |
| Súgó a táblázatok kezeléséhez                                    | A táblák kezelésével kapcsolatos segítségért tekintse meg a [táblázat áttekintését][Overview] ismertető cikket.  A cikk a részletes témakörökre mutató hivatkozásokat is tartalmaz, például a [táblázat adattípusait][Data types], [a táblázatok terjesztését][Distribute], [a táblák indexelését][Index], [a táblák particionálását, a][Partition]táblák [statisztikáit][Statistics] és az [ideiglenes táblákat][Temporary]. |
| Az transzparens adattitkosítás (TDE) folyamatjelző sáv nem frissül a Azure Portal | A TDE állapotát a [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption)segítségével tekintheti meg. |


## <a name="differences-from-sql-database"></a>Különbségek a SQL Database
| Probléma                                 | Felbontás                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Nem támogatott SQL Database funkciók     | Lásd: nem [támogatott táblázatos funkciók][Unsupported table features]. |
| Nem támogatott SQL Database adattípusok   | Lásd: nem [támogatott adattípusok][Unsupported data types].        |
| TÖRLÉSi és frissítési korlátozások         | [A nem támogatott frissítési és törlési szintaxissal][Using CTAS to work around unsupported UPDATE and DELETE syntax]kapcsolatban tekintse meg a [megoldások frissítése][UPDATE workarounds], a [megkerülő megoldások törlése][DELETE workarounds] és a CTAS használata című témakört. |
| A MERGE utasítás nem támogatott      | Lásd: [egyesítési Áthidaló megoldás][MERGE workarounds].                  |
| Tárolt eljárások korlátai          | A tárolt eljárások korlátainak megismeréséhez tekintse meg a [tárolt eljárásokra vonatkozó korlátozásokat][Stored procedure limitations] . |
| A UDF nem támogatja a SELECT utasítások használatát | Ez a UDF jelenlegi korlátozása.  Az általunk támogatott szintaxishoz lásd: [create Function][CREATE FUNCTION] . |

## <a name="next-steps"></a>További lépések
Ha további segítségre van szüksége a probléma megoldásához, néhány további erőforrást is kipróbálhat.

* [Blogok]
* [Funkciókérések]
* [Videók]
* [CAT-csapat blogok]
* [Támogatási jegy létrehozása]
* [MSDN-fórum]
* [Stack Overflow-fórum]
* [Twitter]

<!--Image references-->

<!--Article references-->
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SSMS]: /sql/ssms/download-sql-server-management-studio-ssms
[SSDT for Visual Studio]: sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Támogatási jegy létrehozása]: sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: sql-data-warehouse-manage-compute-overview.md
[DWU]: sql-data-warehouse-overview-what-is.md
[request a quota increase]: sql-data-warehouse-get-started-create-support-ticket.md
[Learning how to monitor your queries]: sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: sql-data-warehouse-get-started-provision.md
[SQL Data Warehouse best practices]: sql-data-warehouse-best-practices.md
[Table sizes]: sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: sql-data-warehouse-tables-overview.md
[Data types]: sql-data-warehouse-tables-data-types.md
[Distribute]: sql-data-warehouse-tables-distribute.md
[Index]: sql-data-warehouse-tables-index.md
[Partition]: sql-data-warehouse-tables-partition.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[Temporary]: sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: resource-classes-for-workload-management.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: sql-data-warehouse-develop-ctas.md
[UPDATE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: sql-data-warehouse-authentication.md


<!--MSDN references-->
[sys.database_principals]: /sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql
[CREATE FUNCTION]: /sql/t-sql/statements/create-function-sql-data-warehouse
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Blogok]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[CAT-csapat blogok]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Funkciókérések]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-fórum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Stack Overflow-fórum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videók]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[Databricks]: https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse#load-data-into-azure-sql-data-warehouse
