---
title: Alkalmazások fejlesztésének áttekintése
description: Megismerheti az SQL Database-hez elérhető adatkapcsolattárakat és ajánlott eljárásokat az alkalmazások csatlakoztatásához.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 11/14/2019
ms.openlocfilehash: 741906bbe9de68459b2e4a704a243fde4771b3a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80067332"
---
# <a name="sql-database-application-development-overview"></a>SQL Database alkalmazás-fejlesztés áttekintése

Ez a cikk ismerteti az alapvető szempontokat, amelyeket a fejlesztőknek érdemes figyelembe venniük az Azure SQL Database-hez való csatlakozáshoz használt kód írásakor. Ez a cikk a Azure SQL Database összes üzembe helyezési modelljére vonatkozik (önálló adatbázis, rugalmas készlet, felügyelt példány).

> [!TIP]
> Ha be kell állítania a Azure SQL Database, tekintse meg az [önálló adatbázisok](sql-database-single-database-quickstart-guide.md) és a [felügyelt példányok](sql-database-managed-instance-quickstart-guide.md) első lépések útmutatóit.
>

## <a name="language-and-platform"></a>Nyelv és platform

A Azure SQL Database összekapcsolásához és lekérdezéséhez különböző [programozási nyelveket és platformokat](sql-database-connect-query.md) használhat. Megtalálhatja a Azure SQL Databasehoz való kapcsolódáshoz használható [minta alkalmazásokat](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) .

Használhatja a nyílt forráskódú eszközöket, például a [gepárd](https://github.com/wunderlist/cheetah), az [SQL-CLI](https://www.npmjs.com/package/sql-cli)és a [vs Code](https://code.visualstudio.com/)-ot. Ezen kívül az Azure SQL Database olyan Microsoft-eszközöket is támogat, mint például a [Visual Studio](https://www.visualstudio.com/downloads/) és az [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). A Azure Portal, a PowerShell és a REST API-k segítségével további hatékonyságot érhet el.

## <a name="authentication"></a>Hitelesítés

A Azure SQL Database hozzáférése a bejelentkezésekkel és a tűzfalakkal is védett. A Azure SQL Database SQL Server-és [Azure Active Directory-(HRE-) hitelesítési](sql-database-aad-authentication.md) felhasználókat és bejelentkezéseket is támogat. A HRE-bejelentkezések csak felügyelt példányban érhetők el. 

További információ az [adatbázis-hozzáférés és a bejelentkezés kezeléséről](sql-database-manage-logins.md).

## <a name="connections"></a>Kapcsolatok

Az ügyfél csatlakozási logikájában írja felül az alapértelmezett időtúllépési értéket 30 másodpercre. A 15 másodperces időtúllépési érték túl rövid az internetkapcsolattól függő kapcsolatok számára.

Ha [kapcsolatkészletet](https://msdn.microsoft.com/library/8xx3tyca.aspx) használ, azonnal bontsa a kapcsolatot, ha a program már nem használja aktívan, és nem is tervezi az ismételt használatát.

Kerülje a hosszan futó tranzakciók elkerülését, mert az infrastruktúra vagy a kapcsolatok meghibásodása is visszaállíthatja a tranzakciót. Ha lehetséges, Ossza szét a tranzakciót a több kisebb tranzakcióban, és a [teljesítmény növelése érdekében használjon batchs-t](sql-database-use-batching-to-improve-performance.md).

## <a name="resiliency"></a>Rugalmasság

Azure SQL Database egy felhőalapú szolgáltatás, amely az alapul szolgáló infrastruktúrában vagy a felhőalapú entitások közötti kommunikációban felmerülő átmeneti hibákat várhat. Bár a Azure SQL Database rugalmas a tranzitív infrastruktúra meghibásodása esetén, ezek a hibák hatással lehetnek a kapcsolatra. Ha átmeneti hiba történik a SQL Databasehoz való csatlakozáskor, a kódnak [újra kell próbálkoznia a hívással](sql-database-connectivity-issues.md). Ajánlott, hogy az újrapróbálkozási logika leállítási logikát használjon, hogy több ügyfél egyidejű újrapróbálkozási kísérlete ne terhelje túl az SQL Database-t. Az újrapróbálkozási logika a [SQL Database-ügyfélprogramok hibaüzenetei](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md)függ.

Az Azure SQL Database-ben tervezett karbantartási események előkészítésével kapcsolatos további információkért lásd: [Az Azure karbantartási eseményeinek tervezése Azure SQL Databaseban](sql-database-planned-maintenance.md).

## <a name="network-considerations"></a>Hálózati szempontok

- Győződjön meg róla, hogy az ügyfélprogramot futtató számítógép tűzfala engedélyezi a kimenő TCP-kommunikációt az 1433-as porton.  További információ: [Azure SQL Database tűzfal konfigurálása](sql-database-configure-firewall-settings.md).
- Ha az ügyfélprogram egy Azure-beli virtuális gépen (VM) fut, akkor a SQL Databasehoz kell csatlakoznia, és meg kell nyitnia bizonyos porttartomány-tartományokat a virtuális gépen. További információ: [a 1433-es, ADO.NET 4,5-es és SQL Database-es porton túli portok](sql-database-develop-direct-route-ports-adonet-v12.md).
- A Azure SQL Databasehoz való ügyfélkapcsolatok időnként megkerülik a proxyt, és közvetlenül az adatbázissal működnek. Ekkor válnak fontossá az 1433-astól különböző portok. További információért [Azure SQL Database kapcsolati architektúrát](sql-database-connectivity-architecture.md) és [portokat a 1433 ADO.NET 4,5 és SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
- Felügyelt példány hálózatkezelési konfigurációjának megtekintéséhez lásd: a [felügyelt példányok hálózati konfigurációja](sql-database-howto-managed-instance.md#network-configuration).

## <a name="next-steps"></a>További lépések

Fedezze fel [SQL Database összes funkcióját](sql-database-technical-overview.md).
