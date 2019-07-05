---
title: Azure Database for MySQL a Query Store
description: Ez a cikk ismerteti a Query Store szolgáltatás az Azure Database for MySQL-hez
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: a24bba0786201f4ea1d1be431107f7bfe26a2a8f
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461721"
---
# <a name="monitor-azure-database-for-mysql-performance-with-query-store"></a>Azure Database Query Store a MySQL teljesítményének figyeléséhez

**A következőkre vonatkozik:**  Azure Database for MySQL 5.7.

> [!NOTE]
> Query Store az előzetes verzióban.

Az Azure Database for MySQL-hez a Query Store szolgáltatás lehetővé teszi nyomon követheti a lekérdezési teljesítmény az idő függvényében. Query Store egyszerűbbé teszi a teljesítménnyel kapcsolatos hibaelhárítás segít gyorsan található leghosszabban futó és a leginkább erőforrás-igényes lekérdezéseket. Query Store automatikusan rögzíti a lekérdezések és a futásidejű statisztikája előzményeit, és megőrzi őket a felülvizsgálatra. Elválasztja a idő Windows adatokat, így láthatja, hogy az adatbázis használati mintái. Az összes felhasználó, adatbázisok és lekérdezések tárolódik a **mysql** séma-adatbázis az Azure Database for MySQL-példányt.

## <a name="common-scenarios-for-using-query-store"></a>Általános forgatókönyvek a Query Store használata

A lekérdezéstár használható számos forgatókönyv esetében, beleértve a következőket:

- Romlott lekérdezések észlelése
- Amely meghatározza, hogy hányszor egy lekérdezést hajtott egy adott időtartományban
- A lekérdezés átlagos végrehajtási idő összehasonlítása a különböző időtartományok nagy változásokat megtekintéséhez

## <a name="enabling-query-store"></a>Query Store engedélyezése

Query Store az választható szolgáltatása, így azt nem aktív, alapértelmezés szerint a kiszolgálón. A lekérdezéstár engedélyezve van, vagy az egy adott kiszolgáló összes adatbázisára globálisan letiltani, és nem kapcsolható be- vagy kikapcsolása adatbázisonként.

### <a name="enable-query-store-using-the-azure-portal"></a>Engedélyezze a Query Store az Azure portal használatával

1. Jelentkezzen be az Azure Portalon, és válassza ki az Azure Database for MySQL-kiszolgálóhoz.
1. Válassza ki **kiszolgáló paramétereinek** a a **beállítások** a menü részét.
1. Keresse meg a query_store_capture_mode paramétert.
1. Állítsa az értékét az összes és **mentése**.

Várja meg a statisztika a Query Store engedélyezése:

1. Keresse meg a query_store_wait_sampling_capture_mode paramétert.
1. Állítsa az értékét az összes és **mentése**.

Lehetővé teszi az adatok megőrzéséhez a mysql-adatbázisban az első köteg akár 20 percig.

## <a name="information-in-query-store"></a>A Query Store információk

Query Store két tárolók rendelkezik:

- A lekérdezés-végrehajtás statisztikai adatok megőrzése tárolni egy futásidejének statisztikai adatait.
- Várjon, amíg egy várakozási statisztika üzlet megőrzése statisztikai adatok.

Lemezterület-használat minimalizálása érdekében a futásidejű végrehajtási statisztika a futásidejű statisztikai tárolójában időkeretben egy rögzített, konfigurálható vannak összesítve. Ezekkel az áruházakkal található információk lekérdezésével a query store nézetek jelenik meg.

A következő lekérdezést a lekérdezésekkel kapcsolatos információkat a Query Store adja vissza:

```sql
SELECT * FROM mysql.query_store;
```

Vagy a lekérdezés várakozási statistics:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Keresés várakozási lekérdezések

Várjon eseménytípusok különböző várakozási események egyesítendő hasonló a gyűjtőkbe. Query Store biztosít a várakozási esemény típusa, bizonyos várakozási esemény neve és a szóban forgó lekérdezés. Folyamatban van az alkalmazáslogikának, ezeket várja meg az információkat a lekérdezés futásidejű statisztikai azt jelenti, hogy mi járul hozzá, hogy a lekérdezési teljesítményt nyújt egy mélyrehatóbb ismereteket szerezhet.

Az alábbiakban néhány példát, hogyan szerezhet a számítási feladatokhoz, a várakozási statisztikák a Query Store további betekintést:

