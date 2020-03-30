---
title: Problémák a forrásadatbázisok csatlakoztatásával kapcsolatban
titleSuffix: Azure Database Migration Service
description: Megtudhatja, hogyan háríthatók el az Azure Database Migration Service forrásadatbázisokhoz való csatlakoztatásával kapcsolatos ismert problémák/hibák.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 3eef7476a729c7b83290b9d8d86ba06524bed72b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297103"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>A forrásadatbázisokhoz való csatlakozás során fellépő DMS-hibák elhárítása

Az alábbi cikk részletesen ismerteti, hogyan oldhatja meg az Azure Database Migration Service (DMS) forrásadatbázishoz való csatlakoztatásakor felmerülő lehetséges problémákat. Az alábbi szakaszok egy adott forrásadatbázis-típusra vonatkoznak, és felsorolják a részletes adatokkal és a kapcsolódás elhárításával kapcsolatos információkra mutató hivatkozásokkal együtt előforduló hibát.

## <a name="sql-server"></a>SQL Server

A forrás SQL Server adatbázishoz való csatlakozással és azok megoldásával kapcsolatos lehetséges problémákaz alábbi táblázatban találhatók.

| Hiba         | Ok és hibaelhárítás részletei |
| ------------- | ------------- |
| Az SQL-kapcsolat nem sikerült. Hálózattal kapcsolatos vagy példányspecifikus hiba történt az SQL Serverhez való kapcsolódás során. A kiszolgáló nem található vagy nem érhető el. Ellenőrizze, hogy a példányneve helyes-e, és hogy az SQL Server úgy van-e beállítva, hogy engedélyezze a távoli kapcsolatokat.<br> | Ez a hiba akkor fordul elő, ha a szolgáltatás nem találja a forráskiszolgálót. A probléma megoldásához olvassa el a Hiba a forrás SQL Serverhez való csatlakozás ról [dinamikus port vagy elnevezett példány használata esetén.](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance) |
| **53-as hiba** - az SQL-kapcsolat nem sikerült. (Továbbá az 1-es, 2-es, 5-ös, 53-as, 233-as, 258-as, 1225-ös, 11001-es hibakódok esetében)<br><br> | Ez a hiba akkor fordul elő, ha a szolgáltatás nem tud csatlakozni a forráskiszolgálóhoz. A probléma megoldásához olvassa el az alábbi forrásokat, majd próbálkozzon újra. <br><br>  [Interaktív felhasználói útmutató a kapcsolódási probléma elhárításához](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Az SQL Server Azure SQL Database rendszerbe való áttelepítésének előfeltételei](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [Az SQL Server Azure SQL Database felügyelt példányba való áttelepítésének előfeltételei](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **18456-os hiba** - A bejelentkezés nem sikerült.<br> | Ez a hiba akkor fordul elő, ha a szolgáltatás nem tud csatlakozni a forrásadatbázishoz a megadott T-SQL hitelesítő adatok használatával. A probléma megoldásához ellenőrizze a megadott hitelesítő adatokat. Az [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) vagy a táblázat ban felsorolt hibaelhárítási dokumentumokra is hivatkozhat, majd próbálkozzon újra. |
| Hibásan formázott{0}AccountName érték ' ' megadva. A Fióknév várt formátuma DomainName\Felhasználónév<br> | Ez a hiba akkor fordul elő, ha a felhasználó a Windows-hitelesítést választja, de a felhasználónevet érvénytelen formátumban adja meg. A probléma megoldásához adja meg a felhasználónevet a megfelelő formátumban a Windows-hitelesítéshez, vagy válassza az **SQL Authentication**lehetőséget. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

A forrásAWS RDS MySQL adatbázishoz való csatlakozással és azok megoldásával kapcsolatos lehetséges problémákat az alábbi táblázat tartalmazza.

| Hiba         | Ok és hibaelhárítás részletei |
| ------------- | ------------- |
| **Hiba [2003]**[HY000] - a kapcsolat nem sikerült. HIBA [HY000] [MySQL][ODBC x.x(w) illesztőprogram] Nem lehet csatlakozni a MySQL kiszolgálóhoz a következőn: '{server}' (10060) | Ez a hiba akkor fordul elő, ha a MySQL ODBC illesztőprogram nem tud csatlakozni a forráskiszolgálóhoz. A probléma megoldásához olvassa el az alábbi táblázatban felsorolt hibaelhárítási dokumentumokat, majd próbálkozzon újra.<br> |
| **Hiba [2005]**[HY000] - a kapcsolat nem sikerült. HIBA [HY000] [MySQL][ODBC x.x(w) illesztőprogram] Ismeretlen MySQL kiszolgálóállomás "{server}" | Ez a hiba akkor fordul elő, ha a szolgáltatás nem találja a forrásállomást az RDS-en. A probléma lehet, hogy a felsorolt forrás nem létezik, vagy probléma van az RDS-infrastruktúrával. A probléma megoldásához olvassa el az alábbi táblázatban felsorolt hibaelhárítási dokumentumokat, majd próbálkozzon újra.<br> |
| **Hiba[1045]**[HY000] - a kapcsolat nem sikerült. HIBA [HY000] [MySQL][ODBC x.x(w) illesztőprogram] A hozzáférés megtagadva a(z) "{user}'@'{server}" felhasználószámára (jelszóval: IGEN) | Ez a hiba akkor fordul elő, ha a MySQL ODBC illesztőprogram érvénytelen hitelesítő adatok miatt nem tud csatlakozni a forráskiszolgálóhoz. Ellenőrizze a megadott hitelesítő adatokat. Ha a probléma továbbra is fennáll, ellenőrizze, hogy a forrásszámítógép rendelkezik-e a megfelelő hitelesítő adatokkal. Előfordulhat, hogy alaphelyzetbe kell állítania a jelszót a konzolon. Ha továbbra is problémát tapasztal, olvassa el a táblázat alatti feljegyzésben felsorolt hibaelhárítási dokumentumokat, majd próbálkozzon újra.<br> |
| **Hiba[9002]**[HY000] - a kapcsolat nem sikerült. HIBA [HY000] [MySQL][ODBC x.x(w) illesztőprogram] Előfordulhat, hogy a kapcsolati karakterlánc nem megfelelő. Referenciákért látogasson el a portálra.| Ez a hiba akkor fordul elő, ha a kapcsolat a kapcsolati karakterláncmal kapcsolatos probléma miatt megszakad. Ellenőrizze, hogy a megadott kapcsolati karakterlánc érvényes-e. A probléma megoldásához olvassa el az alábbi táblázatban felsorolt hibaelhárítási dokumentumokat, majd próbálkozzon újra.<br> |
| **Hiba a bináris naplózásban. A változó binlog_format értéke "{value}". Kérjük, változtassa meg a "sor".** | Ez a hiba akkor fordul elő, ha hiba történt a bináris naplózásban; a változó binlog_format rossz értékkel rendelkezik. A probléma megoldásához módosítsa a paramétercsoport binlog_format "ROW" (ROW) formában lévő binlog_format, majd indítsa újra a példányt. További információt a [Bináris naplózási beállítások és változók](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) vagy [az AWS RDS MySQL adatbázisnaplófájlok dokumentációjában talál.](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)<br> |

> [!NOTE]
> A forrásAWS RDS MySQL-adatbázishoz való csatlakozással kapcsolatos hibák elhárításáról az alábbi forrásokban talál további információt:
> * [Az Amazon RDS-kapcsolattal kapcsolatos problémák elhárítása](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Hogyan oldhatom meg az Amazon RDS adatbázispéldányhoz való kapcsolódással kapcsolatos problémákat?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

A forráshoz való csatlakozással, az AWS RDS PostgreSQL adatbázissal kapcsolatos lehetséges problémákat az alábbi táblázat tartalmazza.

| Hiba         | Ok és hibaelhárítás részletei |
| ------------- | ------------- |
| **Hiba:[101]**[08001] - a kapcsolat nem sikerült. HIBA [08001] időeltetés lejárt. | Ez a hiba akkor fordul elő, ha a Postgres illesztőprogram nem tud csatlakozni a forráskiszolgálóhoz. A probléma megoldásához olvassa el az alábbi táblázatban felsorolt hibaelhárítási dokumentumokat, majd próbálkozzon újra. |
| **Hiba: A wal_level paraméter értéke "{value}". A replikáció engedélyezéséhez módosítsa "logikai" állapotra.** | Ez a hiba akkor fordul elő, ha a wal_level paraméter értéke nem megfelelő. A probléma megoldásához módosítsa az rds.logical_replication paramétercsoportban 1-re, majd indítsa újra a példányt. További információ: [Elő-előfeltételek az Azure PostgreSQL-re való áttéréshez a DMS vagy](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites) [a PostgreSQL használatával az Amazon RDS-en.](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html) |

> [!NOTE]
> A forrásAWS RDS PostgreSQL adatbázishoz való csatlakozással kapcsolatos problémák elhárításáról az alábbi forrásokban talál további információt:
> * [Az Amazon RDS-kapcsolattal kapcsolatos problémák elhárítása](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Hogyan oldhatom meg az Amazon RDS adatbázispéldányhoz való kapcsolódással kapcsolatos problémákat?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

A forrásAWS RDS SQL Server adatbázishoz való csatlakozással és azok megoldásával kapcsolatos lehetséges problémákaz alábbi táblázatban találhatók.

| Hiba         | Ok és hibaelhárítás részletei |
| ------------- | ------------- |
| **53-as hiba** - az SQL-kapcsolat nem sikerült. Hálózattal kapcsolatos vagy példányspecifikus hiba történt az SQL Serverhez való kapcsolódás során. A kiszolgáló nem található, vagy nem volt elérhető. Ellenőrizze, hogy a példányneve helyes-e, és hogy az SQL Server úgy van-e beállítva, hogy engedélyezze a távoli kapcsolatokat. (szolgáltató: Named Pipes Provider, hiba: 40 - Nem lehet kapcsolatot nyitni az SQL Server kiszolgálóval | Ez a hiba akkor fordul elő, ha a szolgáltatás nem tud csatlakozni a forráskiszolgálóhoz. A probléma megoldásához olvassa el az alábbi táblázatban felsorolt hibaelhárítási dokumentumokat, majd próbálkozzon újra. |
| **18456-os hiba** - A bejelentkezés nem sikerült. A(z) "{user}" felhasználó bejelentkezése nem sikerült | Ez a hiba akkor fordul elő, ha a szolgáltatás nem tud csatlakozni a forrásadatbázishoz a megadott T-SQL hitelesítő adatokkal. A probléma megoldásához ellenőrizze a megadott hitelesítő adatokat. Az alábbi táblázatban szereplő [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) vagy hibaelhárítási dokumentumokra is hivatkozhat, majd próbálkozzon újra. |
| **87-es hiba** - A kapcsolati karakterlánc érvénytelen. Hálózattal kapcsolatos vagy példányspecifikus hiba történt az SQL Serverhez való kapcsolódás során. A kiszolgáló nem található vagy nem érhető el. Ellenőrizze, hogy a példányneve helyes-e, és hogy az SQL Server úgy van-e beállítva, hogy engedélyezze a távoli kapcsolatokat. (szolgáltató: SQL hálózati adapterek, hiba: 25 - A kapcsolati karakterlánc érvénytelen) | Ez a hiba akkor fordul elő, ha a szolgáltatás érvénytelen kapcsolati karakterlánc miatt nem tud csatlakozni a forráskiszolgálóhoz. A probléma megoldásához ellenőrizze a megadott kapcsolati karakterláncot. Ha a probléma továbbra is fennáll, olvassa el az alábbi táblázatban felsorolt hibaelhárítási dokumentumokat, majd próbálkozzon újra. |
| **Hiba - A kiszolgálótanúsítvány nem megbízható.** A kapcsolat sikeresen létrejött a kiszolgálóval, de a bejelentkezési folyamat során hiba történt. (szolgáltató: SSL-szolgáltató, hiba: 0 - A tanúsítványláncot nem megbízható hatóság bocsátotta ki.) | Ez a hiba akkor fordul elő, ha a használt tanúsítvány nem megbízható. A probléma megoldásához meg kell találnia egy megbízható tanúsítványt, majd engedélyeznie kell azt a kiszolgálón. Másik lehetőségként a megbízhatósági tanúsítvány beállítást is választhatja a csatlakozás közben. Csak akkor tegye ezt a műveletet, ha ismeri a használt tanúsítványt, és megbízik benne. <br> Az önaláírt tanúsítvánnyal titkosított TLS-kapcsolatok nem nyújtanak erős biztonságot, de ki vannak téve a köztes támadásoknak. Ne hagyatkozzon a TLS-re, amely önaláírt tanúsítványokat használ éles környezetben vagy az internethez kapcsolódó kiszolgálókon. <br> További információt az [SSL használata Microsoft SQL Server DB instance-mal](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) vagy [oktatóanyaggal: Az RDS SQL Server áttelepítése az Azure-ba a DMS használatával](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites)című témakörben talál. |
| **300-as hiba** – A felhasználó nem rendelkezik a szükséges engedélyekkel. VIEW SERVER STATE engedély megtagadva a(z) "{server}" objektumon, a(z) "{database}" adatbázison. | Ez a hiba akkor fordul elő, ha a felhasználónak nincs engedélye az áttelepítés végrehajtására. A probléma megoldásához további részletekért tekintse meg a [GRANT Server Permissions – Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) vagy [oktatóanyag: Az RDS SQL Server áttelepítése az Azure-ba](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) a DMS használatával című dokumentumban. |

> [!NOTE]
> Az AWS RDS SQL Server forráshoz való csatlakozással kapcsolatos problémák elhárításáról az alábbi forrásokban talál további információt:
>
> * [Az SQL Server kapcsolódási hibáinak megoldása](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [Hogyan oldhatom meg az Amazon RDS adatbázispéldányhoz való kapcsolódással kapcsolatos problémákat?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Ismert problémák

* [Az Azure SQL Database-be való online áttelepítéssel kapcsolatos ismert problémák/áttelepítési korlátozások](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Ismert problémák/áttelepítési korlátozások az online áttelepítéssel kapcsolatban a MySQL Azure-adatbázisába](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Ismert problémák/áttelepítési korlátozások az online áttelepítéssel kapcsolatban a PostgreSQL Azure Database szolgáltatásba](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>További lépések

* Tekintse meg a cikket [az Azure Database Migration Service PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Tekintse meg a [kiszolgálóparamétereinek konfigurálása az Azure Database for MySQL-ben az Azure Portal használatával című cikket.](https://docs.microsoft.com/azure/mysql/howto-server-parameters)
* Tekintse meg a cikk [az Azure Database Migration Service használatának előfeltételeit](https://docs.microsoft.com/azure/dms/pre-reqs).
* Tekintse meg az [Azure Database Migration Service használatával kapcsolatos gyakori kérdéseket.](https://docs.microsoft.com/azure/dms/faq)
