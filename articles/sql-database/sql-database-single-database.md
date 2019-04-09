---
title: Mi az, hogy egy Azure SQL Database önálló adatbázisok |} A Microsoft Docs
description: További tudnivalók az Azure SQL Database önálló adatbázis
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 034/08/2019
ms.openlocfilehash: 804e60b141f6ad4f089710d4b85c52a8dad33cfc
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59269520"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Mi az az Azure SQL Database egy adatbázis

Egyetlen adatbázis-telepítési beállítás saját erőforráskészlettel az Azure SQL Database adatbázist hoz létre, és a egy SQL Database-kiszolgálón keresztül felügyelt. Önálló adatbázis, az egyes adatbázisok el különítve a önálló, hordozható, és a saját szolgáltatási szinten belül a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) vagy [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) és a egy garantált a számítási méret.

> [!IMPORTANT]
> Önálló adatbázis az egyik Azure SQL Database három üzembe helyezési lehetőségeit. A másik kettőt vannak [rugalmas készletek](sql-database-elastic-pool.md) és [felügyelt példány](sql-database-managed-instance.md).
> [!NOTE]
> Egy Azure SQL Database-ben szószedet, lásd: [SQL-adatbázis használati szószedet](sql-database-glossary-terms.md)

## <a name="dynamic-scalability"></a>A dinamikus méretezhetőség

Létrehozhatja első alkalmazását egy egyedülálló, kisméretű adatbázis alacsony áron alacsony ár-teljesítmény szolgáltatási szinten a havonta, majd [módosíthatja a szolgáltatásszintet](sql-database-single-database-scale.md) manuálisan vagy programon keresztül bármikor thehigher ár-teljesítmény Service szint, hogy a megoldás igényeinek. Úgy módosíthatja a teljesítményt, hogy az nem jár leállással az alkalmazás vagy az ügyfelek számára. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre, és lehetővé teszi, hogy csak azokért az erőforrásokért fizessen, amelyekre és amikor szüksége van.

## <a name="single-databases-and-elastic-pools"></a>Önálló adatbázisok és rugalmas készletek

Önálló adatbázis is áthelyezhetők, be- vagy kívüli egy [rugalmas készlet](sql-database-elastic-pool.md) az erőforrás-megosztás. Számos vállalkozás és alkalmazás számára elegendő az, ha önálló adatbázisokat tudnak létrehozni, majd azok teljesítményét szükség szerint felfelé és lefelé tudják skálázni – különösen akkor, ha a használati minták viszonylag jól jelezhetők előre. Azonban előre nem látható használati minták esetén nehézségekbe ütközhet a költségek és az üzleti modell kezelése. Rugalmas készletek úgy tervezték, hogy a probléma megoldásához. A koncepció egyszerű. Az egyes adatbázisok helyett a készlet teljesítmény-erőforrásokat, és a készlet közös teljesítményéért erőforrások helyett egy önálló adatbázis teljesítményéért fizet.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás

Használja a beépített [alkalmazásteljesítmény-figyelési](sql-database-performance.md) és [riasztási eszközökben](sql-database-insights-alerts-portal.md), teljesítmény-értékeléssel kombinált. Ezek lehetővé teszik az aktuális vagy a projekthez kapcsolódó teljesítményigényeken alapuló fel- vagy leskálázás hatásainak gyors kiértékelését. Az SQL Database emellett [metrikák és diagnosztikai naplók kibocsátásával](sql-database-metrics-diag-logging.md) is képes megkönnyíteni a felügyeletet.

## <a name="availability-capabilities"></a>Rendelkezésre állás

Önálló adatbázisok, rugalmas készletek és a felügyelt példányok összes adja meg a rendelkezésre állási jellemzői. További információ: [rendelkezésre állási jellemzőinek](sql-database-technical-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>A Transact-SQL eltérései

A legtöbb Transact-SQL funkcióival, mellyel az alkalmazások teljes mértékben támogatja a Microsoft SQL Server és az Azure SQL Database. Például az SQL-alapösszetevők például adattípusok, a operátorok, a karakterlánc, a aritmetikai, logikai, és a kurzor funkciók, munka azonos SQL Server és SQL Database. Azonban néhány T-SQL különbségek vannak a DDL (data-definíciós nyelv) és a T-SQL-utasításokkal, és csak részlegesen támogatott lekérdezések DML (adatok adatkezelési language) elemek (Ez a cikk későbbi részében bemutatjuk).
Emellett vannak bizonyos funkciók és a szintaxis, amely nem támogatott egyáltalán, mivel a Azure SQL Database az elkülöníteni a funkciókat a főadatbázis és az operációs rendszer. Emiatt a legtöbb kiszolgálói szintű tevékenység nem illik az SQL Database. T-SQL-utasítások és beállítások nem érhető el, ha kiszolgálói szintű beállításokat vagy operációsrendszer-összetevők, konfigurálása, vagy adja meg a fájlrendszer-konfigurációt. Ilyen képességek szükségesek, ha megfelelő alternatív gyakran érhető el bármilyen egyéb módon az SQL Database-ből vagy más Azure funkciót vagy szolgáltatást.

További információkért lásd: [feloldása Transact-SQL különbségek az SQL Database áttelepítése során](sql-database-transact-sql-information.md).

## <a name="security"></a>Biztonság

Az SQL Database biztosít széles körű [beépített biztonsági és megfelelőségi](sql-database-security-overview.md) szolgáltatásokat, amelyek segítik a különféle biztonsági és megfelelőségi követelmények alkalmazását.

> [!IMPORTANT]
> Az Azure SQL Database (az összes központi telepítési beállítások), egy több megfelelőségi szabvány tanúsított módon. További információkért lásd: a [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/compliance/) ahol megtalálhatja a legfrissebb listáját az SQL Database megfelelőségi minősítései közül is bemutat.

## <a name="next-steps"></a>További lépések

- Való használatának gyors megkezdése egy önálló adatbázis, a [egyetlen adatbázis rövid guide.md](sql-database-single-database-quickstart-guide.md).
- Az SQL Server-adatbázis áttelepítése az Azure-bA kapcsolatos további információkért lásd: [áttelepítése az Azure SQL Database](sql-database-single-database-migrate.md).
- A támogatott funkciókkal kapcsolatos tudnivalókat lásd: [Funkciók](sql-database-features.md).
