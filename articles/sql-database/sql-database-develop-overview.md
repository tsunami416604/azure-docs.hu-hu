---
title: Alkalmazásfejlesztés – áttekintés
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067332"
---
# <a name="sql-database-application-development-overview"></a>SQL Database-alkalmazásfejlesztés – áttekintés

Ez a cikk ismerteti az alapvető szempontokat, amelyeket a fejlesztőknek érdemes figyelembe venniük az Azure SQL Database-hez való csatlakozáshoz használt kód írásakor. Ez a cikk az Azure SQL Database összes üzembe helyezési modelljére vonatkozik (egyetlen adatbázis, rugalmas készletek, felügyelt példány).

> [!TIP]
> Tekintse meg az első lépések útmutatók [egyetlen adatbázisok](sql-database-single-database-quickstart-guide.md) és [felügyelt példányok,](sql-database-managed-instance-quickstart-guide.md) ha be kell állítania az Azure SQL-adatbázis.
>

## <a name="language-and-platform"></a>Nyelv és platform

Különböző [programozási nyelvek és platformok](sql-database-connect-query.md) segítségével csatlakozhat és lekérdezheti az Azure SQL Database-t. Az Azure SQL-adatbázishoz való csatlakozáshoz használható [mintaalkalmazásokat](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) is megtalálhatja.

Tudod emelőerő nyit- forrás szerszámok szeret [gepárd](https://github.com/wunderlist/cheetah), [sql-cli](https://www.npmjs.com/package/sql-cli), [VS Kód.](https://code.visualstudio.com/) Ezen kívül az Azure SQL Database olyan Microsoft-eszközöket is támogat, mint például a [Visual Studio](https://www.visualstudio.com/downloads/) és az [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). Az Azure Portal, a PowerShell és a REST API-k is használhatják a további termelékenység et.

## <a name="authentication"></a>Hitelesítés

Az Azure SQL Database-hez való hozzáférést bejelentkezések és tűzfalak védik. Az Azure SQL Database támogatja az SQL Server és az [Azure Active Directory (AAD) hitelesítési](sql-database-aad-authentication.md) felhasználók és bejelentkezések. Az AAD-bejelentkezések csak a felügyelt példányban érhetők el. 

További információ az [adatbázis-hozzáférés és](sql-database-manage-logins.md)a bejelentkezés kezeléséről.

## <a name="connections"></a>Kapcsolatok

Az ügyfél csatlakozási logikájában írja felül az alapértelmezett időtúllépési értéket 30 másodpercre. A 15 másodperces időtúllépési érték túl rövid az internetkapcsolattól függő kapcsolatok számára.

Ha [kapcsolatkészletet](https://msdn.microsoft.com/library/8xx3tyca.aspx) használ, azonnal bontsa a kapcsolatot, ha a program már nem használja aktívan, és nem is tervezi az ismételt használatát.

Kerülje a hosszú ideig futó tranzakciókat, mert bármilyen infrastruktúra- vagy kapcsolathiba visszaállíthatja a tranzakciót. Ha lehetséges, ossza fel a tranzakciót a több kisebb tranzakcióra, és használja a [kötegelést a teljesítmény javítása érdekében.](sql-database-use-batching-to-improve-performance.md)

## <a name="resiliency"></a>Rugalmasság

Az Azure SQL Database egy felhőalapú szolgáltatás, ahol átmeneti hibákat várhat, amelyek az alapul szolgáló infrastruktúrában vagy a felhőentitások közötti kommunikációban fordulnak elő. Bár az Azure SQL Database rugalmas a tranzitív infrastruktúra hibák, ezek a hibák hatással lehetnek a kapcsolat. Ha az SQL Database-hez való csatlakozás közben átmeneti hiba történik, a kódnak újra meg kell [próbálnia a hívást.](sql-database-connectivity-issues.md) Ajánlott, hogy az újrapróbálkozási logika leállítási logikát használjon, hogy több ügyfél egyidejű újrapróbálkozási kísérlete ne terhelje túl az SQL Database-t. Az újrapróbálkozási logika az [SQL Database ügyfélprogramok hibaüzenetétől](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md)függ.

Az Azure SQL-adatbázistervezett karbantartási eseményeire való felkészülésről az [Azure-karbantartási események tervezése az Azure SQL Database-ben](sql-database-planned-maintenance.md)című témakörben talál további információt.

## <a name="network-considerations"></a>Hálózati szempontok

- Győződjön meg róla, hogy az ügyfélprogramot futtató számítógép tűzfala engedélyezi a kimenő TCP-kommunikációt az 1433-as porton.  További információ: [Konfiguráljon egy Azure SQL Database tűzfalat.](sql-database-configure-firewall-settings.md)
- Ha az ügyfélprogram csatlakozik az SQL-adatbázishoz, miközben az ügyfél egy Azure virtuális gépen (VM) fut, meg kell nyitnia bizonyos porttartományokat a virtuális gépen. További információ: [Portok túl 1433 ADO.NET 4.5 és az SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
- Az Azure SQL Database-kiszolgálóval létesített ügyfélkapcsolatok néha megkerülik a proxyt, és közvetlenül kommunikálnak az adatbázissal. Ekkor válnak fontossá az 1433-astól különböző portok. További információ: [Az Azure SQL Database kapcsolatarchitektúra](sql-database-connectivity-architecture.md) és [portok túl 1433 ADO.NET 4.5 és az SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
- Felügyelt példány hálózati konfigurációját lásd: [a felügyelt példányok hálózati konfigurációja.](sql-database-howto-managed-instance.md#network-configuration)

## <a name="next-steps"></a>További lépések

Fedezze fel az SQL Database összes [képességét.](sql-database-technical-overview.md)
