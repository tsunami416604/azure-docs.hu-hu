---
title: Optimalizálja autovacuum az Azure Database for PostgreSQL-kiszolgáló
description: Ez a cikk bemutatja, hogyan optimalizálhatók a autovacuum az Azure Database for PostgreSQL-kiszolgálóhoz.
author: dianaputnam
ms.author: dianas
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 91f24bf90f3e1a8a0c383a5820c6816748090807
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51629102"
---
# <a name="optimizing-autovacuum-on-azure-database-for-postgresql-server"></a>Optimalizálás autovacuum, Azure database for PostgreSQL-kiszolgáló 
Ez a cikk ismerteti, hogyan lehet hatékonyan optimalizálása autovacuum, Azure database for postgresql-hez.

## <a name="overview-of-autovacuum"></a>Autovacuum áttekintése
PostgreSQL database nagyobb párhuzamosság engedélyezése MVCC használ. Minden frissítés egy beszúrási és törlési eredményez, és minden törlése folyamatban van a soft-törlésre van kijelölve a sorokra eredményez. A helyreállítható-jelölés kézbesíthetetlen később kiürítendő rekordok azonosítása eredményez. PostgreSQL éri el, mindezt vákuumos feladat futtatásával.

Manuálisan vagy automatikusan a vákuumos feladat indítható el. További kézbesíthetetlen rekordokat tartalmazó fenn, ha az adatbázis (nagy erőforrásigényű) frissítési tapasztal, vagy a műveletek, és kevesebb üresjáratban törlése.  Vákuum gyakoribb futtatásához szükség értéke nagyobb, ha az adatbázis jelentős terhelés; így a futó feladatok vákuumos **manuálisan** kényelmetlen lehet.

Autovacuum konfigurálható és a hangolási előnyeit. Az alapértelmezett értékeket, PostgreSQL részét képező próbál győződjön meg, hogy a termék a különféle eszközöket és azok Raspberry a feldolgozási működik, és a ideális konfigurációs értékeket számos tényezőtől függ:
- Rendelkezésre álló - erőforrások teljes Termékváltozat és a tárhely mérete.
- Erőforrás-használatot.
- Az egyes objektumok jellemzőit.

## <a name="autovacuum-benefits"></a>Autovacuum előnyei
Ha vákuum időről időre nem futtatja, a kézbesíthetetlen rekordok felsorolásának gyűlnek eredményezhet:
- Adatok közelítés – nagyobb méretű adatbázisokat és táblákat.
- Nagyobb az optimálisnál rosszabb indexek.
- Nagyobb i/o.

## <a name="monitoring-bloat-with-autovacuum-queries"></a>Figyelési növekedést autovacuum lekérdezésekkel
Az alábbi mintalekérdezés úgy terveztek, hogy a "XYZ" nevű tábla élő és nem elő rekordok számát: ' SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup / n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_ HOL relname táblák = "XYZ" sorrend szerint n_dead_tup DESC;'

## <a name="autovacuum-configurations"></a>Autovacuum konfigurációk
A konfigurációs paramétereket autovacuum szabályozó két fő kérdést köré szerveződik:
- Ez indításának időpontját?
- Mennyit kell azt tiszta elindítása után?

