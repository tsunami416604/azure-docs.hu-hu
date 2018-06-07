---
title: Intelligens, amelyen az Azure SQL Database teljesítménnyel kapcsolatos problémák elhárítása |} Microsoft Docs
description: Intelligens Insights segítséget nyújt az Azure SQL Database teljesítménnyel kapcsolatos problémák elhárítása.
services: sql-database
author: danimir
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: v-daljep
ms.openlocfilehash: 68ccf6f64f90200359322f35ca081aa6b53493f9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648268"
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Intelligens, amelyen az Azure SQL Database teljesítménnyel kapcsolatos problémák elhárítása

Ezen a lapon észlelt az Azure SQL Database teljesítménnyel kapcsolatos információkat nyújt a [intelligens Insights](sql-database-intelligent-insights.md) adatbázis teljesítményének diagnosztikai naplófájlok. Ez a diagnosztika napló küldhető [Azure Naplóelemzés](../log-analytics/log-analytics-azure-sql.md), [Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage), vagy egy külső megoldás egyéni DevOps és jelentéskészítési lehetőségei képességek.

> [!NOTE]
> Egy SQL-adatbázis teljesítményének hibaelhárítási útmutató intelligens Insights keresztül, tekintse meg a [ajánlott a hibaelhárítási folyamat](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) folyamatábra ebben a dokumentumban.
>

## <a name="detectable-database-performance-patterns"></a>Észlelhető adatbázis teljesítményének minták

Intelligens Insights teljesítményproblémák automatikusan észleli az SQL Database lekérdezés végrehajtási várakozási idő, a hibák, vagy a időtúllépéseket alapján. Majd exportálja a észlelt teljesítmény minták a diagnosztika a naplófájlba. Észlelhető teljesítmény mintákat az alábbi táblázat foglalja össze:

