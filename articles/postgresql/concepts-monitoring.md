---
title: Monitorozás és hangolás Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk az Azure Database for PostgreSQL – egyetlen kiszolgáló figyelési és hangolási funkcióit ismerteti.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 799b2b6ee6074472a951e69dec7e9a87056373b4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384026"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL figyelése és finomhangolása – egyetlen kiszolgáló
A kiszolgálók figyelési adatai segítenek a számítási feladatok megoldásában és optimalizálásában. Azure Database for PostgreSQL különböző figyelési lehetőségeket biztosít a kiszolgáló működésének áttekintéséhez.

## <a name="metrics"></a>Mérőszámok
Azure Database for PostgreSQL különböző mérőszámokat biztosít, amelyek betekintést nyújtanak a PostgreSQL-kiszolgálót támogató erőforrások viselkedésére. Minden metrika egy egyperces gyakorisággal van kibocsátva, és akár 30 napig is eltarthat. A mérőszámokra vonatkozó riasztásokat is beállíthat. Részletes útmutatást a [riasztások beállítása](howto-alert-on-metric.md)című témakörben talál. Az egyéb feladatok közé tartozik az automatizált műveletek beállítása, a speciális elemzések végrehajtása és az archiválási előzmények. További információt az [Azure mérőszámok áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md)című témakörben talál.

### <a name="list-of-metrics"></a>Metrikák listája
Ezek a metrikák a Azure Database for PostgreSQL számára érhetők el:

|Metrika|Metrika megjelenített neve|Unit (Egység)|Leírás|
|---|---|---|---|
|cpu_percent|CPU-százalék|Százalék|A használatban lévő CPU százalékos aránya.|
|memory_percent|Memória százaléka|Százalék|A használatban lévő memória százalékos aránya.|
|io_consumption_percent|IO-százalék|Százalék|A használatban lévő IO százalékos aránya.|
|storage_percent|Tárolási százalék|Százalék|A kiszolgáló maximális száma által felhasznált tárterület százalékos aránya.|
|storage_used|Felhasznált tárterület|Bájt|A használatban lévő tárterület mennyisége. A szolgáltatás által használt tárterület magában foglalhatja az adatbázisfájlok, a tranzakciós naplók és a kiszolgáló naplófájljait is.|
|storage_limit|Tárolási korlát|Bájt|A kiszolgáló maximális tárterülete.|
|serverlog_storage_percent|Kiszolgáló naplójának tárolási százaléka|Százalék|A kiszolgáló naplófájl-tárolási helyének maximális tárterületének százalékos értéke.|
|serverlog_storage_usage|Kiszolgáló naplójának tárolója|Bájt|A kiszolgáló által használt log-tároló mennyisége.|
|serverlog_storage_limit|Kiszolgáló naplójának tárolási korlátja|Bájt|A kiszolgáló maximális kiszolgálói naplózási tárterülete.|
|active_connections|Aktív kapcsolatok|Darabszám|A kiszolgálóval létesített aktív kapcsolatok száma.|
|connections_failed|Sikertelen kapcsolatok|Darabszám|A kiszolgálóhoz való sikertelen kapcsolódások száma.|
|network_bytes_egress|Kimenő hálózat|Bájt|A hálózat aktív kapcsolatokon keresztül.|
|network_bytes_ingress|Bejövő hálózat|Bájt|A hálózat aktív kapcsolatokon keresztül.|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|A felhasznált biztonsági mentési tár mennyisége.|
|pg_replica_log_delay_in_bytes|Replikák maximális késése|Bájt|A fő és a legnagyobb késleltetésű replika közötti késés bájtban. Ez a metrika csak a főkiszolgálón érhető el.|
|pg_replica_log_delay_in_seconds|Replika késése|másodperc|Az utolsó visszajátszott tranzakció óta eltelt idő. Ez a metrika csak replika kiszolgálók esetében érhető el.|