Alább néhány frissítheti autovacuum konfigurációs paraméterek alapulnak a fenti kérdések, valamint útmutatást:
Paraméter|Leírás|Alapértelmezett érték
---|---|---
autovacuum_vacuum_threshold|Adja meg a frissített vagy törölt rekordok táblában VÁKUUMOT aktiválásához szükséges minimális számát. Az alapértelmezett érték 50 rekordokat. Ez a paraméter csak akkor állítható be, a postgresql.conf fájlban vagy a kiszolgáló parancssorból. A beállítás módosításával a table storage paraméter felülbírálható egyedi táblák.|50
autovacuum_vacuum_scale_factor|Adja meg a tábla mérete autovacuum_vacuum_threshold hozzá, amikor eldönti, hogy VÁKUUMOT aktiválásához töredékéért. Az alapértelmezett érték a 0.2-es (a tábla méretű 20 %-os). Ez a paraméter csak akkor állítható be, a postgresql.conf fájlban vagy a kiszolgáló parancssorból. A beállítás módosításával a table storage paraméter felülbírálható egyedi táblák.|5 %-os
autovacuum_vacuum_cost_limit|Értékét adja meg költség korlát, amely használható a VÁKUUM automatikus műveleteket. Ha a -1 (Ez az alapértelmezett beállítás) van megadva, a rendszeres vacuum_cost_limit érték használható. Az érték eloszlik a futó autovacuum dolgozók körében, ha egynél több feldolgozói. A minden korlátokat összegét nem haladja meg a változó értékét. Ez a paraméter csak akkor állítható be, a postgresql.conf fájlban vagy a kiszolgáló parancssorból. A beállítás módosításával a table storage paraméter felülbírálható egyedi táblák.|-1
autovacuum_vacuum_cost_delay|Adja meg a költségek késleltetési érték, amely használható automatikus VÁKUUM operations. Ha meg van adva a -1, a rendszeres vacuum_cost_delay érték használható. Az alapértelmezett érték: 20 ezredmásodperc. Ez a paraméter csak akkor állítható be, a postgresql.conf fájlban vagy a kiszolgáló parancssorból. A beállítás módosításával a table storage paraméter felülbírálható egyedi táblák.|20 ms
autovacuum_nap_time|Adja meg a minimális késleltetési autovacuum közötti bármely adott adatbázist futtat. Mindegyik ciklikus a démon megvizsgálja az adatbázist, és problémák VÁKUUM és elemzés parancsok szükség szerint az adott adatbázisban lévő táblák. A késleltetés idejének mérése másodpercben történik, és az alapértelmezett érték egy perc (1 perc). Ez a paraméter csak akkor állítható be, a postgresql.conf fájlban vagy a kiszolgáló parancssorból.|15 s
autovacuum_max_workers|Megadja a autovacuum folyamatok, egyidejűleg futó (nem a autovacuum indítója) maximális száma. Az alapértelmezett érték három. Ez a paraméter csak akkor állítható kiszolgáló indításakor.|3
A fenti konfigurációk az Azure portal vagy Azure parancssori felület használatával frissíthetők.

## <a name="autovacuum-cost"></a>Autovacuum költség
Az alábbiakban a "" futtatásával járó költségek vákuumos műveletet:
- Zárolni az oldalak vákuum futtat az adatokon.
- Számítást és memóriát használnak vákuum futtatásakor.

Ez azt jelenti, hogy vákuum nem futtathatja az alábbiak túl gyakran, vagy túl ritkán kell lennie az adaptív számítási feladatokra. Azt javasoljuk, hogy minden autovacuum paraméterek módosításainak tesztelése a kompromisszumot kínál a mindegyiknek miatt.

## <a name="autovacuum-start-trigger"></a>Autovacuum kezdő eseményindító
Autovacuum akkor aktiválódik, ha a kézbesíthetetlen rekordok száma meghaladja a autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples, reltuples itt egy konstans.

Autovacuum adattisztítást kell tartani az adatbázis betöltési, ellenkező esetben sikerült elfogyott a tárterület futtassa, és a lekérdezések egy általános lassulás élmény. Amortizált idővel, mely vákuum díj megtisztítja kézbesítetlen rekordokat tartalmazó meg kell egyeznie az a sebesség, amellyel kézbesíthetetlen rekordok jönnek létre.

Sok frissítése/törlése adatbázis több kézbesíthetetlen rekordokat tartalmazó rendelkezik, és további tárterületre van szükség. Általában a legtöbb adatbázisok frissítése/törlése profitál az autovacuum_vacuum_scale_factor alacsony értékeit és alacsony érték autovacuum_vacuum_threshold kézbesíthetetlen rekordokat tartalmazó hosszan tartó felhalmozódásához elkerülése érdekében. Használhatja a magasabb értékek mindkét paraméter kisebb adatbázisok, mert vákuum szükség a kevésbé sürgős. Emlékeztető, amelyek gyakran intéznek vacuuming cserébe számítási és memória érhető el.

