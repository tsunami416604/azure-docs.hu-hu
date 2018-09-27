---
title: Monitorozás és teljesítményhangolás – Azure SQL Database |} A Microsoft Docs
description: Tippek a teljesítmény-finomhangolási az Azure SQL Database révén értékelése és javítása.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 07/16/2018
ms.openlocfilehash: c79285247950510791ede915fcf0e5373792044f
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165041"
---
# <a name="monitoring-and-performance-tuning"></a>Figyelés és a teljesítmény hangolása

Az Azure SQL Database automatikusan kezeli, és a szolgáltatás rugalmas, ahol Ön is egyszerűen figyelheti, erőforrásainak hozzáadása vagy eltávolítása (CPU, memória, i/o), keresse meg a javaslatok, amelyek javítani az adatbázis teljesítményét, vagy lehetővé teszik az adatbázis alkalmazkodik számítási feladatához és automatikusan optimalizálhatja a teljesítményt.

## <a name="overview-of-monitoring-database-performance-in-azure-sql-database"></a>Az Azure SQL Database adatbázis teljesítményének figyelése áttekintése
Egy Azure SQL-adatbázis teljesítményének figyelése az erőforrás-használatnak a kiválasztott adatbázis teljesítményszintjéhez viszonyított figyelésével kezdődik. Azt határozza meg, hogy az adatbázis többletkapacitással rendelkezik, vagy nem tudja, mert az erőforrások vannak maximumot, és úgy dönt, hogy, ideje, módosítsa a számítási méretet és -szolgáltatások az adatbázis a monitorozási funkciók a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) vagy [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md). Nyomon követheti az adatbázis a [az Azure portal](https://portal.azure.com) a következő grafikus eszközök, vagy pedig SQL [dinamikus felügyeleti nézetekkel (DMV-kkel)](sql-database-monitoring-with-dmvs.md).

Az Azure SQL Database lehetővé teszi, azokat a lehetőségeket és a lekérdezési teljesítmény optimalizálása áttekintésével erőforrások módosítása nélkül [teljesítmény-finomhangolási ajánlásait](sql-database-advisor.md). A hiányzó indexek és rosszul optimalizált lekérdezések az adatbázis gyenge teljesítményének gyakori okai. Ezeket a számítási feladat teljesítményét a hangolási javaslatokat is alkalmazható.
Is tegye lehetővé, Azure SQL database [automatikus optimalizálása a lekérdezések teljesítményének](sql-database-automatic-tuning.md) alkalmazásával az összes azonosított javaslatok és adatbázis-teljesítmény javítása ellenőrzése. A megfigyelés és hibaelhárítás az adatbázis teljesítményét a következő lehetőségek állnak rendelkezésére:

- Az a [az Azure portal](https://portal.azure.com), kattintson a **SQL-adatbázisok**, és válassza ki az adatbázist, majd használja a figyelés diagramra a hamarosan eléri a maximális erőforrások kereséséhez. DTU-fogyasztásának alapértelmezés szerint jelenik meg. Kattintson a **szerkesztése** módosíthatja az időtartományt és látható értékeket.
- Használat [lekérdezési Terheléselemző](sql-database-query-performance.md) azonosítására, hogy a legtöbbet az erőforrások költségek lekérdezéseket.
- Használat [az SQL Database Advisor](sql-database-advisor-portal.md) létrehozása és az indexek elvetését, a lekérdezések paraméterezése és a séma problémáinak javítása javaslatok megtekintése.
- Használat [Azure SQL-Intelligent Insights](sql-database-intelligent-insights.md) automatikus ellenőrzésére, az adatbázis teljesítményét. Ha a teljesítménybeli problémát észlel, a részletek és a legfelső szintű okok elemzése (RCA) a probléma diagnosztikai napló jön létre. Ha lehetséges teljesítményének fokozása javaslat biztosítunk.
- [Automatikus hangolás engedélyezése](sql-database-automatic-tuning-enable.md) , és lehetővé teszik az Azure SQL database automatikusan javítja az azonosított teljesítménybeli problémák.
- Használhatja még [dinamikus felügyeleti nézetekkel (DMV-kkel)](sql-database-monitoring-with-dmvs.md), [kiterjesztett eseményeken (`XEvents`) (sql-database/sql-database-xevent-db-diff-from-svr.md), és a [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) teljesítmény valós idejű paraméterek. Lásd: [teljesítmény-útmutató](sql-database-performance-guidance.md) módszereket, amelyek segítségével az Azure SQL Database teljesítményének növelése, ha néhány problémát, ezek a jelentések vagy nézetek segítségével azonosítani tudja.

## <a name="monitor-databases-using-the-azure-portal"></a>Adatbázisok figyelése Azure Portal használatával
Az [Azure Portalon](https://portal.azure.com/) az adatbázis kiválasztásával és a **Figyelés** diagramra történő kattintással figyelheti egy önálló adatbázist erőforrás-használat. Ekkor megjelenik a **Metrika** ablak, amelyet a **Diagram szerkesztése** gombra kattintva módosíthat. Adja hozzá a következő metrikákat:

* Processzorhasználat (%)
* DTU-kihasználtság (%)
* Adat IO kihasználtsága (%)
* Adatbázis méretének kihasználtsága

Ezek a metrikák hozzáadása után továbbra is megtekintheti őket a a **figyelés** további információt a diagramon az **metrika** ablak. A négy metrika az átlagos kihasználtság százalékos arányát jeleníti meg az adatbázis **DTU-jához** viszonyítva. Tekintse meg a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) cikkekben további információt a szolgáltatási szintekről.  

![Adatbázis-teljesítményének szolgáltatásszint-figyelése.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

A metrikákhoz riasztásokat is lehet konfigurálni. Kattintson a **Riasztás hozzáadása** gombra a **Metrika** ablakban. A riasztás konfigurálásához kövesse a Varázslót. Lehetőség van riasztást kérni, ha a metrikák túllépnek egy bizonyos küszöböt, vagy egy bizonyos küszöb alá esnek.

Például ha az adatbázisban munkaterhelés-növekedésére számít, beállíthatja, hogy riasztást kapjon elektronikus üzenet formájában abban az esetben, ha az adatbázisra vonatkozó bármelyik metrika eléri a 80 százalékot. Segítségével ez korai figyelmeztetésként szolgálhat döntse el, ha lehetséges, hogy váltson át a következő nagyobb számítási méret.

A teljesítmény-mérőszámok is segítségével meghatározhatja, hogy ha Ön tudni számítási kisebb méretre. Tegyük fel, hogy Standard S2 adatbázist használ, és a metrikák azt mutatják, hogy az adatbázis átlagos kihasználtsága egy adott időpontban nem több, mint 10 százalék. Ebben az esetben valószínű, hogy az adatbázis Standard S1 teljesítményszinten is megfelelően fog működni. Vegye figyelembe az esetlegesen hirtelen, vagy ingadozó munkaterheléseket a kisebb számítási méretre elvégzése előtt.

## <a name="improving-database-performance-with-more-resources"></a>További erőforrások az adatbázis teljesítményének növelése

Végül ha nem találhatók gyakorlatban hasznosítható elemek, amelyek javíthatja az adatbázis teljesítményét, módosíthatja az Azure SQL Database-ben rendelkezésre álló erőforrások mennyiségét. Hozzárendelhet további erőforrások módosításával a [szolgáltatási szint dtu-k](sql-database-service-tiers-dtu.md) egy önálló adatbázis vagy növelje az egy rugalmas készlet edtu-k tetszőleges időpontban. Azt is megteheti Ha használja a [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md), módosíthatja a szolgáltatási rétegben, vagy növelje az adatbázis számára elkülönített erőforrások. 
1. Az önálló adatbázisokhoz is [szolgáltatásszintek módosítása](sql-database-service-tiers-dtu.md) vagy [számítási erőforrások](sql-database-service-tiers-vcore.md) igény szerinti adatbázis-teljesítmény javítása érdekében.
2. Több adatbázis esetén fontolja meg [rugalmas készletek](sql-database-elastic-pool-guidance.md) erőforrások automatikus méretezése.

## <a name="tune-and-refactor-application-or-database-code"></a>Hangolás és újrabontása alkalmazás vagy adatbázis-kód

Módosíthatja az alkalmazás kódjában, hogy több optimálisan az adatbázist, módosítsa az indexek, tervek kényszerített vagy mutatók segítségével manuálisan alkalmazkodik a számítási feladatok az adatbázis. Keresse meg az egyes tanácsokat és javaslatokat manuális finomhangolásához és a kód újraírását a [teljesítmény-útmutató témakör](sql-database-performance-guidance.md) cikk.


## <a name="next-steps"></a>További lépések

- Engedélyezze az Azure SQL Database automatikus finomhangolása és lehetővé teszik a számítási feladat teljes mértékben kezelése, automatikus finomhangolási funkció: [automatikus hangolás engedélyezése](sql-database-automatic-tuning-enable.md).
- Manuális hangolás használatához áttekintheti [finomhangolási javaslatai az Azure Portalon](sql-database-advisor-portal.md) , és manuálisan a alkalmazni azokat, amelyek javítják a lekérdezések teljesítményét.
- Módosítsa úgy módosítja az adatbázist a rendelkezésre álló erőforrások [Azure SQL Database-szolgáltatásszintek](sql-database-performance-guidance.md)
