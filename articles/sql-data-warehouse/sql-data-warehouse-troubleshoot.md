---
title: Hibaelhárítás az Azure SQL Data warehouse-bA |} A Microsoft Docs
description: Hibaelhárítás az Azure SQL Data warehouse-bA.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 4/26/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 02591185914f3b04a70af3b7c5d607f4a2865806
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65154266"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Hibaelhárítás az Azure SQL Data warehouse-bA
Ez a cikk a gyakori hibaelhárítási kérdések listája.

## <a name="connecting"></a>Csatlakozás
| Probléma                                                        | Megoldás:                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| "NT AUTHORITY\NÉVTELEN bejelentkezés" felhasználó bejelentkezése sikertelen volt. (A Microsoft SQL Server, a hiba: 18456) | Ez a hiba akkor fordul elő, amikor egy AAD-felhasználót úgy próbál csatlakozni a master adatbázisban, de nem rendelkezik egy felhasználót a főadatbázisban.  A probléma, vagy adja meg az SQL Data Warehouse kívánt kapcsolat időpontban csatlakozni, vagy adja hozzá a felhasználót a master adatbázishoz.  Lásd: [biztonsági áttekintése] [ Security overview] további részleteivel. |
| Az egyszerű "MyUserName" kiszolgálója nem érhetik el a "master" adatbázis aktuális biztonsági környezetében. Nem lehet megnyitni a felhasználói alapértelmezett adatbázist. A bejelentkezés sikertelen volt. "MyUserName" felhasználó bejelentkezése sikertelen volt. (A Microsoft SQL Server, a hiba: 916) | Ez a hiba akkor fordul elő, amikor egy AAD-felhasználót úgy próbál csatlakozni a master adatbázisban, de nem rendelkezik egy felhasználót a főadatbázisban.  A probléma, vagy adja meg az SQL Data Warehouse kívánt kapcsolat időpontban csatlakozni, vagy adja hozzá a felhasználót a master adatbázishoz.  Lásd: [biztonsági áttekintése] [ Security overview] további részleteivel. |
| CTAIP hiba                                                  | Ez a hiba akkor fordulhat elő, egy bejelentkezés létrehozása után az SQL server főadatbázisában, de nem az SQL Data Warehouse-adatbázisban.  Ha ezt a hibát tapasztal, vessen egy pillantást a [biztonsági áttekintése] [ Security overview] cikk.  Ez a cikk bemutatja, hogyan hozhat létre a bejelentkezést és felhasználót a master, majd egy felhasználó létrehozása az SQL Data Warehouse-adatbázis. |
| Tűzfal által blokkolva                                          | Az Azure SQL Database által védett kiszolgáló és az adatbázis adatbázisszintű tűzfalakra, győződjön meg arról, hogy csak ismert IP-címek rendelkezik hozzáféréssel egy adatbázishoz. A tűzfalak biztonságosak alapértelmezett, ami azt jelenti, hogy explicit módon engedélyeznie kell, és IP-címet vagy címtartományt, mielőtt az csatlakozna.  A tűzfal hozzáférés konfigurálásához kövesse [kiszolgálói tűzfal-hozzáférés konfigurálása az ügyfél IP-] [ Configure server firewall access for your client IP] a a [utasításokat kiépítés] [Provisioning instructions]. |
| Eszköz vagy az illesztőprogram nem tud csatlakozni                           | Az SQL Data Warehouse használatát javasolja [SSMS][SSMS], [SSDT a Visual Studio][SSDT for Visual Studio], vagy [sqlcmd] [ sqlcmd] az adatok lekérdezéséhez. Az illesztőprogramok és csatlakozás az SQL Data Warehouse további információkért lásd: [illesztőprogramok az Azure SQL Data Warehouse] [ Drivers for Azure SQL Data Warehouse] és [csatlakozhat az Azure SQL Data Warehouse] [ Connect to Azure SQL Data Warehouse] cikkeket. |

## <a name="tools"></a>Eszközök
| Probléma                                                        | Megoldás:                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| A Visual Studio object explorer hiányzik az AAD-felhasználók           | Ez egy ismert probléma.  Áthidaló megoldásként a felhasználók megtekintéséhez [sys.database_principals][sys.database_principals].  Lásd: [hitelesítés az Azure SQL Data Warehouse] [ Authentication to Azure SQL Data Warehouse] tudhat meg többet az Azure Active Directory használatával az SQL Data Warehouse szolgáltatással. |
| Parancsfájl-kezelési, a parancsfájl-kezelési varázslóval vagy való csatlakozás SSMS manuális lassú, nem válaszol, vagy hibák előállító | Győződjön meg arról, hogy létrejöttek-e a felhasználók a master adatbázisban. A parancsprogram-beállítások győződjön meg arról is, hogy a motor edition "Microsoft Azure SQL Data Warehouse Edition" van beállítva, és motor-típus: "Microsoft Azure SQL Database". |
| Parancsfájlok sikertelen létrehozása az ssms-ben                             | Az SQL data warehouse egy parancsfájl generálása sikertelen lesz, ha a beállítás "Generate parancsfájl a függő objektumok" beállítás értéke "True". Áthidaló megoldásként felhasználók kell manuálisan Ugrás a eszközök -> Beállítások -> SQL Server Object Explorer -> Generate parancsfájl a függő beállításokat, és "false" értékűre |

