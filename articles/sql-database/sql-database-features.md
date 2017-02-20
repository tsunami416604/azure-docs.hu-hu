---
title: "Az Azure SQL Database funkcióinak áttekintése | Microsoft Docs"
description: "Ez az oldal áttekintést nyújt az Azure SQL Database logikai kiszolgálóiról és adatbázisairól, valamint funkciótámogatási mátrixot is tartalmaz, hivatkozásokkal az egyes felsorolt funkcióhoz."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/01/2017
ms.author: carlrab; jognanay
translationtype: Human Translation
ms.sourcegitcommit: 21be71a1b4c79ecec8af02d08f65c41128c5ef73
ms.openlocfilehash: 50a465f314909c10bc3c3f95be2d9dc377d433a7


---
# <a name="azure-sql-database-features"></a>Az Azure SQL Database funkciói
Ez a témakör áttekintést nyújt az Azure SQL Database logikai kiszolgálóiról és adatbázisairól, valamint funkciótámogatási mátrixot is tartalmaz, hivatkozásokkal az egyes felsorolt funkcióhoz. 

## <a name="what-is-an-azure-sql-database-logical-server"></a>Mi az Azure SQL Database logikai kiszolgáló?
Az Azure SQL Database logikai kiszolgáló több adatbázis központi felügyeleti pontjaként működik. Az SQL Database-ben a kiszolgáló egy logikai szerkezet, amely nem azonos a helyszíni SQL Server-példánnyal. Az SQL Database szolgáltatás nem garantálja az adatbázisok helyét a logikai kiszolgálójukhoz képest, valamint nem kínál példányszintű hozzáférést és funkciókat. További információ az Azure SQL logikai kiszolgálókról: [Logikai kiszolgálók](sql-database-server-overview.md). 

## <a name="what-is-an-azure-sql-database"></a>Mi az az Azure SQL Database?
Az Azure SQL Database mindegyik adatbázisa egy logikai kiszolgálóval van társítva. Az adatbázisok:

- lehetnek önálló, [saját erőforráskészlettel](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus) rendelkező adatbázisok (DTU);
- részét képezhetik egy [adatbáziskészletnek](sql-database-elastic-pool.md), amely [egy erőforráskészleten osztozik](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus) (eDTU);
- részét képezhetik [horizontálisan skálázott adatbázisok kibővíthető készletének](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling), amely önálló vagy készletezett adatbázisokból állhat;
- részét képezhetik egy [több-bérlős SaaS kialakítási mintában](sql-database-design-patterns-multi-tenancy-saas-applications.md) szereplő adatbáziskészletnek, mely adatbázisok lehetnek önálló, készletezett vagy mindkétféle adatbázisok. 

További információk az Azure SQL Database-adatbázisokkal kapcsolatban: [SQL Database-adatbázisok](sql-database-overview.md).

## <a name="what-features-are-supported"></a>Milyen funkciók támogatottak?

Az alábbi táblázatok az Azure SQL Database és az SQL Server legfontosabb funkcióit, azok támogatási lehetőségeit, illetve a funkciók egyes platformokra vonatkozó bővebb információira mutató hivatkozásokat tartalmazzák. A Transact-SQL funkcióival kapcsolatban kövesse a táblázatban szereplő hivatkozást, amely a funkció kategóriájához tartozik. További háttérinformációkért az adott típusú funkciók támogatása hiányának okairól lásd még az [Azure SQL Database és a Transact-SQL nyelv eltéréseit](sql-database-transact-sql-information.md) ismertető cikket.

A&12;-es verzió funkcióinak hozzáadása folyamatosan történik. Ezért javasoljuk, hogy látogassa meg az Azure Szolgáltatási hírek webhelyét, és használja a szűrőket:

* Szűrjön [SQL Database szolgáltatásra](https://azure.microsoft.com/updates/?service=sql-database).
* Szűrjön az általános elérhetőséggel kapcsolatos [bejelentésekre](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) az SQL Database funkcióira vonatkozóan.

> [!TIP]
> Ha tesztelni szeretné, hogy egy meglévő adatbázis kompatibilis-e az Azure SQL Database-szel, tekintse meg az [SQL Server-adatbázis migrálása az Azure-ba](sql-database-cloud-migrate.md) című témakört.
>

| **Funkció** | **SQL Server** | **Azure SQL Database** | 
| --- | :---: | :---: | 
| Aktív georeplikáció | Nem támogatott – lásd [AlwaysOn rendelkezésre állási csoportok](https://msdn.microsoft.com/library/hh510230.aspx) | [Támogatott](sql-database-geo-replication-overview.md)
| Always Encrypted | [Támogatott](https://msdn.microsoft.com/library/mt163865.aspx) | [Támogatott](sql-database-always-encrypted.md) |
| AlwaysOn rendelkezésre állási csoportok | [Támogatott](https://msdn.microsoft.com/library/hh510230.aspx) | Nem támogatott – lásd [Aktív georeplikáció](sql-database-geo-replication-overview.md) |
| Adatbázis csatolása | [Támogatott](https://msdn.microsoft.com/library/ms190209.aspx) | Nem támogatott |
| Alkalmazás-szerepkörök | [Támogatott](https://msdn.microsoft.com/library/ms190998.aspx) | [Támogatott](https://msdn.microsoft.com/library/ms190998.aspx) |
| Automatikus méretezés | Nem támogatott | [Támogatott](sql-database-scale-up.md) |
| Azure Active Directory | Nem támogatott | [Támogatott](sql-database-aad-authentication.md) |
| Azure Data Factory | Nem támogatott – lásd [SQL Server Integration Services (SSIS)](https://msdn.microsoft.com/library/ms141026.aspx) | [Támogatott](https://azure.microsoft.com/services/data-factory/) |
| Naplózás | [Támogatott](https://msdn.microsoft.com/library/cc280386.aspx) | [Támogatott](sql-database-auditing-get-started.md) |
| BACPAC-fájl (exportálás) | [Támogatott](https://msdn.microsoft.com/library/hh213241.aspx) | [Támogatott](sql-database-export.md) |
| BACPAC-fájl (importálás) | [Támogatott](https://msdn.microsoft.com/library/hh710052.aspx) | [Támogatott](sql-database-import.md) |
| BACKUP és RESTORE utasítások | [Támogatott](https://msdn.microsoft.com/library/ff848768.aspx) | Nem támogatott |
| Beépített funkciók | [Támogatott](https://msdn.microsoft.com/library/ms174318.aspx) | [A legtöbb esetben](https://msdn.microsoft.com/library/ms174318.aspx) |
| Adatváltozások rögzítése | [Támogatott](https://msdn.microsoft.com/library/cc645937.aspx) | Nem támogatott |
| Változások követése | [Támogatott](https://msdn.microsoft.com/library/bb933875.aspx) | [Támogatott](https://msdn.microsoft.com/library/bb933875.aspx) |
| Rendezési utasítások | [Támogatott](https://msdn.microsoft.com/library/ff848763.aspx) | [Támogatott](https://msdn.microsoft.com/library/ff848763.aspx) |
| Oszlopcentrikus indexek | [Támogatott](https://msdn.microsoft.com/library/gg492088.aspx) | [Csak Premium Edition](https://msdn.microsoft.com/library/gg492088.aspx) |
| Közös nyelvi futtatókörnyezet (CLR) | [Támogatott](https://msdn.microsoft.com/library/ms131102.aspx) | Nem támogatott |
| Tartalmazott adatbázisok | [Támogatott](https://msdn.microsoft.com/library/ff929071.aspx) | Beépített |
| Tartalmazott felhasználók | [Támogatott](https://msdn.microsoft.com/library/ff929188.aspx) | [Támogatott](sql-database-manage-logins.md#non-administrator-users) |
| Vezérlőnyelvi kulcsszavak | [Támogatott](https://msdn.microsoft.com/library/ms174290.aspx) | [Támogatott](https://msdn.microsoft.com/library/ms174290.aspx) |
| Adatbázisközi lekérdezések | [Támogatott](https://msdn.microsoft.com/library/dn584627.aspx) | [Rugalmas lekérdezések](sql-database-elastic-query-overview.md) |
| Kurzorok | [Támogatott](https://msdn.microsoft.com/library/ms181441.aspx) | [Támogatott](https://msdn.microsoft.com/library/ms181441.aspx) | 
| Adattömörítés | [Támogatott](https://msdn.microsoft.com/library/cc280449.aspx) | [Támogatott](https://msdn.microsoft.com/library/cc280449.aspx) |
| Adatbázisok biztonsági mentése | [Felhasználók számára elérhető](https://msdn.microsoft.com/library/ms187048.aspx) | [Beépítve](sql-database-automated-backups.md) |
| Adatbázisbeli levelezés | [Támogatott](https://msdn.microsoft.com/library/ms189635.aspx) | Nem támogatott |
| Adatbázis-tükrözés | [Támogatott](https://msdn.microsoft.com/library/ms189852.aspx) | Nem támogatott |
| Adatbázis-konfigurációs lehetőségek | [Támogatott](https://msdn.microsoft.com/library/mt629158.aspx) | [Támogatott](https://msdn.microsoft.com/library/mt629158.aspx) |
| Data Quality Services (DQS) | [Támogatott](https://msdn.microsoft.com/library/ff877925.aspx) | Nem támogatott |
| Adatbázis-pillanatképek | [Támogatott](https://msdn.microsoft.com/library/ms175158.aspx) | Nem támogatott |
| Adattípusok | [Támogatott](https://msdn.microsoft.com/library/ms187752.aspx) | [Támogatott](https://msdn.microsoft.com/library/ms187752.aspx) |  
| DBCC-utasítások | [Az összes](https://msdn.microsoft.com/library/ms188796.aspx) | [Néhány](https://msdn.microsoft.com/library/ms188796.aspx) |
| DDL-utasítások | [Támogatott](https://msdn.microsoft.com/library/ff848799.aspx) | [A legtöbb esetben](https://msdn.microsoft.com/library/ff848799.aspx)
| DDL-eseményindítók | [Támogatott](https://msdn.microsoft.com/library/ms175941.aspx) | [Csak adatbázis](https://msdn.microsoft.com/library/ms175941.aspx) |
| Elosztott tranzakciók | [MS DTC](https://msdn.microsoft.com/library/ms131665.aspx) | Csak korlátozott SQL Database-beli forgatókönyvek |
| DML-utasítások | [Támogatott](https://msdn.microsoft.com/library/ff848766.aspx) | [A legtöbb esetben](https://msdn.microsoft.com/library/ff848766.aspx) |
| DML-eseményindítók | [Támogatott](https://msdn.microsoft.com/library/ms178110.aspx) | [Támogatott](https://msdn.microsoft.com/library/ms178110.aspx) |
| DMV-k | [Az összes](https://msdn.microsoft.com/library/ms188754.aspx) | [Néhány](https://msdn.microsoft.com/library/ms188754.aspx) |
| rugalmas készletek | Nem támogatott | [Támogatott](sql-database-elastic-pool.md) |
| Rugalmas feladatok | Nem támogatott – lásd [SQL Server Agent](https://msdn.microsoft.com/library/ms189237.aspx) | [Támogatott](sql-database-elastic-jobs-getting-started.md) | 
| Rugalmas lekérdezések | Nem támogatott – lásd [Adatbázisközi lekérdezések](https://msdn.microsoft.com/library/dn584627.aspx) | [Támogatott](sql-database-elastic-query-overview.md) |
| Eseményértesítések | [Támogatott](https://msdn.microsoft.com/library/ms186376.aspx) | [Támogatott](sql-database-insights-alerts-portal.md) |
| Kifejezések | [Támogatott](https://msdn.microsoft.com/library/ms190286.aspx) | [Támogatott](https://msdn.microsoft.com/library/ms190286.aspx) |
| Bővített események | [Támogatott](https://msdn.microsoft.com/library/bb630282.aspx) | [Néhány](sql-database-xevent-db-diff-from-svr.md) |
| Bővített tárolt eljárások | [Támogatott](https://msdn.microsoft.com/library/ms164627.aspx) | Nem támogatott |
| Fájlcsoportok | [Támogatott](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) | [Csak az elsődleges](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) |
| Fájlstream | [Támogatott](https://msdn.microsoft.com/library/gg471497.aspx) | Nem támogatott |
| Teljes szöveges keresés | [Támogatott](https://msdn.microsoft.com/library/ms142571.aspx) | [Külső fejlesztők szóhatárolói nem támogatottak](https://msdn.microsoft.com/library/ms142571.aspx) |
| Functions | [Támogatott](https://msdn.microsoft.com/library/ms174318.aspx) | [A legtöbb esetben](https://msdn.microsoft.com/library/ms174318.aspx) |
| Memóriabeli optimalizálás | [Támogatott](https://msdn.microsoft.com/library/dn133186.aspx) | [Csak Premium Edition](https://msdn.microsoft.com/library/dn133186.aspx) |
| Feladatok | [SQL Server Agent](https://msdn.microsoft.com/library/ms189237.aspx) | [Támogatott](sql-database-elastic-jobs-getting-started.md) |
| JSON-adatok támogatása | [Támogatott](https://msdn.microsoft.com/library/dn921897.aspx) | [Támogatott](sql-database-json-features.md) |
| Nyelvi elemek | [Támogatott](https://msdn.microsoft.com/library/ff848807.aspx) | [A legtöbb esetben](https://msdn.microsoft.com/library/ff848807.aspx) |  
| Társított kiszolgálók | [Támogatott](https://msdn.microsoft.com/library/ms188279.aspx) | Nem támogatott – lásd [Rugalmas lekérdezés](sql-database-elastic-query-horizontal-partitioning.md) |
| Naplóküldés | [Támogatott](https://msdn.microsoft.com/library/ms187103.aspx) | Nem támogatott – lásd [Aktív georeplikáció](sql-database-geo-replication-overview.md) |
| Felügyeleti parancsok | [Támogatott](https://msdn.microsoft.com/library/ms190286.aspx)| [Nem támogatott](https://msdn.microsoft.com/library/ms190286.aspx) |
| Master Data Services (MDS) | [Támogatott](https://msdn.microsoft.com/library/ff487003.aspx) | Nem támogatott |
| Minimális naplózás tömeges importálás során | [Támogatott](https://msdn.microsoft.com/library/ms190422.aspx) | Nem támogatott |
| Rendszeradatok módosítása | [Támogatott](https://msdn.microsoft.com/library/ms178028.aspx) | Nem támogatott |
| Online indexműveletek | [Támogatott](https://msdn.microsoft.com/library/ms177442.aspx) | [A tranzakciók mérete a szolgáltatási szint alapján korlátozott](https://msdn.microsoft.com/library/ms177442.aspx) |
| Operátorok | [Támogatott](https://msdn.microsoft.com/library/ms174986.aspx) | [A legtöbb esetben](https://msdn.microsoft.com/library/ms174986.aspx) |
| Időponthoz kötött adatbázis-visszaállítás | [Támogatott](https://msdn.microsoft.com/library/ms179451.aspx) | [Támogatott](sql-database-recovery-using-backups.md#point-in-time-restore) |
| PolyBase | [Támogatott](https://msdn.microsoft.com/library/mt143171.aspx) | [Nem támogatott]
| Házirendalapú felügyelet | [Támogatott](https://msdn.microsoft.com/library/bb510667.aspx) | Nem támogatott |
| Predikátumok | [Támogatott](https://msdn.microsoft.com/library/ms189523.aspx) | [A legtöbb esetben](https://msdn.microsoft.com/library/ms189523.aspx)
| Erőforrás-vezérlő | [Támogatott](https://msdn.microsoft.com/library/bb933866.aspx) | [Beépítve](sql-database-service-tiers.md) |
| Adatbázis visszaállítása biztonsági mentésből | [Támogatott](https://msdn.microsoft.com/library/ms187048.aspx#anchor_6) | [Kizárólag beépített biztonsági másolatokból](sql-database-recovery-using-backups.md) |
| Sorszintű biztonság | [Támogatott](https://msdn.microsoft.com/library/dn765131.aspx) | [Támogatott](https://msdn.microsoft.com/library/dn765131.aspx) |
| Biztonsági utasítások | [Támogatott](https://msdn.microsoft.com/library/ff848791.aspx) | [Néhány](https://msdn.microsoft.com/library/ff848791.aspx) |
| Szemantikai keresés | [Támogatott](https://msdn.microsoft.com/library/gg492075.aspx) | Nem támogatott |
| Sorozatszámok | [Támogatott](https://msdn.microsoft.com/library/ff878058.aspx) | [Támogatott](https://msdn.microsoft.com/library/ff878058.aspx) |
| Szolgáltatásközvetítő | [Támogatott](https://msdn.microsoft.com/library/bb522893.aspx) | Nem támogatott |
| Kiszolgálókonfigurációs beállítások | [Támogatott](https://msdn.microsoft.com/library/ms189631.aspx) | Nem támogatott – lásd [Adatbázis-konfigurációs beállítások](https://msdn.microsoft.com/library/mt629158.aspx) |
| Utasítások megadása | [Támogatott](https://msdn.microsoft.com/library/ms190356.aspx) | [A legtöbb esetben](https://msdn.microsoft.com/library/ms190356.aspx) 
| Térbeli | [Támogatott](https://msdn.microsoft.com/library/bb933790.aspx) | [Támogatott](https://msdn.microsoft.com/library/bb933790.aspx) |
| SQL Server Agent | [Támogatott](https://msdn.microsoft.com/library/ms189237.aspx) | Nem támogatott – lásd [Rugalmas feladatok](sql-database-elastic-jobs-getting-started.md) |
| SQL Server Analysis Services (SSAS) | [Támogatott](https://msdn.microsoft.com/library/bb522607.aspx) | Nem támogatott – lásd [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| SQL Server Integration Services (SSIS) | [Támogatott](https://msdn.microsoft.com/library/ms141026.aspx) | Nem támogatott – lásd [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) |
| SQL Server PowerShell | [Támogatott](https://msdn.microsoft.com/library/hh245198.aspx) | [Támogatott](https://msdn.microsoft.com/library/hh245198.aspx) |
| SQL Server Profiler | [Támogatott](https://msdn.microsoft.com/library/ms181091.aspx) | Nem támogatott – lásd [Bővített események](https://msdn.microsoft.com/library/ms181091.aspx) |
| SQL Server-replikáció | [Támogatott](https://msdn.microsoft.com/library/ms151198.aspx) | [Kizárólag tranzakciós és pillanatkép-replikációs előfizetők](sql-database-cloud-migrate-compatible-using-transactional-replication.md) |
| SQL Server Reporting Services (SSRS) | [Támogatott](https://msdn.microsoft.com/library/ms159106.aspx) | Nem támogatott |
| Tárolt eljárások | [Támogatott](https://msdn.microsoft.com/library/ms190782.aspx) | [Támogatott](https://msdn.microsoft.com/library/ms190782.aspx) |
| A rendszer tárolt függvényei | [Támogatott](https://msdn.microsoft.com/library/ff848780.aspx) | [Néhány](https://msdn.microsoft.com/library/ff848780.aspx) |
| A rendszer tárolt eljárásai | [Támogatott](https://msdn.microsoft.com/library/ms187961.aspx) | [Néhány](https://msdn.microsoft.com/library/ms187961.aspx) |
| Rendszertáblák | [Támogatott](https://msdn.microsoft.com/library/ms179932.aspx) | [Néhány](https://msdn.microsoft.com/library/ms179932.aspx) |
| Rendszernézetek | [Támogatott](https://msdn.microsoft.com/library/ms177862.aspx) | [Néhány](https://msdn.microsoft.com/library/ms177862.aspx)
| Tábla particionálása | [Támogatott](https://msdn.microsoft.com/library/ms190787.aspx) | [Csak az elsődleges fájlcsoport](https://msdn.microsoft.com/library/ms190787.aspx) |
| Ideiglenes táblák | [Helyi és globális](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) | [Csak helyi](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) |
| Historikus táblák | [Támogatott](https://msdn.microsoft.com/library/dn935015.aspx) | [Támogatott](sql-database-temporal-tables.md) |
| Tranzakciós utasítások | [Támogatott](https://msdn.microsoft.com/library/ms174377.aspx) | [Támogatott](https://msdn.microsoft.com/library/ms174377.aspx) |
| Változók | [Támogatott](https://msdn.microsoft.com/library/ff848809.aspx) | | [Támogatott](https://msdn.microsoft.com/library/ff848809.aspx) | 
| Transzparens adattitkosítás (TDE)  | [Támogatott](https://msdn.microsoft.com/library/bb934049.aspx) | [Támogatott](https://msdn.microsoft.com/dn948096.aspx) |
| Windows Server feladatátvételi fürtszolgáltatás | [Támogatott](https://msdn.microsoft.com/library/hh270278.aspx) | Nem támogatott – lásd [Aktív georeplikáció](sql-database-geo-replication-overview.md) |
| XML-indexek | [Támogatott](http://msdn.microsoft.com/library/bb934097.aspx) | [Támogatott](http://msdn.microsoft.com/library/bb934097.aspx) |
| XML-utasítások | [Támogatott](https://msdn.microsoft.com/library/ff848798.aspx) | [Támogatott](https://msdn.microsoft.com/library/ff848798.aspx) |

## <a name="next-steps"></a>Következő lépések

- Az Azure SQL Database szolgáltatással kapcsolatos tudnivalók: [Mi az SQL Database?](sql-database-technical-overview.md)
- Az Azure SQL logikai kiszolgálók áttekintését lásd: [SQL Database logikai kiszolgálók áttekintése](sql-database-server-overview.md)
- Az Azure SQL Database-adatbázisok áttekintését lásd: [Az SQL Database-adatbázisok áttekintése](sql-database-overview.md)
- A Transact-SQL támogatásával és eltéréseivel kapcsolatos tudnivalókat lásd: [Azure SQL Database Transact-SQL különbségek](sql-database-transact-sql-information.md)
- A pontos erőforráskvótákat és -korlátozásokat a **szolgáltatásszint** határozza meg. A szolgáltatásszintek áttekintését lásd: [SQL Database-szolgáltatásszintek](sql-database-service-tiers.md).
- A biztonsági szolgáltatások áttekintését lásd [az Azure SQL Database biztonsági szolgáltatásainak áttekintésével](sql-database-security-overview.md) foglalkozó cikkben.
- Az SQL Database illesztőinek rendelkezésre állásával és támogatásával kapcsolatos tudnivalókat lásd: [Adatkapcsolattárak az SQL Database-hez és az SQL Serverhez](sql-database-libraries.md).



<!--HONumber=Feb17_HO2-->