| Észlelhető teljesítmény minták | Outputted részletei |
| :------------------- | ------------------- |
| [Az erőforrás-korlátozások](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Rendelkezésre álló erőforrások (dtu-k), adatbázis munkaszál vagy adatbázis-bejelentkezési munkamenetek a figyelt előfizetésben elérhető fogyasztásának elérte-korlátok, amelyek hatására az SQL Database teljesítménnyel kapcsolatos problémák. |
| [Munkaterhelések növelése](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | Munkaterhelés növelését vagy az adatbázis terhelése folyamatos felhalmozódásához volt észlelhető, amelyek hatására az SQL Database teljesítménnyel kapcsolatos problémákat. |
| [Memória](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | Memória biztosít kért munkavállalók kell megvárnia a memória-kiosztásokat statisztikailag jelentős mennyiségű időt. Vagy nagyobb felhalmozódása a dolgozók által kért memória biztosít létezik, amely befolyásolja az SQL-adatbázis teljesítményét. |
| [Zárolás](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | Túl sok adatbázis-zárolás észlelt, amely befolyásolja az SQL-adatbázis teljesítményét. |
| [Nagyobb MAXDOP](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | Párhuzamossági beállítás (MAXDOP) maximális fokú megváltozott, és hatással van a lekérdezés végrehajtása hatékonyságát. |
| [Pagelatch versengés](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Pagelatch versengés észlelt, amely befolyásolja az SQL-adatbázis teljesítményét. Több szál egyszerre ugyanazon a memóriában levő puffer oldalak elérésére tett kísérlet. Nagyobb várakozási idő, az eredmény érintő SQL-adatbázis teljesítménye. |
| [Hiányzó Index](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | Egy hiányzó index problémát észlelt, amely befolyásolja az SQL-adatbázis teljesítményét. |
| [Új lekérdezés](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | Új lekérdezés észlelt, amely hatással van az összteljesítményre SQL-adatbázis. |
| [Szokatlan várakozási statisztika](sql-database-intelligent-insights-troubleshoot-performance.md#unusual-wait-statistic) | Szokatlan adatbázis várakozási idő észlelt, amely befolyásolja az SQL-adatbázis teljesítményét. |
| [A TempDB versengés](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Több szál erőforrásokhoz próbálnak hozzáférni a azonos tempDB, amely azt eredményezi, a szűk keresztmetszetek, amely befolyásolja az SQL-adatbázis teljesítménye. |
| [A rugalmas készlet DTU kevés](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | Kevés a rendelkezésre álló Edtu a rugalmas készletben befolyásolja az SQL-adatbázis teljesítményét. |
| [Regressziós megtervezése](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | Egy új tervet vagy egy meglévő terv számítási változása volt észlelhető, SQL-adatbázis teljesítményét érintő. |
| [Adatbázis-alapú kötődő konfigurációs érték módosítása](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | A konfiguráció módosítása az adatbázis SQL-adatbázis teljesítményét befolyásolja. |
| [Lassú ügyfél](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | Nem tudja használni az SQL-adatbázis elég gyors kimenete lassú alkalmazás ügyfél észlelt, amely befolyásolja az SQL-adatbázis teljesítményét. |
| [Árképzési szint alacsonyabb szintre való visszalépést](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | Egy árképzési szint alacsonyabb szintre való visszalépést művelet csökkent SQL adatbázis teljesítményét érintő elérhető erőforrások. |

> [!TIP]
> Az SQL-adatbázis optimális teljesítményének folyamatos, engedélyezése [Azure SQL Database automatikus hangolása](https://docs.microsoft.com/azure/sql-database/sql-database-automatic-tuning). Ez az SQL Database beépített funkciói egyedi szolgáltatása folyamatosan figyeli az SQL-adatbázis, automatikusan beállítja a indexek és lekérdezés végrehajtási terv javításokat vonatkozik.
>

Az alábbi szakasz a fent felsorolt észlelhető teljesítmény minták részletesebben ismerteti.

## <a name="reaching-resource-limits"></a>Az erőforrás-korlátozások

### <a name="what-is-happening"></a>mi történik

Ebben a mintában észlelhető teljesítmény érhető el erőforrás-korlátozások, a munkavégző vonatkozó korlátokat és a munkamenet korlátok elérése kapcsolatos teljesítményproblémák egyesíti. Ezen teljesítményprobléma észleli, miután a diagnosztikai naplófájl Leírás mezőt azt jelzi, hogy a teljesítménycsökkenés oka kapcsolatos erőforrás, a munkavégző vagy a munkamenet korlátok.

SQL-adatbázis erőforrásainak általában nevezzük [DTU-erőforrásokat](https://docs.microsoft.com/azure/sql-database/sql-database-what-is-a-dtu). A Processzor- és IO (adatok és a tranzakciós napló IO) erőforrások kevert mérték állnak. A mintát erőforrás korlátok elérése a rendszer felismeri észlelésekor lekérdezési teljesítmény romlását okozzák bármelyik a mért erőforrás korlátok elérése.

A munkamenet-korlátok erőforrás azt jelzi, hogy az SQL-adatbázist a rendelkezésre álló egyidejű bejelentkezések száma. Ez a teljesítmény minta van ismer fel, ha az SQL-adatbázishoz kapcsolódó alkalmazások elérte az adatbázis elérhető egyidejű bejelentkezések száma. Ha alkalmazások próbálnak további munkamenetek, mint amennyi egy adatbázis elérhető, a lekérdezési teljesítményt befolyásolja.

Egy adott esetben erőforrás korlátok el, mert a rendelkezésre álló munkavállalók nem számítanak a DTU-használatát a munkavégző korlátok elérése. Adatbázison munkavégző korlátok elérése okozhat a megnövekedhet erőforrás-specifikus várakozási idő szerint, így ez a lekérdezés teljesítménycsökkenést.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai naplófájl kimenete lekérdezés kivonatokat a lekérdezések, amelyek hatással a teljesítmény- és erőforrás-használat százalékos aránya. Ez az információ kiindulási pontként használható az adatbázisban munkaterhelés optimalizálása. Különösen optimalizálhatja a lekérdezések, amelyek hatással vannak a teljesítmény romlását indexek hozzáadásával. Vagy egy további még munkaterhelés terjesztési alkalmazásokat is optimalizálhatja. Ha nem sikerül terhelésének csökkentése vagy optimalizálást, érdemes megfontolni az SQL-adatbázis előfizetéshez növelje meg a rendelkezésre álló erőforrások tarifacsomagját.

Elérte az elérhető munkamenet-korlátok, ha az alkalmazások optimalizálhatja a adatbázis bejelentkezések számának csökkentésével. Ha nem tud az adatbázishoz az alkalmazásaiból bejelentkezések számának csökkentése érdekében, fontolja meg az adatbázis árképzési szintjének növelése. Vagy ossza fel, és az adatbázis áthelyezése több elosztott terhelésű alkalmazások és szolgáltatások terjesztési több adatbázisra.

Lásd: munkamenet korlátok feloldásával kapcsolatos további javaslatok [SQL adatbázis maximális bejelentkezések határain kezelése](https://blogs.technet.microsoft.com/latam/2015/06/01/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/). Az előfizetési szinten érhető el erőforrás-határértékeken regisztrációval, lásd: [SQL-adatbázis erőforrás korlátok](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits).

## <a name="workload-increase"></a>Munkaterhelések növelése

### <a name="what-is-happening"></a>mi történik

Ez a teljesítmény-minta azonosítja a probléma miatt a munkaterhelés növelését, vagy egy munkaterhelés pile-up súlyosabb formájában.

Az észlelés több metrikák útján történik. Az alapvető metrika mért növelését keresi az elmúlt munkaterhelés alaptervhez képest terheléseknél engedélyezett. Észlelési más formája alapján mérési aktív munkaszál egy nagymértékű növekedésének megfelelő méretű-e a lekérdezés teljesítményét.

Súlyosabb formájában a munkaterhelés előfordulhat, hogy folyamatosan egymásra az SQL-adatbázis nem kezeli az alkalmazások és szolgáltatások miatt. Egy folyamatosan növekvő munkaterhelés méretet, amelyet a munkaterhelés pile-up feltétel eredménye. Ezen állapot miatt növekszik, a munkaterhelés megvárja-e a végrehajtási időt. Ez a feltétel jelenti az egyik a legsúlyosabb károkat okozó adatbázis teljesítménye. A probléma a megszakított munkaszálak számának növekedése figyelési keresztül észlel. 

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai naplófájl kiírja a lekérdezések, amelyek végrehajtása növekedett, és a legnagyobb mértékben a terhelés növekedésével a lekérdezés lekérdezés kivonatát. Ez az információ kiindulási pontként használható a munkaterhelés optimalizálása. A lekérdezés a terhelés növekedésével követőt azonosítottak különösen fontos a kiindulási pontként.

Érdemes lehet a munkaterheléseket több egyenletesen az adatbázishoz való terjesztésére. Vegye figyelembe, hogy a lekérdezést, amely indexek hozzáadásával érinti a teljesítmény optimalizálása. A számítási feladatok között több adatbázist is előfordulhat, hogy terjesztése. Ha ezek a megoldások nem lehetséges, érdemes megfontolni az SQL-adatbázis előfizetéshez növelje meg a rendelkezésre álló erőforrások tarifacsomagját.

## <a name="memory-pressure"></a>Memória

### <a name="what-is-happening"></a>mi történik

Ez a teljesítmény minta azt jelzi, hogy akár teljesítménycsökkenés az aktuális adatbázis oka Memóriaterhelést, vagy több súlyos formájában memória pile-up feltétel, az elmúlt hét nap teljesítménybeli alapértékek képest.

Memóriaprobléma jelöli, amelyben nincs nagyszámú munkaszál kérelmező memória biztosít az SQL-adatbázis teljesítményének feltétel. A nagy mennyiségű okozza a magas kihasználtság memóriahiány, amelyben az SQL-adatbázis nem képes hatékonyan memóriát lefoglalni minden Worker kérésre. A leggyakoribb okai a problémához kapcsolódó egy kézzel az SQL-adatbázis számára rendelkezésre álló memória mennyiségét. Másrészt munkaterhelés növekedése növekedését munkaszál és a memóriaprobléma okozza.

A memóriaigény súlyosabb formátuma pile-up memóriahiány. Ez az állapot azt jelzi, hogy magasabb hány munkaszálat kért memória biztosít, mint a memória felszabadítása lekérdezések. A kérelmező memória is engedélyezi a munkaszálak számának előfordulhat, hogy lehet folyamatosan növekvő (felhalmozódjanak) mert az SQL-adatbázismotor nem elég hatékony foglalható le memória az igény kielégítésére. A memóriahiány pile-up jelenti az egyik a legsúlyosabb károkat okozó adatbázis teljesítménye.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai naplófájl kiírja a memória objektum adattár részletei a legmagasabb okát magas a memóriahasználat és a megfelelő időbélyeggel jelölésű adminisztrátor (Ez azt jelenti, hogy a munkavégző szál). Ezt az információt alapját képező hibaelhárításhoz használja. 

Optimalizálása, vagy távolítsa el a legmagasabb memóriahasználata írnokok kapcsolódó lekérdezések. Is biztosíthatja, hogy nem tervezi használni az adatok nem kérdez le. Jó gyakorlat, hogy mindig használja a WHERE záradék a lekérdezésekben. Emellett javasoljuk, hogy hozzon létre fürtözetlen indexeire kereshető adatokat vizsgálja meg helyett.

A munkaterhelés optimalizálása, vagy több adatbázis szétosztása is csökkentheti. Vagy a számítási feladatok több adatbázis közötti elosztását. Ha ezek a megoldások nem lehetséges, érdemes megfontolni a tarifacsomagot az SQL-adatbázis előfizetéshez növelje az adatbázis számára elérhető memória-erőforrások mennyiségét.

További hibaelhárítási javaslatokat, lásd: [memória meditation biztosít: az SQL Server érthetetlen memória fogyasztói több névvel](https://blogs.msdn.microsoft.com/sqlmeditation/2013/01/01/memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/).

## <a name="locking"></a>Zárolás

### <a name="what-is-happening"></a>mi történik

Ez a teljesítmény minta azt jelzi, hogy akár teljesítménycsökkenés az aktuális adatbázis, amelyben túlzott adatbázis-zárolás észleli az elmúlt hét nap teljesítménybeli alapértékek képest. 

A modern RDBMS zárolás elengedhetetlen, amelyben teljesítmény teljes méretű több egyidejű dolgozó munkatársak és párhuzamos adatbázis tranzakcióihoz futtatja, ahol csak lehetséges többszálas rendszerek bevezetését. A beépített hozzáférés mechanizmus, ahol csak egyetlen tranzakció kizárólag férhetnek hozzá a sorok, a lapok, a táblák és a fájlok, amelyek szükségesek, és egy másik tranzakció erőforrások befolyásolják hivatkozik zárolás ebben a környezetben. Ha a tranzakció, amelyek számára az erőforrások zárolva velük történik, az ezeket az erőforrásokat a zárolás úgy, amely lehetővé teszi, hogy más tranzakciók szükséges erőforrások eléréséhez. A zárolás további információkért lásd: [a database Engine zárolása](https://msdn.microsoft.com/library/ms190615.aspx).

Ha az SQL-kezelő által végrehajtott tranzakciók zárolni használja az erőforrások eléréséhez hosszabb ideig vár, a várakozási idő okozza a munkaterhelés végrehajtási teljesítmény csökkenése. 

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai naplófájl kimenete, használhatja a hibaelhárítási alapját képező zárolási adatokat. A jelentett blokkoló lekérdezések, ez azt jelenti, hogy azok a lekérdezések, amelyek a zárolási teljesítménycsökkenés elemzése, és távolítsa el őket. Bizonyos esetekben a blokkolási lekérdezések optimalizálása sikeres lehet, hogy legyen.

A probléma elhárítása érdekében a legegyszerűbb és legbiztonságosabb módja tartsa rövid tranzakciók és a zárolási kezdjen a legköltségesebb lekérdezések csökkentése érdekében. Kisebb műveletek műveletek nagy kötegelt bonthatja. Jó gyakorlat az, hogy csökkentse a lekérdezés zárolási erőforrásigényét azáltal, hogy a lekérdezés felhasználását. Csökkenti a nagy vizsgálatokat, mert növeli a megtalálásuk esélyét holtpont, és kedvezőtlen hatással az általános adatbázis teljesítménye. Azonosított lekérdezések, amelyek zárolását hozzon létre új indexeket, vagy oszlopok hozzáadása a meglévő index a táblázatbeolvasás elkerülése érdekében. 

További javaslatokat, lásd: [blokkoló zárolási eszkalációs az SQL Server által okozott problémák megoldásához](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in).

## <a name="increased-maxdop"></a>Nagyobb MAXDOP

### <a name="what-is-happening"></a>mi történik

Ez észlelhető teljesítmény minta azt jelzi, hogy egy feltétel, amelyben a kiválasztott lekérdezés végrehajtási terv lett párhuzamos működésű több mint kellett volna. Az SQL-adatbázis lekérdezésoptimalizáló is növelheti a számítási feladat teljesítményére, felgyorsíthatja dolog, ahol csak lehetséges párhuzamos lekérdezések végrehajtása. Bizonyos esetekben párhuzamos lekérdezések feldolgozásának munkavállalók egymáshoz szinkronizálását, és ugyanabban a lekérdezésben végrehajtása kevesebb párhuzamos munkavállalók, vagy akár bizonyos esetekben egy egyetlen munkavégző szál képest képest eredmények egyesítése Várakozás több időt tölt a.

A szakértő rendszer elemzi az aktuális adatbázis teljesítményének összehasonlítja az eredeti időszakkal. Meghatározza, hogy ha egy korábban elindított lekérdezése lassabb, mint előtt, mert a lekérdezés-végrehajtási terv több mint párhuzamos működésű kell tenni.

A MAXDOP kiszolgálói konfigurációs beállítás megadása az SQL Database segítségével szabályozhatja, hogy hány Processzormag használatát ugyanabban a lekérdezésben végre párhuzamosan is használható. 

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztika naplóban, amelynek végrehajtása időtartama magasabb, hiszen azok egynél kellett volna volt párhuzamos működésű lekérdezések kapcsolódó lekérdezés kivonatok kimenete. A napló is kiírja CXP várakozási idő. Most azt az időszakot, egyetlen szervező/koordinátor szál (szál 0) arra vár, hogy az eredmények egyesíti, és azokat, amelyek áthelyezése előtt minden más szálak jelöli. Emellett a diagnosztikai naplófájl kimenete, amely a gyenge teljesítményű lekérdezések végrehajtása általános várakoztak a várakozási idő. Ezt az információt alapját képező hibaelhárításhoz használja.

Először is optimalizálhatja, vagy egyszerűsítse a összetett lekérdezések. Jó gyakorlat az, hogy hosszú kötegelt feladatok kisebb feloszthatja. Is ellenőrizze, hogy létrehozta a lekérdezéseket indexek. Manuálisan is párhuzamossági (MAXDOP) a legnagyobb mértékben kényszeríthet egy lekérdezést, amely a gyenge hajt végre lett megjelölve. Ez a művelet T-SQL használatával, lásd: [a MAXDOP kiszolgálói konfigurációs beállítás](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

A beállítás a MAXDOP kiszolgáló konfigurációs beállítás nulla (0), mint az alapértelmezett érték azt jelzi, hogy SQL-adatbázis segítségével az összes rendelkezésre álló logikai Processzormagok parallelize szálainak egyetlen lekérdezés végrehajtása. Beállítás MAXDOP egy (1) azt jelzi, hogy csak egyetlen mag egy egyetlen lekérdezés-végrehajtás nem használható. Gyakorlatilag ez azt jelenti, hogy a párhuzamos végrehajtás ki van kapcsolva. Attól függően, hogy a /-esetenként az adatbázis és a diagnosztika rendelkezésre álló magot naplózza az adatokat, észlelheti a párhuzamos lekérdezés-végrehajtás, abban az esetben, ha a probléma megoldására szolgáló magok száma MAXDOP lehetőséget.

## <a name="pagelatch-contention"></a>Pagelatch versengés

### <a name="what-is-happening"></a>mi történik

Ez a teljesítmény minta azt jelzi, hogy az aktuális adatbázisban munkaterhelés miatti teljesítményromlás pagelatch versengés az elmúlt hét nap munkaterhelés alaptervhez képest.

Zárolás van életben ahhoz, hogy SQL-adatbázis által használt egyszerűsített szinkronizálási mechanizmus többszálas. A memórián belüli struktúrák, amely tartalmazza az indexet, a lapok és a más belső struktúrákat konzisztencia garantálják.

Nincsenek számos különböző típusú zárolás van életben elérhető az SQL-adatbázishoz. Egyszerűség érdekében puffer zárolás van életben védelmére használhatók a memóriában lapok pufferkészletben. IO zárolás van életben még nincs betöltve ebbe a pufferkészlet lapok védelmére használhatók. Adatok írni vagy olvasni a pufferkészlet oldal, amikor egy munkavégző szál kell először szerezzen be egy puffer zárolás a lap. Ha egy munkavégző szál megpróbál hozzáférni egy oldal, amely még nem érhető el a memóriában lévő pufferkészletben, egy IO kérelem betölteni a szükséges adatokat a tárolóból. Az események sorozatát teljesítménycsökkenés súlyosabb formája jelzi.

A lap zárolás van életben a versengés következik be, amikor több szál egyidejűleg próbál szerezni egy nagyobb várakozási idő bemutatja a lekérdezés-végrehajtás azonos memórián belüli struktúrán zárolás van életben. Esetén pagelatch IO versengés, ha adatokat érhető el a tárolóból, a várakozási idő az ennél nagyobb. Ez jelentősen csökkentheti alkalmazások és szolgáltatások teljesítményét. Pagelatch versengés a leggyakrabban használt szálak egymás vár, és több Processzor rendszeren erőforrások versengő a forgatókönyvet.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai naplófájl kimenete pagelatch versengés részleteit. Ezt az információt alapját képező hibaelhárításhoz használja.

Mivel a pagelatch SQL-adatbázis egy belső ellenőrzési mechanizmus, automatikusan meghatározza, hogy mikor érdemes használni azokat. Alkalmazás döntések, beleértve annak kialakítását séma, hatással lehet a zárolás van életben determinisztikus viselkedését miatt pagelatch viselkedését.

Egy hatékony módszer a zárolási versenyt szekvenciális Indexkulcs cseréje a segítségével egyenlően osztható el a Beszúrás az index tartományon keresztül hálója kulcs. Általában a index kezdő oszlop osztja el a munkaterhelés arányosan. Fontolja meg a tábla particionáló egy másik módszer. A particionált tábla számított oszlop séma particionálás kivonatát létrehozása egy általánosan használt megközelítés kiküszöböléséhez versengés túlzott zárolás. Esetén pagelatch IO versengés indexek bevezetéséről megelőzheti a teljesítménycsökkenés oka. 

További információkért lásd: [Diagnosztizálás és hárítsa el az SQL Server versengés zárolni](http://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) (PDF-fájl letöltése).

## <a name="missing-index"></a>Hiányzó Index

### <a name="what-is-happening"></a>mi történik

Ez a teljesítmény minta azt jelzi, hogy az aktuális adatbázis munkaterhelés teljesítménycsökkenés oka egy hiányzó index az elmúlt hét nap alaptervhez képest.

Az index lekérdezéseit felgyorsítása érdekében használatos. A táblabeli adatok gyors hozzáférést biztosít a dataset lapok kell meglátogatott vagy beolvasott számának csökkentésével.

Adott lekérdezések, amelyek teljesítménycsökkenést, hogy az észlelés, amelynek létrehozása indexek hasznos a teljesítmény lenne keresztül azonosítja.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai naplófájl lekérdezés kivonatokat a lekérdezések, alkalmazások és szolgáltatások teljesítményét befolyásoló azonosított kimenete. Indexek ezeket a lekérdezéseket hozhat létre. Is optimalizálhatja vagy távolítsa el ezeket a lekérdezéseket, ha azok nem lesz szükség. A jó teljesítmény gyakorlat az, hogy kerülje a nem használt adatok lekérdezése.

> [!TIP]
> Tudta, hogy az SQL Database beépített funkciói automatikusan kezelheti a legjobban teljesítő indexeket az adatbázisok esetében?
>
> Az SQL-adatbázis optimális teljesítményének folyamatos, javasoljuk, hogy engedélyezze [SQL-adatbázis automatikus hangolása](sql-database-automatic-tuning.md). Az SQL Database beépített funkciói ezen egyedi szolgáltatása folyamatosan figyeli az SQL-adatbázis és automatikusan beállítja és hoz létre az adatbázis indexeinek.
>

## <a name="new-query"></a>Új lekérdezés

### <a name="what-is-happening"></a>mi történik

A teljesítmény mintát azt jelzi, hogy egy új lekérdezést észlelt, amely rosszul végrehajtása és a munkaterhelések teljesítményét, a hét nap teljesítménybeli alapértékek képest.

Jó teljesítményű lekérdezés írása néha nehéz feladat lehet. A lekérdezések írásáról további információkért lásd: [írása az SQL-lekérdezések](https://msdn.microsoft.com/library/bb264565.aspx). A meglévő lekérdezési teljesítmény optimalizálása érdekében tekintse meg a [lekérdezés hangolása](https://msdn.microsoft.com/library/ms176005.aspx).

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai naplófájlok legfeljebb két új legtöbb CPU-felhasználása lekérdezéseket, ideértve a lekérdezés kivonatok kimeneti adatokat. Mivel a észlelt lekérdezés hatással van a számítási feladat teljesítményére, optimalizálhatja a lekérdezést. Jó gyakorlat az, hogy adatbeolvasás csak kell használnia. Javasoljuk továbbá lekérdezésekkel a WHERE záradékban. Javasoljuk továbbá, összetett lekérdezések egyszerűsítése, és őket felosztása kisebb lekérdezésekre. Egy másik jó gyakorlat az, hogy nagy kötegelt lekérdezések kötegelt kisebb lekérdezésekre lebontva. Új lekérdezések indexeinek bemutatása érdemes általában elhárítása érdekében ezen teljesítményprobléma.

Érdemes lehet [Azure SQL adatbázis-lekérdezési Terheléselemző](sql-database-query-performance.md).

## <a name="unusual-wait-statistic"></a>Szokatlan várakozási statisztika

### <a name="what-is-happening"></a>mi történik

Ebben a mintában észlelhető teljesítmény azt jelzi, hogy a munkaterhelés teljesítménycsökkenés, amelyben gyenge teljesítményű lekérdezések azonosítja az elmúlt hét nap munkaterhelés alaptervhez képest.

Ebben az esetben a rendszer nem lehet besorolni a gyenge teljesítményű lekérdezések a más kimutatható standard teljesítmény kategóriák, de azt észlelte, hogy a várakozási statisztika a regressziós felelős. Ezért úgy ítéli meg őket, a lekérdezések *szokatlan várakozási statisztika*, ahol a szokatlan Várjon, amíg a regressziós felelős statisztika is tesz elérhetővé. 

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai naplófájl kiírja a szokatlan várakozási idő – részletek információk, az érintett lekérdezések és a várakozási idő lekérdezés kivonatokat.

A rendszer sikeresen nem sikerült azonosítani a gyenge teljesítményű lekérdezések esetében, mert a diagnosztikai adatokat manuális hibaelhárítási jó kiindulási pont. Optimalizálhatja a lekérdezések teljesítményét. Bevált gyakorlat az csak adatokat kell használni, és egyszerűbbé és kisebb összetett lekérdezések lebontva beolvasása. 

További információ a lekérdezési teljesítmény optimalizálása: [lekérdezés hangolása](https://msdn.microsoft.com/library/ms176005.aspx).

## <a name="tempdb-contention"></a>A TempDB versengés

### <a name="what-is-happening"></a>mi történik

Ez észlelhető teljesítmény minta, amelyben a szűk keresztmetszetek a tempDB erőforrások elérésére tett kísérlet szálak létezik adatbázis teljesítményének feltételt jelzi. (Ez az állapot nem kapcsolódó IO.) Ezen teljesítményprobléma a jellemző forgatókönyv, hogy az összes létrehozása, használata és előfordulásoknál kis tempDB táblák egyidejű lekérdezések több száz. A rendszer azt észlelte, hogy a tempDB két tábla használatával párhuzamos lekérdezések száma az elmúlt hét nap teljesítménybeli alapértékek képest adatbázis teljesítményét befolyásoló elegendő statisztikai többszörösére növelhető.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai naplófájl kimenete tempDB versengés részleteit. A információt használhatja kiindulási pontként hibaelhárításhoz. Két dolgot is végezze el az ilyen versengés enyhítse, és növelhető a teljes munkaterhelés: leállíthatja, az ideiglenes táblák használata. A memóriaoptimalizált táblák is használhatja. 

További információkért lásd: [memóriaoptimalizált táblák bemutatása](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables). 

## <a name="elastic-pool-dtu-shortage"></a>A rugalmas készlet DTU kevés

### <a name="what-is-happening"></a>mi történik

Ebben a mintában észlelhető teljesítmény azt jelzi, hogy egy akár teljesítménycsökkenés az aktuális adatbázis munkaterhelés az elmúlt hét nap alaptervhez képest. Az előfizetés a rugalmas készletben használható Dtu hiánya okozza. 

SQL-adatbázis erőforrásainak általában nevezzük [DTU-erőforrásokat](sql-database-what-is-a-dtu.md), amely a Processzor- és IO (adatok és a tranzakciós napló IO) erőforrások kevert mérték áll. [A rugalmas készlet Azure-erőforrások](sql-database-elastic-pool.md) célra méretezéshez több adatbázis közötti megosztott rendelkezésre álló eDTU-erőforrások készleteként szolgálnak. Ha a rugalmas készletben rendelkezésre álló eDTU-erőforrások nem elég nagy az adatbázisok támogatásához a készletben, a rendszer a rendszer észlelt egy rugalmas készlet DTU kevés teljesítményprobléma.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai naplófájl kiírja a rugalmas készlet információkat, felsorolja a felső DTU-igényes adatbázis és a felső-igényes adatbázis használja a készlethez tartozó százalékaként tartalmaz.

Mivel ez a teljesítmény-feltétel a rugalmas készlet edtu / készlethez használ több adatbázis kapcsolódik, akkor a hibaelhárítási lépéseket a legfontosabb DTU-igényes adatbázis összpontosítanak. A felső-igényes adatbázis a munkaterhelés, amely tartalmazza ezeket az adatbázisokat a felső-igényes lekérdezések optimalizálását csökkentése érdekében. Akkor is biztosíthatja, hogy nem használt adatok nem kérdez le. Egy másik megoldás, alkalmazás optimalizálása a felső DTU-igényes adatbázisok segítségével, és újra elosztják a terhelést több adatbázis között.

Ha csökkentése és a felső DTU-igényes adatbázis aktuális terhelése optimalizálása nem lehetséges, érdemes megfontolni a rugalmas készlet árképzési szint. Például növelheti, nő a rugalmas készletben használható i az eredményeket.

## <a name="plan-regression"></a>Regressziós megtervezése

### <a name="what-is-happening"></a>mi történik

Ez észlelhető teljesítmény minta jelöli, amelyben SQL-adatbázist használja az optimálisnál gyengébb végrehajtási lekérdezéstervet feltétel. Az optimálisnál gyengébb terv általában hatására nagyobb lekérdezés-végrehajtás, ami már várjon az aktuális és az egyéb lekérdezések időpontokat.

Az SQL-adatbázis meghatározza, hogy a lekérdezés végrehajtási terv a legkevésbé a lekérdezés-végrehajtás költsége. A lekérdezések és a munkaterhelések módosítása típusaként néha a meglévő előfizetések a következők nem hatékony, vagy például SQL-adatbázis nem végezze el a megfelelő értékelése. Javítási alkot lekérdezés végrehajtási tervek manuálisan kényszeríthető.

Ebben a mintában észlelhető teljesítmény egyesíti a három különböző eseteit terv regressziós: új terv regressziós, a régi terv regresszióra és a meglévő módosította tervek munkaterhelés. Az adott típusú terv regressziós történt megtalálható a *részletek* tulajdonság a diagnosztika naplóban.

Az új csomag regressziós feltétel olyan állapotban, amelyben az SQL-adatbázis egy új lekérdezés végrehajtási terv, amely nem annyira hatékony, mint a régi terv végrehajtásának elkezdése hivatkozik. Amikor az SQL-adatbázis vált egy új, hatékonyabb tervet a régi terv, amely nem annyira hatékony, mint az új csomag használatával állapotára utal a régi terv regressziós feltétel. A meglévő módosította tervek munkaterhelés regressziós, amelyben a régi és az új csomagok folyamatosan alternatív, a gyenge teljesítményű terv felé több fog egyenleg állapotára utal.

A terv regresszió további információkért lásd: [Mi az, hogy az SQL Server terv regressziós?](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/06/09/what-is-plan-regression-in-sql-server/). 

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai naplófájl kiírja a lekérdezés a kivonatok, a jó terv azonosítója, a rossz terv azonosítója és a lekérdezés azonosítók. Ezt az információt alapját képező hibaelhárításhoz használja.

Terv jobb hajt végre az adott lekérdezések, amelyek a megadott lekérdezés kivonatok azonosíthatja, elemezheti. Miután eldöntötte, mely terv jobban működik a lekérdezések, manuálisan kényszerítheti. 

További információkért lásd: [megtudhatja, hogyan megakadályozza az SQL Server a terv regresszió](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/04/25/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/).

> [!TIP]
> Tudta, hogy az SQL Database beépített funkciói automatikusan kezelheti a legjobban teljesítő lekérdezés végrehajtási tervek az adatbázisok esetében?
>
> Az SQL-adatbázis optimális teljesítményének folyamatos, javasoljuk, hogy engedélyezze [SQL-adatbázis automatikus hangolása](sql-database-automatic-tuning.md). Ez az SQL Database beépített funkciói egyedi szolgáltatása folyamatosan figyeli az SQL-adatbázis és automatikusan beállítja és hoz létre a legjobban teljesítő lekérdezés végrehajtási tervek az adatbázisok esetében.
>

## <a name="database-scoped-configuration-value-change"></a>Adatbázis-alapú kötődő konfigurációs érték módosítása

### <a name="what-is-happening"></a>mi történik

Ez észlelhető teljesítmény minta azt jelzi, hogy egy feltétel, amelyben az adatbázishoz kötődő konfigurációs változása okoz teljesítmény regressziós észlelt az elmúlt hét nap adatbázis-munkaterhelések viselkedésére képest. Ebben a mintában azt jelzi, hogy az adatbázishoz kötődő konfigurációs végzett legutóbbi változás úgy tűnik, hogy hasznos lehet az adatbázis teljesítménye.

Minden egyes adatbázis adatbázishoz kötődő konfigurációs módosítások állíthat be. Ez a konfiguráció egyedi optimalizálásához az adatbázis-eseti alapon szolgál. A következő beállításokat lehet megadni minden egyes adatbázis számára: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES és törölje a jelet PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai naplófájlok kimenetek adatbázishoz kötődő konfigurációs elvégzett módosítások nemrég, ami miatt az előző hét nap-munkaterhelések viselkedésére képest teljesítménycsökkenést. Visszaállíthatja a konfigurációs módosítások az előző értéket. Is hangolhassa értékkel a kívánt teljesítményszintet eléréséig. Adatbázis-hatóköri konfiguráció értékek átmásolhatja egy hasonló adatbázis megfelelő teljesítményt biztosít. Ha nem sikerül, a teljesítmény hibaelhárításához, alapértelmezett SQL-adatbázis alapértelmezett értékekre, és próbálja meg ehhez az alaptervhez-től kezdődő finomhangolásához.

Az adatbázishoz kötődő konfigurációs és a konfiguráció módosítása a T-SQL-szintaxis optimalizálásához további információkért lásd: [Alter adatbázishoz kötődő konfigurációs (Transact-SQL)](https://msdn.microsoft.com/library/mt629158.aspx).

## <a name="slow-client"></a>Lassú ügyfél

### <a name="what-is-happening"></a>mi történik

Ez észlelhető teljesítmény minta egy feltételt, amelyben az SQL-adatbázis használata az ügyfél nem lehet felhasználni a kimenet az adatbázisból gyors az adatbázis küldése jelzi. SQL-adatbázis nem tárolja a végrehajtott lekérdezések eredményeit a puffer, mert lelassul, és a folytatás előtt a továbbított lekérdezést kimenetek fogyasztó ügyfél vár. Ez a feltétel is melyikhez kapcsolódhat egy hálózatot, amely nem elég elég gyors átvitelére a fogyasztó ügyfél kimeneteinek az SQL-adatbázis.

Ez a feltétel csak akkor, ha a teljesítmény regressziós észleli az elmúlt hét nap adatbázis-munkaterhelések viselkedésére képest jön létre. Ezen teljesítményprobléma észlelésekor előző teljesítménybeli viselkedésének csak akkor, ha egy statisztikailag jelentős teljesítménycsökkenés képest.

### <a name="troubleshooting"></a>Hibaelhárítás

Ez észlelhető teljesítmény minta egy ügyféloldali feltételt jelzi. Hibaelhárítás szükség, az ügyféloldali alkalmazás vagy ügyféloldali hálózati. A diagnosztikai naplófájl kiírja a lekérdezés kivonatokat készít, és úgy tűnik, hogy az ügyfél számára az elmúlt két órában felhasználni azokat a legtöbb várni várakozási idők. Ezt az információt alapját képező hibaelhárításhoz használja.

A lekérdezés az felhasználásához az alkalmazás teljesítményét is optimalizálhatja. Is érdemes lehet lehetséges hálózatot késési problémák szempontjából. A teljesítmény romlását probléma lett az utolsó 7 napos teljesítménybeli alapértékek változása alapján, mert a használatával megvizsgálhatja, hogy legutóbbi alkalmazás vagy a hálózati feltétel módosítások miatt a teljesítményesemény regressziós. 

## <a name="pricing-tier-downgrade"></a>Árképzési szint alacsonyabb szintre való visszalépést

### <a name="what-is-happening"></a>mi történik

Ebben a mintában észlelhető teljesítmény azt jelzi, hogy egy feltétel, amelyben az SQL-adatbázis-előfizetés az árképzési szint vissza lett. Csökkentési rendelkezésre álló erőforrások (Dtu) az adatbázishoz, mert a rendszer észlelt egy legördülő az aktuális adatbázis teljesítményét, az elmúlt hét nap alaptervhez képest.

Emellett okozhatja egy feltételt, amely az SQL-adatbázis-előfizetés az árképzési szint vissza és frissítettek egy magasabb réteg rövid időn belül. Az ideiglenes teljesítménycsökkenés kimutatása van outputted a Részletek területen a diagnosztika napló árképzési szint alacsonyabb szintre való visszalépést és frissítését.

### <a name="troubleshooting"></a>Hibaelhárítás

Ha a tarifacsomagot, és ezért érhető el az SQL Database dtu-i csökken, és a teljesítmény elégedett, nincs mit kell tennie. Ha Ön csökkenteni a díjcsomagot és színvonalas az SQL database teljesítménnyel, csökkenteni az adatbázis-terhelések, vagy növelje a magasabb szintű tarifacsomagban használható.

## <a name="recommended-troubleshooting-flow"></a>Ajánlott hibaelhárítási folyamata

 Kövesse a folyamatábra a teljesítménnyel kapcsolatos problémák elhárítása intelligens Insights segítségével ajánlott módszer.

Hozzáférés intelligens Insights Azure SQL elemzés címen az Azure portálon keresztül. Kísérlet történt a bejövő teljesítményriasztás megkereséséhez, és válassza ki azt. Határozza meg, mi történik az észlelések oldalon. A megadott kiváltó okának elemzése a problémát, a lekérdezés szövegének, a lekérdezési idő trendeket és a incidens alakulása láthatja. Kísérlet történt a probléma megoldásához a teljesítménycsökkenés oka kiküszöböléséhez az intelligens Insights ajánlás használatával. 

[![Hibaelhárítási folyamatábra](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Válassza ki a folyamatábra PDF-verziójának letöltéséhez.

Intelligens Insights általában kell végrehajtani a kiváltó okának elemzése a teljesítménycsökkenés oka egy órája. Ha a probléma nem találja az intelligens Insights és alapvető fontosságú, használja a Lekérdezéstár kell azonosítania a teljesítmény a probléma okának. (Ezek a problémák általában kevesebb, mint egy órával korábbiak.) További információkért lásd: [figyelemmel kísérni a teljesítményét a Lekérdezéstár használatával](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>További lépések
- Ismerje meg, [intelligens Insights](sql-database-intelligent-insights.md) fogalmakat.
- Használja a [intelligens Insights Azure SQL Database teljesítményét diagnosztikai naplófájl](sql-database-intelligent-insights-use-diagnostics-log.md).
- A figyelő [Azure SQL adatbázis Azure SQL elemzés használatával](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Ismerje meg, hogy [gyűjtése és felhasználása az Azure-erőforrások naplóadatait](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).
