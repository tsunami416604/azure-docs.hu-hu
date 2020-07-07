---
title: Az ajánlott eljárások lekérdezése Azure Database for PostgreSQL – egyetlen kiszolgálón
description: Ez a cikk a Azure Database for PostgreSQL egyetlen kiszolgálón található lekérdezési tár ajánlott eljárásait ismerteti.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 51239f4cf49784dd47470e1272b90508eaf25e6f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "70764234"
---
# <a name="best-practices-for-query-store"></a>Ajánlott eljárások a lekérdezési tárolóhoz

**A következőkre vonatkozik:** Azure Database for PostgreSQL – egykiszolgálós verzió: 9,6, 10, 11

Ez a cikk a Query Store Azure Database for PostgreSQL-ban való használatának ajánlott eljárásait ismerteti.

## <a name="set-the-optimal-query-capture-mode"></a>Az optimális lekérdezés-rögzítési mód beállítása
Lehetővé teszi a lekérdezési tároló számára az Ön számára fontos információk rögzítését. 

|**pg_qs. query_capture_mode** | **Forgatókönyv**|
|---|---|
|_Mind_  |Alaposan elemezheti a munkaterhelést az összes lekérdezés és a végrehajtási gyakoriságuk és egyéb statisztikáik tekintetében. Azonosítsa a számítási feladatok új lekérdezéseit. Annak észlelése, hogy az alkalmi lekérdezések a felhasználók vagy az automatikus paraméterezés lehetőségeinek azonosítására szolgálnak-e. Az _összes_ erőforrás-felhasználás megnövelt erőforrás-használati díjat tartalmaz. |
|_Felső_  |A legfontosabb lekérdezésekre koncentrálhat – ezek az ügyfelek által kiállítottak.
|_Nincs_ |Már rögzített egy lekérdezési készletet és egy időablakot, amelyet meg szeretne vizsgálni, és el szeretné távolítani a más lekérdezések által esetlegesen bevezethető zavaró tényezőket. A _nincs_ megfelelő tesztelési és pad-jelölési környezetekhez. _Nincs_ szükség körültekintően, mivel előfordulhat, hogy a fontos új lekérdezések nyomon követésére és optimalizálására lehetősége van. A korábbi időpontokban nem állíthatók helyre adatok. |

A lekérdezési tároló a várakozási statisztikák tárolására is tartalmaz egy tárolót. Van egy további, a várakozási statisztikát szabályozó rögzítési mód lekérdezése: **pgms_wait_sampling. query_capture_mode** a _none_ vagy _az All_értékre állítható be. 

> [!NOTE] 
> **pg_qs. query_capture_mode** felülírja **pgms_wait_sampling. query_capture_mode**. Ha pg_qs. query_capture_mode nincs _, a_pgms_wait_sampling. query_capture_mode beállításnak nincs hatása. 


## <a name="keep-the-data-you-need"></a>Tartsa meg a szükséges adatgyűjtést
A **pg_qs. retention_period_in_days** paraméter a lekérdezési tár adatmegőrzési időszakát napokban adja meg. A rendszer törli a régebbi lekérdezési és statisztikai adatokat. Alapértelmezés szerint a Query Store úgy van konfigurálva, hogy 7 napig őrizze meg az adattárolást. Kerülje a nem használt korábbi adatszolgáltatások megőrzését. Ha továbbra is meg kell őriznie az adatmennyiséget, növelje az értéket.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>A várakozási statisztika mintavételezési gyakoriságának beállítása 
A **pgms_wait_sampling. history_period** paraméter azt határozza meg, hogy milyen gyakran (ezredmásodpercben) várakozik az események mintavétele. Minél rövidebb az időszak, annál gyakoribb a mintavételezés. A rendszer további információkat kér le, de az erőforrás-felhasználás nagyobb költségeket is tartalmaz. Növelje ezt az időszakot, ha a kiszolgáló terhelés alatt van, vagy nincs szüksége a részletességre


## <a name="get-quick-insights-into-query-store"></a>Gyors betekintést kaphat a lekérdezési tárolóba
A Azure Portal [lekérdezési terheléselemző](concepts-query-performance-insight.md) használatával gyorsan betekintést nyerhet a lekérdezési tárolóban található adatelemzésbe. A vizualizációk felveszik a leghosszabb ideig futó lekérdezéseket és a leghosszabb várakozási eseményeket.

## <a name="next-steps"></a>További lépések
- Megtudhatja, hogyan kérhet le vagy állíthat be paramétereket a [Azure Portal](howto-configure-server-parameters-using-portal.md) vagy az [Azure CLI](howto-configure-server-parameters-using-cli.md)használatával.