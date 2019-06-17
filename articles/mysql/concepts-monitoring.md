---
title: Figyelés az Azure Database for MySQL-hez
description: Ez a cikk ismerteti a monitorozási és riasztási az Azure Database for MySQL, többek között a Processzor, a storage és a kapcsolat statisztikai mérőszámait.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/05/2019
ms.openlocfilehash: 0122f952e586d0535fc2e482c7b78266f8809272
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67062437"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Figyelés az Azure Database for MySQL-hez
A kiszolgálók adatainak segítségével hibaelhárítása és optimalizálhatja a számítási feladatok számára. Azure Database for MySQL-hez különböző mérőszámokat, amelyek a kiszolgáló viselkedését betekintést biztosít.

## <a name="metrics"></a>Mérőszámok
Az összes Azure-metrikagyűjtéshez rendelkezik egy egy perces gyakoriságot, és mindegyik metrikát biztosít előzmények 30 nap. A metrikákhoz riasztásokat is beállíthatja. Lépésenkénti útmutatásért lásd: [riasztások beállítása](howto-alert-on-metric.md). Egyéb feladatok közé tartozik, automatizált műveletek, speciális analitikai végrehajtása és archiváláshoz előzmények. További információkért lásd: a [Azure metrikáinak áttekintésében](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Mérőszámok listája
Ezek a metrikák érhetők el az Azure Database for MySQL-hez:

|Metrika|Metrika megjelenített neve|Unit (Egység)|Leírás|
|---|---|---|---|
|cpu_percent|Százalékos Processzorhasználat|Százalék|Processzorhasználat százalékos használja.|
|memory_percent|Memória százalékos aránya|Százalék|Használt memória aránya.|
|io_consumption_percent|I/o-százalék|Százalék|Százalékos i/o-használja.|
|storage_percent|Tárolási százalékos aránya|Százalék|A kiszolgálón kívül felhasznált tárterület százalékos által legnagyobb.|
|storage_used|Felhasznált tárterület|Bájt|A használatban lévő tárterület mérete. A szolgáltatás által használt tárterület tartalmazhatják az adatbázisfájlokat, tranzakciós naplókhoz és a webkiszolgáló-naplókkal.|
|serverlog_storage_percent|Server Log storage százalékban|Százalék|A százalékos aránya ki a kiszolgáló maximális kiszolgálói naplók tárolására használt kiszolgálói naplók tárolásához.|
|serverlog_storage_usage|Kiszolgálói naplók tárolására használt|Bájt|Kiszolgálói naplók tárolására használt mennyisége.|
|serverlog_storage_limit|Log storage maximális|Bájt|A maximális kiszolgálói naplók tárolásához, ehhez a kiszolgálóhoz.|
|storage_limit|Tárolási kapacitása|Bájt|Ez a kiszolgáló maximális tárterülete.|
|active_connections|Az aktív kapcsolatok|Count|A kiszolgáló aktív kapcsolatok száma.|
|connections_failed|Sikertelen kapcsolatok|Count|Nem sikerült a kiszolgálóval létesített kapcsolatok száma.|
|seconds_behind_master|Replikációs késés másodpercben|Darabszám|Az adatbázisreplika-kiszolgálót a fő kiszolgálón van elmaradt másodpercek számát.|
|network_bytes_egress|Kimenő hálózat|Bájt|Kimenő hálózati forgalom az aktív kapcsolatok között.|
|network_bytes_ingress|Bejövő hálózat|Bájt|Hálózati az aktív kapcsolatok között.|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|A felhasznált biztonsági mentési tárterület mennyisége.|

## <a name="server-logs"></a>Kiszolgálói naplók
Lassú lekérdezések engedélyezheti és naplóvizsgálat a kiszolgálón. Ezek a naplók keresztül is elérhetőek az Azure diagnosztikai naplók a Azure Monitor naplók, az Event Hubs és a Storage-fiókot. Naplózásával kapcsolatos további tudnivalókért látogasson el a [auditnaplók](concepts-audit-logs.md) és [lassú lekérdezések naplóinak](concepts-server-logs.md) cikkeket.

## <a name="query-store"></a>Lekérdezéstár
[Query Store](concepts-query-store.md) nyilvános előzetes verziójú funkció, amely nyomon követi a lekérdezési teljesítményt idő többek között lekérdezés futásidejének statisztikai adatait, és várjon eseményeket. A funkció továbbra is fennáll, lekérdezés futásidejű teljesítményadatait a a **mysql** séma. Szabályozhatja, hogy a gyűjtemény és az adatok különböző konfigurációs belül keresztül.

## <a name="query-performance-insight"></a>Lekérdezési terheléselemző
[Lekérdezési Terheléselemző](concepts-query-performance-insight.md) Query Store az Azure Portalon elérhető képi megjelenítésekhez együtt működik. Ezekbe a diagramokba engedélyezése elsődlegeskulcs-lekérdezések azonosíthatja, hogy hatással lehet a teljesítményre. Lekérdezési Terheléselemző nyilvános előzetes verzióban érhető el, és elérhető a **intelligens teljesítmény** az Azure Database for MySQL-kiszolgáló portáloldalán szakaszában.

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok
A [teljesítménnyel kapcsolatos javaslatok](concepts-performance-recommendations.md) szolgáltatás lehetőségeket biztosít a számítási feladatok teljesítményének javítása azonosítja. Teljesítménnyel kapcsolatos javaslatok a nyilvános előzetes verziója biztosít, amelyek a számítási feladatok teljesítményének javítása érdekében új indexek létrehozására vonatkozó javaslatok. Index ajánlások előállításához, a szolgáltatás figyelembe veszi különféle adatbázis jellemzőit, beleértve a séma- és a számítási feladatok Query Store által jelentett módon. Után minden teljesítmény javaslat megvalósítása, ügyfelek kell teljesítménytesztelési ezeket a módosításokat hatásának vizsgálatában.

## <a name="next-steps"></a>További lépések
- Lásd: [riasztások beállítása](howto-alert-on-metric.md) riasztás létrehozása a metrika az útmutatást.
- Elérése és exportálása a metrikák az Azure Portalon, a REST API vagy a parancssori felület használatával kapcsolatos további információkért lásd: a [Azure metrikáinak áttekintésében](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Blogunkból a [ajánlott eljárások a server figyelési](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/).
