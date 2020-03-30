---
title: Query Store - Azure-adatbázis a PostgreSQL-hez – Egyetlen kiszolgáló
description: Ez a cikk az Azure Database for PostgreSQL – Single Server Lekérdezéstároló szolgáltatását ismerteti.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: ccc503e6718ee8f516920cfbea3ad86e7ed81d84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768265"
---
# <a name="monitor-performance-with-the-query-store"></a>Teljesítmény figyelése a Lekérdezéstárolóval

**A következőkre vonatkozik:** Azure Database for PostgreSQL – Egykiszolgálós verzió9.6, 10, 11

A Query Store szolgáltatás az Azure Database for PostgreSQL segítségével nyomon követheti a lekérdezési teljesítményt az idő múlásával. A Query Store leegyszerűsíti a teljesítményhibaelhárítást azáltal, hogy gyorsan megtalálhatja a leghosszabb ideig futó és a legtöbb erőforrás-igényes lekérdezést. A Query Store automatikusan rögzíti a lekérdezések és a futásidejű statisztikák előzményeit, és megőrzi azokat az ellenőrzéshez. Időablakok szerint választja el az adatokat, így láthatja az adatbázis használati mintáit. Az összes felhasználó, adatbázis és lekérdezés adatait egy **azure_sys** nevű adatbázis tárolja az Azure Database for PostgreSQL-példányban.

> [!IMPORTANT]
> Ne módosítsa a **azure_sys** adatbázist vagy annak sémáját. Ezzel megakadályozza a Query Store és a kapcsolódó teljesítményfunkciók megfelelő működését.

## <a name="enabling-query-store"></a>Lekérdezéstároló engedélyezése
A Query Store egy opt-in szolgáltatás, így alapértelmezés szerint nem aktív a kiszolgálón. Az üzlet globálisan engedélyezve van vagy le van tiltva egy adott kiszolgáló összes adatbázisában, és nem kapcsolható be vagy ki adatbázisonként.

### <a name="enable-query-store-using-the-azure-portal"></a>Lekérdezési tároló engedélyezése az Azure Portalon
1. Jelentkezzen be az Azure Portalon, és válassza ki az Azure Database for PostgreSQL-kiszolgálót.
2. A menü **Beállítások** szakaszában válassza a **Kiszolgálóparaméterek** lehetőséget.
3. Keresse meg `pg_qs.query_capture_mode` a paramétert.
4. Állítsa az `TOP` értéket és a **Mentés értéket.**

A lekérdezési tárolóban a várakozási statisztikák engedélyezése: 
1. Keresse meg `pgms_wait_sampling.query_capture_mode` a paramétert.
1. Állítsa az `ALL` értéket és a **Mentés értéket.**


Azt is beállíthatja, hogy ezek a paraméterek az Azure CLI használatával.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

Engedélyezze az első köteg adat 20 percig történő tárolását az azure_sys adatbázisban.

## <a name="information-in-query-store"></a>Információ a lekérdezéstárolóban
A Query Store két tárolóval rendelkezik:
- A lekérdezés végrehajtási statisztikáiadatainak megőrzéséhez futóidő-statisztika tároló.
- Várakozási statisztika tároló a várakozási statisztikák ravonatkozó adataihoz.

A Query Store használatának gyakori forgatókönyvei a következők:
- A lekérdezés adott időablakban történő végrehajtásának számának meghatározása
- A lekérdezés átlagos végrehajtási idejének összehasonlítása az időablakok között a nagy delták megtekintéséhez
- Az elmúlt X óra leghosszabb ideig futó lekérdezéseinek azonosítása
- Az erőforrásokra várakozó legfontosabb N-lekérdezések azonosítása
- Egy adott lekérdezés várakozási jellegének megértése

A helyhasználat minimalizálása érdekében a futásidejű állapotstatisztikák ban a futásidejű állapotok tárházának futásidejű végrehajtási statisztikái egy rögzített, konfigurálható időablakon keresztül kerülnek összesítésre. Az ezekben az üzletekben lévő információk a lekérdezési tároló nézeteinek lekérdezésével láthatók.

## <a name="access-query-store-information"></a>Az Access Lekérdezési tároló adatai

A Query Store-adatok a Postgres kiszolgálón lévő azure_sys adatbázisban tárolódnak. 

