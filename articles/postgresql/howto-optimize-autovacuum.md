---
title: Egy Azure Database for PostgreSQL-kiszolgáló a autovacuum optimalizálása
description: Ez a cikk bemutatja, hogyan optimalizálhatók a autovacuum egy Azure Database for PostgreSQL-kiszolgáló található.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: e8e9991f20481deee85a6d582582335eb98e3c24
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55815217"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql-server"></a>Egy Azure Database for PostgreSQL-kiszolgáló a autovacuum optimalizálása 
Ez a cikk ismerteti egy Azure Database for PostgreSQL-kiszolgáló a autovacuum hatékonyan optimalizálása.

## <a name="overview-of-autovacuum"></a>Autovacuum áttekintése
PostgreSQL database nagyobb párhuzamosság engedélyezése multiversion egyidejűség-vezérlés (MVCC) használja. Minden frissítés egy beszúrási és törlési eredményez, és minden törlése folyamatban van a helyreállítható megjelölve törlésre sorok eredményez. Helyreállítható-jelölés azonosítja a kézbesíthetetlen később kiürítendő rekordok. Ezek a feladatok elvégzésére PostgreSQL vákuumos feladatot futtat egy.

Manuálisan vagy automatikusan a vákuumos feladat indítható el. További kézbesíthetetlen rekordokat tartalmazó létezik, amikor az adatbázis (nagy erőforrásigényű) frissítési vagy törlési művelet. Kevesebb kézbesíthetetlen rekordokat tartalmazó létezik, amikor az adatbázis inaktív. Az adatbázis-terhelés esetén (nagy erőforrásigényű), ami lehetővé teszi futó vákuumos feladatok gyakran vákuum kell *manuálisan* kényelmetlen lehet.

Autovacuum konfigurálható és a hangolási előnyeit. Az alapértelmezett értékeket, PostgreSQL részét képező próbálja meg a termék a különféle eszközök működésének ellenőrzésére. Ilyen eszköz például a Raspberry a feldolgozási. Az ideális konfigurációs értékek függenek a:
- Összes erőforrás érhető el, például a Termékváltozat és a tárhely mérete.
- Erőforrás-használatot.
- Az egyes objektumok jellemzőit.

## <a name="autovacuum-benefits"></a>Autovacuum előnyei
Ha nem időről időre vákuum, a kézbesíthetetlen rekordok felsorolásának gyűlnek eredményezhet:
- Adatok közelítés, például a nagyobb méretű adatbázisokat és táblákat.
- Nagyobb az optimálisnál rosszabb indexek.
- Nagyobb i/o.

## <a name="monitor-bloat-with-autovacuum-queries"></a>A figyelő növekedést autovacuum lekérdezésekkel
Az alábbi mintalekérdezés úgy terveztek, hogy az XYZ nevű tábla élő és nem elő rekordok számát:
 
    'SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;'

## <a name="autovacuum-configurations"></a>Autovacuum konfigurációk
A konfigurációs paramétereket, amelyek vezérlik a autovacuum alapuló két legfontosabb kérdésekre kaphat választ:
- Ez indításának időpontját?
- Mennyit kell azt tiszta elindítása után?

