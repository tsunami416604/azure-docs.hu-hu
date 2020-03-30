---
title: Query Store – Azure-adatbázis a MariaDB-hez
description: Ismerje meg a Query Store funkciót az Azure Database for MariaDB-ben, amely segít nyomon követni a teljesítményt az idő múlásával.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a502638744009fc34a7f0a27f8034b89d2c8fa26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527809"
---
# <a name="monitor-azure-database-for-mariadb-performance-with-query-store"></a>Az Azure-adatbázis figyelése a MariaDB teljesítményéhez a Query Store segítségével

**A következőkre vonatkozik:** Azure-adatbázis a MariaDB 10.2-hez

A Query Store funkció az Azure Database for Mariadb lehetővé teszi a lekérdezési teljesítmény nyomon követését az idő múlásával. A Query Store leegyszerűsíti a teljesítményhibaelhárítást azáltal, hogy gyorsan megtalálhatja a leghosszabb ideig futó és a legtöbb erőforrás-igényes lekérdezést. A Query Store automatikusan rögzíti a lekérdezések és a futásidejű statisztikák előzményeit, és megőrzi azokat az ellenőrzéshez. Időablakok szerint választja el az adatokat, így láthatja az adatbázis használati mintáit. Az összes felhasználó, adatbázis és lekérdezés adatait a **mysql** séma-adatbázis a MariaDB-példány ban tárolja.

## <a name="common-scenarios-for-using-query-store"></a>Gyakori forgatókönyvek a Query Store használatához

A lekérdezéstároló számos esetben használható, többek között a következőkben:

- Visszafejlődött lekérdezések észlelése
- A lekérdezés adott időablakban történő végrehajtásának számának meghatározása
- A lekérdezés átlagos végrehajtási idejének összehasonlítása az időablakok között a nagy delták megtekintéséhez

## <a name="enabling-query-store"></a>Lekérdezéstároló engedélyezése

A Query Store egy opt-in szolgáltatás, így alapértelmezés szerint nem aktív a kiszolgálón. A lekérdezéstároló globálisan engedélyezve van vagy le van tiltva egy adott kiszolgáló összes adatbázisában, és nem kapcsolható be vagy ki adatbázisonként.

### <a name="enable-query-store-using-the-azure-portal"></a>Lekérdezési tároló engedélyezése az Azure Portalon

1. Jelentkezzen be az Azure Portalon, és válassza ki az Azure Database for MariaDB-kiszolgálót.
1. A menü **Beállítások** szakaszában válassza a **Kiszolgálóparaméterek** lehetőséget.
1. Keresse meg a query_store_capture_mode paramétert.
1. Állítsa az ÉRTÉKET ALL és **Mentés értékre.**

A lekérdezési tárolóban a várakozási statisztikák engedélyezése:

1. Keresse meg a query_store_wait_sampling_capture_mode paramétert.
1. Állítsa az ÉRTÉKET ALL és **Mentés értékre.**

Várjon 20 percet, amíg az adatok első kötege megmarad a mysql adatbázisban.

## <a name="information-in-query-store"></a>Információ a lekérdezéstárolóban

A Query Store két tárolóval rendelkezik:

- A futásidejű statisztikák tárolja a lekérdezés végrehajtási statisztikai adatainak megőrzéséhez.
- A várakozási statisztikák a várakozási statisztikák ra vonatkozó adatokat tárolják.

A helyhasználat minimalizálása érdekében a futásidejű statisztikai tárolóban a futásidejű végrehajtási statisztikák egy rögzített, konfigurálható időablakon keresztül összesíthetők. Az ezekben az üzletekben lévő információk a lekérdezési tároló nézeteinek lekérdezésével láthatók.

A következő lekérdezés a Query Store lekérdezéseivel kapcsolatos információkat ad vissza:

```sql
SELECT * FROM mysql.query_store;
```

Vagy ez a lekérdezés a várakozási statisztikákhoz:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Várakozási lekérdezések keresése

