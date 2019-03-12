---
title: Azure Database for postgresql-hez a Query Store
description: Ez a cikk ismerteti a Query Store szolgáltatás az Azure Database for postgresql-hez.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 8cd4cf12390ff29754b55d2827ea4750e7123a27
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57730489"
---
# <a name="monitor-performance-with-the-query-store"></a>A Query Store teljesítmény figyelése

**A következőkre vonatkozik:** Azure Database for PostgreSQL 9.6 és 10

> [!IMPORTANT]
> A Query Store szolgáltatás jelenleg nyilvános előzetes verzióban.


Az Azure Database for postgresql-hez a Query Store szolgáltatás lehetővé teszi nyomon követheti a lekérdezési teljesítmény az idő függvényében. Query Store egyszerűbbé teszi a teljesítménnyel kapcsolatos hibaelhárítás segít gyorsan található leghosszabban futó és a leginkább erőforrás-igényes lekérdezéseket. Query Store automatikusan rögzíti a lekérdezések és a futásidejű statisztikája előzményeit, és megőrzi őket a felülvizsgálatra. Elválasztja a idő Windows adatokat, így láthatja, hogy az adatbázis használati mintái. Az összes felhasználó, adatbázisok és lekérdezések nevű adatbázis tárolva van **azure_sys** az Azure Database for PostgreSQL-példány található.

> [!IMPORTANT]
> Ne módosítsa a **azure_sys** adatbázis vagy a sémák. Így nem fogja tudni Query Store és a kapcsolódó teljesítménnyel kapcsolatos szolgáltatások megfelelően működik-e.

## <a name="enabling-query-store"></a>Query Store engedélyezése
Query Store az választható szolgáltatása, így azt nem aktív, alapértelmezés szerint a kiszolgálón. A tároló engedélyezett vagy letiltott globálisan az adatbázisokat a megadott kiszolgálón, és nem kapcsolható be- vagy kikapcsolása adatbázisonként.

### <a name="enable-query-store-using-the-azure-portal"></a>Engedélyezze a Query Store az Azure portal használatával
1. Jelentkezzen be az Azure Portalon, és válassza ki az Azure Database for PostgreSQL-kiszolgálóhoz.
2. Válassza ki **kiszolgáló paramétereinek** a a **beállítások** a menü részét.
3. Keresse meg a `pg_qs.query_capture_mode` paraméter.
4. Állítsa az értékét `TOP` és **mentése**.

Várja meg a statisztika a Query Store engedélyezése: 
5. Keresse meg a `pgms_wait_sampling.query_capture_mode` paraméter.
6. Állítsa az értékét `ALL` és **mentése**.


Másik megoldásként ezeket a paramétereket az Azure CLI használatával is beállíthatja.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

Lehetővé teszi az adatok a azure_sys adatbázisban megőrizni az első köteg akár 20 percig.

## <a name="information-in-query-store"></a>A Query Store információk
Query Store két tárolók rendelkezik:
- A futásidejű statisztikái áruház a lekérdezés-végrehajtás statisztikai adatok megőrzése.
- Várjon, amíg egy várakozási statisztikái üzlet megőrzése statisztikai adatok.

A Query Store gyakori forgatókönyvek a következők:
- Amely meghatározza, hogy hányszor egy lekérdezést hajtott egy adott időtartományban
- A lekérdezés átlagos végrehajtási idő összehasonlítása a különböző időtartományok nagy változásokat megtekintéséhez
- Leghosszabb futó lekérdezések azonosítása az elmúlt óra X
- Erőforrások várakozik a legfontosabb N lekérdezések azonosítása
- Egy adott lekérdezés várakozási jellegű, ismertetése

Lemezterület-használat minimalizálása érdekében a futásidejű végrehajtási statisztika a futásidejű statisztikái tárolójában időkeretben egy rögzített, konfigurálható vannak összesítve. Ezekkel az áruházakkal található információk lekérdezésével a query store nézetek jelenik meg.

