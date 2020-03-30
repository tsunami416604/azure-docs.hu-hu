---
title: Figyelés – Azure-adatbázis a MariaDB-hez
description: Ez a cikk ismerteti a metrikák az Azure Database for MariaDB, beleértve a CPU, a tárolás és a kapcsolat statisztikák metrikák.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 20a2066dc0228fe9c2fee09387d96bf6aafeb32f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531974"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Figyelés a MariaDB Azure-adatbázisában
A kiszolgálók adatainak figyelése segít a számítási feladatok hibaelhárításával és optimalizálásával. A MariaDB Azure Database különböző mutatókat biztosít, amelyek betekintést nyújtanak a kiszolgáló viselkedésébe.

## <a name="metrics"></a>Mérőszámok
Minden Azure-metrikák egy perces gyakorisággal rendelkezik, és minden metrika 30 nap az előzmények. Riasztások konfigurálhatók a metrikák. Egyéb feladatok közé tartozik az automatizált műveletek beállítása, a speciális elemzések végrehajtása és az előzmények archiválása. További információt az [Azure Metrics áttekintése című témakörben talál.](../monitoring-and-diagnostics/monitoring-overview-metrics.md)

A részletes útmutatásról a [Riasztások beállítása témakörben talál.](howto-alert-metric.md)

### <a name="list-of-metrics"></a>Mérőszámok listája
Ezek a mérőszámok a MariaDB Azure Database szolgáltatásához érhetők el:

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Leírás|
|---|---|---|---|
|cpu_percent|PROCESSZOR százalék|Százalék|A használt processzor százalékos aránya.|
|memory_percent|Memória százaléka|Százalék|A használt memória százalékos aránya.|
|io_consumption_percent|IO százalék|Százalék|A használatban lévő io százaléka.|
|storage_percent|Tárolási százalék|Százalék|A kiszolgáló maximális tárhelyén kívüli tárhely százalékos aránya.|
|storage_used|Használt tároló|Bájt|A használatban lévő tárhely mennyisége. A szolgáltatás által használt tároló tartalmazhat adatbázisfájlokat, tranzakciónaplókat és kiszolgálónaplókat.|
|serverlog_storage_percent|Kiszolgálói napló tárolási százaléka|Százalék|A kiszolgáló naplótárolójának a kiszolgáló maximális tárolójából felhasznált százalékos aránya.|
|serverlog_storage_usage|Használt kiszolgálónapló-tároló|Bájt|A használt kiszolgálói naplótároló mennyisége.|
|serverlog_storage_limit|Kiszolgálónapló tárolási korlátja|Bájt|A kiszolgáló naplójának maximális tárolója.|
|storage_limit|Tárolási korlát|Bájt|A kiszolgáló maximális tárhelye.|
|active_connections|Aktív kapcsolatok|Darabszám|A kiszolgálóval létesített aktív kapcsolatok száma.|
|connections_failed|Sikertelen kapcsolatok|Darabszám|A kiszolgálóval létesített sikertelen kapcsolatok száma.|
|network_bytes_egress|Kimenő hálózat|Bájt|Hálózat kikapcsolása az aktív kapcsolatok között.|
|network_bytes_ingress|Bejövő hálózat|Bájt|Hálózati kapcsolat az aktív kapcsolatok között.|

## <a name="server-logs"></a>Kiszolgálói naplók

A kiszolgálón engedélyezheti a lassú lekérdezésnaplózást. Ezek a naplók az Azure diagnosztikai naplók az Azure Monitor naplók, Event Hubs és a storage-fiók keresztül is elérhetők. Ha többet szeretne megtudni a naplózásról, látogasson el a [kiszolgálónaplók oldalára.](concepts-server-logs.md)

## <a name="query-store"></a>Lekérdezéstár

[A Query Store](concepts-query-store.md) nyomon követi a lekérdezések teljesítményét az idő múlásával, beleértve a lekérdezésfutási statisztikáit és a várakozási eseményeket. A szolgáltatás megőrzi a lekérdezés futásidejű teljesítményadatait a **mysql** sémában. Az adatok gyűjtését és tárolását különböző konfigurációs gombokon keresztül szabályozhatja.

