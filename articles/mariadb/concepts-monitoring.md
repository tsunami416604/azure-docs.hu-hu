---
title: Figyelés Azure Database for MariaDB
description: Ez a cikk a Azure Database for MariaDB figyelésére és riasztására vonatkozó mérőszámokat ismerteti, beleértve a CPU-t, a tárolást és a kapcsolatok statisztikáit.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e3c25798be8af26c1f5e5c1178395cd1688bb132
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382048"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Figyelés Azure Database for MariaDB
A kiszolgálók figyelési adatai segítenek a számítási feladatok megoldásában és optimalizálásában. A Azure Database for MariaDB különböző mérőszámokat biztosít, amelyek betekintést nyújtanak a kiszolgáló működésére.

## <a name="metrics"></a>Mérőszámok
Minden Azure-metrika egyperces gyakorisággal rendelkezik, és minden metrika 30 napos előzményt biztosít. A mérőszámokra vonatkozó riasztásokat is beállíthat. Az egyéb feladatok közé tartozik az automatizált műveletek beállítása, a speciális elemzések végrehajtása és az archiválási előzmények. További információt az [Azure mérőszámok áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md)című témakörben talál.

Részletes útmutatást a [riasztások beállítása](howto-alert-metric.md)című témakörben talál.

### <a name="list-of-metrics"></a>Metrikák listája
Ezek a metrikák a Azure Database for MariaDB számára érhetők el:

|Metrika|Metrika megjelenített neve|Unit (Egység)|Leírás|
|---|---|---|---|
|cpu_percent|CPU-százalék|Százalék|A használatban lévő CPU százalékos aránya.|
|memory_percent|Memória százaléka|Százalék|A használatban lévő memória százalékos aránya.|
|io_consumption_percent|IO-százalék|Százalék|A használatban lévő IO százalékos aránya.|
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

## <a name="service-health"></a>Szolgáltatás állapota
Az [Azure szolgáltatás állapota](../service-health/overview.md) az előfizetésben lévő összes szolgáltatás állapotára vonatkozó értesítéseket tartalmazza. Service Health riasztásokat állíthat be, amelyek az előnyben részesített kommunikációs csatornákon keresztül értesítik Önt, ha vannak olyan problémák vagy változások, amelyek befolyásolhatják az Ön által használt Azure-szolgáltatásokat és-régiókat.

A Azure Database for MariaDB ütemezett karbantartási eseményei a **tervezett karbantartási** esemény típusával tekinthetők meg. A **szolgáltatás-állapottal kapcsolatos riasztások**létrehozásával kapcsolatban tekintse meg a [műveletnapló riasztások létrehozása a szolgáltatás értesítéseiről](../service-health/alerts-activity-log-service-notifications.md) című cikket.

> [!IMPORTANT]
> A tervezett karbantartási értesítések előzetes verzióban érhetők el az USA keleti régiójában és csak Egyesült Királyság déli régiója.

## <a name="next-steps"></a>Következő lépések

- A metrikák Azure Portal, REST API vagy parancssori felülettel való eléréséről és exportálásáról további információt az [Azure mérőszámok áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md)című témakörben talál.
  - A riasztások metrikai létrehozásával kapcsolatos útmutatást a riasztások [beállítása](howto-alert-metric.md) című témakörben tekintheti meg.
