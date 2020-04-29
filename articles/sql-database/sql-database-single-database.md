---
title: Mi az egyetlen adatbázis?
description: További tudnivalók a Azure SQL Database önálló adatbázisairól
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/08/2019
ms.openlocfilehash: fc63de4057def632d3ac1980e8cb3eaedbff2175
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79500758"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Mi az a Azure SQL Database önálló adatbázisa?

Az önálló adatbázis-telepítési lehetőség létrehoz egy adatbázist a Azure SQL Database saját erőforrásaival, és egy SQL Database-kiszolgálón keresztül felügyeli. Egyetlen adatbázis esetében minden adatbázis el van különítve egymástól és hordozható számítógépektől, amelyek mindegyike saját szolgáltatási réteggel rendelkezik a [DTU-alapú vásárlási modellben](sql-database-service-tiers-dtu.md) vagy a [virtuális mag-alapú vásárlási modellben](sql-database-service-tiers-vcore.md) , valamint egy garantált számítási méretet.

> [!IMPORTANT]
> Az önálló adatbázisok a Azure SQL Database három központi telepítési lehetőségének egyike. A másik kettő a [rugalmas készletek](sql-database-elastic-pool.md) és a [felügyelt példány](sql-database-managed-instance.md).
> [!NOTE]
> A Azure SQL Database használati feltételeinek szószedetét lásd: [SQL Database kifejezések szószedete](sql-database-glossary-terms.md)

## <a name="dynamic-scalability"></a>Dinamikus skálázhatóság

Az első alkalmazást egy kis méretű, különálló adatbázison is létrehozhatja, alacsony áron, a kiszolgáló nélküli számítási rétegben, vagy a kiépített számítási szinten kis számítási mérettel. A [számítási vagy szolgáltatási szintet](sql-database-single-database-scale.md) manuálisan vagy programozottan változtathatja meg a megoldás igényeinek megfelelően. Úgy módosíthatja a teljesítményt, hogy az nem jár leállással az alkalmazás vagy az ügyfelek számára. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre, és lehetővé teszi, hogy csak azokért az erőforrásokért fizessen, amelyekre és amikor szüksége van.

## <a name="single-databases-and-elastic-pools"></a>Önálló adatbázisok és rugalmas készletek

Az erőforrás-megosztáshoz egyetlen adatbázis helyezhető be vagy ki egy [rugalmas készletbe](sql-database-elastic-pool.md) . Számos vállalkozás és alkalmazás számára elegendő az, ha önálló adatbázisokat tudnak létrehozni, majd azok teljesítményét szükség szerint felfelé és lefelé tudják skálázni – különösen akkor, ha a használati minták viszonylag jól jelezhetők előre. Azonban előre nem látható használati minták esetén nehézségekbe ütközhet a költségek és az üzleti modell kezelése. A rugalmas készletek megoldást jelentenek erre a problémára. A koncepció egyszerű. A teljesítményadatokat egy készlethez kell hozzárendelni, és nem egy önálló adatbázishoz, hanem a készlet kollektív teljesítmény-erőforrásaiért kell fizetnie, nem pedig egyetlen adatbázis teljesítményére.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás

A beépített [Teljesítményfigyelő](sql-database-performance-guidance.md) és [riasztási eszközöket](sql-database-insights-alerts-portal.md)a teljesítmény-minősítésekkel együtt használva használhatja. Ezek lehetővé teszik az aktuális vagy a projekthez kapcsolódó teljesítményigényeken alapuló fel- vagy leskálázás hatásainak gyors kiértékelését. Emellett a SQL Database a könnyebb monitorozás érdekében [mérőszámokat és erőforrás-naplókat is képes kibocsátani](sql-database-metrics-diag-logging.md) .

## <a name="availability-capabilities"></a>Rendelkezésre állás

Az önálló adatbázisok, a rugalmas készletek és a felügyelt példányok számos rendelkezésre állási jellemzőt biztosítanak. További információ: [rendelkezésre állási jellemzők](sql-database-technical-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>Transact-SQL-különbségek

Az alkalmazások által használt legtöbb Transact-SQL-szolgáltatás teljes mértékben támogatott Microsoft SQL Server és Azure SQL Database egyaránt. Például az alapvető SQL-összetevők, például az adattípusok, a operátorok, a karakterláncok, a aritmetikai, a logikai és a kurzor függvények azonos módon működnek SQL Server és SQL Database. Van azonban néhány, a DDL (adatdefiníciós nyelv) és a DML (adatmanipulációs nyelv) elemeinek néhány T-SQL-eltérése, ami csak részben támogatott T-SQL-utasításokat és-lekérdezéseket eredményez (amit a cikk későbbi részében tárgyalunk).
Emellett vannak olyan szolgáltatások és szintaxisok is, amelyek egyáltalán nem támogatottak, mert Azure SQL Database úgy lett kialakítva, hogy elkülönítse a szolgáltatásokat a főadatbázis és az operációs rendszer függőségeitől. Ennek megfelelően a legtöbb kiszolgálói szintű tevékenység nem megfelelő a SQL Database számára. A T-SQL-utasítások és-beállítások nem érhetők el, ha a kiszolgálói szintű beállításokat, az operációs rendszer összetevőit vagy a fájlrendszer konfigurációját konfigurálja. Ha ilyen képességekre van szükség, a megfelelő alternatíva gyakran elérhető más módon, SQL Database vagy egy másik Azure-szolgáltatásból vagy-szolgáltatásból.

További információ: [Transact-SQL különbségek feloldása az áttelepítés során SQL Databasera](sql-database-transact-sql-information.md).

## <a name="security"></a>Biztonság

SQL Database számos [beépített biztonsági és megfelelőségi](sql-database-security-overview.md) funkciót kínál, amelyek segítségével az alkalmazás megfelel a különböző biztonsági és megfelelőségi követelményeknek.

> [!IMPORTANT]
> Azure SQL Database (az összes központi telepítési lehetőség), számos megfelelőségi szabványnak megfelelő minősítéssel rendelkezik. További információkért tekintse meg a [Microsoft Azure adatvédelmi központot](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , ahol megtalálhatja a SQL Database megfelelőségi minősítések legfrissebb listáját.

## <a name="next-steps"></a>További lépések

- Egyetlen adatbázis gyors megkezdéséhez kezdje az [egyetlen adatbázis rövid útmutatóját](sql-database-single-database-quickstart-guide.md).
- A SQL Server-adatbázisok Azure-ba történő áttelepítésével kapcsolatos információkért lásd: [áttelepítés Azure SQL Databasere](sql-database-single-database-migrate.md).
- A támogatott funkciókkal kapcsolatos tudnivalókat lásd: [Funkciók](sql-database-features.md).
