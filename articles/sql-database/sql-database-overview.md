---
title: "Az Azure SQL Database áttekintése | Microsoft Docs"
description: "Ez az oldal áttekintést nyújt az Azure SQL Database-adatbázisokról."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/28/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: 315fb49ba25c46afaa6571d9249ecd1c8da13e91


---
# <a name="azure-sql-database-overview"></a>Az Azure SQL Database áttekintése
Ez a témakör áttekintést nyújt az Azure SQL Database-adatbázisokról. Az Azure SQL logikai kiszolgálókkal kapcsolatos információkat a [logikai kiszolgálókkal](sql-database-server-overview.md) foglalkozó cikkben tekintheti meg.

## <a name="what-is-azure-sql-database"></a>Mi az Azure SQL Database?
Az Azure SQL Database mindegyik adatbázisa egy logikai kiszolgálóval van társítva. Az adatbázisok:

- lehetnek önálló, [saját erőforráskészlettel](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus) rendelkező adatbázisok (DTU);
- Részét képezhetik egy [rugalmas készletnek](sql-database-elastic-pool.md), amely [egy erőforráskészleten osztozik](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus) (eDTU-k);
- részét képezhetik [horizontálisan skálázott adatbázisok kibővíthető készletének](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling), amely önálló vagy készletezett adatbázisokból állhat;
- részét képezhetik egy [több-bérlős SaaS kialakítási mintában](sql-database-design-patterns-multi-tenancy-saas-applications.md) szereplő adatbáziskészletnek, mely adatbázisok lehetnek önálló, készletezett vagy mindkétféle adatbázisok. 

## <a name="how-do-i-connect-and-authenticate-to-an-azure-sql-database"></a>Hogyan lehet csatlakozni egy Azure SQL Database-adatbázishoz, és a csatlakozáshoz milyen hitelesítés szükséges?

- **Hitelesítés és engedélyezés**: az Azure SQL Database az SQL- és az Azure Active Directory-hitelesítést támogatja (bizonyos korlátozásokkal, lásd: [Csatlakozás SQL Database-hez Azure Active Directory-hitelesítéssel](sql-database-aad-authentication.md)). Az Azure SQL Database-adatbázisokhoz a kiszolgáló master adatbázisán vagy közvetlenül egy felhasználói adatbázison keresztül lehet csatlakozni, és a hitelesítés is ezeken keresztül végezhető el. További információkért lásd az [Adatbázisok és bejelentkezések Azure SQL Database-ben történő kezelésével foglalkozó](sql-database-manage-logins.md) cikket. A Windows-hitelesítés nem támogatott. 
- **TDS**: A Microsoft Azure SQL Database a TDS protokoll 7.3-as és újabb ügyfélverzióit támogatja.
- **TCP/IP**: Kizárólag TCP/IP-kapcsolatok engedélyezettek.
- **SQL Database-tűzfal**: Az adatok védelme érdekében az SQL Database-tűzfal mindaddig megakadályozza az adatbázis-kiszolgáló vagy a hozzá tartozó adatbázisok elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. Lásd: [Tűzfalak](sql-database-firewall-configure.md)

## <a name="what-collations-are-supported"></a>Mely rendezések támogatottak?
A Microsoft Azure SQL Database által alapértelmezés szerint használt adatbázisrendezés az **SQL_LATIN1_GENERAL_CP1_CI_AS**, amelyben a **LATIN1_GENERAL** az angol (Egyesült Államok), a **CP1** az 1252-es kódlap, a **CI** a kis- és nagybetűk meg nem különböztetése, az **AS** pedig az ékezetérzékenység. A V12-adatbázisok rendezésének módosítása nem lehetséges. További információk a rendezés beállításáról: [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## <a name="what-are-the-naming-requirements-for-database-objects"></a>Milyen névadási követelmények vonatkoznak az adatbázis-objektumokra?

Minden új objektum nevének meg kell felelnie az SQL Server azonosítókra vonatkozó szabályainak. További információk: [Azonosítók](https://msdn.microsoft.com/library/ms175874.aspx).

## <a name="what-features-are-supported-by-azure-sql-databases"></a>Milyen funkciókat támogatnak az Azure SQL Database-adatbázisok?

A támogatott funkciókkal kapcsolatos tudnivalókat lásd: [Funkciók](sql-database-features.md). További háttérinformációkért az adott típusú funkciók támogatása hiányának okairól lásd még az [Azure SQL Database és a Transact-SQL nyelv eltéréseit](sql-database-transact-sql-information.md) ismertető cikket.

## <a name="how-do-i-manage-an-azure-sql-database"></a>Hogyan kezelhetek egy Azure SQL Database-adatbázist?

Az Azure SQL Database logikai kiszolgálók többféle módon kezelhetők:
- [Azure Portal](sql-database-manage-portal.md)
- [PowerShell](sql-database-manage-powershell.md)
- [Transact-SQL](sql-database-manage-azure-ssms.md)
- [REST](/rest/api/sql/)

## <a name="next-steps"></a>Következő lépések

- Az Azure SQL Database szolgáltatással kapcsolatos tudnivalók: [Mi az SQL Database?](sql-database-technical-overview.md)
- A támogatott funkciókkal kapcsolatos tudnivalókat lásd: [Funkciók](sql-database-features.md).
- Az Azure SQL logikai kiszolgálók áttekintését lásd: [SQL Database logikai kiszolgálók áttekintése](sql-database-server-overview.md)
- A Transact-SQL támogatásával és eltéréseivel kapcsolatos tudnivalókat lásd: [Azure SQL Database Transact-SQL különbségek](sql-database-transact-sql-information.md)
- A pontos erőforráskvótákat és -korlátozásokat a **szolgáltatásszint** határozza meg. A szolgáltatásszintek áttekintését lásd: [SQL Database-szolgáltatásszintek](sql-database-service-tiers.md).
- A biztonsági szolgáltatások áttekintését lásd [az Azure SQL Database biztonsági szolgáltatásainak áttekintésével](sql-database-security-overview.md) foglalkozó cikkben.
- Az SQL Database illesztőinek rendelkezésre állásával és támogatásával kapcsolatos tudnivalókat lásd: [Adatkapcsolattárak az SQL Database-hez és az SQL Serverhez](sql-database-libraries.md).




<!--HONumber=Dec16_HO4-->