> [!NOTE]
> A várakozási statisztikákat nem szabad engedélyezni a csúcsterhelési órákban, és a bizalmas számítási feladatok esetében határozatlan ideig be kell kapcsolni. <br>A magas cpu-kihasználtsítással vagy alacsonyabb virtuális magokkal konfigurált kiszolgálókon futó számítási feladatok esetén körültekintően járjon el a várakozási statisztikák engedélyezésekor. Nem szabad bekapcsolni a végtelenségig. 

A várakozási eseménytípusok a hasonlóság miatt különböző várakozási eseményeket kombinálnak gyűjtőkben. A Query Store biztosítja a várakozási esemény típusát, a konkrét várakozási esemény nevét és a kérdéses lekérdezést. A várakozási adatok és a lekérdezés futásidejű statisztikái nak korrelációja azt jelenti, hogy mélyebben megértheti, hogy mi járul hozzá a lekérdezés teljesítményjellemzőihez.

Íme néhány példa arra, hogyan szerezhet több betekintést a számítási feladatokba a Query Store várakozási statisztikái nak használatával:

| **Megfigyelés** | **Művelet** |
|---|---|
|High Lock vár | Ellenőrizze az érintett lekérdezések lekérdezésszövegeit, és azonosítsa a célentitásokat. Keresse meg a Query Store-ban az ugyanazon entitást módosító egyéb lekérdezéseket, amelyek gyakran és/vagy nagy időtartamúak. A lekérdezések azonosítása után fontolja meg az alkalmazás logikájának módosítását az egyidejűség javítása érdekében, vagy használjon kevésbé korlátozó elkülönítési szintet. |
|Magas puffer I/O vár | Keresse meg a lekérdezések nagy számú fizikai olvasás a Query Store.Find the queries with a high number of physical reads in Query Store. Ha a lekérdezések egyeznek a magas I/O-várakozás, fontolja meg egy index az alapul szolgáló entitás, a vizsgálatok helyett keres. Ez minimálisra csökkentené a lekérdezések i/o-terhelését. Ellenőrizze a kiszolgáló **teljesítményre vonatkozó javaslatait** a portálon, és ellenőrizze, hogy vannak-e olyan indexjavaslatok ehhez a kiszolgálóhoz, amelyek optimalizálják a lekérdezéseket. |
|Nagy memória vár | Keresse meg a legnépszerűbb memóriafogyasztó lekérdezéseket a Query Store-ban. Ezek a lekérdezések valószínűleg késleltetik az érintett lekérdezések további előrehaladását. Ellenőrizze a **kiszolgáló teljesítményre vonatkozó javaslatait** a portálon, és ellenőrizze, hogy vannak-e olyan indexjavaslatok, amelyek optimalizálnák ezeket a lekérdezéseket.|

## <a name="configuration-options"></a>Beállítási lehetőségek

Ha a Lekérdezéstároló engedélyezve van, az adatokat 15 perces összesítési időszakokba menti, ablakonként legfeljebb 500 különböző lekérdezést.

A Query Store paramétereinek konfigurálásához a következő beállítások érhetők el.

| **Paraméter** | **Leírás** | **Alapértelmezett** | **Tartomány** |
|---|---|---|---|
| query_store_capture_mode | Kapcsolja be/ki a lekérdezéstároló funkciót az érték alapján. Megjegyzés: Ha performance_schema ki van kapcsolva, query_store_capture_mode bekapcsolása bekapcsolja performance_schema és a funkcióhoz szükséges teljesítményséma-eszközök egy részhalmazát. | AZ ÖSSZES | NINCS, MINDEN |
| query_store_capture_interval | A lekérdezés tárolási rögzítési időköz percben. Lehetővé teszi a lekérdezési mutatók összesítésének időközének megadását | 15 | 5 - 60 |
| query_store_capture_utility_queries | Be- vagy kikapcsolás a rendszerben futó összes segédprogram-lekérdezés rögzítéséhez. | NO | IGEN, NEM. |
| query_store_retention_period_in_days | Időablak napokban az adatok lekérdezési tárolóban való megőrzéséhez. | 7 | 1 - 30 |

