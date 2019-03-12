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
ms.date: 02/07/2019
ms.openlocfilehash: efb6d932e616ada6b8dfff637af469c16fc2f293
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761821"
---
# <a name="sql-database-application-development-overview"></a>Az SQL Database-alapú alkalmazásfejlesztés – áttekintés

Ez a cikk ismerteti az alapvető szempontokat, amelyeket a fejlesztőknek érdemes figyelembe venniük az Azure SQL Database-hez való csatlakozáshoz használt kód írásakor. Ez a cikk az Azure SQL Database (önálló adatbázisok, rugalmas készletek és a felügyelt példánynak) minden üzembehelyezési modell vonatkozik.

> [!TIP]
> Tekintse meg az első lépések útmutatók [önálló adatbázisok](sql-database-single-database-quickstart-guide.md) és [felügyelt példányai](sql-database-managed-instance-quickstart-guide.md) Ha szeretné beállítani az Azure SQL Database.
>

## <a name="language-and-platform"></a>Nyelv és platform

Használhatja a különböző [programozási nyelvek és platformok](sql-database-connect-query.md) történő csatlakozásról és lekérdezésről Azure SQL Database. Annak [mintaalkalmazások](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) , amellyel csatlakozhat az Azure SQL Database.

Kihasználhatja a nyílt forráskódú eszközökkel, mint például [cheetah](https://github.com/wunderlist/cheetah), [sql-cli](https://www.npmjs.com/package/sql-cli), [VS Code](https://code.visualstudio.com/). Ezen kívül az Azure SQL Database olyan Microsoft-eszközöket is támogat, mint például a [Visual Studio](https://www.visualstudio.com/downloads/) és az [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). Használhatja az Azure portal, PowerShell, és a REST API-k segítségével hatékonyságot.

## <a name="authentication"></a>Authentication

Hozzáférés az Azure SQL Database-bejelentkezések és a tűzfal védi. Az Azure SQL Database támogatja mind az SQL Server és [Azure Active Directory (AAD) hitelesítés](sql-database-aad-authentication.md) felhasználók és bejelentkezések. Csak a felügyelt példány AAD bejelentkezések érhetők el. 

Tudjon meg többet [adatbázis-hozzáférés és a bejelentkezés kezelése](sql-database-manage-logins.md).

## <a name="connections"></a>Kapcsolatok

Az ügyfél csatlakozási logikájában írja felül az alapértelmezett időtúllépési értéket 30 másodpercre. A 15 másodperces időtúllépési érték túl rövid az internetkapcsolattól függő kapcsolatok számára.

Ha [kapcsolatkészletet](https://msdn.microsoft.com/library/8xx3tyca.aspx) használ, azonnal bontsa a kapcsolatot, ha a program már nem használja aktívan, és nem is tervezi az ismételt használatát.

Kerülje a hosszú ideig futó tranzakció, hiszen minden infrastruktúra vagy csatlakozási hiba előfordulhat, hogy állítsa vissza a tranzakciót. Ha lehetséges, a tranzakció több kisebb tranzakciók fel [teljesítmény javítása érdekében kötegelés](sql-database-use-batching-to-improve-performance.md).

## <a name="resiliency"></a>Rugalmasság

Azure SQL Database egy felhőalapú szolgáltatás, így várhatóan fordulhat elő átmeneti hibák az alapul szolgáló infrastruktúra vagy a felhőbeli entitás közötti kommunikációt. Bár a tranzitív infrastruktúra hibák a rugalmas Azure SQL Database, ezek a hibák befolyásolhatják a kapcsolatot. SQL-adatbázishoz való kapcsolódás során egy átmeneti hiba akkor fordul elő, ha a kódot kell [ismételje meg a hívás](sql-database-connectivity-issues.md). Ajánlott, hogy az újrapróbálkozási logika leállítási logikát használjon, hogy több ügyfél egyidejű újrapróbálkozási kísérlete ne terhelje túl az SQL Database-t. Újrapróbálkozási logika függ a [SQL Database-ügyfélprogramok hibaüzenetei](sql-database-develop-error-messages.md).

Hogyan lehet felkészülni a tervezett karbantartási események az Azure SQL Database kapcsolatos további információkért lásd: [tervezése az Azure SQL Database Azure karbantartási események](sql-database-planned-maintenance.md).

## <a name="network-considerations"></a>A hálózatokkal kapcsolatos szempontok

- Győződjön meg róla, hogy az ügyfélprogramot futtató számítógép tűzfala engedélyezi a kimenő TCP-kommunikációt az 1433-as porton.  További információ: [Az Azure SQL Database-tűzfalak konfigurálása](sql-database-configure-firewall-settings.md).
- Ha az ügyfélprogram kapcsolódik az SQL Database-beli virtuális gépen (VM) futtatott, meg kell nyitnia bizonyos portokat a virtuális gépen. További információ: [Az ADO.NET 4.5 és az SQL Database 1433-ason túli](sql-database-develop-direct-route-ports-adonet-v12.md).
- Az Azure SQL Database-ügyfélkapcsolatok néha a proxyt, és közvetlenül kommunikáljanak az adatbázis. Ekkor válnak fontossá az 1433-astól különböző portok. További információ [Azure SQL Database kapcsolati architektúra](sql-database-connectivity-architecture.md) és [az ADO.NET 4.5 és az SQL Database 1433-Ason túli](sql-database-develop-direct-route-ports-adonet-v12.md).
- Hálózati konfiguráció egy felügyelt példányra, lásd: [felügyelt példányok hálózati konfigurációja](sql-database-howto-managed-instance.md#network-configuration).

## <a name="next-steps"></a>További lépések

Fedezze fel az [SQL Database összes képességét](sql-database-technical-overview.md).