A következő lekérdezés a Query Store lekérdezéseivel kapcsolatos információkat ad vissza:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Vagy ez a lekérdezés a várakozási statisztika:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

A Query Store-adatokat az [Azure Monitor Naplók](../azure-monitor/log-query/log-query-overview.md) elemzési és riasztási, Event Hubs streamelésre és az Azure Storage archiválási. A konfigurálandó naplókategóriák a **QueryStoreRuntimeStatistics** és **a QueryStoreWaitStatistics**. A telepítésről az [Azure Monitor diagnosztikai beállításokról](../azure-monitor/platform/diagnostic-settings.md) szóló cikkben olvashat.


## <a name="finding-wait-queries"></a>Várakozási lekérdezések keresése
A várakozási eseménytípusok a hasonlóság miatt különböző várakozási eseményeket kombinálnak gyűjtőkben. A Query Store biztosítja a várakozási esemény típusát, a konkrét várakozási esemény nevét és a kérdéses lekérdezést. A várakozási adatok és a lekérdezés futásidejű statisztikái nak korrelációja azt jelenti, hogy mélyebben megértheti, hogy mi járul hozzá a lekérdezés teljesítményjellemzőihez.

Íme néhány példa arra, hogyan szerezhet több betekintést a számítási feladatokba a Query Store várakozási statisztikái nak használatával:

| **Megfigyelés** | **Művelet** |
|---|---|
|High Lock vár | Ellenőrizze az érintett lekérdezések lekérdezésszövegeit, és azonosítsa a célentitásokat. Keresse meg a Query Store-ban az ugyanazon entitást módosító egyéb lekérdezéseket, amelyek gyakran és/vagy nagy időtartamúak. A lekérdezések azonosítása után fontolja meg az alkalmazás logikájának módosítását az egyidejűség javítása érdekében, vagy használjon kevésbé korlátozó elkülönítési szintet.|
| Magas puffer I/O vár | Keresse meg a lekérdezések nagy számú fizikai olvasás a Query Store.Find the queries with a high number of physical reads in Query Store. Ha a lekérdezések egyeznek a magas I/O várakozás, fontolja meg egy index bevezetése az alapul szolgáló entitás, annak érdekében, hogy nem keres, hanem a vizsgálatok. Ez minimálisra csökkentené a lekérdezések i/o-terhelését. Ellenőrizze a kiszolgáló **teljesítményre vonatkozó javaslatait** a portálon, és ellenőrizze, hogy vannak-e olyan indexjavaslatok ehhez a kiszolgálóhoz, amelyek optimalizálják a lekérdezéseket.|
| Nagy memória vár | Keresse meg a legnépszerűbb memóriafogyasztó lekérdezéseket a Query Store-ban. Ezek a lekérdezések valószínűleg késleltetik az érintett lekérdezések további előrehaladását. Ellenőrizze a **kiszolgáló teljesítményre vonatkozó javaslatait** a portálon, és ellenőrizze, hogy vannak-e olyan indexjavaslatok, amelyek optimalizálnák ezeket a lekérdezéseket.|

## <a name="configuration-options"></a>Beállítási lehetőségek
Ha a Lekérdezéstároló engedélyezve van, az adatokat 15 perces összesítési időszakokba menti, ablakonként legfeljebb 500 különböző lekérdezést. 

A Query Store paramétereinek konfigurálásához a következő beállítások érhetők el.

| **Paraméter** | **Leírás** | **Alapértelmezett** | **Tartomány**|
|---|---|---|---|
| pg_qs,query_capture_mode | Beállítja a nyomon követett állításokat. | Nincs | nincs, felül, minden |
| pg_qs,max_query_text_length | Beállítja a mentett maximális lekérdezési hosszt. A hosszabb lekérdezések csonkolva lesznek. | 6000 | 100 - 10ezer |
| pg_qs.retention_period_in_days | Beállítja a megőrzési időszakot. | 7 | 1 - 30 |
| pg_qs.track_utility | Beállítja, hogy a segédprogram-parancsok nyomon követhetőek-e | itt: | be, ki |

A következő beállítások kifejezetten a statisztikák várakozására vonatkoznak.