## <a name="query-performance-insight"></a>Lekérdezési terheléselemző

[A Query Performance Insight](concepts-query-performance-insight.md) a Query Store-ral együttműködve biztosítja az Azure Portalról elérhető vizualizációkat. Ezek a diagramok lehetővé teszik a teljesítményt befolyásoló kulcsfontosságú lekérdezések azonosítását. A Lekérdezési teljesítmény insight érhető el az Azure Database for MariaDB-kiszolgáló portállapjának **Intelligens teljesítmény** szakaszában.

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok

A [Teljesítményjavaslatok](concepts-performance-recommendations.md) funkció azonosítja a számítási feladatok teljesítményének javítására szolgáló lehetőségeket. A teljesítményjavaslatok javaslatokat tartalmaznak olyan új indexek létrehozására, amelyek javíthatják a számítási feladatok teljesítményét. Indexjavaslatok létrehozásához a szolgáltatás figyelembe veszi a különböző adatbázis-jellemzőket, beleértve a sémát és a Query Store által jelentett munkaterhelést. Bármely teljesítményajánlás végrehajtása után az ügyfeleknek tesztelniük kell a teljesítményt, hogy értékeljék a változások hatását.

## <a name="planned-maintenance-notification"></a>Tervezett karbantartási értesítés

**A tervezett karbantartási értesítések** lehetővé teszik, hogy értesítéseket kapjon a közelgő tervezett karbantartásról az Azure-adatbázisba a MariaDB-hez. Ezek az értesítések integrálva vannak a Service Health tervezett [karbantartásával,](../service-health/overview.md) és lehetővé teszik, hogy az előfizetések összes ütemezett karbantartását egy helyen tekintse meg. Emellett segít a különböző erőforráscsoportok megfelelő közönségének értesítésének méretezése, mivel előfordulhat, hogy különböző kapcsolattartók felelősek a különböző erőforrásokért. Az esemény előtt 72 órával értesítést kap a közelgő karbantartásról.

> [!Note]
> Minden kísérletet megteszünk annak **érdekében,** hogy a tervezett karbantartási értesítést 72 órával előre értesítsük az összes eseményről. Kritikus vagy biztonsági javítások esetén azonban előfordulhat, hogy az értesítések közelebb kerülnek az eseményhez, vagy kimaradnak.

### <a name="to-receive-planned-maintenance-notification"></a>Tervezett karbantartási értesítés fogadása

1. A [portálon](https://portal.azure.com)válassza a **Szolgáltatás állapota**lehetőséget.
2. A **Riasztások csoportban** válassza az **Állapotriasztások lehetőséget.**
3. Válassza a **+ Szolgáltatásállapot-riasztás hozzáadása** lehetőséget, és töltse ki a mezőket.
4. Töltse ki a szükséges mezőket. 
5. Válassza ki az **Esemény típust**, válassza a Tervezett karbantartás lehetőséget, vagy **válassza ki az összeset.** **Planned maintenance**
6. A **műveletcsoportok** határozzák meg, hogyan szeretné megkapni a riasztást (kap egy e-mailt, kiváltó logikai alkalmazás, stb)  
7. Győződjön meg arról, hogy a létrehozáskor a szabály engedélyezése beállítás Igen.
8. A riasztás befejezéséhez válassza a **Figyelmeztetési szabály létrehozása** lehetőséget.

A **szolgáltatásállapot-riasztások**létrehozásának részletes lépéseit a [Tevékenységnapló-értesítések létrehozása című](../service-health/alerts-activity-log-service-notifications.md)dokumentum ban tájékformált.

> [!IMPORTANT]
> A tervezett karbantartási értesítések jelenleg előzetes verzióban vannak

## <a name="next-steps"></a>További lépések

- Az Azure Portal, a REST API vagy a CLI használatával a metrikák eléréséről és exportálásáról az [Azure Metrics overview című témakörben olvashat bővebben.](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
  - [Olvassa el a Riasztások beállítása](howto-alert-metric.md) útmutatást egy metrika riasztásának létrehozásához.
