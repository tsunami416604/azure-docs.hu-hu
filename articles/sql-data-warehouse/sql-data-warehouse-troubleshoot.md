---
title: "Hibaelhárítás az Azure SQL Data Warehouse |} Microsoft Docs"
description: "Hibaelhárítás az Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: 51f1e444-9ef7-4e30-9a88-598946c45196
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 03/30/2017
ms.author: kevin;barbkess
ms.openlocfilehash: d269e62b8d49a6c96ce40c2e31c4096e16e07793
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse hibaelhárítása
Ez a témakör néhány a gyakori hibaelhárítási kérdések hozzáadunk kapunk az ügyfelektől.

## <a name="connecting"></a>Csatlakozás
| Probléma | Megoldás: |
|:--- |:--- |
| "NT AUTHORITY\NÉVTELEN bejelentkezés" felhasználó bejelentkezése sikertelen volt. (A Microsoft SQL Server, hiba: 18456) |Ez akkor fordul elő, amikor egy AAD-felhasználó megpróbál csatlakozni a master adatbázisban, de nem rendelkezik a felhasználó a főadatbázisban.  Ez a probléma elhárításához adjon meg az SQL Data Warehouse kapcsolat időpontban csatlakozni, vagy vegye fel a felhasználót a fő adatbázist kíván.  Lásd: [biztonsági áttekintése] [ Security overview] cikkben olvashat. |
| A kiszolgáló egyszerű "sajátfelhasználónév" nincs hozzáférhetnek a "fő" adatbázis aktuális biztonsági környezetében. Nem lehet megnyitni a felhasználói alapértelmezett adatbázist. A bejelentkezés sikertelen volt. "Sajátfelhasználónév" felhasználó bejelentkezése sikertelen volt. (A Microsoft SQL Server, hiba: 916) |Ez akkor fordul elő, amikor egy AAD-felhasználó megpróbál csatlakozni a master adatbázisban, de nem rendelkezik a felhasználó a főadatbázisban.  Ez a probléma elhárításához adjon meg az SQL Data Warehouse kapcsolat időpontban csatlakozni, vagy vegye fel a felhasználót a fő adatbázist kíván.  Lásd: [biztonsági áttekintése] [ Security overview] cikkben olvashat. |
| CTAIP hiba |Ez a hiba akkor fordulhat elő, a bejelentkezés az SQL-kiszolgáló adatbázisában, de nem az SQL Data Warehouse-adatbázis létrehozása.  Ha ezt a hibát észlel, vessen egy pillantást a [biztonsági áttekintése] [ Security overview] cikk.  Ez a cikk ismerteti, hogyan hozzon létre egy felhasználónevet és egy felhasználó létrehozása a master és a felhasználó az SQL Data Warehouse-adatbázis létrehozása. |
| Tiltsa le tűzfal |Az Azure SQL-adatbázisok kiszolgáló és az adatbázis szintjén tűzfallal védett biztosításához csak ismert IP-címek van egy adatbázis eléréséhez. A tűzfalak biztonságosabbak alapértelmezett, ami azt jelenti, hogy explicit módon engedélyeznie kell, és az IP-cím vagy a címtartományt, mielőtt az csatlakozna.  A tűzfalbeállításokat, a hozzáférés, kövesse a lépéseket a [kiszolgáló tűzfal elérésének konfigurálása az ügyfél IP-címhez] [ Configure server firewall access for your client IP] a a [utasításokat kiépítés][Provisioning instructions]. |
| Az eszköz vagy az illesztőprogram nem lehet kapcsolódni |Az SQL Data Warehouse használatát javasolja [SSMS][SSMS], [az SSDT a Visual Studio][SSDT for Visual Studio], vagy [sqlcmd] [ sqlcmd] az adatok lekérdezésére. Illesztőprogramok és a csatlakozás az SQL Data Warehouse további részletekért lásd: [az Azure SQL Data Warehouse illesztőprogramok] [ Drivers for Azure SQL Data Warehouse] és [csatlakozás az Azure SQL Data Warehouse] [ Connect to Azure SQL Data Warehouse] cikkeket. |