## <a name="performance"></a>Teljesítmény
| Probléma                                                        | Megoldás:                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Lekérdezési teljesítmény hibaelhárítása                            | Ha egy adott lekérdezés hibaelhárítása kívánt, kezdje [megtudhatja, hogyan figyelheti a lekérdezések][Learning how to monitor your queries]. |
| Gyenge lekérdezési teljesítmény és a tervek gyakran egy hiányzó statisztika eredménye | Gyenge teljesítményt leggyakoribb oka a statisztikákat a táblák hiánya.  Lásd: [Táblastatisztika karbantartása] [ Statistics] statisztikák létrehozása és azok miért a teljesítmény kritikus fontosságú. |
| Alacsony egyidejűségi / lekérdezések várólistára helyezve                             | Tudnivalók a [számítási feladatok kezeléséhez] [ Workload management] fontos annak érdekében, hogy tisztában van az egyidejűség lefoglalt memória elosztása érdekében. |
| Ajánlott eljárások megvalósítása                              | A legjobb hely a megismerkedik a lekérdezési teljesítmény javítására van [SQL Data Warehouse – gyakorlati tanácsok] [ SQL Data Warehouse best practices] cikk. |
| A méretezés teljesítményének növelése                      | Néha a teljesítmény javítása a megoldás egyszerűen hozzáadásához a nagyobb számítási teljesítmény, a lekérdezések által [az SQL Data Warehouse méretezése][Scaling your SQL Data Warehouse]. |
| Gyenge lekérdezések teljesítményét eredményeként gyenge index minőségét     | Néhány eset lekérdezések lassulnak le, mert [oszlopcentrikus indexek gyenge minőségének][Poor columnstore index quality].  Ebben a cikkben további információt és hogyan [szegmens minőségének javítására indexek újraépítése][Rebuild indexes to improve segment quality]. |

## <a name="system-management"></a>Rendszerfelügyelet
| Probléma                                                        | Megoldás:                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: Nem sikerült végrehajtani a műveletet, mert a kiszolgáló túllépné az engedélyezett adatbázis-tranzakciós egységek, 45000 vonatkozó kvótát. | Vagy csökkentse a [DWU] [ DWU] a létrehozni kívánt adatbázis vagy [a kvóta növelésére][request a quota increase]. |
| Lemezterület-kihasználás kivizsgálása                              | Lásd: [méretek tábla] [ Table sizes] megérteni a lemezterület-kihasználás, a rendszer. |
| Táblák kezelésében is segíteni                                    | Tekintse meg a [táblák áttekintésével] [ Overview] cikkben segítséget a táblák kezelése.  Ez a cikk hivatkozásokat is tartalmaz, például további információt tartalmazó témakörökre [tábla adattípusaival][Data types], [terjesztése egy tábla][Distribute], [Tábla indexelése][Index], [tábla particionálása][Partition], [Táblastatisztika karbantartása] [ Statistics] és [ideiglenes táblák][Temporary]. |
| Az Azure Portalon nem frissíti a transzparens titkosítást (TDE) folyamatjelző sáv | A TDE-n keresztül állapotát megtekintheti [powershell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption). |

## <a name="polybase"></a>PolyBase
| Probléma                                           | Megoldás:                                                   |
| :---------------------------------------------- | :----------------------------------------------------------- |
| Kivitel sikertelen, és a TINYINT és dátum típusú             | Az ORC és a Parquet fájlformátum, dátum típusú értékek közé kell esnie 1970-01-01-00:00:01 UTC és 2038-01-19-03:14:07. TINYINT típusú értékek 0 – 127 karakter között kell lennie.    |
| Probléma a parquet eszközökben tizedes tört szám típus: Spark rendszerből írása DecimalType(18,4) írja be, és valós vagy double típusú oszlop importálása biztosít a "hiba: java.base/java.lang.Long java.base/java.lang.Float nem konvertálható". | Bigint importálja és 10000, vagy használjon nullával kell a [Databricks] az SQL DW connector. |
| A parquet vagy egyéb probléma dátumtípus: Spark típusú dátum és a egy számoszlop való importálásakor, írja be a dátum vagy dátum/idő ad "hiba: java.base/java.lang.Integer parquet.io.api.Binary nem konvertálható". | Kell egy másik Spark-típust (int) és számítási a dátum vagy használ a [Databricks] az SQL DW connector. |

## <a name="differences-from-sql-database"></a>Különbségek az SQL Database-ből
| Probléma                                 | Megoldás:                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Nem támogatott az SQL Database-funkciók     | Lásd: [nem támogatott funkciók tábla][Unsupported table features]. |
| Nem támogatott az SQL Database-adattípusok   | Lásd: [támogatott adattípusok][Unsupported data types].        |
| Törlés és a frissítési korlátozások         | Lásd: [frissítési megkerülő megoldások][UPDATE workarounds], [törlése lehetséges megoldások] [ DELETE workarounds] és [megkerüléséhez a CTAS használata nem támogatott frissítési és TÖRLÉS szintaxis][Using CTAS to work around unsupported UPDATE and DELETE syntax]. |
| A MERGE utasítás nem támogatott.      | Lásd: [egyesítési megoldások][MERGE workarounds].                  |
| Tárolt eljárás korlátozások          | Lásd: [tárolt eljárás korlátozások] [ Stored procedure limitations] tárolt eljárásokra vonatkozó korlátozások némelyike megértéséhez. |
| Felhasználói függvények nem támogatják a SELECT utasításokban | Ez az aktuális korlátozása az UDF-EK.  Lásd: [CREATE FUNCTION] [ CREATE FUNCTION] támogatunk szintaxisának. |

## <a name="next-steps"></a>További lépések
További segítségre van szüksége a megoldás a problémára Íme néhány más erőforrások, próbálja meg.

* [Blogok]
* [Funkciókérések]
* [Videók]
* [CAT csapatblogok]
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
[CAT csapatblogok]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Funkciókérések]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-fórum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Stack Overflow-fórum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videók]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[Databricks]: https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse#load-data-into-azure-sql-data-warehouse
