---
title: Mi az egyetlen adatbázis?
description: Tudnivalók az azure-beli SQL-adatbázis egyetlen adatbázisáról
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500758"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Mi az egyetlen adatbázis az Azure SQL Database-ben?

Az egyetlen adatbázis-telepítési beállítás létrehoz egy adatbázist az Azure SQL Database-ben saját erőforrásokkészletével, és egy SQL Database-kiszolgálón keresztül kezeli. Egyetlen adatbázissal minden adatbázis el van különítve egymástól és hordozható, mindegyik saját szolgáltatási szinttel a [DTU-alapú vásárlási modellen](sql-database-service-tiers-dtu.md) vagy [virtuálismag-alapú vásárlási modellen](sql-database-service-tiers-vcore.md) belül, és garantált számítási méret.

> [!IMPORTANT]
> Egyetlen adatbázis az Azure SQL Database három telepítési lehetőségének egyike. A másik kettő [rugalmas készletek](sql-database-elastic-pool.md) és [felügyelt példány.](sql-database-managed-instance.md)
> [!NOTE]
> Az Azure SQL Database kifejezéseinek szószedetét az [SQL Database kifejezéseinek szószedete című témakörben található.](sql-database-glossary-terms.md)

## <a name="dynamic-scalability"></a>Dinamikus skálázhatóság

Az első alkalmazást egy kis méretű, egyetlen adatbázisra hozhatja létre alacsony költséggel a kiszolgáló nélküli számítási rétegben vagy egy kis számítási méretben a kiépített számítási szinten. A [számítási vagy szolgáltatási szintet](sql-database-single-database-scale.md) manuálisan vagy programozott módon bármikor módosíthatja a megoldás igényeinek megfelelően. Úgy módosíthatja a teljesítményt, hogy az nem jár leállással az alkalmazás vagy az ügyfelek számára. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre, és lehetővé teszi, hogy csak azokért az erőforrásokért fizessen, amelyekre és amikor szüksége van.

## <a name="single-databases-and-elastic-pools"></a>Önálló adatbázisok és rugalmas készletek

Egyetlen adatbázis áthelyezhető egy rugalmas [készletbe](sql-database-elastic-pool.md) erőforrás-megosztáshoz. Számos vállalkozás és alkalmazás számára elegendő az, ha önálló adatbázisokat tudnak létrehozni, majd azok teljesítményét szükség szerint felfelé és lefelé tudják skálázni – különösen akkor, ha a használati minták viszonylag jól jelezhetők előre. Azonban előre nem látható használati minták esetén nehézségekbe ütközhet a költségek és az üzleti modell kezelése. A rugalmas készletek megoldást jelentenek erre a problémára. A koncepció egyszerű. Teljesítményerőforrásokat egy készlethez rendel, nem pedig egy különálló adatbázishoz, és nem egyetlen adatbázis teljesítményéért, hanem a készlet kollektív teljesítményerőforrásaiért kell fizetnie.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás

A beépített [teljesítményfigyelő](sql-database-performance-guidance.md) és [riasztási eszközöket](sql-database-insights-alerts-portal.md)és a teljesítményminősítéseket kombinálva használhatja. Ezek lehetővé teszik az aktuális vagy a projekthez kapcsolódó teljesítményigényeken alapuló fel- vagy leskálázás hatásainak gyors kiértékelését. Emellett az SQL Database [metrikákat és erőforrásnaplókat is képes kikérni](sql-database-metrics-diag-logging.md) a könnyebb figyelés érdekében.

## <a name="availability-capabilities"></a>Rendelkezésre állás

Egyetlen adatbázisok, rugalmas készletek és felügyelt példányok mind számos rendelkezésre állási jellemzők. További információt a [Rendelkezésre állás jellemzői című témakörben talál.](sql-database-technical-overview.md#availability-capabilities)

## <a name="transact-sql-differences"></a>Transact-SQL különbségek

Az alkalmazások által használt Transact-SQL legtöbb szolgáltatás teljes mértékben támogatott a Microsoft SQL Server és az Azure SQL Database rendszerben. Az SQL alapvető összetevői, például az adattípusok, operátorok, karakterláncok, aritmetikai, logikai és kurzorfüggvények például azonos módon működnek az SQL Server és az SQL Database rendszerben. Van azonban néhány T-SQL különbség a DDL (adatdefiníciós nyelv) és a DML (adatmanipulációs nyelv) elemekben, amelyek csak részben támogatott T-SQL-utasításokat és lekérdezéseket eredményeznek (amelyeket a cikk későbbi részében tárgyalunk).
Emellett vannak olyan funkciók és szintaxis, amely egyáltalán nem támogatott, mert az Azure SQL Database úgy tervezték, hogy elkülönítse a funkciókat a fő adatbázis és az operációs rendszer függőségeitől. Mint ilyen, a legtöbb kiszolgálószintű tevékenység nem megfelelő az SQL Database számára. A T-SQL utasítások és beállítások nem érhetők el, ha kiszolgálószintű beállításokat, operációsrendszer-összetevőket vagy fájlrendszer-konfigurációt adnak meg. Ha ilyen képességekre van szükség, egy megfelelő alternatíva gyakran elérhető valamilyen más módon az SQL Database vagy egy másik Azure-szolgáltatás vagy szolgáltatás.

További információt a [Transact-SQL-különbségek feloldása az SQL Database-re való áttelepítés során](sql-database-transact-sql-information.md)című témakörben talál.

## <a name="security"></a>Biztonság

Az SQL Database [beépített biztonsági és megfelelőségi](sql-database-security-overview.md) funkciók széles skáláját kínálja, hogy az alkalmazás megfeleljen a különböző biztonsági és megfelelőségi követelményeknek.

> [!IMPORTANT]
> Az Azure SQL Database (az összes üzembe helyezési lehetőség) számos megfelelőségi szabványnak megfelelően lett hitelesítve. További információt a [Microsoft Azure Adatvédelmi központban](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) talál, ahol megtalálhatja az SQL Database megfelelőségi tanúsítványainak legfrissebb listáját.

## <a name="next-steps"></a>További lépések

- Egyetlen adatbázis gyors megkezdéséhez kezdje az [Egyetlen adatbázis rövid útmutatójával.](sql-database-single-database-quickstart-guide.md)
- Az SQL Server-adatbázisok Azure-ba való áttelepítéséről az [Áttelepítés az Azure SQL-adatbázisba](sql-database-single-database-migrate.md)című témakörben olvashat.
- A támogatott funkciókkal kapcsolatos tudnivalókat lásd: [Funkciók](sql-database-features.md).