Az alábbiakban néhány autovacuum konfigurációs paramétereket, frissítheti a fenti kérdések, valamint útmutatást alapján.
Paraméter|Leírás|Alapértelmezett érték
---|---|---
autovacuum_vacuum_threshold|A frissített vagy törölt rekordok egy táblában vákuumos műveletet aktiválásához szükséges minimális számát határozza meg. Az alapértelmezett érték 50 rekordokat. A paraméter értéke csak a postgresql.conf fájlban vagy a kiszolgáló parancssorból. Egyedi táblák a beállításának felülbírálása, módosítsa a table storage paramétereket.|50
autovacuum_vacuum_scale_factor|Adja meg a tábla mérete autovacuum_vacuum_threshold hozzá, amikor eldönti, hogy egy vákuumos műveletet aktiválásához töredékéért. Az alapértelmezett érték 0.2-es, amely tábla méretének 20 százaléka. A paraméter értéke csak a postgresql.conf fájlban vagy a kiszolgáló parancssorból. Egyedi táblák a beállításának felülbírálása, módosítsa a table storage paramétereket.|5 %-os
autovacuum_vacuum_cost_limit|Automatikus vákuumos műveletekben használt költség korlát értékét adja meg. Ha a -1 van megadva, ez az alapértelmezett beállítás, a rendszeres vacuum_cost_limit érték szolgál. Ha egynél több feldolgozói, az érték eloszlik a futó autovacuum dolgozók körében. A minden korlátokat összegét nem haladja meg a változó értékét. A paraméter értéke csak a postgresql.conf fájlban vagy a kiszolgáló parancssorból. Egyedi táblák a beállításának felülbírálása, módosítsa a table storage paramétereket.|-1
autovacuum_vacuum_cost_delay|Az automatikus vákuumos műveletekben használt költség késleltetés értéket ad meg. Ha meg van adva a -1, a rendszeres vacuum_cost_delay érték szolgál. Az alapértelmezett érték: 20 ezredmásodperc. A paraméter értéke csak a postgresql.conf fájlban vagy a kiszolgáló parancssorból. Egyedi táblák a beállításának felülbírálása, módosítsa a table storage paramétereket.|20 ms
autovacuum_nap_time|Adja meg a minimális késleltetési autovacuum közötti bármely adott adatbázist futtat. Mindegyik ciklikus a démon megvizsgálja az adatbázist, és problémák VÁKUUM és elemzés parancsok szükség szerint az adott adatbázisban lévő táblák. A késleltetés idejének mérése másodpercben történik, és az alapértelmezett érték egy perc (1 perc). A paraméter értéke csak a postgresql.conf fájlban vagy a kiszolgáló parancssorból.|15 s
autovacuum_max_workers|Itt adható meg autovacuum folyamatok, a autovacuum indítója, egyidejűleg futtatható eltérő maximális számát. Az alapértelmezett érték három. A paraméter értéke csak a kiszolgáló indításakor.|3
Bírálja felül az egyes táblák beállításait, módosítsa a table storage paramétereket. 

## <a name="autovacuum-cost"></a>Autovacuum költség
Az alábbiakban a "" futtatásával járó költségek vákuumos műveletet:

- A lapok, amely a vákuum futtat zárolva vannak.
- Számítást és memóriát használatosak, amikor egy vákuumos feladat fut-e.

Ennek eredményeképpen nem vákuumos feladatok futtatása vagy túl gyakran, vagy túl ritkán. Egy vákuumos feladatnak szüksége van a számítási feladathoz igazítani. Tesztelje az összes autovacuum paraméter módosításokat a kompromisszumot kínál a mindegyiknek miatt.

## <a name="autovacuum-start-trigger"></a>Autovacuum kezdő eseményindító
Autovacuum akkor aktiválódik, ha a kézbesíthetetlen rekordok száma meghaladja a autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples. Reltuples Íme egy konstans.

Az adatbázis betöltési autovacuum adattisztítást kell tartani. Ellenkező esetben előfordulhat, hogy elfogy a storage, és a lekérdezések egy általános lassulás élmény. Amortizált idővel, a sebesség, amellyel egy vákuumos művelet megtisztítja kézbesíthetetlen rekordokat tartalmazó meg kell egyeznie az a sebesség, amellyel kézbesíthetetlen rekordok jönnek létre.

Számos frissítések és törlések adatbázis további kézbesíthetetlen rekordokat tartalmazó rendelkezik, és további tárterületre van szükség. Általában a legtöbb adatbázisok frissíti, és benefit töröl az autovacuum_vacuum_scale_factor és autovacuum_vacuum_threshold alacsony értéket. A kis-értékek kézbesíthetetlen rekordokat tartalmazó hosszan tartó felhalmozódásához megakadályozása. A magasabb értékek használhatja mindkét paraméter kisebb adatbázisok, mert vacuuming szükség a kevésbé sürgős. Gyakori vacuuming cserébe számítási és memória érhető el.

