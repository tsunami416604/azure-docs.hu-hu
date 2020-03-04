---
title: Figyelés és Finomhangolás – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk az Azure Database for PostgreSQL – egyetlen kiszolgáló figyelési és hangolási funkcióit ismerteti.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 2d2bb5440e6d23a4cb5781244ba33ab4c5be2612
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252587"
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
|pg_replica_log_delay_in_seconds|Replika késése|Másodperc|Az utolsó visszajátszott tranzakció óta eltelt idő. Ez a metrika csak replika kiszolgálók esetében érhető el.|

## <a name="server-logs"></a>Kiszolgálói naplók
Engedélyezheti a naplózást a kiszolgálón. Ezek a naplók [Azure monitor naplók](../azure-monitor/log-query/log-query-overview.md), Event Hubs és Storage-fiók Azure diagnosztikai naplóiban is elérhetők. Ha többet szeretne megtudni a naplózásról, látogasson el a [kiszolgálói naplók](concepts-server-logs.md) oldalra.

## <a name="query-store"></a>Lekérdezéstár
A [lekérdezési tár](concepts-query-store.md) nyomon követi a lekérdezési teljesítményt az idő múlásával, beleértve a lekérdezési futtatókörnyezet statisztikáit és a várakozási eseményeket. A szolgáltatás egy **azure_sys** nevű rendszeradatbázisban tartja fenn a lekérdezési futtatókörnyezet teljesítményadatait a query_store sémában. Az adatgyűjtést és-tárolást különböző konfigurációs gombokon keresztül szabályozhatja.

## <a name="query-performance-insight"></a>Lekérdezési terheléselemző
A [lekérdezési terheléselemző](concepts-query-performance-insight.md) a lekérdezési tárolóval együtt a Azure Portal elérhető vizualizációk biztosítására is használható. Ezek a diagramok lehetővé teszik a teljesítményre gyakorolt legfontosabb lekérdezések azonosítását. A Lekérdezési terheléselemző a Azure Database for PostgreSQL-kiszolgáló portál lapjának **támogatás + hibaelhárítás** szakaszában érhető el.

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok
A [teljesítményre vonatkozó javaslatok](concepts-performance-recommendations.md) funkció a munkaterhelés teljesítményének növelésére szolgáló lehetőségeket azonosítja. A teljesítménnyel kapcsolatos javaslatok olyan új indexek létrehozásához nyújtanak javaslatokat, amelyek képesek a számítási feladatok teljesítményének javítására. Az indexelési javaslatok előállításához a funkció figyelembe veszi a különböző adatbázis-jellemzőket, beleértve annak sémáját és a lekérdezési tároló által jelentett munkaterhelést. A teljesítményre vonatkozó javaslat bevezetését követően az ügyfeleknek tesztelni kell a teljesítményt a változások hatásának kiértékeléséhez. 

## <a name="planned-maintenance-notification"></a>Tervezett karbantartási értesítés

A **tervezett karbantartási értesítések** lehetővé teszik, hogy riasztásokat kapjon a közelgő tervezett karbantartásról a Azure Database for PostgreSQL-egyetlen kiszolgálóra. Ezek az értesítések a [Service Health](../service-health/overview.md) tervezett karbantartásával vannak integrálva, és lehetővé teszik az előfizetések összes ütemezett karbantartásának megtekintését egy helyen. Emellett segít az értesítések méretezésében a megfelelő célközönségek számára a különböző erőforráscsoportok esetében, mivel előfordulhat, hogy a különböző kapcsolattartók különböző erőforrásokért felelősek. A közelgő karbantartási 72 órával az esemény előtt értesítést fog kapni.

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
> A tervezett karbantartási értesítések jelenleg előzetes verzióban érhetők el

## <a name="next-steps"></a>Következő lépések
- A riasztások metrikai létrehozásával kapcsolatos útmutatást a riasztások [beállítása](howto-alert-on-metric.md) című témakörben tekintheti meg.
- A metrikák Azure Portal, REST API vagy parancssori felülettel való eléréséről és exportálásáról további információt az [Azure mérőszámok áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md)című témakörben talál.
- A [kiszolgáló figyelésére vonatkozó ajánlott eljárásokért](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/)olvassa el a blogot.