| **Megfigyelés** | **Művelet** |
|---|---|
|Magas zárolás várakozik | Ellenőrizze a lekérdezés szövegét az érintett lekérdezések, és a cél entitások azonosítása. Keresse meg a Query Store más lekérdezések módosítása ugyanazon entitás, amely gyakran hajtja végre, és/vagy nagy időtartama van. Ezek a lekérdezések azonosítása, után fontolja meg az alkalmazáslogika egyidejűségi javítására módosítja, vagy egy kevésbé korlátozó elkülönítési szintet használja. |
|Magas puffer i/o-várakozások | A lekérdezések nagy számú fizikai olvasások található Query Store. Ha azok megfelelnek a lekérdezések magas i/o-vár, fontolja meg a vizsgálatok helyett az alapul szolgáló entitásra, ehhez index bemutatása kér. Ez lenne a i/o-terhelést lekérdezések minimalizálása érdekében. Ellenőrizze a **teljesítménnyel kapcsolatos javaslatok** a kiszolgáló a portálon, hogy vannak-e a kiszolgáló, amely akkor lekérdezések optimalizálásához ajánlott indexek megtekintéséhez. |
|Magas memória vár | A felső memória-igényes lekérdezéseket a Query Store találja. Ezek a lekérdezések vannak valószínűleg késleltetése további az érintett lekérdezések előrehaladását. Ellenőrizze a **teljesítménnyel kapcsolatos javaslatok** a kiszolgáló a portálon, hogy vannak-e ezeket a lekérdezéseket szeretné optimalizálni ajánlott indexek megtekintéséhez.|

## <a name="configuration-options"></a>Beállítási lehetőségek

Ha engedélyezve van a Query Store a 15 perces összesítési windows rendszerben legfeljebb 500 különböző lekérdezések száma ablak menti adatokat.

A következő beállítások konfigurálásához a Query Store paraméterek érhetők el.

| **A paraméter** | **Leírás** | **Alapértelmezett** | **Címtartomány** |
|---|---|---|---|
| query_store_capture_mode | Kapcsolja be a lekérdezés store szolgáltatás be-és kikapcsolása az érték alapján. Megjegyzés: Ha performance_schema OFF, query_store_capture_mode bekapcsolása bekapcsolása performance_schema és a egy részét ez a szolgáltatás számára szükséges teljesítményt séma eszközök. | AZ ÖSSZES | NINCS, AZ ÖSSZES |
| query_store_capture_interval | A lekérdezéstár rögzítése időköz percben. Meg lehet adni az időköz, amelyben a lekérdezés metrikák összesítése | 15 | 5 - 60 |
| query_store_capture_utility_queries | Kapcsolja be vagy ki BEÁLLÍTÁST a rendszer végrehajtja az összes segédprogram lekérdezés rögzítése. | NO | IGEN, NINCS |
| query_store_retention_period_in_days | Ez alatt az időszak található a lekérdezéstárban az adatok megőrzése (nap). | 7 | 1 - 30 |

A következő beállítások kifejezetten statisztika várjon vonatkoznak.

| **A paraméter** | **Leírás** | **Alapértelmezett** | **Címtartomány** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Lehetővé teszi, hogy kapcsolja be / ki a várakozási statisztikák. | EGYIK SEM | NINCS, AZ ÖSSZES |
| query_store_wait_sampling_frequency | Wait-mintavétel másodpercek alatt Alters gyakoriságát. 5 és 300 másodperc. | 30 | 5-300 |

> [!NOTE]
> Jelenleg **query_store_capture_mode** felülírja ezt a konfigurációt, ami azt jelenti, mindkettő **query_store_capture_mode** és **query_store_wait_sampling_capture_mode** az összes várakozási statistics működéséhez engedélyezni kell. Ha **query_store_capture_mode** ki van kapcsolva, akkor várjon statisztika ki van kapcsolva, valamint óta várakozási statisztika a performance_schema engedélyezve van, és a lekérdezéstár által rögzített akkor használja.

Használja a [az Azure portal](howto-server-parameters.md) vagy [Azure CLI-vel](howto-configure-server-parameters-using-cli.md) beolvasni vagy megadni egy másik értéket a paraméterhez.

## <a name="views-and-functions"></a>Nézetek és függvények