A következő beállítások kifejezetten a statisztikák várakozására vonatkoznak.

| **Paraméter** | **Leírás** | **Alapértelmezett** | **Tartomány** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Lehetővé teszi a várakozási statisztikák be- és kikapcsolást. | Nincs | NINCS, MINDEN |
| query_store_wait_sampling_frequency | Másodpercek alatt módosítja a várakozási mintavétel gyakoriságát. 5-300 másodperc. | 30 | 5-300 |

> [!NOTE]
> Jelenleg **query_store_capture_mode** felülírja ezt a konfigurációt, ami azt jelenti, hogy mind **a query_store_capture_mode,** mind **a query_store_wait_sampling_capture_mode** engedélyezni kell az ALL számára, hogy a várakozási statisztikák működjenek. Ha **query_store_capture_mode** ki van kapcsolva, akkor a várakozási statisztika is ki van kapcsolva, mivel a várakozási statisztika az engedélyezve lévő performance_schema és a lekérdezéstároló által rögzített query_text használja.

Az [Azure Portal](howto-server-parameters.md) használatával egy másik értéket kaphat le vagy állíthat be egy paraméterhez.

## <a name="views-and-functions"></a>Nézetek és függvények

A Query Store megtekintése és kezelése a következő nézetek és függvények használatával. A [nyilvános kijelölési jogosultságú szerepkörben](howto-create-users.md#create-additional-admin-users) bárki használhatja ezeket a nézeteket a Query Store-ban lévő adatok megtekintéséhez. Ezek a nézetek csak a **mysql** adatbázisban érhetők el.

A lekérdezések normalizálva vannak a szerkezetük megvizsgálásával a konstansok és állandók eltávolítása után. Ha két lekérdezés azonos, kivéve a konstans értékeket, akkor ugyanaz tfogja ugyanazt a kivonatot.

### <a name="mysqlquery_store"></a>mysql.query_store

Ez a nézet a Query Store összes adatát adja vissza. Minden egyes különálló adatbázis-azonosítóhoz, felhasználói azonosítóhoz és lekérdezésazonosítóhoz egy sor tartozik.

| **Név** | **Adattípus** | **IS_NULLABLE** | **Leírás** |
|---|---|---|---|
| `schema_name`| varchar (64) | NO | A séma neve |
| `query_id`| bigint (20) | NO| Az adott lekérdezéshez létrehozott egyedi azonosító, ha ugyanaz a lekérdezés különböző sémában hajt végre, a rendszer új azonosítót hoz létre |
| `timestamp_id` | időbélyeg| NO| Időbélyeg, amelyben a lekérdezés végrehajtása történik. Ez a query_store_interval konfiguráción alapul.|
| `query_digest_text`| hosszú szöveg| NO| A normalizált lekérdezés szövege az összes konstans eltávolítása után|
| `query_sample_text` | hosszú szöveg| NO| A tényleges lekérdezés első megjelenése konstansokkal|
| `query_digest_truncated` | bit| IGEN| Azt jelzi, hogy a lekérdezés szövege csonkolva lett-e. Az érték Igen lesz, ha a lekérdezés hosszabb, mint 1 KB|
| `execution_count` | bigint (20)| NO| Az időbélyeg-azonosítóhoz / a beállított időköz-időszakban a lekérdezés végrehajtása ianumber|
| `warning_count` | bigint (20)| NO| A lekérdezés által a belső ellenőrzés során generált figyelmeztetések száma|
| `error_count` | bigint (20)| NO| A lekérdezés által az időköz során generált hibák száma|
| `sum_timer_wait` | double| IGEN| A lekérdezés teljes végrehajtási ideje az intervallum ban|
| `avg_timer_wait` | double| IGEN| A lekérdezés átlagos végrehajtási ideje az intervallum ban|
| `min_timer_wait` | double| IGEN| A lekérdezés minimális végrehajtási ideje|
| `max_timer_wait` | double| IGEN| Maximális végrehajtási idő|
| `sum_lock_time` | bigint (20)| NO| A lekérdezés végrehajtásához használt összes zárolásra fordított teljes idő ebben az időszakban|
| `sum_rows_affected` | bigint (20)| NO| Érintett sorok száma|
| `sum_rows_sent` | bigint (20)| NO| Az ügyfélnek küldött sorok száma|
| `sum_rows_examined` | bigint (20)| NO| Megvizsgált sorok száma|
| `sum_select_full_join` | bigint (20)| NO| Teljes illesztések száma|
| `sum_select_scan` | bigint (20)| NO| A kiválasztott vizsgálatok száma |
| `sum_sort_rows` | bigint (20)| NO| Rendezett sorok száma|
| `sum_no_index_used` | bigint (20)| NO| Azon alkalmak száma, amikor a lekérdezés nem használt indexeket|
| `sum_no_good_index_used` | bigint (20)| NO| Azon alkalmak száma, amikor a lekérdezés-végrehajtási motor nem használt jó indexeket|
| `sum_created_tmp_tables` | bigint (20)| NO| Létrehozott ideiglenes táblák teljes száma|
| `sum_created_tmp_disk_tables` | bigint (20)| NO| A lemezben létrehozott ideiglenes táblák teljes száma (I/O-t hoz létre)|
| `first_seen` | időbélyeg| NO| A lekérdezés első előfordulása (UTC) az összesítési ablakban|
| `last_seen` | időbélyeg| NO| A lekérdezés utolsó előfordulása (UTC) ebben az összesítési ablakban|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

Ez a nézet várakozási események adatait adja vissza a Query Store-ban. Minden egyes különálló adatbázis-azonosítóhoz, felhasználói azonosítóhoz, lekérdezésazonosítóhoz és eseményhez egy sor tartozik.

| **Név**| **Adattípus** | **IS_NULLABLE** | **Leírás** |
|---|---|---|---|
| `interval_start` | időbélyeg | NO| Az intervallum kezdete (15 perces növekmény)|
| `interval_end` | időbélyeg | NO| Az intervallum vége (15 perces növekmény)|
| `query_id` | bigint (20) | NO| Létrehozott egyedi azonosító a normalizált lekérdezésen (lekérdezéstárolóból)|
| `query_digest_id` | varchar [32] | NO| A normalizált lekérdezés szövege az összes konstans eltávolítása után (a lekérdezéstárolóból) |
| `query_digest_text` | hosszú szöveg | NO| A tényleges lekérdezés első megjelenése konstansokkal (lekérdezéstárolóból) |
| `event_type` | varchar [32] | NO| A várakozási esemény kategóriája |
| `event_name` | varchar(128) | NO| A várakozási esemény neve |
| `count_star` | bigint (20) | NO| A lekérdezés időközében mintavételezett várakozási események száma |
| `sum_timer_wait_ms` | double | NO| A lekérdezés teljes várakozási ideje (ezredmásodpercben) az időköz alatt |

### <a name="functions"></a>Functions

| **Név**| **Leírás** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Az összes lekérdezési táradat törlése a megadott időbélyegző előtt |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Az összes várakozási esemény adatának törlése a megadott időbélyegző előtt |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Törli azokat az ajánlásokat, amelyek lejárata az adott időbélyegző előtt van |

## <a name="limitations-and-known-issues"></a>Korlátozások és ismert problémák

- Ha a MariaDB-kiszolgálón van a paraméter, `default_transaction_read_only` a Query Store nem tud adatokat rögzíteni.
- A Lekérdezéstároló funkció megszakítható, ha hosszú\>Unicode-lekérdezésekkel találkozik ( = 6000 bájt).
- A várakozási statisztikák megőrzési ideje 24 óra.
- Wait statisztika használ minta ti elfog egy töredéke az események. A frekvencia a paraméterrel `query_store_wait_sampling_frequency`módosítható.

## <a name="next-steps"></a>További lépések

- További információ a [Query Performance Insights](concepts-query-performance-insight.md) ról