A következő lekérdezést a lekérdezésekkel kapcsolatos információkat a Query Store adja vissza:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Vagy a lekérdezés a várakozási statisztikái:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

## <a name="finding-wait-queries"></a>Keresés várakozási lekérdezések
Várjon eseménytípusok különböző várakozási események egyesítendő hasonló a gyűjtőkbe. Query Store biztosít a várakozási esemény típusa, bizonyos várakozási esemény neve és a szóban forgó lekérdezés. Folyamatban van az alkalmazáslogikának, ezeket várja meg az információkat a lekérdezés futásidejű statisztikai azt jelenti, hogy mi járul hozzá, hogy a lekérdezési teljesítményt nyújt egy mélyrehatóbb ismereteket szerezhet.

Az alábbiakban néhány példát, hogyan szerezhet a számítási feladatokhoz, a várakozási statisztikák a Query Store további betekintést:

| **Megfigyelés** | **Művelet** |
|---|---|
|Magas zárolás várakozik | Ellenőrizze a lekérdezés szövegét az érintett lekérdezések, és a cél entitások azonosítása. Keresse meg a Query Store más lekérdezések módosítása ugyanazon entitás, amely gyakran hajtja végre, és/vagy nagy időtartama van. Ezek a lekérdezések azonosítása, után fontolja meg az alkalmazáslogika egyidejűségi javítására módosítja, vagy egy kevésbé korlátozó elkülönítési szintet használja.|
| Magas puffer i/o-várakozások | A lekérdezések nagy számú fizikai olvasások található Query Store. Ha azok megfelelnek a lekérdezések magas i/o-vár, fontolja meg a vizsgálatok helyett nyugodtan Dolgozhatok addig is az alapul szolgáló entitásra, index bemutatása kér. Ez lenne a i/o-terhelést lekérdezések minimalizálása érdekében. Ellenőrizze a **teljesítménnyel kapcsolatos javaslatok** a kiszolgáló a portálon, hogy vannak-e a kiszolgáló, amely akkor lekérdezések optimalizálásához ajánlott indexek megtekintéséhez.|
| Magas memória vár | A felső memória-igényes lekérdezéseket a Query Store találja. Ezek a lekérdezések vannak valószínűleg késleltetése további az érintett lekérdezések előrehaladását. Ellenőrizze a **teljesítménnyel kapcsolatos javaslatok** a kiszolgáló a portálon, hogy vannak-e ezeket a lekérdezéseket szeretné optimalizálni ajánlott indexek megtekintéséhez.|

## <a name="configuration-options"></a>Beállítási lehetőségek
Ha engedélyezve van a Query Store a 15 perces összesítési windows rendszerben legfeljebb 500 különböző lekérdezések száma ablak menti adatokat. 

A következő beállítások konfigurálásához a Query Store paraméterek érhetők el.
| **A paraméter** | **Leírás** | **Alapértelmezett** | **Címtartomány**|
|---|---|---|---|
| pg_qs.query_capture_mode | Megadja, hogy melyik utasításokat követi. | nincs | nincs, az első, az összes |
| pg_qs.max_query_text_length | A lekérdezés maximális hossza, melyekbe menthetők a állítja be. Hosszabb lekérdezések csonkolva lesz. | 6000 | 100 - 10 EZER |
| pg_qs.retention_period_in_days | A megőrzési időszak beállítása. | 7 | 1 - 30 |
| pg_qs.track_utility | Megadja, hogy segédprogram parancsokat a rendszer nyomon követi-e | be | bekapcsolt kikapcsolt |

A következő beállítások kifejezetten statisztika várjon vonatkoznak.
| **A paraméter** | **Leírás** | **Alapértelmezett** | **Címtartomány**|
|---|---|---|---|
| pgms_wait_sampling.query_capture_mode | Utasítások nyomon követi a csoportok várjon statisztikák. | nincs | nincs, az összes|
| Pgms_wait_sampling.history_period | Állítsa a gyakoriságot, ezredmásodpercben, mely várakozási esemény történik az adatgyűjtés. | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs.query_capture_mode** felülír **pgms_wait_sampling.query_capture_mode**. Ha pg_qs.query_capture_mode nem sem, akkor a pgms_wait_sampling.query_capture_mode beállítás nem befolyásolja.


