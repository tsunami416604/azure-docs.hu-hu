---
title: A Query Store gyakorlati tanácsai a PostgreSQL Azure Database szolgáltatásában – Egykiszolgálós
description: Ez a cikk a PostgreSQL - Single Server Azure Database Query Store ajánlott eljárásokat ismerteti.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 51239f4cf49784dd47470e1272b90508eaf25e6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70764234"
---
# <a name="best-practices-for-query-store"></a>Gyakorlati tanácsok a Lekérdezéstárolóhoz

**A következőkre vonatkozik:** Azure Database for PostgreSQL – Egykiszolgálós verzió9.6, 10, 11

Ez a cikk ismerteti a Query Store használatával kapcsolatos gyakorlati tanácsokat a PostgreSQL Azure Database-ben.

## <a name="set-the-optimal-query-capture-mode"></a>Az optimális lekérdezésrögzítési mód beállítása
Hagyja, hogy a Query Store rögzítse az Ön számára fontos adatokat. 

|**pg_qs,query_capture_mode** | **Forgatókönyv**|
|---|---|
|_Minden_  |Alaposan elemezze a számítási feladatokat az összes lekérdezés és azok végrehajtási gyakorisága és egyéb statisztikák tekintetében. Azonosítsa az új lekérdezéseket a munkaterhelésben. Annak észlelése, hogy az ad hoc lekérdezések segítségével azonosítsa a felhasználói vagy automatikus paraméterezés lehetőségeit. _Minden_ jön-val egy megnövelt erőforrás-felhasználás költsége. |
|_Fent_  |Összpontosítsa figyelmét a legnépszerűbb lekérdezésekre - az ügyfelek által kiadott kérdésekre.
|_Nincs_ |Már rögzített egy lekérdezéskészletet és egy időablakot, amelyet meg szeretne vizsgálni, és meg szeretné szüntetni a többi lekérdezés által bevezetett zavaró tényezőket. _Egyik sem_ alkalmas tesztelési és pad-jelölési környezetben. _Egyik et sem_ szabad óvatosan használni, mivel előfordulhat, hogy elszalasztja a fontos új lekérdezések nyomon követésének és optimalizálásának lehetőségét. Az előző időablakok adatait nem lehet helyreállítani. |

A Query Store egy tárolót is tartalmaz a várakozási statisztikákhoz. Van egy további rögzítési módú lekérdezés, amely a várakozási statisztikákat szabályozza: **pgms_wait_sampling.query_capture_mode** beállítható _none_ vagy _all (A_rögzítési mód ) beállítására. 

> [!NOTE] 
> **pg_qs.query_capture_mode** **pgms_wait_sampling.query_capture_mode**. Ha pg_qs.query_capture_mode _nincs,_ a pgms_wait_sampling.query_capture_mode beállításnak nincs hatása. 


## <a name="keep-the-data-you-need"></a>A szükséges adatok megtartása
A **pg_qs.retention_period_in_days** paraméter napokban adja meg a Query Store adatmegőrzési időszakát. A régebbi lekérdezési és statisztikai adatok törlődnek. Alapértelmezés szerint a Query Store úgy van beállítva, hogy az adatokat 7 napig őrizze meg. Kerülje a nem használni kívánt előzményadatok megőrzését. Növelje az értéket, ha hosszabb ideig kell tartania az adatokat.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>A várakozási statisztikák mintavételezésének gyakoriságának beállítása 
A **pgms_wait_sampling.history_period** paraméter azt adja meg, hogy a várakozási események mintavételezése milyen gyakran (ezredmásodpercben) történik. Minél rövidebb az időszak, annál gyakoribb a mintavétel. További információk beolvasása, de ez a költségek nagyobb erőforrás-felhasználás. Növelje ezt az időszakot, ha a kiszolgáló terhelés alatt van, vagy nincs szüksége a részletességre


## <a name="get-quick-insights-into-query-store"></a>Gyors információk a Query Store-ba
A [Query Performance Insight](concepts-query-performance-insight.md) segítségével az Azure Portalon gyors betekintést az adatok at Query Store. A vizualizációk a leghosszabb ideig futó lekérdezéseket és a leghosszabb várakozási eseményeket adják elő az idő múlásával.

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan szerezhet be vagy állíthat be paramétereket az [Azure Portalon](howto-configure-server-parameters-using-portal.md) vagy az [Azure CLI használatával.](howto-configure-server-parameters-using-cli.md)