| **Paraméter** | **Leírás** | **Alapértelmezett** | **Tartomány**|
|---|---|---|---|
| pgms_wait_sampling.query_capture_mode | Beállítja, hogy mely utasítások kerülnek nyomon a várakozási statisztikákhoz. | Nincs | nincs, minden|
| Pgms_wait_sampling.history_period | Állítsa be azt a gyakoriságot ezredmásodpercben, amikor a várakozási események mintavételezése megtörténik. | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs.query_capture_mode** **pgms_wait_sampling.query_capture_mode**. Ha pg_qs.query_capture_mode NINCS, a pgms_wait_sampling.query_capture_mode beállításnak nincs hatása.


Az [Azure Portalon](howto-configure-server-parameters-using-portal.md) vagy az [Azure CLI-ben](howto-configure-server-parameters-using-cli.md) egy másik értéket kaphat le vagy állíthat be egy paraméterhez.

## <a name="views-and-functions"></a>Nézetek és függvények
A Query Store megtekintése és kezelése a következő nézetek és függvények használatával. A PostgreSQL nyilvános szerepkörében bárki használhatja ezeket a nézeteket a Query Store-ban lévő adatok megtekintéséhez. Ezek a nézetek csak a **azure_sys** adatbázisban érhetők el.

A lekérdezések normalizálva vannak a szerkezetük megvizsgálásával a konstansok és állandók eltávolítása után. Ha két lekérdezés azonos, kivéve a konstans értékeket, akkor ugyanaz tfogja ugyanazt a kivonatot.

### <a name="query_storeqs_view"></a>query_store,qs_view
Ez a nézet a Query Store összes adatát adja vissza. Minden egyes különálló adatbázis-azonosítóhoz, felhasználói azonosítóhoz és lekérdezésazonosítóhoz egy sor tartozik. 

|**Név**   |**Típus** | **Referencia**  | **Leírás**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | Azonosító a runtime_stats_entries táblából|
|user_id    |Oid    |pg_authid.oid  |A nyilatkozatot elvégző felhasználó OID-ja|
|db_id  |Oid    |pg_database.oid    |Annak az adatbázisnak az OID-ja, amelyben az utasítást végrehajtották|
|query_id   |bigint  || Belső kivonatkód, a kimutatás elemzési fájából számítva|
|query_sql_text |Varchar(10000)  || A képviseleti nyilatkozat szövege. Az azonos struktúrával rendelkező különböző lekérdezések csoportosítva vannak; ez a szöveg a fürt első lekérdezésének szövege.|
|plan_id    |bigint |   |A lekérdezésnek megfelelő terv azonosítója, még nem érhető el|
|start_time |időbélyeg  ||  A lekérdezések időgyűjtők szerint összesítve vannak – a gyűjtő időtartama alapértelmezés szerint 15 perc. Ez a bejegyzéshez tartozó időgyűjtőnek megfelelő kezdési időpont.|
|end_time   |időbélyeg  ||  A bejegyzéshez tartozó időgyűjtőnek megfelelő befejezési időpont.|
|Hívások  |bigint  || A végrehajtott lekérdezés ek száma|
|total_time |dupla pontosság   ||  A lekérdezés teljes végrehajtási ideje ezredmásodpercben|
|min_time   |dupla pontosság   ||  A lekérdezés minimális végrehajtási ideje ezredmásodpercben|
|max_time   |dupla pontosság   ||  A lekérdezés végrehajtási idejének maximális ideje ezredmásodpercben|
|mean_time  |dupla pontosság   ||  Átlagos lekérdezés-végrehajtási idő, ezredmásodpercben|
|stddev_time|   dupla pontosság    ||  A lekérdezés végrehajtási idejének szórása ezredmásodpercben |
|Sorok   |bigint ||  Az utasítás által beolvasott vagy érintett sorok teljes száma|
|shared_blks_hit|   bigint  ||  A megosztott blokkgyorsítótár-találatok teljes száma a kimutatás szerint|
|shared_blks_read|  bigint  ||  A nyilatkozat által olvasott megosztott blokkok teljes száma|
|shared_blks_dirtied|   bigint   || A nyilatkozat által bemocskolt megosztott blokkok teljes száma |
|shared_blks_written|   bigint  ||  A nyilatkozat által írt megosztott blokkok teljes száma|
|local_blks_hit|    bigint ||   A helyi blokkgyorsítótár-találatok teljes száma a kimutatás szerint|
|local_blks_read|   bigint   || A nyilatkozat által olvasott helyi blokkok teljes száma|
|local_blks_dirtied|    bigint  ||  A nyilatkozat által bemocskolt helyi blokkok száma összesen|
|local_blks_written|    bigint  ||  A nyilatkozatáltal írt helyi blokkok teljes száma|
|temp_blks_read |bigint  || A nyilatkozat által felolvasott ideiglenes blokkok teljes száma|
|temp_blks_written| bigint   || A nyilatkozat által írt ideiglenes blokkok teljes száma|
|blk_read_time  |dupla pontosság    || Az olvasási blokkok olvasásával töltött utasítás teljes ideje ezredmásodpercben (ha track_io_timing engedélyezve van, ellenkező esetben nulla)|
|blk_write_time |dupla pontosság    || A kimutatás írási blokkjainak teljes ideje ezredmásodpercben (ha track_io_timing engedélyezve van, ellenkező esetben nulla)|
    