Használja a [az Azure portal](howto-configure-server-parameters-using-portal.md) vagy [Azure CLI-vel](howto-configure-server-parameters-using-cli.md) beolvasni vagy megadni egy másik értéket a paraméterhez.

## <a name="views-and-functions"></a>Nézetek és függvények
Megtekintheti és kezelheti a Query Store a következő nézetek és függvények használatával. A PostgreSQL nyilvános szerepkör szervezeten belül bárki használhatja ezeket a nézeteket a Query Store adatait. Ezek a nézetek csak állnak rendelkezésre a **azure_sys** adatbázis.

Lekérdezések vannak normalizált literálok és konstansok eltávolítása után azok szerkezetét megtekintésével. Ha két lekérdezést konstansértékekkel kivételével azonosak, rendelkeznek azonos a kivonata.

### <a name="querystoreqsview"></a>query_store.qs_view
Ez a nézet az összes adat Query Store adja vissza. Az egyes különálló adatbázisok azonosító, a felhasználói Azonosítót és a lekérdezés azonosítóját. egy sor van 

|**Name (Név)**   |**Típus** | **Hivatkozások**  | **Leírás**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | A runtime_stats_entries tábla azonosítója|
|user_id    |objektumazonosító    |pg_authid.oid  |Az utasítás végrehajtása felhasználó Objektumazonosítója|
|db_id  |objektumazonosító    |pg_database.oid    |OID-adatbázis, amelyben a rendszer az utasítást hajtott végre|
|query_id   |bigint  || A belső kivonatkód, az utasítás elemzési fa erősségével|
|query_sql_text |Varchar(10000)  || Egy tipikus utasítás szövege. Ugyanazzal a struktúrával különböző lekérdezéseket fürtözöttek együtt; Ez a szöveg az a szöveg az első a lekérdezések a fürt számára.|
|plan_id    |bigint |   |Ez a lekérdezés nem érhető el megfelelő még a csomag azonosítója|
|start_time |időbélyeg  ||  Összesítjük lekérdezések idő gyűjtők által - kérelemegységeket időtartamának alapértelmezés szerint 15 percenként. Ez az a kezdő időpont a idő gyűjtőhöz a bejegyzéshez tartozó megfelelő.|
|end_time   |időbélyeg  ||  A time gyűjtőhöz a bejegyzéshez tartozó megfelelő befejezési időpontja.|
|hívás  |bigint  || A lekérdezés végrehajtása hányszor|
|total_time |a kétszeres pontosság   ||  A lekérdezés teljes végrehajtási idő, ezredmásodpercben|
|min_time   |a kétszeres pontosság   ||  Minimális lekérdezés-végrehajtási idő (MS)|
|max_time   |a kétszeres pontosság   ||  Lekérdezés maximális végrehajtási idő (MS)|
|mean_time  |a kétszeres pontosság   ||  Jelenti azt, hogy a lekérdezés-végrehajtási idő (MS)|
|stddev_time|   a kétszeres pontosság    ||  A lekérdezés végrehajtási idő, ezredmásodpercben szórása |
|sor   |bigint ||  Beolvasott vagy a-utasítás által érintett sorok száma|
|shared_blks_hit|   bigint  ||  Az utasítás által megosztott blokk gyorsítótár-találatok teljes száma|
|shared_blks_read|  bigint  ||  Olvassa el a-utasítás által megosztott blokkok száma összesen|
|shared_blks_dirtied|   bigint   || Az utasítás által dirtied megosztott blokkok száma összesen |
|shared_blks_written|   bigint  ||  Az utasítás által írt megosztott blokkok száma összesen|
|local_blks_hit|    bigint ||   Az utasítás által helyi gyorsítótár-találatok teljes száma|
|local_blks_read|   bigint   || Az utasítás által beolvasott helyi blokkok száma összesen|
|local_blks_dirtied|    bigint  ||  Az utasítás által dirtied helyi blokkok száma összesen|
|local_blks_written|    bigint  ||  Az utasítás által írt helyi blokkok száma összesen|
|temp_blks_read |bigint  || Az utasítás által beolvasott ideiglenes blokkok száma összesen|
|temp_blks_written| bigint   || Az utasítás által írt ideiglenes blokkok száma összesen|
|blk_read_time  |a kétszeres pontosság    || Teljes az utasítás olvasási blokkok ezredmásodpercben töltött idő (ha track_io_timing engedélyezve van, egyébként nulla)|
|blk_write_time |a kétszeres pontosság    || Teljes az utasítás írása blokkok ezredmásodpercben töltött idő (ha track_io_timing engedélyezve van, egyébként nulla)|
    