## <a name="server-logs"></a>Kiszolgálói naplók
Engedélyezheti a naplózást a kiszolgálón. Ezek a naplók [Azure monitor naplók](../azure-monitor/log-query/log-query-overview.md), Event Hubs és Storage-fiók Azure diagnosztikai naplóiban is elérhetők. Ha többet szeretne megtudni a naplózásról, látogasson el a [kiszolgálói naplók](concepts-server-logs.md) oldalra.

## <a name="query-store"></a>Lekérdezéstár
A [lekérdezési tár](concepts-query-store.md) nyomon követi a lekérdezési teljesítményt az idő múlásával, beleértve a lekérdezési futtatókörnyezet statisztikáit és a várakozási eseményeket. A szolgáltatás egy **azure_sys** nevű rendszeradatbázisban tartja fenn a lekérdezési futtatókörnyezet teljesítményadatait a query_store sémában. Az adatgyűjtést és-tárolást különböző konfigurációs gombokon keresztül szabályozhatja.

## <a name="query-performance-insight"></a>Lekérdezési terheléselemző
A [lekérdezési terheléselemző](concepts-query-performance-insight.md) a lekérdezési tárolóval együtt a Azure Portal elérhető vizualizációk biztosítására is használható. Ezek a diagramok lehetővé teszik a teljesítményre gyakorolt legfontosabb lekérdezések azonosítását. A lekérdezés teljesítményének elemzése a Azure Database for PostgreSQL-kiszolgáló portál lapjának **support + hibaelhárítás** szakaszában érhető el.

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok
A [teljesítményre vonatkozó javaslatok](concepts-performance-recommendations.md) funkció a munkaterhelés teljesítményének növelésére szolgáló lehetőségeket azonosítja. A teljesítménnyel kapcsolatos javaslatok olyan új indexek létrehozásához nyújtanak javaslatokat, amelyek képesek a számítási feladatok teljesítményének javítására. Az indexelési javaslatok előállításához a funkció figyelembe veszi a különböző adatbázis-jellemzőket, beleértve annak sémáját és a lekérdezési tároló által jelentett munkaterhelést. A teljesítményre vonatkozó javaslat bevezetését követően az ügyfeleknek tesztelni kell a teljesítményt a változások hatásának kiértékeléséhez. 

## <a name="service-health"></a>Szolgáltatás állapota
Az [Azure szolgáltatás állapota](../service-health/overview.md) az előfizetésben lévő összes szolgáltatás állapotára vonatkozó értesítéseket tartalmazza. Service Health riasztásokat állíthat be, amelyek az előnyben részesített kommunikációs csatornákon keresztül értesítik Önt, ha vannak olyan problémák vagy változások, amelyek befolyásolhatják az Ön által használt Azure-szolgáltatásokat és-régiókat.

Megtekintheti a Azure Database for PostgreSQL-egyetlen kiszolgáló ütemezett karbantartási eseményeit a **tervezett karbantartási** esemény típusának használatával. A **szolgáltatás-állapottal kapcsolatos riasztások**létrehozásával kapcsolatban tekintse meg a [műveletnapló riasztások létrehozása a szolgáltatás értesítéseiről](../service-health/alerts-activity-log-service-notifications.md) című cikket.

> [!IMPORTANT]
> A tervezett karbantartási értesítések előzetes verzióban érhetők el az USA keleti régiójában és csak Egyesült Királyság déli régiója.

## <a name="next-steps"></a>Következő lépések
- A riasztások metrikai létrehozásával kapcsolatos útmutatást a riasztások [beállítása](howto-alert-on-metric.md) című témakörben tekintheti meg.
- A metrikák Azure Portal, REST API vagy parancssori felülettel való eléréséről és exportálásáról további információt az [Azure mérőszámok áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md)című témakörben talál.
- A [kiszolgáló figyelésére vonatkozó ajánlott eljárásokért](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/)olvassa el a blogot.