### <a name="query_storequery_texts_view"></a>query_store.query_texts_view
Ez a nézet lekérdezési szövegadatokat ad vissza a Query Store-ban. Minden egyes query_text egy-egy sor tartozik.

|**Név**|  **Típus**|   **Leírás**|
|---|---|---|
|query_text_id  |bigint     |A query_texts tábla azonosítója|
|query_sql_text |Varchar(10000)     |A képviseleti nyilatkozat szövege. Az azonos struktúrával rendelkező különböző lekérdezések csoportosítva vannak; ez a szöveg a fürt első lekérdezésének szövege.|

### <a name="query_storepgms_wait_sampling_view"></a>query_store.pgms_wait_sampling_view
Ez a nézet várakozási események adatait adja vissza a Query Store-ban. Minden egyes különálló adatbázis-azonosítóhoz, felhasználói azonosítóhoz, lekérdezésazonosítóhoz és eseményhez egy sor tartozik.

|**Név**|  **Típus**|   **Referencia**| **Leírás**|
|---|---|---|---|
|user_id    |Oid    |pg_authid.oid  |A nyilatkozatot elvégző felhasználó OID-ja|
|db_id  |Oid    |pg_database.oid    |Annak az adatbázisnak az OID-ja, amelyben az utasítást végrehajtották|
|query_id   |bigint     ||Belső kivonatkód, a kimutatás elemzési fájából számítva|
|event_type |szöveg       ||Az esemény típusa, amelyre a háttér-háttérközpont várakozik|
|Esemény  |szöveg       ||The wait event name if backend is currently waiting|
|Hívások  |Egész szám        ||A rögzített esemény száma|


### <a name="functions"></a>Functions
Query_store.qs_reset() eredménye érvénytelen

`qs_reset` elveti a Query Store által eddig összegyűjtött összes statisztikát. Ezt a funkciót csak a kiszolgálófelügyeleti szerepkör hajthatja végre.

Query_store.staging_data_reset() eredménye érvénytelen

`staging_data_reset` Elveti a Query Store által a memóriában gyűjtött összes statisztikát (azaz azokat az adatokat, amelyek még nem ürítették ki az adatbázisba). Ezt a funkciót csak a kiszolgálófelügyeleti szerepkör hajthatja végre.

## <a name="limitations-and-known-issues"></a>Korlátozások és ismert problémák
- Ha egy PostgreSQL-kiszolgáló default_transaction_read_only paraméterrel rendelkezik, a Query Store nem tud adatokat rögzíteni.
- A Lekérdezéstároló funkció megszakítható, ha hosszú Unicode-lekérdezésekkel találkozik (>= 6000 bájt).
- [Olvassa replikák replikák](concepts-read-replicas.md) replikálja Query Store adatokat a főkiszolgálóról. Ez azt jelenti, hogy az olvasási replika lekérdezési tárolója nem szolgáltat statisztikákat az olvasási replikán futó lekérdezésekről.


## <a name="next-steps"></a>További lépések
- További információ [azokról az esetekről, amikor a Lekérdezéstároló különösen hasznos lehet.](concepts-query-store-scenarios.md)
- További információ a [Query Store használatával kapcsolatos gyakorlati tanácsokról.](concepts-query-store-best-practices.md)