Az alapértelmezett mértéktényező 20 %-os jól alacsony százalékos arányában kézbesíthetetlen rekordokat tartalmazó táblák működik. A magas százalékos kézbesíthetetlen rekordokat tartalmazó táblák jól nem működik. Például egy 20 GB-os tábla a méretezési tényező a rendszer lefordítja arra kézbesíthetetlen rekordokat tartalmazó 4 GB. Egy 1 TB-os tábla leállási rekordokat 200 GB.

A PostgreSQL táblázat vagy a példány szintjén állíthatja be ezeket a paramétereket. Még ma beállíthatja ezeket a paramétereket a tábla szintjén csak az Azure Database for postgresql-hez.

## <a name="estimate-the-cost-of-autovacuum"></a>Autovacuum költségének
Futó autovacuum "költséges", és a futtatókörnyezet vákuumos műveletek szabályozása paraméterei. A következő paraméterek segítségével vákuum futtatásával járó költségeket megbecsülheti:
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

A vákuumos folyamat olvassa be a fizikai lapok és kézbesíthetetlen rekordokat keres. Kell díjat fizetni, 1 (vacuum_cost_page_hit) összes lapjának shared_buffers minősül. Az összes többi oldalán tekinthetők kell 20 (vacuum_cost_page_dirty), ha kézbesíthetetlen rekordokat tartalmazó létezik, vagy (vacuum_cost_page_miss), 10 díjat fizetni, ha nincs kézbesíthetetlen rekordok léteznek. A vákuumos művelet leáll, amikor a folyamat meghaladja a autovacuum_vacuum_cost_limit. 

A korlát elérése után a folyamat alvó állapotban marad a autovacuum_vacuum_cost_delay paraméterrel megadott újra megkezdése előtt idejére. Ha nem elérte a korlátot, autovacuum elindul, a autovacuum_nap_time paraméter által meghatározott érték utáni.

Összefoglalva a autovacuum_vacuum_cost_delay és autovacuum_vacuum_cost_limit paraméterek szabályozhatja, mennyi adat-karbantartási idő egységenként engedélyezett. Fontos, hogy a legtöbb tarifacsomagok túl alacsony az alapértelmezett értékeket. Az optimális a paraméterek értékei vannak árképzési szint-függő, és ennek megfelelően kell konfigurálni.

A autovacuum_max_workers paraméter határozza meg, amelyek egyidejűleg futtathatók autovacuum folyamatok maximális számát.

A PostgreSQL táblázat vagy a példány szintjén állíthatja be ezeket a paramétereket. Még ma beállíthatja ezeket a paramétereket a tábla szintjén csak az Azure Database for postgresql-hez.

## <a name="optimize-autovacuum-per-table"></a>Táblánként autovacuum optimalizálása
Minden az előző konfigurációs paramétereket táblánként konfigurálhatja. Például:
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Autovacuum egy olyan tábla szinkron folyamat. Nagyobb százalékos aránya, amelyek a tábla rendelkezik, a magasabb kézbesíthetetlen rekordok autovacuum "költség". Akkor is feloszthatja az táblákat, amelyek nagy mértékű és törléseket több táblára felosztva. Táblák felosztás segít autovacuum párhuzamosíthatja, és költségcsökkentés a "" egy táblán autovacuum végrehajtásához. Győződjön meg arról, hogy a munkavállalók liberally ütemezett párhuzamos autovacuum-feldolgozók száma is növelhető.

## <a name="next-steps"></a>További lépések
Használja, és finomhangolása autovacuum kapcsolatos további tudnivalókért tekintse meg a következő PostgreSQL dokumentációja:

 - [Fejezet 18-kiszolgálókonfiguráció](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
 - [24. fejezet rutin adatbázis-karbantartási feladatok](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