### <a name="querystorequerytextsview"></a>query_store.query_texts_view
Ez a nézet a Query Store adja vissza szöveges adatokat lekérdezni. Az egyes különálló akkor egy sor van.

|**Name (Név)**|  **Típus**|   **Leírás**|
|---|---|---|
|query_text_id  |bigint     |A query_texts tábla azonosítója|
|query_sql_text |Varchar(10000)     |Egy tipikus utasítás szövege. Ugyanazzal a struktúrával különböző lekérdezéseket fürtözöttek együtt; Ez a szöveg az a szöveg az első a lekérdezések a fürt számára.|

### <a name="querystorepgmswaitsamplingview"></a>query_store.pgms_wait_sampling_view
Ez a nézet értéket ad vissza a Query Store eseményadatai várjon. Az egyes különböző adatbázis-azonosító, a felhasználói azonosító, a lekérdezés Azonosítóját és a esemény egy sor van.

|**Name (Név)**|  **Típus**|   **Hivatkozások**| **Leírás**|
|---|---|---|---|
|user_id    |objektumazonosító    |pg_authid.oid  |Az utasítás végrehajtása felhasználó Objektumazonosítója|
|db_id  |objektumazonosító    |pg_database.oid    |OID-adatbázis, amelyben a rendszer az utasítást hajtott végre|
|query_id   |bigint     ||A belső kivonatkód, az utasítás elemzési fa erősségével|
|event_type |szöveg       ||Az esemény, amelynek a háttérrendszer arra vár, hogy típusát|
|esemény  |szöveg       ||A várakozási esemény neve, ha a háttérrendszer jelenleg vár|
|hívás  |Egész szám        ||Az ugyanahhoz az eseményhez rögzített száma|


### <a name="functions"></a>Functions
Query_store.qs_reset() void adja vissza

`qs_reset` az összes statisztika Query Store által eddig összegyűjtött elveti. Ez a funkció csak a kiszolgálói rendszergazdai szerepkör által hajtható végre.

Query_store.staging_data_reset() returns void

`staging_data_reset` a memória a Query Store (azaz az adatokat a memóriában, amely nem kiürítése megtörtént, de az adatbázishoz) által gyűjtött összes statisztikai elveti. Ez a funkció csak a kiszolgálói rendszergazdai szerepkör által hajtható végre.

## <a name="limitations-and-known-issues"></a>Korlátozások és ismert problémák
- Ha egy PostgreSQL-kiszolgáló rendelkezik a paraméter default_transaction_read_only, Query Store az adatok rögzítése nem tudja.
- Query Store funkciókat is lehet megszakítani, ha találkozik hosszú Unicode-lekérdezések (> = 6000 bájt).


## <a name="next-steps"></a>További lépések
- Tudjon meg többet [forgatókönyvek, ahol a Query Store különösen hasznos lehet](concepts-query-store-scenarios.md).
- Tudjon meg többet [ajánlott eljárások a Query Store](concepts-query-store-best-practices.md).
