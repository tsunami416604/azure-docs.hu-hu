---
title: "Az Azure SQL Database logikai kiszolgálók áttekintése | Microsoft Docs"
description: "Ez az oldal az Azure SQL logikai kiszolgálók használata során megfontolandó szempontokat és irányelveket tartalmaz."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: servers
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: 17e2830dceeaa313dd0fd7d406bf68a75b6f900e


---
# <a name="azure-sql-database-logical-servers"></a>Azure SQL Database logikai kiszolgálók

Ez a témakör az Azure SQL logikai kiszolgálók használata során megfontolandó szempontokat és irányelveket tartalmaz. Az Azure SQL Database-adatbázisokkal kapcsolatos tudnivalók: [SQL Database-adatbázisok](sql-database-overview.md).

## <a name="what-is-an-azure-sql-database-logical-server"></a>Mi az Azure SQL Database logikai kiszolgáló?
Az Azure SQL Database logikai kiszolgáló több adatbázis központi felügyeleti pontjaként működik. Az SQL Database-ben a kiszolgáló egy logikai szerkezet, amely nem azonos a helyszíni SQL Server-példánnyal. Az SQL Database szolgáltatás nem garantálja az adatbázisok helyét a logikai kiszolgálójukhoz képest, valamint nem kínál példányszintű hozzáférést és funkciókat. További információ az Azure SQL logikai kiszolgálókról: [Logikai kiszolgálók](sql-database-server-overview.md). 

Az Azure Database logikai kiszolgáló:

- Egy Azure-előfizetésen belül jön létre, de az erőforrásaival együtt áthelyezhető másik előfizetésre.
- Az adatbázisok, rugalmas készletek és adattárházak szülőerőforrása.
- Névteret biztosít az adatbázisok, rugalmas készletek és adattárházak számára.
- Erős élettartam-szemantikai képességekkel rendelkező logikai tároló – ha töröl egy kiszolgálót, a tárolt adatbázisok, rugalmas készletek és adattárházak is törlődnek.
- Részt vesz az Azure szerepköralapú hozzáférés-vezérlésében (RBAC); a kiszolgálóban lévő adatbázisok és rugalmas készletek a kiszolgálótól öröklik a hozzáférési jogosultságokat
- Az Azure erőforrás-kezelési céljaira használt adatbázisok és rugalmas készletek identitásának magas rendű eleme (lásd az adatbázisokhoz és készletekhez tartozó URL-sémát).
- Közösen helyezi el egy adott régió erőforrásait.
- Kapcsolódási végpontot biztosít az adatbázis-hozzáféréshez (<serverName>.database.windows.net)
- Egy master adatbázishoz való kapcsolódással hozzáférést biztosít a tárolt erőforrásokra vonatkozó metaadatokhoz a DMV-ken keresztül 
- Hatókört biztosít az adatbázisokra vonatkozó felügyeleti házirendhez: bejelentkezések, tűzfal, naplózás, fenyegetések észlelése stb. 
- A fő előfizetésben foglalt kvóta korlátozza (előfizetésenként hat kiszolgáló – [az előfizetések korlátai itt tekinthetők meg](../azure-subscription-service-limits.md))
- Hatókört biztosít a tárolt erőforrások adatbázis- és a DTU-kvótáinak (pl. 45000 DTU a 12-es verzióban).
- Hatókört biztosít a tárolt erőforrásokon engedélyezett képességek verziókezeléséhez (a legfrissebb verzió a 12-es).
- Kiszolgálószintű rendszerbiztonsági tagként bejelentkezve a kiszolgáló minden adatbázisa felügyelhető.
- Képes az olyan helyszíni SQL Server-példányokhoz hasonló bejelentkezések kezelésére, amelyek a kiszolgáló egy vagy több adatbázisához hozzáféréssel rendelkeznek, és korlátozott rendszergazdai jogosultságokkal ruházhatók fel. További információk: [Bejelentkezések](sql-database-manage-logins.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-sql-database-logical-server"></a>Hogyan lehet csatlakozni egy Azure SQL Database logikai kiszolgálóhoz, és milyen hitelesítés szükséges a csatlakozáshoz?

- **Hitelesítés és engedélyezés**: az Azure SQL Database az SQL- és az Azure Active Directory-hitelesítést támogatja (bizonyos korlátozásokkal, lásd: [Csatlakozás SQL Database-hez Azure Active Directory-hitelesítéssel](sql-database-aad-authentication.md)). Az Azure SQL Database-adatbázisokhoz a kiszolgáló master adatbázisán vagy közvetlenül egy felhasználói adatbázison keresztül lehet csatlakozni, és a hitelesítés is ezeken keresztül végezhető el. További információkért lásd az [Adatbázisok és bejelentkezések Azure SQL Database-ben történő kezelésével foglalkozó](sql-database-manage-logins.md) cikket. A Windows-hitelesítés nem támogatott. 
- **TDS**: A Microsoft Azure SQL Database a TDS protokoll 7.3-as és újabb ügyfélverzióit támogatja.
- **TCP/IP**: Kizárólag TCP/IP-kapcsolatok engedélyezettek.
- **SQL Database-tűzfal**: Az adatok védelme érdekében az SQL Database-tűzfal mindaddig megakadályozza az adatbázis-kiszolgáló vagy a hozzá tartozó adatbázisok elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. Lásd: [Tűzfalak](sql-database-firewall-configure.md)

## <a name="what-collations-are-supported"></a>Mely rendezések támogatottak?

A Microsoft Azure SQL Database (a master adatbázist is beleértve) alapértelmezett adatbázis-rendezése az **SQL_LATIN1_GENERAL_CP1_CI_AS**, amelyben a **LATIN1_GENERAL** az angol (Egyesült Államok), a **CP1** az 1252 kódlap, a **CI** a kis- és nagybetűk meg nem különböztetése, az **AS** pedig az ékezetérzékenység. A 12-es verziójú adatbázisok rendezésének módosítása nem ajánlott. További információk a rendezés beállításáról: [Rendezés (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## <a name="what-are-the-naming-requirements-for-database-objects"></a>Milyen névadási követelmények vonatkoznak az adatbázis-objektumokra?

Minden új objektum nevének meg kell felelnie az SQL Server azonosítókra vonatkozó szabályainak. További információk: [Azonosítók](https://msdn.microsoft.com/library/ms175874.aspx).

## <a name="what-features-are-supported"></a>Milyen funkciók támogatottak?

A támogatott funkciókkal kapcsolatos tudnivalókat lásd: [Funkciók](sql-database-features.md). További háttérinformációkért az adott típusú funkciók támogatása hiányának okairól lásd még az [Azure SQL Database és a Transact-SQL nyelv eltéréseit](sql-database-transact-sql-information.md) ismertető cikket.

## <a name="how-do-i-manage-a-logical-server"></a>Hogyan kezelhetem a logikai kiszolgálót?

Az Azure SQL Database logikai kiszolgálók többféle módon kezelhetők:
- [Azure Portal](sql-database-manage-portal.md)
- [PowerShell](sql-database-manage-powershell.md)
- [REST](/rest/api/sql/)

## <a name="next-steps"></a>Következő lépések

- Az Azure SQL Database szolgáltatással kapcsolatos tudnivalók: [Mi az SQL Database?](sql-database-technical-overview.md)
- A támogatott funkciókkal kapcsolatos tudnivalókat lásd: [Funkciók](sql-database-features.md).
- Az Azure SQL Database-adatbázisok áttekintését lásd: [Az SQL Database-adatbázisok áttekintése](sql-database-overview.md)
- A Transact-SQL támogatásával és eltéréseivel kapcsolatos tudnivalókat lásd: [Azure SQL Database Transact-SQL különbségek](sql-database-transact-sql-information.md)
- A pontos erőforráskvótákat és -korlátozásokat a **szolgáltatásszint** határozza meg. A szolgáltatásszintek áttekintését lásd: [SQL Database-szolgáltatásszintek](sql-database-service-tiers.md).
- A biztonsági szolgáltatások áttekintését lásd [az Azure SQL Database biztonsági szolgáltatásainak áttekintésével](sql-database-security-overview.md) foglalkozó cikkben.
- Az SQL Database illesztőinek rendelkezésre állásával és támogatásával kapcsolatos tudnivalókat lásd: [Adatkapcsolattárak az SQL Database-hez és az SQL Serverhez](sql-database-libraries.md).




<!--HONumber=Dec16_HO4-->


