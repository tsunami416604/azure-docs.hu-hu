---
title: Az Azure SQL Database-alapú alkalmazásfejlesztés áttekintése | Microsoft Docs
description: Megismerheti az SQL Database-hez elérhető adatkapcsolattárakat és ajánlott eljárásokat az alkalmazások csatlakoztatásához.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 7473f89b711e804dbe96d299bc6f47adaceb6859
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55465214"
---
# <a name="sql-database-application-development-overview"></a>Az SQL Database-alapú alkalmazásfejlesztés – áttekintés

Ez a cikk ismerteti az alapvető szempontokat, amelyeket a fejlesztőknek érdemes figyelembe venniük az Azure SQL Database-hez való csatlakozáshoz használt kód írásakor.

> [!TIP]
> A kiszolgálók és a kiszolgálóalapú tűzfalak létrehozásáról, a kiszolgáló tulajdonságainak megtekintéséről, az SQL Server Management Studióval való csatlakozásról, a master adatbázis lekérdezéséről, a mintaadatbázis, illetve üres adatbázis létrehozásáról, az adatbázis tulajdonságainak lekérdezéséről, valamint az SQL Server Management Studióval való kapcsolódásról és a mintaadatbázis lekérdezéséről szóló oktatóanyagok az [Első lépéseket ismertető oktatóanyagban](sql-database-get-started-portal.md) találhatók.
>

## <a name="language-and-platform"></a>Nyelv és platform
Különböző programozási nyelvekhez és platformokhoz érhetők el kódminták. A kódmintákra mutató hivatkozások a következő helyen találhatók:

További információ: [Adatkapcsolattárak az SQL Database és SQL Server](sql-database-libraries.md).

## <a name="tools"></a>Eszközök

Kihasználhatja a nyílt forráskódú eszközökkel, mint például [cheetah](https://github.com/wunderlist/cheetah), [sql-cli](https://www.npmjs.com/package/sql-cli), [VS Code](https://code.visualstudio.com/). Ezen kívül az Azure SQL Database olyan Microsoft-eszközöket is támogat, mint például a [Visual Studio](https://www.visualstudio.com/downloads/) és az [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).  A hatékonyságot az Azure felügyeleti portál, a PowerShell és a REST API-k használatával növelheti tovább.

## <a name="resource-limitations"></a>Erőforrás-korlátozások

Az Azure SQL Database két különböző mechanizmussal adatbázis számára elérhető erőforrások kezelése: Erőforrás-szabályozással és a korlátok érvényesítése. További információkért lásd:

- [DTU-alapú modell erőforráskorlátok – önálló adatbázis](sql-database-dtu-resource-limits-single-databases.md)
- [DTU-alapú modell erőforráskorlátok – rugalmas készletek](sql-database-dtu-resource-limits-elastic-pools.md)
- [Virtuálismag-alapú erőforráskorlátok – önálló adatbázisok](sql-database-vcore-resource-limits-single-databases.md)
- [Virtuálismag-alapú erőforráskorlátok – rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md)

## <a name="security"></a>Biztonság

Az Azure SQL Database erőforrásokat biztosít a hozzáférés korlátozásához, az adatok védelméhez és a tevékenységek monitorozásához az SQL Database-adatbázisokban.

* További információ: [SQL-adatbázis védelme](sql-database-security-overview.md).

## <a name="authentication"></a>Hitelesítés

- Az Azure SQL Database az SQL Server-alapú és az [Azure Active Directory-alapú](sql-database-aad-authentication.md) hitelesítést használó felhasználókat és bejelentkezéseket is támogatja.
- Meg kell adnia egy konkrét adatbázist az alapértelmezett *master* adatbázis használata helyett.
- Az SQL Database-ben a **USE myDatabaseName;** Transact-SQL utasítással nem válthat másik adatbázisra.
- További információ: [Az SQL Database biztonsága: Adatbázis-hozzáférés és a bejelentkezési biztonság felügyelete](sql-database-manage-logins.md).

## <a name="resiliency"></a>Rugalmasság

Ha átmeneti hiba történik az SQL Database-hez való kapcsolódáskor, akkor a kódnak újra kell próbálkoznia a hívással.  Ajánlott, hogy az újrapróbálkozási logika leállítási logikát használjon, hogy több ügyfél egyidejű újrapróbálkozási kísérlete ne terhelje túl az SQL Database-t.

- Kódminták:  Kódmintákért az újrapróbálkozási logika, a nyelv, a választott mintákat: [Adatkapcsolattárak az SQL Database és SQL Server](sql-database-libraries.md).
- További információ: [Az SQL Database-ügyfélprogramok hibaüzenetei](sql-database-develop-error-messages.md).

## <a name="managing-connections"></a>Kapcsolatok kezelése

- Az ügyfél csatlakozási logikájában írja felül az alapértelmezett időtúllépési értéket 30 másodpercre.  A 15 másodperces időtúllépési érték túl rövid az internetkapcsolattól függő kapcsolatok számára.
- Ha [kapcsolatkészletet](https://msdn.microsoft.com/library/8xx3tyca.aspx) használ, azonnal bontsa a kapcsolatot, ha a program már nem használja aktívan, és nem is tervezi az ismételt használatát.

## <a name="network-considerations"></a>A hálózatokkal kapcsolatos szempontok

- Győződjön meg róla, hogy az ügyfélprogramot futtató számítógép tűzfala engedélyezi a kimenő TCP-kommunikációt az 1433-as porton.  További információ: [Az Azure SQL Database-tűzfalak konfigurálása](sql-database-configure-firewall-settings.md).
- Ha az ügyfélprogram kapcsolódik az SQL Database-beli virtuális gépen (VM) futtatott, meg kell nyitnia bizonyos portokat a virtuális gépen. További információ: [Az ADO.NET 4.5 és az SQL Database 1433-ason túli](sql-database-develop-direct-route-ports-adonet-v12.md).
- Az Azure SQL Database-ügyfélkapcsolatok néha a proxyt, és közvetlenül kommunikáljanak az adatbázis. Ekkor válnak fontossá az 1433-astól különböző portok. További információ [Azure SQL Database kapcsolati architektúra](sql-database-connectivity-architecture.md) és [az ADO.NET 4.5 és az SQL Database 1433-Ason túli](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="data-sharding-with-elastic-scale"></a>Adatok horizontális skálázása rugalmas méretezéssel

A rugalmas méretezéssel leegyszerűsíthető horizontális felskálázás (és leskálázás) folyamata. 

- [Tervezési minták több-bérlős SaaS-alkalmazásokhoz az Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).
- [Adatfüggő útválasztás](sql-database-elastic-scale-data-dependent-routing.md).
- [Ismerkedés az Azure SQL Database rugalmasan méretezhető előzetes verziójával](sql-database-elastic-scale-get-started.md).

## <a name="next-steps"></a>További lépések

Fedezze fel az [SQL Database összes képességét](sql-database-technical-overview.md).