Megtekintheti és kezelheti a Query Store a következő nézetek és függvények használatával. Szervezeten belül bárki a [válassza jogosultság nyilvános szerepkör](howto-create-users.md#how-to-create-additional-admin-users-in-azure-database-for-mysql) ezek a nézetek segítségével a Query Store adatait. Ezek a nézetek csak állnak rendelkezésre a **mysql** adatbázis.

Lekérdezések vannak normalizált literálok és konstansok eltávolítása után azok szerkezetét megtekintésével. Ha két lekérdezést konstansértékekkel kivételével azonosak, rendelkeznek azonos a kivonata.

### <a name="mysqlquerystore"></a>MySQL.query_store

Ez a nézet az összes adat Query Store adja vissza. Az egyes különálló adatbázisok azonosító, a felhasználói Azonosítót és a lekérdezés azonosítóját. egy sor van

| **Name** | **Adattípus** | **IS_NULLABLE** | **Leírás** |
|---|---|---|---|
| `schema_name`| varchar(64) | NO | A séma neve |
| `query_id`| bigint(20) | NO| Az adott lekérdezés jön létre, ha ugyanabból a lekérdezés végrehajtja a különböző séma, új azonosító egyedi Azonosítót hoz létre. |
| `timestamp_id` | timestamp| NO| A Timestamp, amelyben a lekérdezés végrehajtása. Ez a konfiguráció alapján a query_store_interval|
| `query_digest_text`| LONGTEXT| NO| Az összes literálok eltávolítása után a normalizált lekérdezés szövege|
| `query_sample_text` | LONGTEXT| NO| A tényleges lekérdezés literálok az első megjelenés|
| `query_digest_truncated` | bit| IGEN| Hogy a lekérdezés szövege csonkolva lettek. Ha a lekérdezés 1 KB-nál nagyobb érték lesz Igen|
| `execution_count` | bigint(20)| NO| A lekérdezés végrehajtása van, az időbélyegző-azonosító / a konfigurált időköz időszakban száma|
| `warning_count` | bigint(20)| NO| Ez a lekérdezés a belső során létrehozott figyelmeztetések száma|
| `error_count` | bigint(20)| NO| A lekérdezési időköze alatt kerülne sor generált hibák száma|
| `sum_timer_wait` | double| IGEN| A lekérdezési időköze alatt kerülne sor teljes végrehajtási idő|
| `avg_timer_wait` | double| IGEN| A lekérdezési időköze alatt kerülne sor átlagos végrehajtási ideje|
| `min_timer_wait` | double| IGEN| Ez a lekérdezés minimális végrehajtási ideje|
| `max_timer_wait` | double| IGEN| Maximális végrehajtási ideje|
| `sum_lock_time` | bigint(20)| NO| Idő telik el a lekérdezés végrehajtása során ez alatt az időszak a zárolások teljes mennyisége|
| `sum_rows_affected` | bigint(20)| NO| Érintett sorok száma|
| `sum_rows_sent` | bigint(20)| NO| Az ügyfélnek küldött sorok száma|
| `sum_rows_examined` | bigint(20)| NO| Vizsgált sorok száma|
| `sum_select_full_join` | bigint(20)| NO| Teljes JOIN illesztések számát|
| `sum_select_scan` | bigint(20)| NO| Válassza ki a vizsgálatok száma |
| `sum_sort_rows` | bigint(20)| NO| Rendezi a sorok száma|
| `sum_no_index_used` | bigint(20)| NO| Ha a lekérdezés nem használta indexek hányszor|
| `sum_no_good_index_used` | bigint(20)| NO| Ha a lekérdezést végrehajtó motor nem használja bármely megfelelő indexek hányszor|
| `sum_created_tmp_tables` | bigint(20)| NO| Létrehozott ideiglenes táblák teljes száma|
| `sum_created_tmp_disk_tables` | bigint(20)| NO| Lemez (állít elő, i/o) létrehozott ideiglenes táblák teljes száma|
| `first_seen` | timestamp| NO| A első előfordulása (UTC) a lekérdezés az összesítési időszak alatt|
| `last_seen` | timestamp| NO| A utolsó előfordulása (UTC) a lekérdezés e összesítési időszak|

### <a name="mysqlquerystorewaitstats"></a>MySQL.query_store_wait_stats

Ez a nézet értéket ad vissza a Query Store eseményadatai várjon. Az egyes különböző adatbázis-azonosító, a felhasználói azonosító, a lekérdezés Azonosítóját és a esemény egy sor van.

| **Name**| **Adattípus** | **IS_NULLABLE** | **Leírás** |
|---|---|---|---|
| `interval_start` | timestamp | NO| Az intervallum (15 perces növekmény) kezdete|
| `interval_end` | timestamp | NO| Intervallum (15 perces növekmény)|
| `query_id` | bigint(20) | NO| A normalizált Query (lekérdezés áruházból) létrehozott egyedi azonosító|
| `query_digest_id` | varchar(32) | NO| A normalizált lekérdezés szövege (a query store) literálok lehetnek eltávolítása után |
| `query_digest_text` | LONGTEXT | NO| A tényleges lekérdezés (a query store) literálok az első megjelenés |
| `event_type` | varchar(32) | NO| A várakozási esemény kategóriája |
| `event_name` | varchar(128) | NO| A várakozási esemény neve |
| `count_star` | bigint(20) | NO| A mintavétel esetében a lekérdezési időköze alatt kerülne sor várakozási események száma |
| `sum_timer_wait_ms` | double | NO| Teljes várakozási idő (ezredmásodpercben) a lekérdezési időköze alatt kerülne sor |

### <a name="functions"></a>Functions

| **Name**| **Leírás** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | A megadott időbélyeg előtt az összes lekérdezés tároló adatainak kiürítése |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Az összes pon várjon eseményadatok előtt az adott időbélyeg |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Javaslatok, amelynek érvényessége a megadott időbélyeg előtt van kiürítése |

## <a name="limitations-and-known-issues"></a>Korlátozások és ismert problémák

- Ha egy MySQL-kiszolgálót a paraméter `default_transaction_read_only` , Query Store adatok rögzítése nem tudja.
- Query Store funkciókat is lehet megszakítani, ha találkozik hosszú Unicode-lekérdezések (\>= 6000 bájt).
- Várakozási statisztika megőrzési időtartama 24 órán keresztül.
- Várjon statisztika használ minta ti rögzítési események töredékéért. A gyakoriság a paraméter használatával módosítható `query_store_wait_sampling_frequency`.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [lekérdezési teljesítmény Elemzéseihez](concepts-query-performance-insight.md)