---
title: Autovákuum optimalizálása - Azure Database for PostgreSQL - Single Server
description: Ez a cikk azt ismerteti, hogyan optimalizálhatja az automatikus vákuumot egy Azure-adatbázison a PostgreSQL - Single Server szolgáltatáshoz
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 1917bd6744e100db54fe959292e29486f8a1784b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770186"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql---single-server"></a>Automatikus vákuum optimalizálása PostgreSQL-hez készült Azure-adatbázisban – Egykiszolgálós
Ez a cikk bemutatja, hogyan lehet hatékonyan optimalizálni az automatikus vákuumot egy Azure Database for PostgreSQL-kiszolgálón.

## <a name="overview-of-autovacuum"></a>Az autovákuum áttekintése
A PostgreSQL multiverziós egyidejűség-vezérlést (MVCC) használ a nagyobb adatbázis-egyidejűség engedélyezéséhez. Minden frissítés beszúrást és törlést eredményez, és minden törlés azt eredményezi, hogy a sorok törlésre lágyan vannak megjelölve. A lágy jelölés azonosítja az okat később megtisztítandó halott tuple-okat. A feladatok elvégzéséhez a PostgreSQL vákuumfeladatot futtat.

A vákuumfeladat manuálisan vagy automatikusan aktiválható. Több halott tuples létezik, ha az adatbázis súlyos frissítési vagy törlési műveleteket tapasztal. Kevesebb halott tuple létezik, ha az adatbázis tétlen. Gyakrabban kell porszívóznia, ha az adatbázis terhelése nehéz, ami *a* manuális anamforrast teszi legyártva.

Autovákuum lehet beállítani, és előnyöket tuning. Az alapértelmezett értékek, amelyeket a PostgreSQL szállít, megpróbálják biztosítani, hogy a termék mindenféle eszközön működjön. Ezek az eszközök közé tartozik a Málna Pis. Az ideális konfigurációs értékek a következőktől függenek:
- Összes rendelkezésre álló erőforrások, például a termékváltozat és a tároló mérete.
- Erőforrás-használat.
- Egyedi objektumjellemzők.

## <a name="autovacuum-benefits"></a>Autovákuum előnyei
Ha nem vákuum időről időre, a halott tuples, hogy felhalmozódnak vezethet:
- Az adatok felfújódnak, például nagyobb adatbázisok és táblák.
- Nagyobb, optimálistól elmaradó indexek.
- Megnövekedett I/O.

## <a name="monitor-bloat-with-autovacuum-queries"></a>Monitor felfújni autovákuum os lekérdezések
Az alábbi mintalekérdezés az XYZ nevű tábla halott és élő tuple-jainak számát azonosítja:
 
    'SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;'

## <a name="autovacuum-configurations"></a>Autovákuum konfigurációk
Az automatikus vákuumot szabályozó konfigurációs paraméterek két kulcsfontosságú kérdésre adott válaszokon alapulnak:
- Mikor kell elkezdeni?
- Mennyit kell tisztítani, miután elkezdődik?

Íme néhány automatikus vákuum konfigurációs paraméter, amelyet az előző kérdések alapján frissíthet, valamint néhány útmutatást.

