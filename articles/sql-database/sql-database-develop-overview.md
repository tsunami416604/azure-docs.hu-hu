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
ms.custom: development
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: 18dc3cce7451d90b6b65b990b80c05e7f6decb56


---
# <a name="sql-database-application-development-overview"></a>SQL Database-alapú alkalmazásfejlesztés – áttekintés
Ez a cikk ismerteti az alapvető szempontokat, amelyeket a fejlesztőknek érdemes figyelembe venniük az Azure SQL Database-hez való csatlakozáshoz használt kód írásakor.

> [!TIP]
> A kiszolgálók és a kiszolgálóalapú tűzfalak létrehozásáról, a kiszolgáló tulajdonságainak megtekintéséről, az SQL Server Management Studióval való csatlakozásról, a master adatbázis lekérdezéséről, a mintaadatbázis, illetve üres adatbázis létrehozásáról, az adatbázis tulajdonságainak lekérdezéséről, valamint az SQL Server Management Studióval való kapcsolódásról és a mintaadatbázis lekérdezéséről szóló oktatóanyagok az [Első lépéseket ismertető oktatóanyagban](sql-database-get-started.md) találhatók.
>

## <a name="language-and-platform"></a>Nyelv és platform
Különböző programozási nyelvekhez és platformokhoz érhetők el kódminták. A kódmintákra mutató hivatkozások a következő helyen találhatók: 

* További információ: [Adatkapcsolattárak az SQL Database-hez és az SQL Serverhez](sql-database-libraries.md)

## <a name="resource-limitations"></a>Erőforrás-korlátozások
Az Azure SQL Database két különböző mechanizmussal kezeli az adatbázis számára elérhető erőforrásokat: erőforrás-szabályozással és a korlátozások kényszerítésével.

* További információ: [Azure SQL Database erőforrás-korlátozások](sql-database-resource-limits.md)

## <a name="security"></a>Biztonság
Az Azure SQL Database erőforrásokat biztosít a hozzáférés korlátozásához, az adatok védelméhez és a tevékenységek monitorozásához az SQL Database-adatbázisokban.

* Bővebb információ: [Az SQL Database-adatbázis védelme](sql-database-security-overview.md)

## <a name="authentication"></a>Authentication
* Az Azure SQL Database az SQL Server-alapú és az [Azure Active Directory-alapú](sql-database-aad-authentication.md) hitelesítést használó felhasználókat és bejelentkezéseket is támogatja.
* Meg kell adnia egy konkrét adatbázist az alapértelmezett *master* adatbázis használata helyett.
* Az SQL Database-ben a **USE myDatabaseName;** Transact-SQL utasítással nem válthat másik adatbázisra.
* További információ: [Az SQL Database biztonsága: adatbázis-hozzáférés és a bejelentkezési biztonság felügyelete](sql-database-manage-logins.md)

## <a name="resiliency"></a>Resiliency
Ha átmeneti hiba történik az SQL Database-hez való kapcsolódáskor, akkor a kódnak újra kell próbálkoznia a hívással.  Ajánlott, hogy az újrapróbálkozási logika leállítási logikát használjon, hogy több ügyfél egyidejű újrapróbálkozási kísérlete ne terhelje túl az SQL Database-t.

* Kódminták: Az újrapróbálkozási logikát szemléltető kódmintákért tekintse meg a kívánt nyelv mintáit a következő cikkben: [Adatkapcsolattárak az SQL Database-hez és az SQL Serverhez](sql-database-libraries.md)
* További információ: [Az SQL Database-ügyfélprogramok hibaüzenetei](sql-database-develop-error-messages.md)

## <a name="managing-connections"></a>Kapcsolatok kezelése
* Az ügyfél csatlakozási logikájában írja felül az alapértelmezett időtúllépési értéket 30 másodpercre.  A 15 másodperces időtúllépési érték túl rövid az internetkapcsolattól függő kapcsolatok számára.
* Ha [kapcsolatkészletet](http://msdn.microsoft.com/library/8xx3tyca.aspx) használ, azonnal bontsa a kapcsolatot, ha a program már nem használja aktívan, és nem is tervezi az ismételt használatát.

## <a name="network-considerations"></a>Hálózati megfontolások
* Győződjön meg róla, hogy az ügyfélprogramot futtató számítógép tűzfala engedélyezi a kimenő TCP-kommunikációt az 1433-as porton.  További információk: [Az Azure SQL Database tűzfalának konfigurálása](sql-database-configure-firewall-settings.md)
* Ha Azure-beli virtuális gépen futtatott ügyfélprogram kapcsolódik az SQL Database 12-es verziójához, akkor meg kell nyitnia bizonyos portokat a virtuális gépen. További információ: [Az ADO.NET 4.5 és az SQL Database 12-es verziójának 1433-ason túli portjai](sql-database-develop-direct-route-ports-adonet-v12.md)
* Az Azure SQL Database 12-es verziójának ügyfélkapcsolatai időnként közvetlenül az adatbázissal lépnek kapcsolatba, és kihagyják a proxyt. Ekkor válnak fontossá az 1433-astól különböző portok. További információ: [Az ADO.NET 4.5 és az SQL Database 12-es verziójának 1433-ason túli portjai](sql-database-develop-direct-route-ports-adonet-v12.md)

## <a name="data-sharding-with-elastic-scale"></a>Adatok horizontális skálázása rugalmas méretezéssel
A rugalmas méretezéssel leegyszerűsíthető horizontális felskálázás (és leskálázás) folyamata. 

* [Tervezési minták az Azure SQL Database-t használó több-bérlős SaaS-alkalmazásokhoz](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Adatfüggő útválasztás](sql-database-elastic-scale-data-dependent-routing.md)
* [Ismerkedés az Azure SQL Database rugalmas méretezési funkciójának előzetes verziójával](sql-database-elastic-scale-get-started.md)

## <a name="next-steps"></a>Következő lépések
Fedezze fel az [SQL Database összes képességét](https://azure.microsoft.com/services/sql-database/).




<!--HONumber=Dec16_HO4-->


