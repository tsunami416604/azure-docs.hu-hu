---
title: Az Azure SQL Database erőforrás korlátozza áttekintése |} Microsoft Docs
description: Ez a lap néhány gyakori DTU-alapú erőforrás korlátot, az önálló adatbázisok Azure SQL Database ismerteti.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 6806b0c5b5e5ac5e1189f628786f0c8f9b223395
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750951"
---
# <a name="overview-azure-sql-database-resource-limits"></a>Áttekintés az Azure SQL Database erőforrás korlátok 

Ez a cikk ismerteti az Azure SQL Database erőforrás áttekintést korlátozza, és mi történik, ha az erőforrás e elérte vagy meghaladta vonatkozóan nyújt információkat.

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>Mi az, hogy a kiszolgálók és adatbázisok maximális számát?

| Maximum | Érték |
| :--- | :--- |
| Adatbázis kiszolgálónként | 5000 |
| Bármely régióban előfizetésenként kiszolgálók alapértelmezett száma | 20 |
| Bármely régióban előfizetésenként kiszolgálók maximális száma | 200 |
|||

> [!NOTE]
> Az alapértelmezett értéknél több kiszolgáló kvótáját megszerzéséhez új támogatási kérelem a "Kvóta" probléma típusú az előfizetéshez tartozó Azure-portálon küldheti el.

> [!IMPORTANT]
> Mivel az adatbázisok számának megközelíti a felső határ az egyes kiszolgáló, a következő akkor fordulhat elő:
> - Növekvő késés futó lekérdezések a master adatbázisban.  Ez magában foglalja az erőforrás-kihasználtságának statisztikája sys.resource_stats például nézetek.
> - Növelje a késés felügyeleti műveleteket, majd portál nézőpontok, például az adatbázisok a kiszolgálón számbavétele megjelenítése.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Mi történik az adatbázis erőforrás korlátok elérésekor?

### <a name="compute-dtus-and-edtus--vcores"></a>Számítási (Dtu és edtu-k / vCores)

(Dtu és edtu-k vagy vCores mérve) adatbázis számítási mértéke túlságosan megnő, ha a késés növekedése lekérdezni, és még akkor is, időtúllépés is. Ilyen körülmények lekérdezések előfordulhat, hogy az a szolgáltatás nem helyezi várólistára, és biztosított erőforrások erőforrás-ként történő végrehajtásnak felszabadul.
Magas számítási kihasználtsági észlelt, amikor megoldás lehetőségek a következők:

- Az adatbázis vagy a rugalmas készlet több számítási erőforrással az adatbázis számára teljesítményszintjének növelését. Lásd: [önálló adatbázist erőforrás méretezése](sql-database-single-database-scale.md) és [válik a rugalmas készlet erőforrások](sql-database-elastic-pool-scale.md).
- Minden egyes lekérdezés az erőforrás-használat csökkentésére lekérdezések optimalizálása. További információkért lásd: [lekérdezés hangolása/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

Felhasznált lemezterület adatbázis mérete eléri a maximális méretkorlátot, adatbázis beszúrása és frissítések, amelyek az adatok méretének növelése sikertelen és elküld az ügyfélgépeknek egy [hibaüzenet](sql-database-develop-error-messages.md). Adatbázis-választja ki, és törli továbbra is sikeres.

Amikor magas lemezterület-kihasználás, amikor megoldás lehetőségek a következők:

- Az adatbázis vagy a rugalmas maximális méretének növelését tárolókészlet, vagy vegyen fel további tárolókat. Lásd: [önálló adatbázist erőforrás méretezése](sql-database-single-database-scale.md) és [válik a rugalmas készlet erőforrások](sql-database-elastic-pool-scale.md).
- Ha az adatbázis rugalmas készlethez, majd azt is megteheti az adatbázis helyezheti át a készlet kívül, hogy annak tárolóhelyét nem megosztott a többi adatbázissal.

### <a name="sessions-and-workers-requests"></a>Munkamenetek és alkalmazottak (kérelmek) 

A munkamenetek és alkalmazottak maximális száma a szolgáltatási szint és a teljesítmény szint (Dtu és edtu-k) határozzák meg. Új kérelmek azért lettek elutasítva, amikor munkamenet vagy munkavégző korlát elérésekor, és az ügyfelek hibaüzenetet kap. Során rendelkezésre álló kapcsolatok száma az alkalmazás által szabályozható, a száma párhuzamos munkavállalók gyakran nehezebben becsléséhez, és szabályozhatja. Ez különösen igaz csúcsterhelési időszakokat amikor adatbázis erőforrás gyűjtésének elérésekor és miatt hosszabb futó lekérdezések munkavállalók egymásra, során. 

Munkamenet vagy munkavégző magas kihasználtsága észlelt, amikor megoldás lehetőségek a következők:
- Az adatbázis vagy a rugalmas készlet szolgáltatási szint vagy a teljesítmény szint növelését. Lásd: [önálló adatbázist erőforrás méretezése](sql-database-single-database-scale.md) és [válik a rugalmas készlet erőforrások](sql-database-elastic-pool-scale.md).
- Optimalizálás lekérdezések minden egyes lekérdezés az erőforrás-használat csökkentésére, ha nagyobb munkavégző kihasználtsági oka miatt a versengés a számítási erőforrásokat. További információkért lásd: [lekérdezés hangolása/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

Munkamenet vagy munkavégző magas kihasználtsága észlelt, amikor megoldás lehetőségek a következők:
- A szolgáltatási szint vagy a teljesítmény szint az adatbázis növelését. Lásd: [önálló adatbázist erőforrás méretezése](sql-database-single-database-scale.md) és [válik a rugalmas készlet erőforrások](sql-database-elastic-pool-scale.md).
- Optimalizálás lekérdezések minden egyes lekérdezés az erőforrás-használat csökkentésére, ha nagyobb munkavégző kihasználtsági oka miatt a versengés a számítási erőforrásokat. További információkért lásd: [lekérdezés hangolása/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>További lépések

- Lásd: [SQL-adatbázis – gyakori kérdések](sql-database-faq.md) gyakran feltett kérdésekre adott válaszokat.
- Általános Azure korlátozását kapcsolatos információkért lásd: [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../azure-subscription-service-limits.md).
- További információ a Dtu és edtu-k: [Dtu és edtu-k](sql-database-service-tiers.md#what-are-database-transaction-units-dtus).
- A tempdb méretkorlátait kapcsolatos információkért lásd: https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
