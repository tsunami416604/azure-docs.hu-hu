---
title: Ajánlott eljárások Query Store az Azure Database for postgresql-hez
description: Ez a cikk ismerteti a gyakorlati tanácsok a Query Store az Azure Database for PostgreSQL-hez.
services: postgresql
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: c449aaff9741250cfcfaaa4ee490aafc857c4369
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47395304"
---
# <a name="best-practices-for-query-store"></a>Ajánlott eljárások a Query Store

**A következőkre vonatkozik:** Azure Database for PostgreSQL 9.6 és 10

> [!IMPORTANT]
> A Query Store szolgáltatás jelenleg nyilvános előzetes verzióban csak korlátozott számú régióban.


Ez a cikk ismerteti az ajánlott eljárások a Query Store az Azure Database for postgresql-hez.

## <a name="set-the-optimal-query-capture-mode"></a>Az optimális lekérdezési rögzítési üzemmód beállítása
Lehetővé teszik a Query Store rögzíteni az adatokat, az Ön számára fontos. 

|**pg_qs.query_capture_mode** | **Forgatókönyv**|
|---|---|
|_Az összes_  |A számítási feladatok alaposan tekintetében az összes lekérdezés és a végrehajtás gyakoriságát és más statisztikai elemzése. A számítási feladatok az új lekérdezések azonosítása. Észlelés, ha ad hoc lekérdezések segítségével lehetőségeket határozhat meg a felhasználó vagy az automatikus paraméterezés. _Az összes_ egy nagyobb erőforrás-használat költség is tartalmaz. |
|_felső_  |A leggyakoribb lekérdezések – ezek az ügyfelek által kibocsátott figyelmet összpontosítanak.
|_Egyik sem_ |Korábban már rögzített egy lekérdezési készlet, és ez alatt az időszak, amely a vizsgálni kívánt, és szeretné, hogy más lekérdezések vezethet be a felesleges kiküszöbölése. _Nincs_ egy megfelelő tesztelési és a teljesítménymérés környezetekben. _Nincs_ kell legyen óvatos, előfordulhat, hogy kihagyná nyomon követésére, és fontos új lekérdezések optimalizálása lehetőséget. Adott múltbeli idő Windows adatok nem állíthatók vissza. |

Query Store áruházbeli várakozási statistics is tartalmaz. Egy további rögzítési mód lekérdezés, amely szabályozza a várakozási statisztika: **pgms_wait_sampling.query_capture_mode** állítható _nincs_ vagy _összes_. 

> [!NOTE] 
> **pg_qs.query_capture_mode** felülír **pgms_wait_sampling.query_capture_mode**. Ha a pg_qs.query_capture_mode _none_, a pgms_wait_sampling.query_capture_mode beállítás nem befolyásolja. 


## <a name="keep-the-data-you-need"></a>A szükséges adatok megtartása
A **pg_qs.retention_period_in_days** paraméter adja meg a Query Store az Adatmegőrzés időtartama napokban. Régebbi lekérdezési és statisztikai adat törlődik. Alapértelmezés szerint a Query Store az adatok megőrzése 7 napig van konfigurálva. Elkerülése érdekében, hogy nem szeretné használni korábbi adatok megőrzése mellett. Növelje az értéket, ha szeretné megőrizni az adatokat hosszabb.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>Állítsa be a várakozási statisztikái mintavétel gyakorisága 
A **pgms_wait_sampling.history_period** paraméter gyakoriságát határozza meg (ezredmásodpercben) várakozási esemény történik az adatgyűjtés. Minél rövidebb az időszak, annál a mintavételezés gyakran használják. További információt a rendszer lekéri, de a nagyobb erőforrás-használat költségeit, amely tartalmaz. Növelje az ebben az időszakban, ha a kiszolgáló terhelés alatt van, vagy nincs szükség a granularitási


## <a name="get-quick-insights-into-query-store"></a>Gyors elemzéseket kaphat a Query Store
Használhat [lekérdezési Terheléselemző](concepts-query-performance-insight.md) gyors elemzéseket kaphat az adatok beolvasása a Query Store az Azure Portalon. A Vizualizációk a leghosszabb futó lekérdezések tervezőfelületére, és a leghosszabb várjon az események idővel.

## <a name="next-steps"></a>További lépések
- Ismerje meg, vagy állítsa be a paraméterek használatával a [az Azure portal](howto-configure-server-parameters-using-portal.md) vagy a [Azure CLI-vel](howto-configure-server-parameters-using-cli.md).