Paraméter|Leírás|Alapértelmezett érték
---|---|---
autovacuum_vacuum_threshold|Megadja a vákuumművelet indításához szükséges frissített vagy törölt rekordok minimális számát bármely táblában. Az alapértelmezett érték 50 tuples. Ezt a paramétert csak a postgresql.conf fájlban vagy a kiszolgáló parancssorában állítsa be. Az egyes táblák beállításának felülbírálásához módosítsa a tábla tárolási paramétereit.|50
autovacuum_vacuum_scale_factor|Megadja a táblázat méretének törtrészét, amelyet hozzá szeretne adni autovacuum_vacuum_threshold, amikor eldönti, hogy vákuumműveletet indít-e el. Az alapértelmezett érték 0,2, ami a tábla méretének 20 százaléka. Ezt a paramétert csak a postgresql.conf fájlban vagy a kiszolgáló parancssorában állítsa be. Az egyes táblák beállításának felülbírálásához módosítsa a tábla tárolási paramétereit.|5 százalék
autovacuum_vacuum_cost_limit|Az automatikus vákuumműveletekben használt költségkorlát értékét adja meg. Ha a -1 van megadva, ami az alapértelmezett, akkor a rendszer a normál vacuum_cost_limit értéket használja. Ha egynél több dolgozó van, az érték arányosan oszlik meg a futó automatikus vákuummunkások között. Az egyes dolgozókra vonatkozó korlátok összege nem haladja meg a változó értékét. Ezt a paramétert csak a postgresql.conf fájlban vagy a kiszolgáló parancssorában állítsa be. Az egyes táblák beállításának felülbírálásához módosítsa a tábla tárolási paramétereit.|-1
autovacuum_vacuum_cost_delay|Az automatikus vákuumműveletekben használt költségkésleltetési értéket adja meg. Ha a -1 van megadva, a normál vacuum_cost_delay értéket használja a függvény. Az alapértelmezett érték 20 ezredmásodperc. Ezt a paramétert csak a postgresql.conf fájlban vagy a kiszolgáló parancssorában állítsa be. Az egyes táblák beállításának felülbírálásához módosítsa a tábla tárolási paramétereit.|20 ms
autovacuum_nap_time|Megadja az adott adatbázis automatikus vákuumfuttatása közötti minimális késleltetést. A démon minden körben megvizsgálja az adatbázist, és szükség szerint vákuum- és elemzési parancsokat ad ki az adatbázis tábláihoz. A késleltetés másodpercben van mérve, az alapértelmezett érték pedig egy perc (1 perc). Ezt a paramétert csak a postgresql.conf fájlban vagy a kiszolgáló parancssorában állítsa be.|15 s
autovacuum_max_workers|Megadja az autovákuum-indítón kívül az automatikus vákuumindítón kívül az automatikus vákuumindítótól eltérő automatikus vákuumfolyamatok maximális számát, amelyek egy időben futtathatók. Az alapértelmezett érték három. Ezt a paramétert csak a kiszolgáló indításakor állítsa be.|3

Az egyes táblák beállításainak felülbírálásához módosítsa a tábla tárolási paramétereit. 

## <a name="autovacuum-cost"></a>Autovákuum költsége
Itt vannak a "költségek" a futás egy vákuum művelet:

- A vákuum által futtatott adatlapok zárolva vannak.
- A számítási és memória vákuumfeladat futtatásakor használatos.

Ennek eredményeképpen ne futtasson vákuumfeladatokat túl gyakran vagy túl ritkán. A vákuumfeladatnak alkalmazkodnia kell a munkaterheléshez. Tesztelje az összes autovákuum paraméterváltozást az egyes kompromisszumok miatt.

## <a name="autovacuum-start-trigger"></a>Autovákuum indítóravasz
Autovákuum váltja ki, ha az elhalt tuples száma meghaladja autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples. Itt a reltuples állandó.

Az automatikus porszívózásból történő karbantartásnak lépést kell tartania az adatbázis terhelésével. Ellenkező esetben előfordulhat, hogy elfogy a tárhely, és a lekérdezések általános lassulását tapasztalja. Idővel amortizálva, az a sebesség, amellyel a vákuumművelet megtisztítja az elhalt tuples-t, meg kell egyeznie az elhalt tuples létrehozásának mértékének.

A sok frissítéssel és törléssel rendelkező adatbázisok több halott tuple-t tartalmaznak, és több helyre van szükségük. A sok frissítéssel és törléssel rendelkező adatbázisok általában a autovacuum_vacuum_scale_factor és a autovacuum_vacuum_threshold alacsony értékeiből származnak. Az alacsony értékek megakadályozzák az elhalt tuple-ok hosszan tartó felhalmozódását. A kisebb adatbázisokkal rendelkező mindkét paraméterhez használhat magasabb értékeket, mert a porszívózás szükségessége kevésbé sürgős. A gyakori porszívózás a számítás és a memória árán jár.

