---
title: Kapcsolatos ismert, Azure Database Migration Service csatlakozik a forrásadatbázisok kapcsolatos problémák/hibák hibaelhárítási cikk |} A Microsoft Docs
description: Tudnivalók az ismert problémák/hibák társított Azure Database Migration Service csatlakozik a forrásadatbázisok hibaelhárítása.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: a4ebd1d4c85631cc6ebc1f5787e7545b78d62b5e
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462849"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>A DMS-hibák elhárítása a forrásadatbázisok való csatlakozáskor

A következő cikkben részletesen kapcsolatos lehetséges problémák merülhetnek fel az Azure Database Migration Service (DMS) az adatforrás-adatbázishoz való csatlakozáskor. Minden szakasz az alábbi vonatkozik, egy adott típusú forrásadatbázis, listázása, a hiba esetleg felmerülő együtt részleteit és a kapcsolatának hibaelhárítása kapcsolatos információkra mutató hivatkozásokat.

## <a name="sql-server"></a>SQL Server

Lehetséges problémák társított forrás SQL Server-adatbázishoz szeretne csatlakozni, és miként kezelheti ezeket az alábbi táblázatban szerepelnek.

| Hiba         | OK és hibaelhárítási részletei |
| ------------- | ------------- |
| Nem sikerült az SQL-kapcsolatot. Hálózattal kapcsolatos vagy példányspecifikus hiba történt az SQL Serverhez való kapcsolódás során. A kiszolgáló nem található vagy nem érhető el. Győződjön meg arról, hogy a példány nevének helyességéről, és a SQL Server konfigurálva van a távoli kapcsolatok lehetővé tételéhez.<br> | Ez a hiba akkor fordul elő, ha a szolgáltatás nem tudja találni a forráskiszolgálót. A probléma megoldása érdekében tekintse meg a cikket [hiba történt a forrás SQL-kiszolgáló csatlakozik, a dinamikus port használata esetén, vagy a megnevezett példány](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance). |
| **53-as hiba** – sikertelen SQL-kapcsolat. (Hiba esetében 1, 2, 5, 53-as, 233, 258, 1225, kódok 11001)<br><br> | Ez a hiba akkor fordul elő, ha a szolgáltatás nem tud csatlakozni a forráskiszolgálón. A probléma megoldása érdekében tekintse meg a következő, és próbálkozzon újra. <br><br>  [Interaktív felhasználói útmutatót a kapcsolati problémák elhárítása](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Az Azure SQL Database áttelepítése az SQL Server vonatkozó Előfeltételek](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [Egy Azure SQL Database felügyelt példány az SQL-kiszolgáló áttelepítése előfeltételei](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **Hiba 18456** -bejelentkezés sikertelen volt.<br> | Ez a hiba akkor fordul elő, ha a szolgáltatás nem tud csatlakozni a forrásadatbázis a T-SQL megadott hitelesítő adatokkal. A probléma megoldása érdekében a megadott hitelesítő adatok ellenőrzéséhez. Emellett olvassa el [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) vagy a hibaelhárítási dokumentumok ez alatti megjegyzésben szereplő táblát, és próbálkozzon újra. |
| Helytelen formátumú AccountName értékének "{0}" megadott. AccountName várt formátuma Tartománynév\felhasználónév<br> | Ez a hiba akkor fordul elő, ha a felhasználó kiválasztja a Windows-hitelesítést, de biztosítja a felhasználónév formátuma érvénytelen. A probléma megoldására, vagy adja meg felhasználónevet a megfelelő formátumban Windows-hitelesítést, vagy válasszon **SQL-hitelesítés**. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

Lehetséges problémák társított forrás az AWS RDS MySQL-adatbázishoz szeretne csatlakozni, és miként kezelheti ezeket az alábbi táblázatban szerepelnek.

| Hiba         | OK és hibaelhárítási részletei |
| ------------- | ------------- |
| **Hiba történt [2003]** [HY000] - kapcsolat sikertelen. Hiba történt [HY000] [MySQL] [ODBC x.x(w) illesztőprogram] nem lehet csatlakozni a(z) {server} nevű (10060) a MySQL-kiszolgáló | Ez a hiba akkor fordul elő, ha a MySQL ODBC-illesztő nem tud csatlakozni a forráskiszolgálón. A probléma megoldása érdekében tekintse meg a táblázat alatti megjegyzésben szereplő hibaelhárítási dokumentumok, és próbálkozzon újra.<br> |
| **Hiba történt [2005]** [HY000] - kapcsolat sikertelen. Hiba történt [HY000] [MySQL] [ODBC x.x(w) illesztőprogram] Ismeretlen MySQL server-gazdagép a(z) {server} nevű | Ez a hiba akkor fordul elő, ha a szolgáltatás nem találja a forrás gazdagépen lévő RDS A probléma vagy lehet az oka, hogy a felsorolt forrás nem létezik, vagy a távoli asztali szolgáltatások infrastruktúrával probléma merül fel. A probléma megoldása érdekében tekintse meg a táblázat alatti megjegyzésben szereplő hibaelhárítási dokumentumok, és próbálkozzon újra.<br> |
| **Hiba történt [1045]** [HY000] - kapcsolat sikertelen. Hiba történt [HY000] [MySQL] [ODBC x.x(w) illesztőprogram] a hozzáférés megtagadva "{user}'@'{server}" felhasználó (jelszó használatával: IGEN) | Ez a hiba akkor fordul elő, ha MySQL ODBC-illesztő a forráskiszolgáló érvénytelen hitelesítő adatok miatt nem tud csatlakozni. A megadott hitelesítő adatok ellenőrzéséhez. Ha a probléma továbbra is fennáll, ellenőrizze, hogy a forrásoldali számítógép rendelkezik a megfelelő hitelesítő adatokat. Szükség lehet a jelszót a konzolban. Ha a probléma továbbra is fennáll, tekintse meg a táblázat alatti megjegyzésben szereplő hibaelhárítási dokumentumok, és próbálkozzon újra.<br> |
| **Hiba történt [9002]** [HY000] - kapcsolat sikertelen. Hiba történt [HY000] [MySQL] [ODBC x.x(w) illesztőprogram] a kapcsolati karakterlánc nem lehet megfelelő. Látogasson el a portál a hivatkozásokat.| Ez a hiba akkor fordul elő, ha a kapcsolat kapcsolati probléma miatt nem működik. Ellenőrizze, hogy a megadott kapcsolati karakterlánc érvénytelen. A probléma megoldása érdekében tekintse meg a táblázat alatti megjegyzésben szereplő hibaelhárítási dokumentumok, és próbálkozzon újra.<br> |
| **Hiba történt a bináris naplózás. A binlog_format változó értéke "{value}". Változtassa meg a "sort".** | Ez a hiba akkor fordul elő, ha hiba történt a bináris naplózás. a binlog_format változó értéke a nem megfelelő. A probléma megoldása érdekében módosítsa a binlog_format paraméter csoport "Sor", és indítsa újra a példányhoz. További információkért lásd: [bináris naplózási beállítások és a változók](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) vagy [az AWS RDS MySQL-adatbázis naplófájljai dokumentáció](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html).<br> |

> [!NOTE]
> Forrás az AWS RDS MySQL-adatbázishoz szeretne csatlakozni kapcsolódó problémák hibaelhárításával kapcsolatos további információkért lásd a következőket:
> * [Az Amazon RDS kapcsolódási problémáinak elhárítása](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Hogyan oldhatom fel a problémába ütközik az Amazon RDS adatbázispéldány?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

Lehetséges problémák társított forrás az AWS RDS PostgreSQL-adatbázishoz szeretne csatlakozni, és miként kezelheti ezeket az alábbi táblázatban szerepelnek.

| Hiba         | OK és hibaelhárítási részletei |
| ------------- | ------------- |
| **Hiba történt [101]** [08001] - kapcsolat sikertelen. Hiba történt [08001] túllépte az időkorlátot. | Ez a hiba akkor fordul elő, ha a Postgres illesztőprogramot nem tud csatlakozni a forráskiszolgálón. A probléma megoldása érdekében tekintse meg a táblázat alatti megjegyzésben szereplő hibaelhárítási dokumentumok, és próbálkozzon újra. |
| **Hiba: Wal_level paraméter értéke {value}. Változtassa meg a "logikai" replikáció.** | Ez a hiba akkor fordul elő, ha a paraméter wal_level rendelkezik a megfelelő értéket. A probléma megoldása érdekében módosítsa a paraméter csoport rds.logical_replication 1-re, és indítsa újra a példányhoz. További információkért lásd: [Azure postgresql-hez a DMS használatával történő áttelepítésre vonatkozó Előfeltételek](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites) vagy [az Amazon RDS PostgreSQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html). |

> [!NOTE]
> Forrás az AWS RDS PostgreSQL-adatbázishoz szeretne csatlakozni kapcsolódó problémák hibaelhárításával kapcsolatos további információkért lásd a következőket:
> * [Az Amazon RDS kapcsolódási problémáinak elhárítása](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Hogyan oldhatom fel a problémába ütközik az Amazon RDS adatbázispéldány?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

Lehetséges problémák társított forrás az AWS RDS SQL Server-adatbázishoz szeretne csatlakozni, és miként kezelheti ezeket az alábbi táblázatban szerepelnek.

| Hiba         | OK és hibaelhárítási részletei |
| ------------- | ------------- |
| **53-as hiba** – sikertelen SQL-kapcsolat. Hálózattal kapcsolatos vagy példányspecifikus hiba történt az SQL Serverhez való kapcsolódás során. A kiszolgáló nem található vagy nem érhető el. Győződjön meg arról, hogy a példány nevének helyességéről, és a SQL Server konfigurálva van a távoli kapcsolatok lehetővé tételéhez. (szolgáltató: Nevesített csövek szolgáltató, hiba: 40 – nem nyitható meg a Kapcsolódás az SQL Serverhez | Ez a hiba akkor fordul elő, ha a szolgáltatás nem tud csatlakozni a forráskiszolgálón. A probléma megoldása érdekében tekintse meg a táblázat alatti megjegyzésben szereplő hibaelhárítási dokumentumok, és próbálkozzon újra. |
| **Hiba 18456** -bejelentkezés sikertelen volt. A(z) {user} nevű felhasználó bejelentkezése sikertelen volt | Ez a hiba akkor fordul elő, ha a szolgáltatás nem tud csatlakozni a forrásadatbázis megadott T-SQL hitelesítő adatokkal. A probléma megoldása érdekében a megadott hitelesítő adatok ellenőrzéséhez. Emellett olvassa el [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) vagy a hibaelhárítási dokumentumok ez alatti megjegyzésben szereplő táblát, és próbálkozzon újra. |
| **Hiba 87** -kapcsolati karakterlánc nem érvényes. Hálózattal kapcsolatos vagy példányspecifikus hiba történt az SQL Serverhez való kapcsolódás során. A kiszolgáló nem található vagy nem érhető el. Győződjön meg arról, hogy a példány nevének helyességéről, és a SQL Server konfigurálva van a távoli kapcsolatok lehetővé tételéhez. (szolgáltató: SQL hálózati adapterek, hiba: 25 - kapcsolati karakterlánca nem érvényes) | Ez a hiba akkor fordul elő, ha a szolgáltatás nem tud csatlakozni a forráskiszolgálót egy érvénytelen kapcsolati karakterlánc miatt. A probléma megoldása érdekében ellenőrizze a megadott kapcsolati karakterlánc. Ha a probléma nem szűnik meg, tekintse meg a táblázat alatti megjegyzésben szereplő hibaelhárítási dokumentumok, és próbálkozzon újra. |
| **Hiba – a kiszolgáló tanúsítványa nem megbízható.** Sikeresen létrejött a kapcsolat a kiszolgálóval, de hiba történt a bejelentkezés során. (szolgáltató: SSL-szolgáltató, hiba: 0 – a tanúsítványlánc adta ki, amely nem megbízható hatóság.) | Ez a hiba akkor fordul elő, ha a használt tanúsítvány nem megbízható. A probléma megoldása érdekében meg kell keresnie egy tanúsítványt, amely megbízhatónak minősül, és engedélyezze a kiszolgálón. Másik lehetőségként is a megbízható tanúsítvány lehetőséggel kapcsolódás közben. Ezt a műveletet csak akkor, ha ismeri a használt tanúsítványt, és megbízik benne. <br> SSL-kapcsolatok önaláírt tanúsítványok használata titkosított nem nyújt erős biztonságot – man-in-the-middle támadásokra zajlik. Ne használja éles környezetben az önaláírt tanúsítványok használatát SSL-lel vagy az internethez csatlakozó kiszolgálókon. <br> További információkért lásd: [SSL használatával a Microsoft SQL Server adatbázis-példány](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) vagy [oktatóanyag: A távoli asztali szolgáltatások SQL Server migrálása az Azure DMS használatával](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites). |
| **Hiba történt a 300** -felhasználó nem rendelkezik a szükséges engedélyekkel. VIEW SERVER STATE engedélyt nem kapta meg az objektum a(z) {server} nevű, "{adatbázis}" adatbázis | Ez a hiba akkor fordul elő, ha a felhasználó nem rendelkezik engedéllyel a migrálás elvégzéséhez. A probléma megoldása érdekében tekintse meg [kiszolgáló engedélyek megadása - a Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) vagy [oktatóanyag: A távoli asztali szolgáltatások SQL Server migrálása az Azure DMS használatával](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) további részletekért. |

> [!NOTE]
> A forrás az AWS RDS SQL-kiszolgáló csatlakozik kapcsolódó problémák hibaelhárításával kapcsolatos további információkért lásd a következőket:
>
> * [Az SQL Server kapcsolódási hibáinak megoldása](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [Hogyan oldhatom fel a problémába ütközik az Amazon RDS adatbázispéldány?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Ismert problémák

* [Ismert problémák és a migrálás korlátozások az online migrálást az Azure SQL Database](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Ismert problémák és a migrálás korlátozások az online migrálást az Azure Database MySQL-hez](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Ismert problémák és a migrálás az Azure Database for postgresql-hez online áttelepítések vonatkozó korlátozások](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>További lépések

* A cikk [Azure Database Migrálási szolgáltatás PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* A cikk [kiszolgáló paramétereinek konfigurálása az Azure Database for MySQL-hez az Azure portal segítségével hogyan](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* A cikk [Azure Database Migration Service használatára vonatkozó előfeltételek áttekintése](https://docs.microsoft.com/azure/dms/pre-reqs).
* Tekintse meg a [Azure Database Migration Service használatával kapcsolatos gyakori kérdések](https://docs.microsoft.com/azure/dms/faq).
