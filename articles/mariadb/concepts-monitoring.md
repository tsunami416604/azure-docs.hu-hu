---
title: Figyelés – Azure Database for MariaDB
description: Ez a cikk a Azure Database for MariaDB figyelésére és riasztására vonatkozó mérőszámokat ismerteti, beleértve a CPU-t, a tárolást és a kapcsolatok statisztikáit.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: 708b243d9db16ee8454b4bc0f5c136b9f4399916
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85413195"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Figyelés Azure Database for MariaDB
A kiszolgálók figyelési adatai segítenek a számítási feladatok megoldásában és optimalizálásában. A Azure Database for MariaDB különböző mérőszámokat biztosít, amelyek betekintést nyújtanak a kiszolgáló működésére.

## <a name="metrics"></a>Mérőszámok
Minden Azure-metrika egyperces gyakorisággal rendelkezik, és minden metrika 30 napos előzményt biztosít. A mérőszámokra vonatkozó riasztásokat is beállíthat. Az egyéb feladatok közé tartozik az automatizált műveletek beállítása, a speciális elemzések végrehajtása és az archiválási előzmények. További információt az [Azure mérőszámok áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md)című témakörben talál.

Részletes útmutatást a [riasztások beállítása](howto-alert-metric.md)című témakörben talál.

### <a name="list-of-metrics"></a>Metrikák listája
Ezek a metrikák a Azure Database for MariaDB számára érhetők el:

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Description|
|---|---|---|---|
|cpu_percent|CPU-százalék|Százalék|A használatban lévő CPU százalékos aránya.|
|memory_percent|Memória százaléka|Százalék|A használatban lévő memória százalékos aránya.|
|io_consumption_percent|IO-százalék|Százalék|A használatban lévő IO százalékos aránya. (Nem alkalmazható az alapszintű kiszolgálók esetében.)|
|storage_percent|Tárolási százalék|Százalék|A kiszolgáló maximális száma által felhasznált tárterület százalékos aránya.|
|storage_used|Felhasznált tárterület|Bájt|A használatban lévő tárterület mennyisége. A szolgáltatás által használt tárterület magában foglalhatja az adatbázisfájlok, a tranzakciós naplók és a kiszolgáló naplófájljait is.|
|serverlog_storage_percent|Kiszolgáló naplójának tárolási százaléka|Százalék|A kiszolgáló naplófájl-tárolási helyének maximális tárterületének százalékos értéke.|
|serverlog_storage_usage|Kiszolgáló naplójának tárolója|Bájt|A kiszolgáló által használt log-tároló mennyisége.|
|serverlog_storage_limit|Kiszolgáló naplójának tárolási korlátja|Bájt|A kiszolgáló maximális kiszolgálói naplózási tárterülete.|
|storage_limit|Tárolási korlát|Bájt|A kiszolgáló maximális tárterülete.|
|active_connections|Aktív kapcsolatok|Darabszám|A kiszolgálóval létesített aktív kapcsolatok száma.|
|connections_failed|Sikertelen kapcsolatok|Darabszám|A kiszolgálóhoz való sikertelen kapcsolódások száma.|
|network_bytes_egress|Kimenő hálózat|Bájt|A hálózat aktív kapcsolatokon keresztül.|
|network_bytes_ingress|Bejövő hálózat|Bájt|A hálózat aktív kapcsolatokon keresztül.|

## <a name="server-logs"></a>Kiszolgálói naplók

Engedélyezheti a lassú lekérdezések naplózását a kiszolgálón. Ezek a naplók Azure Monitor naplók, Event Hubs és Storage-fiók Azure diagnosztikai naplóiban is elérhetők. Ha többet szeretne megtudni a naplózásról, látogasson el a [kiszolgálói naplók](concepts-server-logs.md) oldalra.

## <a name="query-store"></a>Lekérdezéstár

A [lekérdezési tár](concepts-query-store.md) nyomon követi a lekérdezési teljesítményt az idő múlásával, beleértve a lekérdezési futtatókörnyezet statisztikáit és a várakozási eseményeket. A szolgáltatás megtartja a lekérdezés futásidejű teljesítményére vonatkozó információkat a **MySQL** -sémában. Az adatgyűjtést és-tárolást különböző konfigurációs gombokon keresztül szabályozhatja.