A 20 százalékos alapértelmezett léptéktényező jól működik az on-kon, ahol alacsony az elhalt tuples százalékos aránya. Ez nem működik jól az asztalokon nagy százalékban halott tuples. Például egy 20 GB-os táblázatban ez a léptéktényező 4 GB holthal-t. Egy 1 TB-os asztalon 200 GB halott tuples.

A PostgreSQL segítségével ezeket a paramétereket a táblázat vagy a példány szintjén állíthatja be. Ma ezeket a paramétereket csak a PostgreSQL Azure Database-ben állíthatja be a tábla szintjén.

## <a name="estimate-the-cost-of-autovacuum"></a>Becsülje meg az autovákuum költségét
Az automatikus vákuum futtatása "költséges", és vannak paraméterek a vákuumműveletek futási idejének szabályozására. A következő paraméterek segítenek megbecsülni a vákuum futásának költségét:
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

A vákuumfolyamat fizikai oldalakat olvas, és ellenőrzi az elhalt tuples-t. shared_buffers minden oldala 1(vacuum_cost_page_hit) költséggel rendelkezik. Az összes többi oldal 20 (vacuum_cost_page_dirty) költséggel rendelkezik, ha léteznek holthal-eltérések, vagy 10 (vacuum_cost_page_miss), ha nincsenek halott tűk. A vákuumművelet leáll, ha a folyamat meghaladja a autovacuum_vacuum_cost_limit. 

A korlát elérése után a folyamat a autovacuum_vacuum_cost_delay paraméter által megadott ideig alvó állapotban van, mielőtt újraindulna. Ha a korlátot nem éri el, az automatikus vákuum a autovacuum_nap_time paraméter által megadott érték után indul el.

Összefoglalva, a autovacuum_vacuum_cost_delay és autovacuum_vacuum_cost_limit paraméterek szabályozzák, hogy mennyi adat karbantartás a megengedett egy egységnyi idő. Vegye figyelembe, hogy az alapértelmezett értékek túl alacsonyak a legtöbb tarifacsomag esetében. Ezeka paraméterek optimális értékei tarifaszint-függőek, és ennek megfelelően kell konfigurálni.

A autovacuum_max_workers paraméter határozza meg az egyidejűleg futtatható automatikus vákuumfolyamatok maximális számát.

A PostgreSQL segítségével ezeket a paramétereket a táblázat vagy a példány szintjén állíthatja be. Ma ezeket a paramétereket csak a PostgreSQL Azure Database-ben állíthatja be a tábla szintjén.

## <a name="optimize-autovacuum-per-table"></a>Optimalizálja az automatikus vákuumot asztalonként
Az összes korábbi konfigurációs paramétert beállíthatja táblaként. Például:
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Az automatikus vákuum egy asztalonkénti szinkron folyamat. A nagyobb százaléka halott tuples, hogy egy asztal, annál magasabb a "költség" az automatikus vákuum. A nagy sebességű frissítéseket tartalmazó táblákat és a törléseket több táblára is feloszthatja. Az asztalok felosztása segít párhuzamosítani az automatikus vákuumot, és csökkenteni a "költséget", hogy egy asztalon végezze el az automatikus vákuumot. Növelheti a párhuzamos autovákuum-dolgozók számát is annak érdekében, hogy a munkavállalók at bőkezűen ütemezze.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni az automatikus vákuum használatáról és hangolásáról, olvassa el a Következő PostgreSQL dokumentációt:

 - [18. fejezet, Kiszolgáló konfigurációja](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
 - [24. fejezet, Rutin adatbázis-karbantartási feladatok](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
