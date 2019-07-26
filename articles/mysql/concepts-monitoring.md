---
title: Figyelés Azure Database for MySQL
description: Ez a cikk a Azure Database for MySQL figyelésére és riasztására vonatkozó mérőszámokat ismerteti, beleértve a CPU-t, a tárolást és a kapcsolatok statisztikáit.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/05/2019
ms.openlocfilehash: 0122f952e586d0535fc2e482c7b78266f8809272
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "67062437"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Figyelés Azure Database for MySQL
A kiszolgálók figyelési adatai segítenek a számítási feladatok megoldásában és optimalizálásában. A Azure Database for MySQL különböző mérőszámokat biztosít, amelyek betekintést nyújtanak a kiszolgáló működésére.

## <a name="metrics"></a>Mérőszámok
Minden Azure-metrika egyperces gyakorisággal rendelkezik, és minden metrika 30 napos előzményt biztosít. A mérőszámokra vonatkozó riasztásokat is beállíthat. Részletes útmutatást a [riasztások beállítása](howto-alert-on-metric.md)című témakörben talál. Az egyéb feladatok közé tartozik az automatizált műveletek beállítása, a speciális elemzések végrehajtása és az archiválási előzmények. További információt az [Azure mérőszámok áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md)című témakörben talál.

### <a name="list-of-metrics"></a>Metrikák listája
Ezek a metrikák a Azure Database for MySQL számára érhetők el:

|Metrika|Metrika megjelenített neve|Unit (Egység)|Leírás|
|---|---|---|---|
|cpu_percent|CPU-százalék|Percent|A használatban lévő CPU százalékos aránya.|
|memory_percent|Memória százaléka|Percent|A használatban lévő memória százalékos aránya.|
|io_consumption_percent|IO-százalék|Percent|A használatban lévő IO százalékos aránya.|
|storage_percent|Tárolási százalék|Percent|A kiszolgáló maximális száma által felhasznált tárterület százalékos aránya.|
|storage_used|Felhasznált tároló|Bájt|A használatban lévő tárterület mennyisége. A szolgáltatás által használt tárterület magában foglalhatja az adatbázisfájlok, a tranzakciós naplók és a kiszolgáló naplófájljait is.|
|serverlog_storage_percent|Kiszolgáló naplójának tárolási százaléka|Percent|A kiszolgáló naplófájl-tárolási helyének maximális tárterületének százalékos értéke.|
|serverlog_storage_usage|Kiszolgáló naplójának tárolója|Bájt|A kiszolgáló által használt log-tároló mennyisége.|
|serverlog_storage_limit|Kiszolgáló naplójának tárolási korlátja|Bájt|A kiszolgáló maximális kiszolgálói naplózási tárterülete.|
|storage_limit|Tárolási korlát|Bájt|A kiszolgáló maximális tárterülete.|
|active_connections|Aktív kapcsolatok|Count|A kiszolgálóval létesített aktív kapcsolatok száma.|
|connections_failed|Sikertelen kapcsolatok|Count|A kiszolgálóhoz való sikertelen kapcsolódások száma.|
|seconds_behind_master|Replikálás késése másodpercben|Count|Azon másodpercek száma, ameddig a replika kiszolgáló lemarad a főkiszolgálón.|
|network_bytes_egress|Hálózat kimenő adatforgalma|Bájt|A hálózat aktív kapcsolatokon keresztül.|
|network_bytes_ingress|Hálózat bejövő adatforgalma|Bájt|A hálózat aktív kapcsolatokon keresztül.|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|A felhasznált biztonsági mentési tár mennyisége.|

## <a name="server-logs"></a>Kiszolgálónaplók
Engedélyezheti a lassú lekérdezést és a naplózást a kiszolgálón. Ezek a naplók Azure Monitor naplók, Event Hubs és Storage-fiók Azure diagnosztikai naplóiban is elérhetők. Ha többet szeretne megtudni a naplózásról, látogasson el a [naplók](concepts-audit-logs.md) és a [lassú lekérdezések naplóinak](concepts-server-logs.md) cikkeibe.

## <a name="query-store"></a>Lekérdezéstár
A [query Store](concepts-query-store.md) egy nyilvános előzetes funkció, amely a lekérdezési teljesítmény időbeli nyomon követését, beleértve a lekérdezési futtatókörnyezet statisztikáit és a várakozási eseményeket. A szolgáltatás megtartja a lekérdezés futásidejű teljesítményére vonatkozó információkat a **MySQL** -sémában. Az adatgyűjtést és-tárolást különböző konfigurációs gombokon keresztül szabályozhatja.

## <a name="query-performance-insight"></a>Lekérdezési terheléselemző
A [lekérdezési terheléselemző](concepts-query-performance-insight.md) a lekérdezési tárolóval együtt a Azure Portal elérhető vizualizációk biztosítására is használható. Ezek a diagramok lehetővé teszik a teljesítményre gyakorolt legfontosabb lekérdezések azonosítását. Lekérdezési terheléselemző nyilvános előzetes verzióban érhető el, és a Azure Database for MySQL-kiszolgáló portál lapjának **intelligens teljesítmény** szakaszában érhető el.

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok
A [teljesítményre vonatkozó javaslatok](concepts-performance-recommendations.md) funkció a munkaterhelés teljesítményének növelésére szolgáló lehetőségeket azonosítja. A teljesítményre vonatkozó javaslatok nyilvános előzetes kiadása olyan új indexek létrehozásához nyújt javaslatokat, amelyek képesek a számítási feladatok teljesítményének javítására. Az indexelési javaslatok előállításához a funkció figyelembe veszi a különböző adatbázis-jellemzőket, beleértve annak sémáját és a lekérdezési tároló által jelentett munkaterhelést. A teljesítményre vonatkozó javaslat bevezetését követően az ügyfeleknek tesztelni kell a teljesítményt a változások hatásának kiértékeléséhez.

## <a name="next-steps"></a>További lépések
- A riasztások metrikai létrehozásával kapcsolatos útmutatást a riasztások [beállítása](howto-alert-on-metric.md) című témakörben tekintheti meg.
- A metrikák Azure Portal, REST API vagy parancssori felülettel való eléréséről és exportálásáról további információt az [Azure mérőszámok áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md)című témakörben talál.
- A [kiszolgáló figyelésére vonatkozó ajánlott eljárásokért](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/)olvassa el a blogot.
