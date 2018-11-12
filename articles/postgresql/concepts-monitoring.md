---
title: Figyelése és hangolása az Azure Database for postgresql-hez
description: Ez a cikk ismerteti a figyelési és finomhangolási funkciókhoz az Azure Database for postgresql-hez.
services: postgresql
author: rachel-msft
ms.author: raagyema
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2018
ms.openlocfilehash: 8ad7369bc940f682bb7c11c3ed4e3444f1bb056a
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51035084"
---
# <a name="monitor-and-tune"></a>Figyelés és hangolás
A kiszolgálók adatainak segítségével hibaelhárítása és optimalizálhatja a számítási feladatok számára. Azure Database for PostgreSQL biztosít a különböző megfigyelési lehetőségeket nyújt betekintést a kiszolgáló a hálózatvédelem működését.

## <a name="metrics"></a>Metrikák
Azure Database for postgresql-hez különböző mérőszámokat, amelyek a PostgreSQL-kiszolgáló támogató erőforrásokban viselkedését betekintést biztosít. Mindegyik metrikát bocsásson ki egy egy perces gyakorisággal, és akár 30 napig előzmények rendelkezik. A metrikákhoz riasztásokat is beállíthatja. Lépésenkénti útmutatásért lásd: [riasztások beállítása](howto-alert-on-metric.md). Egyéb feladatok közé tartozik, automatizált műveletek, speciális analitikai végrehajtása és archiváláshoz előzmények. További információkért lásd: a [Azure metrikáinak áttekintésében](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Mérőszámok listája
Ezek a metrikák érhetők el az Azure Database for postgresql-hez:

|Metrika|Metrika megjelenített neve|Unit (Egység)|Leírás|
|---|---|---|---|---|
|cpu_percent|Százalékos Processzorhasználat|Százalék|Processzorhasználat százalékos használja.|
|memory_percent|Memória százalékos aránya|Százalék|Használt memória aránya.|
|io_consumption_percent|I/o-százalék|Százalék|Százalékos i/o-használja.|
|storage_percent|Tárolási százalékos aránya|Százalék|A kiszolgálón kívül felhasznált tárterület százalékos által legnagyobb.|
|storage_used|Felhasznált tárterület|Bájt|A használatban lévő tárterület mérete. A szolgáltatás által használt tárterület tartalmazhatják az adatbázisfájlokat, tranzakciós naplókhoz és a webkiszolgáló-naplókkal.|
|storage_limit|Tárolási kapacitása|Bájt|Ez a kiszolgáló maximális tárterülete.|
|serverlog_storage_percent|Server Log storage százalékban|Százalék|A százalékos aránya ki a kiszolgáló maximális kiszolgálói naplók tárolására használt kiszolgálói naplók tárolásához.|
|serverlog_storage_usage|Kiszolgálói naplók tárolására használt|Bájt|Kiszolgálói naplók tárolására használt mennyisége.|
|serverlog_storage_limit|Log storage maximális|Bájt|A maximális kiszolgálói naplók tárolásához, ehhez a kiszolgálóhoz.|
|active_connections|Az aktív kapcsolatok|Mennyiség|A kiszolgáló aktív kapcsolatok száma.|
|connections_failed|Sikertelen kapcsolatok|Mennyiség|Nem sikerült a kiszolgálóval létesített kapcsolatok száma.|
|network_bytes_egress|Hálózat kimenő adatforgalma|Bájt|Kimenő hálózati forgalom az aktív kapcsolatok között.|
|network_bytes_ingress|Hálózat bejövő adatforgalma|Bájt|Hálózati az aktív kapcsolatok között.|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|A felhasznált biztonsági mentési tárterület mennyisége.|

## <a name="server-logs"></a>Kiszolgálónaplók
Engedélyezheti a naplózást a kiszolgálón. Ezek a naplók is elérhetők az Azure-diagnosztikai naplók keresztül [Log Analytics](../log-analytics/log-analytics-queries.md), az Event Hubs és a Storage-fiókot. Naplózásával kapcsolatos további tudnivalókért látogasson el a [kiszolgálónaplók](concepts-server-logs.md) lapot.

## <a name="query-store"></a>Lekérdezéstár
[Query Store](concepts-query-store.md) nyilvános előzetes verziójú funkció, amely nyomon követi a lekérdezési teljesítményt idő többek között lekérdezés futásidejének statisztikai adatait, és várjon eseményeket. A funkció továbbra is fennáll, lekérdezés futásidejű teljesítményadatait nevű rendszer-adatbázisok **azure_sys** a query_store séma alapján. Szabályozhatja, hogy a gyűjtemény és az adatok különböző konfigurációs belül keresztül.

## <a name="query-performance-insight"></a>Információ a lekérdezések teljesítményéről
[Lekérdezési Terheléselemző](concepts-query-performance-insight.md) Query Store az Azure Portalon elérhető képi megjelenítésekhez együtt működik. Ezekbe a diagramokba engedélyezése elsődlegeskulcs-lekérdezések azonosíthatja, hogy hatással lehet a teljesítményre. Lekérdezési Terheléselemző nyilvános előzetes verzióban érhető el, és elérhető a **támogatás + hibaelhárítás** az Azure Database for PostgreSQL-kiszolgáló portáloldalán szakaszában.

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok
A [teljesítménnyel kapcsolatos javaslatok](concepts-performance-recommendations.md) szolgáltatás lehetőségeket biztosít a számítási feladatok teljesítményének javítása azonosítja. Teljesítménnyel kapcsolatos javaslatok a nyilvános előzetes verziója biztosít, amelyek a számítási feladatok teljesítményének javítása érdekében új indexek létrehozására vonatkozó javaslatok. Index ajánlások előállításához, a szolgáltatás figyelembe veszi különféle adatbázis jellemzőit, beleértve a séma- és a számítási feladatok Query Store által jelentett módon. Után minden teljesítmény javaslat megvalósítása, ügyfelek kell teljesítménytesztelési ezeket a módosításokat hatásának vizsgálatában. 

## <a name="next-steps"></a>További lépések
- Lásd: [riasztások beállítása](howto-alert-on-metric.md) riasztás létrehozása a metrika az útmutatást.
- Elérése és exportálása a metrikák az Azure Portalon, a REST API vagy a parancssori felület használatával kapcsolatos további információkért lásd: a [Azure metrikáinak áttekintésében](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Blogunkból a [ajánlott eljárások a server figyelési](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/).