## <a name="tools"></a>Eszközök
| Probléma | Megoldás: |
|:--- |:--- |
| A Visual Studio object Explorerben hiányzik az AAD-felhasználókat |Ez az egy ismert probléma.  Megoldás a felhasználók megtekintése [sys.database_principals][sys.database_principals].  Lásd: [hitelesítés az Azure SQL Data Warehouse] [ Authentication to Azure SQL Data Warehouse] tudhat meg többet az Azure Active Directoryval az SQL Data Warehouse szolgáltatással. |
|Manuális parancsfájlok, a parancsfájl-kezelési varázsló segítségével, vagy a SSMS kapcsolatra lassú, lefagyott vagy előállító hibák| Győződjön meg arról, hogy létrejöttek-e a felhasználók a főadatbázisban. A parancsprogram-beállítások ellenőrizze azt is, hogy a "Microsoft Azure SQL Data Warehouse Edition" be van állítva a motor edition és a motor típus: "Microsoft Azure SQL adatbázis".|

## <a name="performance"></a>Teljesítmény
| Probléma | Megoldás: |
|:--- |:--- |
| Lekérdezés teljesítmény hibaelhárítása |Ha egy adott lekérdezés elhárításához próbálja, kezdje [figyelése a lekérdezések tanulási][Learning how to monitor your queries]. |
| Gyenge a lekérdezések teljesítményét és tervek gyakran hiányzó statisztika eredménye |A teljesítmény gyenge leggyakoribb oka a statisztikákat a táblák hiánya.  Lásd: [fenntartása a tábla statisztikai adatainak] [ Statistics] statisztikák létrehozása, és hogy azok miért fontos a teljesítménye. |
| Alacsony feldolgozási / lekérdezések várólistára |Understanding [munkaterhelés felügyeleti] [ Workload management] fontos annak megértése, hogyan együtt memóriafoglalást elosztása érdekében. |
| Megvalósításához ajánlott eljárások |A legjobb hely a start további lehetőségek lekérdezés teljesítményének javítására van [gyakorlati tanácsok az SQL Data Warehouse] [ SQL Data Warehouse best practices] cikk. |
| A méretezés teljesítményének javításával |Egyes esetekben a megoldást a teljesítmény fokozása-hoz egyszerűen adhat hozzá további számítási a lekérdezések teljesítményét [az SQL Data Warehouse skálázás][Scaling your SQL Data Warehouse]. |
| Gyenge lekérdezési teljesítményt, gyenge index minőségi miatt |Néhány eset lekérdezéseket is lassulást miatt [gyenge oszlopcentrikus index minőségi][Poor columnstore index quality].  Ebben a cikkben találhat további információt és [szegmens minőségének javítására indexek újraépítése][Rebuild indexes to improve segment quality]. |

## <a name="system-management"></a>Rendszer-felügyeleti
| Probléma | Megoldás: |
|:--- |:--- |
| 40847. üzenet: Nem sikerült végrehajtani a műveletet, mert a kiszolgáló túllépné az engedélyezett adatbázis tranzakciós egység beállított kvótát 45000. |Vagy csökkentse a [DWU] [ DWU] a létrehozni kívánt adatbázis vagy [a kvóta növelését][request a quota increase]. |
| Lemezterület-kihasználás kivizsgálása |Lásd: [méretek tábla] [ Table sizes] tudni, hogy a lemezterület-kihasználás, a rendszer. |
| Súgó a táblák kezelése |Tekintse meg a [tábla áttekintése] [ Overview] cikk való kezeléséhez a táblák segítségét.  Ez a cikk hivatkozásokat is tartalmaz részletes témakörökre például [adattípusok tábla][Data types], [terjesztése egy tábla][Distribute], [tábla indexelő][Index], [tábla particionáló][Partition], [fenntartása a tábla statisztikai adatainak] [ Statistics] és [az ideiglenes táblák][Temporary]. |
|Átlátható titkosítási (TDE) folyamatjelző sáv nem frissíti az Azure portálon|Megtekintheti a TDE keresztül állapotának [powershell](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption).|

