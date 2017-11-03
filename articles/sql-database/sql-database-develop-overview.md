---
title: "Az Azure SQL Database-alapú alkalmazásfejlesztés áttekintése | Microsoft Docs"
description: "Megismerheti az SQL Database-hez elérhető adatkapcsolattárakat és ajánlott eljárásokat az alkalmazások csatlakoztatásához."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: genemi
ms.assetid: 67c02204-d1bd-4622-acce-92115a7cde03
ms.service: sql-database
ms.custom: develop apps
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: sstein
ms.openlocfilehash: 5948db9a52dc24d75f3fecc4ed166dd327061b37
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="sql-database-application-development-overview"></a>SQL-adatbázis alkalmazások fejlesztői áttekintés
Ez a cikk ismerteti az alapvető szempontokat, amelyeket a fejlesztőknek érdemes figyelembe venniük az Azure SQL Database-hez való csatlakozáshoz használt kód írásakor.

> [!TIP]
> A kiszolgálók és a kiszolgálóalapú tűzfalak létrehozásáról, a kiszolgáló tulajdonságainak megtekintéséről, az SQL Server Management Studióval való csatlakozásról, a master adatbázis lekérdezéséről, a mintaadatbázis, illetve üres adatbázis létrehozásáról, az adatbázis tulajdonságainak lekérdezéséről, valamint az SQL Server Management Studióval való kapcsolódásról és a mintaadatbázis lekérdezéséről szóló oktatóanyagok az [Első lépéseket ismertető oktatóanyagban](sql-database-get-started-portal.md) találhatók.
>

## <a name="language-and-platform"></a>Nyelv és platform
Különböző programozási nyelvekhez és platformokhoz érhetők el kódminták. A kódmintákra mutató hivatkozások a következő helyen találhatók: 

* További információ: [adatkapcsolattárak SQL Database és SQL Server](sql-database-libraries.md).

## <a name="tools"></a>Eszközök 
Használhat olyan nyílt forráskódú eszközöket is, mint például a [Cheetah](https://github.com/wunderlist/cheetah), az [sql-cli](https://www.npmjs.com/package/sql-cli) és a [VS Code](https://code.visualstudio.com/). Ezen kívül az Azure SQL Database olyan Microsoft-eszközöket is támogat, mint például a [Visual Studio](https://www.visualstudio.com/downloads/) és az [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).  A hatékonyságot az Azure felügyeleti portál, a PowerShell és a REST API-k használatával növelheti tovább.

## <a name="resource-limitations"></a>Erőforrás-korlátozások
Az Azure SQL Database két különböző mechanizmussal kezeli az adatbázis számára elérhető erőforrásokat: erőforrás-szabályozással és a korlátozások kényszerítésével.

* További információ: [erőforrás-korlátozások az Azure SQL Database](sql-database-service-tiers.md).

## <a name="security"></a>Biztonság
Az Azure SQL Database erőforrásokat biztosít a hozzáférés korlátozásához, az adatok védelméhez és a tevékenységek monitorozásához az SQL Database-adatbázisokban.

* További információ: [SQL-adatbázisok védelme](sql-database-security-overview.md).

## <a name="authentication"></a>Authentication
* Az Azure SQL Database az SQL Server-alapú és az [Azure Active Directory-alapú](sql-database-aad-authentication.md) hitelesítést használó felhasználókat és bejelentkezéseket is támogatja.
* Meg kell adnia egy konkrét adatbázist az alapértelmezett *master* adatbázis használata helyett.
* Az SQL Database-ben a **USE myDatabaseName;** Transact-SQL utasítással nem válthat másik adatbázisra.
* További információ: [SQL-adatbázis biztonsági: adatbázis-hozzáférési és bejelentkezési biztonság kezeléséhez](sql-database-manage-logins.md).

## <a name="resiliency"></a>Resiliency
Ha átmeneti hiba történik az SQL Database-hez való kapcsolódáskor, akkor a kódnak újra kell próbálkoznia a hívással.  Ajánlott, hogy az újrapróbálkozási logika leállítási logikát használjon, hogy több ügyfél egyidejű újrapróbálkozási kísérlete ne terhelje túl az SQL Database-t.

* Kódminták: mintakódok, mely újrapróbálkozási logika, lásd: a következő nyelvű-példák: [adatkapcsolattárak SQL Database és SQL Server](sql-database-libraries.md).
* További információ: [SQL Database-ügyfélprogramok hibaüzenetei](sql-database-develop-error-messages.md).

## <a name="managing-connections"></a>Kapcsolatok kezelése
* Az ügyfél csatlakozási logikájában írja felül az alapértelmezett időtúllépési értéket 30 másodpercre.  A 15 másodperces időtúllépési érték túl rövid az internetkapcsolattól függő kapcsolatok számára.
* Ha [kapcsolatkészletet](http://msdn.microsoft.com/library/8xx3tyca.aspx) használ, azonnal bontsa a kapcsolatot, ha a program már nem használja aktívan, és nem is tervezi az ismételt használatát.

## <a name="network-considerations"></a>Hálózati kapcsolatos szempontok
* Győződjön meg róla, hogy az ügyfélprogramot futtató számítógép tűzfala engedélyezi a kimenő TCP-kommunikációt az 1433-as porton.  További információ: [konfigurálása az Azure SQL Database-tűzfal](sql-database-configure-firewall-settings.md).
* Ha az ügyfélprogram SQL-adatbázis csatlakozik, amíg az ügyfél egy Azure virtuális gépen (VM) fut, nyissa meg a virtuális gép bizonyos porttartományok. További információ: [kívüli ADO.NET 4.5 és az SQL-adatbázis 1433-as portokon](sql-database-develop-direct-route-ports-adonet-v12.md).
* Az Azure SQL Database-ügyfélkapcsolatok néha megkerülje a proxyt, és az adatbázis közvetlenül kommunikál. Ekkor válnak fontossá az 1433-astól különböző portok. További információ [Azure SQL adatbázis-kapcsolat architektúra](sql-database-connectivity-architecture.md) és [kívüli ADO.NET 4.5 és az SQL-adatbázis 1433-as portokon](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="data-sharding-with-elastic-scale"></a>A rugalmas bővítést adatok horizontális
Rugalmasan méretezhető egyszerűbbé teszi a Méretezés (és). 

* [A több-bérlős SaaS-alkalmazásokhoz az Azure SQL-adatbázis a kialakítási minták](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* [Adatok függő útválasztási](sql-database-elastic-scale-data-dependent-routing.md).
* [Ismerkedés az Azure SQL Database rugalmas bővítést előzetes](sql-database-elastic-scale-get-started.md).

## <a name="next-steps"></a>Következő lépések
Fedezze fel az [SQL Database összes képességét](sql-database-technical-overview.md).