Az alapértelmezett mértéktényező 20 %-os működik jól azokon a táblákon, az alacsony százalékos arányának kézbesíthetetlen rekordokat tartalmazó, de nem a magas százalékos kézbesíthetetlen rekordokat tartalmazó táblák. Például egy 20 GB-os táblán Ez a rendszer lefordítja arra kézbesíthetetlen rekordokat tartalmazó 4 GB és 1 TB-os táblán leállási rekordokat 200 GB.

A PostgreSQL táblázat vagy a példány szintjén állíthatja be ezeket a paramétereket. Még ma ezeket a paramétereket is szintjén adhatók a táblázat csak az Azure Database for postgresql-hez.

## <a name="estimating-the-cost-of-autovacuum"></a>Autovacuum költségeinek
Futó autovacuum "költséges" Equal szabályozása a futtatókörnyezet vákuumos műveletek paraméterei. A következő paraméterek segítségével vákuum futtatásával járó költségeket megbecsülheti:
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

A vákuumos folyamat olvassa be a fizikai lapok és kézbesíthetetlen rekordokat keres. Kell díjat fizetni, 1 (vacuum_cost_page_hit) összes lapjának shared_buffers tekinthető, az összes többi oldalán tekinthetők kell 20 (vacuum_cost_page_dirty), ha kézbesíthetetlen rekordokat tartalmazó létezik, vagy 10 (vacuum_cost_page_miss) díjat fizetni, ha nincs kézbesíthetetlen rekordok léteznek. A vákuumos művelet leáll, amikor a folyamat meghaladja autovacuum_vacuum_cost_limit.  

A korlát elérése után a folyamat az időtartam, mielőtt ismét elindul a autovacuum_vacuum_cost_delay paraméterben megadott alvó. Ha nem elérte a korlátot, autovacuum elindul, a autovacuum_nap_time paraméter által meghatározott érték utáni.

Összefoglalva a autovacuum_vacuum_cost_delay és autovacuum_vacuum_cost_limit paraméterek szabályozhatja, mennyi adat-karbantartási engedélyezett egységnyi idő alatt. Vegye figyelembe, hogy, hogy az alapértelmezett értékei a legtöbb tarifacsomagok túl alacsony. Az optimális a paraméterek értékei vannak árképzési szint-függő, és ennek megfelelően kell konfigurálni.

A autovacuum_max_workers paraméter, amely egyidejűleg futó autovacuum folyamatok maximális száma határozza meg.

A PostgreSQL táblázat vagy a példány szintjén állíthatja be ezeket a paramétereket. Még ma ezeket a paramétereket is szintjén adhatók a táblázat csak az Azure Database for postgresql-hez.

## <a name="optimizing-autovacuum-per-table"></a>Táblánként autovacuum optimalizálása
Az összes fenti konfigurációs paramétereket, sőt is táblánként, például:
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Autovacuum van egy táblázat a szinkron folyamatonként. A nagyobb százalékos aránya kézbesíthetetlen rekordokat tartalmazó tábla van, a magasabb autovacuum "költség".  Táblákat, amelyeken a frissítés/törlés több táblára felosztva nagy mértékű felosztás segít autovacuum párhuzamosíthatja, és a egy táblán autovacuum végrehajtásához "cost" csökkentése érdekében. Annak érdekében, hogy a munkavállalók liberally ütemezett párhuzamos autovacuum feldolgozók száma is növelhető.

## <a name="next-steps"></a>További lépések
Tekintse át a következő PostgreSQL documenatation autovacuum egyszerűsíthetik és használatával kapcsolatos további:
 - PostgreSQL dokumentációja – [fejezet 18-ra, a kiszolgáló konfigurációja](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
 - PostgreSQL-dokumentáció – [fejezet 24, rutin adatbázis-karbantartási feladatok](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