## <a name="query-performance-insight"></a>Lekérdezési terheléselemző

A [lekérdezési terheléselemző](concepts-query-performance-insight.md) a lekérdezési tárolóval együtt a Azure Portal elérhető vizualizációk biztosítására is használható. Ezek a diagramok lehetővé teszik a teljesítményre gyakorolt legfontosabb lekérdezések azonosítását. A Lekérdezési terheléselemző a Azure Database for MariaDB-kiszolgáló portál lapjának **intelligens teljesítmény** szakaszában érhető el.

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok

A [teljesítményre vonatkozó javaslatok](concepts-performance-recommendations.md) funkció a munkaterhelés teljesítményének növelésére szolgáló lehetőségeket azonosítja. A teljesítménnyel kapcsolatos javaslatok olyan új indexek létrehozásához nyújtanak javaslatokat, amelyek képesek a számítási feladatok teljesítményének javítására. Az indexelési javaslatok előállításához a funkció figyelembe veszi a különböző adatbázis-jellemzőket, beleértve annak sémáját és a lekérdezési tároló által jelentett munkaterhelést. A teljesítményre vonatkozó javaslat bevezetését követően az ügyfeleknek tesztelni kell a teljesítményt a változások hatásának kiértékeléséhez.

## <a name="planned-maintenance-notification"></a>Tervezett karbantartási értesítés

A **tervezett karbantartási értesítések** lehetővé teszik, hogy riasztásokat kapjon a Azure Database for MariaDB közelgő tervezett karbantartásáról. Ezek az értesítések a [Service Health](../service-health/overview.md) tervezett karbantartásával vannak integrálva, és lehetővé teszik az előfizetések összes ütemezett karbantartásának megtekintését egy helyen. Emellett segít az értesítések méretezésében a megfelelő célközönségek számára a különböző erőforráscsoportok esetében, mivel előfordulhat, hogy a különböző kapcsolattartók különböző erőforrásokért felelősek. A közelgő karbantartási 72 órával az esemény előtt értesítést fog kapni.

> [!Note]
> Minden eseményről minden kísérletet megteszünk a **tervezett karbantartási értesítés** 72 óra megadására. A kritikus vagy biztonsági javítások esetében azonban előfordulhat, hogy az értesítések közelebb kerülnek az eseményhez, vagy kimaradnak.

### <a name="to-receive-planned-maintenance-notification"></a>Tervezett karbantartási értesítés fogadása

1. A [portálon](https://portal.azure.com)válassza a **Service Health**lehetőséget.
2. A **riasztások** szakaszban válassza az **állapot riasztások**lehetőséget.
3. Válassza a **+ szolgáltatás állapotára vonatkozó riasztás hozzáadása** lehetőséget, és töltse ki a mezőket.
4. Töltse ki a kötelező mezőket. 
5. Válassza ki az **esemény típusát**, válassza a **tervezett karbantartás** lehetőséget, vagy **válassza az összes lehetőséget** .
6. A **műveleti csoportok** azt határozzák meg, hogyan szeretné fogadni a riasztást (e-mail küldése, logikai alkalmazás elindítása stb.)  
7. Győződjön meg arról, hogy az engedélyezés szabály a létrehozáskor Igen értékre van állítva.
8. Válassza a riasztási **szabály létrehozása** lehetőséget a riasztás befejezéséhez

A **szolgáltatás állapotára vonatkozó riasztások**létrehozásával kapcsolatos részletes útmutatásért lásd: [műveletnapló riasztások létrehozása a szolgáltatási értesítéseken](../service-health/alerts-activity-log-service-notifications.md).

> [!IMPORTANT]
> A tervezett karbantartási értesítések jelenleg előzetes verzióban érhetők el minden régióban, **kivéve** az USA nyugati középső régióját

## <a name="next-steps"></a>További lépések

- A metrikák Azure Portal, REST API vagy parancssori felülettel való eléréséről és exportálásáról további információt az [Azure mérőszámok áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md)című témakörben talál.
  - A riasztások metrikai létrehozásával kapcsolatos útmutatást a riasztások [beállítása](howto-alert-metric.md) című témakörben tekintheti meg.