## <a name="polybase"></a>PolyBase
| Probléma | Megoldás: |
|:--- |:--- |
| Betöltési nagy sorok miatt meghiúsul |Nagy sor támogatása jelenleg nem érhető el a Polybase.  Ez azt jelenti, hogy ha a tábla tartalmaz, VARCHAR(MAX), NVARCHAR(MAX) vagy VARBINARY(MAX), külső táblák nem használhatók az adatok betöltésére.  Nagy sorok terhelések jelenleg csak az Azure Data Factory (a BCP-vel), Azure Stream Analytics, SSIS, BCP vagy a .NET SQLBulkCopy osztály támogatott. Nagy sorok PolyBase támogatása egy későbbi kiadásban lesz hozzáadva. |
| MAXIMÁLIS adattípus-táblázat BCP betöltése sikertelen |Nincs olyan ismert probléma, amely megköveteli, hogy az VARCHAR(MAX), NVARCHAR(MAX) vagy VARBINARY(MAX) kerüljenek-e bizonyos esetekben a táblázat végére.  Próbálja meg a maximális oszlopok a tábla végéhez. |

## <a name="differences-from-sql-database"></a>Eltérések a SQL-adatbázis
| Probléma | Megoldás: |
|:--- |:--- |
| Nem támogatott SQL-adatbázis szolgáltatások |Lásd: [táblában funkciók nem támogatott][Unsupported table features]. |
| Nem támogatott SQL-adatbázis adattípusok |Lásd: [nem támogatott típusú adatokat][Unsupported data types]. |
| Törlés és frissítési korlátozások |Lásd: [frissítési megkerülő megoldások][UPDATE workarounds], [törlése lehetséges megoldások] [ DELETE workarounds] és [használatával CTAS kerülő UPDATE és DELETE szintaxis nem támogatott][Using CTAS to work around unsupported UPDATE and DELETE syntax]. |
| MERGE utasítás nem támogatott. |Lásd: [egyesítési lehetséges megoldások][MERGE workarounds]. |
| Tárolt eljárás korlátozásai |Lásd: [tárolt eljárás korlátozások] [ Stored procedure limitations] tárolt eljárásokra vonatkozó korlátozások némelyike megértése. |
| Felhasználó által megadott függvények nem támogatják a SELECT utasítás |Ez az a felhasználó által megadott függvények aktuális korlátozása.  Lásd: [CREATE FUNCTION] [ CREATE FUNCTION] a szintaxis támogatott. |

## <a name="next-steps"></a>Következő lépések
Ha Ön volt nem sikerült megtalálni a megoldást a fenti problémát, az alábbiakban néhány más erőforrások, próbálja meg.

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
[Security overview]: ./sql-data-warehouse-overview-manage-security.md
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx
[SSDT for Visual Studio]: ./sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: ./sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Támogatási jegy létrehozása]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md
[request a quota increase]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Learning how to monitor your queries]: ./sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: ./sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: ./sql-data-warehouse-get-started-provision.md#create-a-server-level-firewall-rule-in-the-azure-portal
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[Table sizes]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: ./sql-data-warehouse-develop-concurrency.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[UPDATE workarounds]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: ./sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md
[Working around the PolyBase UTF-8 requirement]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[sys.database_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[CREATE FUNCTION]: https://msdn.microsoft.com/library/mt203952.aspx
[sqlcmd]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-get-started-connect-sqlcmd/

<!--Other Web references-->
[Blogok]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[CAT csapatblogok]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Funkciókérések]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-fórum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Stack Overflow-fórum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videók]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
